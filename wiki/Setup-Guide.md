# Setup Guide

## 요구 사항

| 구분 | 요구사항 |
|------|----------|
| Unity | 2020.3 LTS 이상 권장 |
| 플랫폼 | Windows / macOS |
| 지식 | Unity 2D, C# 기본 |

## 프로젝트 열기

### 1. 리포지토리 클론

```bash
git clone https://github.com/minjungsung/dongle-game.git
cd dongle-game
```

### 2. Unity에서 열기

1. Unity Hub → **Open** → 클론한 디렉토리 선택
2. Unity 에디터 버전 선택 (2020.3 LTS 이상)
3. 프로젝트 오픈

> **참고**: 이 리포는 핵심 스크립트(Dongle.cs, GameManager.cs)만 포함합니다. Unity 씬, 프리팹, 에셋은 별도 구성이 필요합니다.

### 3. 씬 구성

#### 필요한 게임 오브젝트

```
Hierarchy:
├── Main Camera
├── Canvas
│   ├── StartGroup (Panel + 시작 버튼)
│   ├── EndGroup (Panel + 점수 + 재시작 버튼)
│   ├── ScoreText (Text)
│   ├── MaxScoreText (Text)
│   └── SubTextScore (Text)
├── Line (게임 오버 경계선)
├── Bottom (바닥 충돌체)
├── GameManager (빈 오브젝트)
├── DongleGroup (동글 부모 오브젝트)
└── EffectGroup (이펙트 부모 오브젝트)
```

### 4. 프리팹 생성

#### Dongle 프리팹

1. 새 2D 오브젝트 생성 (Circle Sprite)
2. 컴포넌트 추가:
   - `Rigidbody2D` (중력 사용)
   - `CircleCollider2D`
   - `Animator` (레벨별 스프라이트 전환)
   - `SpriteRenderer`
   - `Dongle.cs` 스크립트
3. ParticleSystem 자식 오브젝트 추가
4. 프리팹으로 저장

#### Animator 설정

Dongle Animator에 `Level` (int) 파라미터를 추가하고, 각 레벨별 스프라이트 상태를 설정합니다:

```
Animator Controller:
  Parameters: Level (int)
  States: Level_0, Level_1, Level_2, ..., Level_N
  Transitions: AnyState → Level_X (Level == X)
```

### 5. GameManager 설정

GameManager 오브젝트에 `GameManager.cs`를 부착하고 인스펙터에서 연결합니다:

```
GameManager Inspector:
  [Core]
  - donglePrefab: Dongle 프리팹
  - dongleGroup: DongleGroup Transform
  - effectPrefab: Effect 프리팹
  - effectGroup: EffectGroup Transform
  - poolSize: 15 (권장)

  [Audio]
  - bgmPlayer: AudioSource (BGM)
  - sfxPlayer[]: AudioSource[] (효과음 채널)
  - sfxClip[]: AudioClip[] (LevelUp, Next, Attach, Button, GameOver)

  [UI]
  - startGroup: StartGroup GameObject
  - endGroup: EndGroup GameObject
  - scoreText: ScoreText Text
  - maxScoreText: MaxScoreText Text
  - subTextScore: SubTextScore Text

  [ETC]
  - line: Line GameObject
  - bottom: Bottom GameObject
```

### 6. 입력 설정

마우스/터치 입력:
- **마우스 클릭 & 드래그**: 동글 좌우 이동
- **마우스 버튼 놓기**: 동글 드롭

모바일 빌드 시 터치 입력이 자동으로 마우스 입력과 매핑됩니다.

## 빌드

### PC 빌드

```
File → Build Settings
  Platform: PC, Mac & Linux Standalone
  Add Open Scenes
  Build
```

### 모바일 빌드

```
File → Build Settings
  Platform: Android / iOS
  Switch Platform
  Player Settings:
    - Resolution: Portrait
    - Target Frame Rate: 60 (코드에서 설정됨)
  Build
```

## 커스터마이징

### 동글 레벨 수 조정

`GameManager`의 `maxLevel` 변수를 조정합니다. Animator에도 해당 레벨 수만큼 상태를 추가해야 합니다.

### 풀 사이즈 조정

`poolSize`를 인스펙터에서 조정합니다 (1~30). 화면에 동시에 존재할 수 있는 최대 동글 수에 영향을 줍니다.

### 경계 범위 조정

`Dongle.cs`의 좌우 경계값을 수정합니다:

```csharp
float leftBorder = -4.2f + transform.localScale.x / 2f;
float rightBorder = 4.2f - transform.localScale.x / 2f;
```
