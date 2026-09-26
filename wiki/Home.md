# Dongle Game

> Unity 캐주얼 머지 퍼즐 게임

## 게임 개요

수박 게임(Suika Game) 스타일의 캐주얼 머지 퍼즐 게임입니다. 동글(Dongle) 오브젝트를 떨어뜨려 같은 레벨의 동글끼리 합치면서 점수를 획득합니다.

### 게임 메카닉

#### 기본 규칙

1. 화면 상단에서 동글을 좌우로 이동시켜 원하는 위치에 떨어뜨림
2. 같은 레벨의 동글 두 개가 닿으면 **머지(Merge)** → 한 단계 높은 레벨의 동글로 변환
3. 머지 시 점수 획득
4. 동글이 화면 상단의 경계선을 넘으면 **게임 오버**

#### 동글 레벨 시스템

```
Level 0 → Level 1 → Level 2 → ... → maxLevel
(작은 동글)                        (가장 큰 동글)
```

- 각 레벨마다 다른 크기와 외형 (Animator의 Level 파라미터로 제어)
- 머지할 때마다 한 단계 상승
- `maxLevel`에 도달하면 더 이상 머지 불가

#### 드래그 & 드롭

```
마우스/터치 입력:
  Drag() → isDrag = true
    → 마우스 X 좌표 추적
    → 좌우 경계 제한 (-4.2 ~ 4.2, 동글 크기 고려)
    → Y 고정 (8), Z 고정 (0)
    → Lerp로 부드러운 이동

  Drop() → isDrag = false
    → 물리 시뮬레이션 활성화
    → 중력에 의해 아래로 낙하
```

#### 오브젝트 풀링

동글과 파티클 이펙트는 풀링으로 관리됩니다:

```
donglePool: List<Dongle>    — 동글 풀 (poolSize: 1~30)
effectPool: List<ParticleSystem> — 이펙트 풀
poolCursor: int             — 현재 풀 커서
```

#### 점수 시스템

- 머지 성공 시 점수 획득
- `scoreText`에 실시간 표시
- `maxScoreText`: `PlayerPrefs`에 저장된 최고 점수 표시
- 게임 종료 시 최고 점수 갱신 검사

#### 사운드

```csharp
public enum Sfx { LevelUp, Next, Attach, Button, GameOver }
```

| 효과음 | 트리거 |
|--------|--------|
| LevelUp | 머지 성공 시 |
| Next | 다음 동글 생성 시 |
| Attach | 동글 충돌/접촉 시 |
| Button | UI 버튼 클릭 시 |
| GameOver | 게임 오버 시 |

### 프로젝트 구조

```
dongle-game/
├── Dongle.cs         # 동글 오브젝트 (물리, 드래그, 머지)
└── GameManager.cs    # 게임 관리 (풀링, 점수, UI, 상태)
```

### UI 구성

```
Canvas
├── StartGroup     — 시작 화면 (게임 시작 버튼)
├── EndGroup       — 게임 오버 화면 (최종 점수, 재시작)
├── ScoreText      — 현재 점수
├── MaxScoreText   — 최고 점수
├── SubTextScore   — 보조 점수 텍스트
├── Line           — 경계선 (게임 오버 기준)
└── Bottom         — 바닥
```

## 관련 페이지

- [[Architecture]] — 게임 루프 및 머지 로직 상세
- [[Setup-Guide]] — Unity 프로젝트 설정 가이드
