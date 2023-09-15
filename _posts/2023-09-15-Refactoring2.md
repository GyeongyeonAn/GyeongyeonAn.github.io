---
layout: single
title:  "[Refactoring] #2 GameManager 클래스 리팩토링"
---

## 리팩토링 전

``` c#
public class GameManager : MonoBehaviour
{
    [SerializeField] public AudioSource audioSource;
    public Player player { get; set; }
    public AudioClip buttonSound;

    public static GameManager instance;
    private void Awake()
    {
        if (instance != null)
        {
            Destroy(this);
            return;
        }
        instance = this;
        DontDestroyOnLoad(gameObject);
        player = new Player();
        player.AddGold(5000);
        audioSource.Play();
    }

    public static void MoveMainScene()
    {
        SceneManager.LoadScene("MainScene");
    }

    public static void MoveBattleScene()
    {
        SceneManager.LoadScene("BattleScene");
    }

    public static void MoveManageScene()
    {
        SceneManager.LoadScene("ManageScene");
    }

    public static void MoveMarketScene()
    {
        SceneManager.LoadScene("MarketScene");
    }

    public static void MoveSettingScene()
    {
        SceneManager.LoadScene("SettingScene");
    }

    public void OnPause()
    {
        Time.timeScale = 0f;
    }

    public void OffPause()
    {
        Time.timeScale = 1f;
    }

    public void ButtonSound()
    {
        gameObject.GetComponent<AudioSource>().PlayOneShot(buttonSound);
    }

    public void SetMusic(AudioClip audio)
    {
        audioSource.Stop();
        audioSource.clip = audio;
        audioSource.Play();
    }
}
```


## 리팩토링 후

``` c#
public class GameManager : MonoBehaviour
{
    [SerializeField] private AudioSource audioSource; // 배경 음악을 재생하는 오디오 소스
    [SerializeField] private AudioClip buttonSound;  // 버튼 클릭 시 재생할 소리

    public static GameManager s_Instance { get; private set; } // GameManager의 인스턴스 (싱글톤)

    public Player player { get; set; } // 플레이어 정보를 관리하는 객체

    private void Awake()
    {
        if (s_Instance != null)
        {
            // 이미 GameManager 인스턴스가 존재하면 중복 생성을 방지하고 경고 메시지를 출력
            Debug.LogWarning("GameManager instance already exists. Destroying duplicate.");
            Destroy(gameObject);
            return;
        }

        // GameManager 인스턴스를 설정하고 다른 씬으로 이동해도 유지되도록 설정
        s_Instance = this;
        DontDestroyOnLoad(gameObject);

        // 플레이어 객체를 생성하고 초기 골드를 설정하여 게임을 시작
        player = new Player();
        player.AddGold(5000);

        // 배경 음악을 재생
        audioSource.Play();
    }

    private void Start()
    {
        // 게임 시작 시 MainScene을 로드
        SceneManager.LoadScene("MainScene");
    }

    public static void MoveScene(string sceneName)
    {
        // 지정된 씬으로 이동하는 정적 메서드
        SceneManager.LoadScene(sceneName);
    }

    public void TogglePause()
    {
        // 일시 정지 상태를 전환 (0 또는 1로)
        Time.timeScale = Time.timeScale == 0f ? 1f : 0f;
    }

    public void PlayButtonSound()
    {
        // 버튼 클릭 시 버튼 사운드를 한 번 재생
        audioSource.PlayOneShot(buttonSound);
    }

    public void SetMusic(AudioClip audio)
    {
        // 음악을 설정하고 재생
        audioSource.Stop();
        audioSource.clip = audio;
        audioSource.Play();
    }
}
```

## 개선한 점
1. 싱글톤 패턴
    - 싱글톤을 프로퍼티로 만들고 set은 클래스 내에서만 되도록 했습니다.
    - static 프로퍼티 변수로 이름을 instance => s_Instance로 변경했습니다.
    - 인스턴스가 이미 존재하면 중복 생성을 방지하고, 경고 메시지를 출력하도록 했습니다.
2. SerializeField 및 Private 변수
    - 변수들을 private로 변경하여 다른 클래스에서 직접 접근하지 못하도록 하였습니다.
    - SerializeField를 사용하여 Inspector에서 변수에 접근할 수 있게 했습니다.
3. 씬 이동 메서드 리팩토링
    - 씬 이동 관련 메서드를 하나로 통합하였습니다. 
    - MoveScene 메서드를 사용하여 씬을 이동할 수 있습니다.
4. Pause 관련 메서드 개선
    - 조건 연산자 (Time.timeScale == 0f ? 1f : 0f)를 사용하여 2개의 함수를 통합했습니다. 
    - TogglePause 메서드를 사용하여 게임 일시 정지 상태를 토글할 수 있도록 합니다.
5. Start 메서드 추가
    - 게임 시작 시 MainScene으로 이동하도록 Start 메서드를 추가하였습니다.
6. 소리 재생 메서드 개선
    - PlayButtonSound 메서드를 사용하여 버튼 클릭 소리를 재생하도록 했습니다.
    - audioSource가 SerializeField를 통해 불러오기 때문에, GetComponent의 호출을 줄일 수 있습니다.
7. 주석
    - 주석을 달아서 코드의 가독성을 높였습니다.