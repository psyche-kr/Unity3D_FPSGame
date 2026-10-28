# FPS

Unity의 CharacterController, 새 Input System, NavMesh를 익히려고 만든 1인칭 슈터 학습 프로젝트입니다. 적 3명을 모두 처치하면 승리하고, 플레이어가 죽으면 패배합니다.

| 항목 | 내용 |
| --- | --- |
| 엔진 | Unity 6000.0.3f1 |
| 언어 | C# |
| 코드 위치 | `Assets/Scripts` |

## 구현 내용

- **플레이어 이동** (`PlayerController`): New Input System(`Move`, `Look`, `Fire`, `Reload`)으로 입력을 받고, `CharacterController`로 이동합니다. 중력과 낙하 속도 상한을 직접 계산하고, 접지 판정이 프레임마다 흔들리는 문제는 0.3초 유예 시간을 두어 처리했습니다. 대각선 이동 속도가 빨라지지 않게 입력 벡터를 정규화했고, 카메라 상하 각도는 ±89도로 제한했습니다.
- **무기** (`Weapon`, `ProjectileWeapon`): 기본 무기는 화면 중앙에서 레이캐스트를 쏘는 히트스캔이며, 탄창과 예비 탄약을 따로 관리하는 재장전, 총알 궤적(`LineRenderer`), 피격 파티클, 발사음을 구현했습니다. `ProjectileWeapon`은 `Weapon`을 상속해 `Fire()`만 바꾼 투척 무기로, 카메라 앞쪽에 30도 위로 힘을 줘서 폭탄(`Bomb`)을 던집니다. 무기는 리스트로 들고 있다가 바꿀 때마다 순서대로 활성화합니다.
- **체력** (`Health`): 피해 처리와 사망 알림을 `IHealthListener` 인터페이스로 분리해서, 플레이어와 적이 각자 사망 처리(`OnDie`)를 구현합니다.
- **적 AI** (`Enemy`): Idle, Follow, Attack, Die 네 상태의 FSM입니다. Idle에서 2초가 지나면 플레이어까지 거리를 재서 2m 이내면 공격, 아니면 `NavMeshAgent`로 추적합니다. 추적 중 2m 안에 들어오거나 경로가 없으면 다시 Idle로 돌아갑니다. 사망하면 2초 뒤에 오브젝트를 지우고 `GameManager`에 알립니다.
- **게임 흐름** (`GameManager`): 남은 적 수를 세서 0이 되면 승리, 플레이어가 죽으면 패배 문구와 재시작·종료 버튼을 보여 줍니다.

## 부록: 데이터 저장 실습

`JsonTest`는 Newtonsoft.Json으로 객체를 직렬화해 `persistentDataPath`에 저장하는 연습이고, `SimpleEncryptionUtility`는 AES로 저장 문자열을 암호화하는 연습입니다(저장할 때마다 새 IV를 만들어 앞에 붙입니다). 암호화 키가 코드에 고정되어 있어 학습용입니다.

## 참고

`Kevin Iglesias`의 모션 에셋 등 `Assets` 아래의 모델과 애니메이션은 외부 에셋입니다.
