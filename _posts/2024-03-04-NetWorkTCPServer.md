---
layout: single
title:  "[네트워크] TCP 통신 - 서버"
---


### 클라이언트 데이터
``` c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Sockets;
using System.Text;
using System.Threading.Tasks;

namespace ServerPractice2
{
    internal class ClientData
    {
        public TcpClient Client { get; set; }
        public byte[] Data { get; set; }

        // 개선할 점 : 클라이언트를 구별할 수 있는 방안이 필요함 
        // TODO : 클라이언트를 구별할 수 있는 Name 설정
        public string mName;
        
        public int mClientNumber;
        

        public ClientData(string name, TcpClient client)
        {
            Client = client;
            Data = new byte[1024];

            // 127.0.0.1:9999 포트번호 중 Split(. :)한 결과중 3번째에 해당하는 번호를
            // Client Number로 설정하기
            string clientEndPoint = client.Client.LocalEndPoint.ToString();
            char[] point = { '.', ':' };
            string[] splitedData = clientEndPoint.Split(point);
            mClientNumber = int.Parse(splitedData[3]);
            Console.WriteLine("{0}번 사용자 접속 성공", mClientNumber);
        }
    }
}
```

### 메인
``` c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace ServerPractice2
{
    internal static class Program
    {
        static void Main()
        {
            // 서버를 실행한다.
            Server server = new Server();
        }
    }
}

```

### Server
``` c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Sockets;
using System.Net;
using System.Text;
using System.Threading.Tasks;
using System.Diagnostics;

namespace ServerPractice2
{
    internal class Server
    {
        private TcpListener server;

        private int mClientCount;

        // 생성자 : 서버 구동이 시작될 때, 실행하는 것
        // 서버가 시작되고, 클라이언트 카운트를 0으로 설정
        public Server()
        {
            StartServer();
            mClientCount = 0;
        }

        private void StartServer()
        {
            // 서버 메모리 생성 부분
            // IP주소(컴퓨터)를 어떤 것이든 받아옴
            // 포트번호 0000 <= 번호 설정에 대해 이해하기 어려움
            // TODO: 포트번호에 대한 이해를 가지고 설정할 수 있도록 구현
            server = new TcpListener(new IPEndPoint(IPAddress.Any, 0000));
            server.Start();
            Console.WriteLine("서버를 시작합니다.");

            while (true)
            {
                PrintUI();
                string key = Console.ReadLine();
                int select;

                // 개선할 점 : 입력 -출력 과정에서 한번 더 키를 클릭해야 UI 조작이 가능한 현상이 있음. 
                // TODO : 한번 클릭하였을 때, 실행되도록 개선하기
                if (int.TryParse(key, out select))
                {
                    switch (select)
                    {
                        case 1:
                            FindClient();
                            break;
                        case 2:
                            server.Stop();
                            return;
                    }
                }
            }
        }

        private void FindClient()
        {
            ClientData callbackClient = new ClientData("클라이언트" + mClientCount++, server.AcceptTcpClient());
            RunAsyncCallback(callbackClient);
        }

        private void RunAsyncCallback(ClientData callbackClient)
        {
            NetworkStream ns = callbackClient.Client.GetStream();
            byte[] buffer = callbackClient.Data;
            
            // 비동기 읽기 실행
            ns.BeginRead(buffer, 0, buffer.Length, new AsyncCallback(ReadAsyncData), callbackClient);
        }

        private void ReadAsyncData(IAsyncResult ar)
        {
            ClientData callbackData = ar.AsyncState as ClientData;
            int count = callbackData.Client.GetStream().EndRead(ar);
            string result = Encoding.Default.GetString(callbackData.Data, 0, count);

            // 클라이언트 번호에 대한 의미는 크게 없다.
            Console.WriteLine("{0}번 클라이언트 : {1}", callbackData.mClientNumber, result);
            RunAsyncCallback(callbackData);
        }

        private void PrintUI()
        {
            // UI(사용자 인터페이스
            Console.WriteLine("==============서버 인터페이스==============");
            Console.WriteLine("1. 클라이언트 검색");
            Console.WriteLine("2. 서버 종료");
            Console.WriteLine("==========================================");
        }
    }
}
```

