# 🛡️ Project: BANG-SQUAD


> "Unreal Engine 5 기반의 객체지향/데이터 주도(Data-Driven) 설계와 실무 표준 컨벤션을 엄격히 적용한 4인 협동 3D 멀티플레이어 액션 RPG"

## 📖 프로젝트 개요 (Overview)
- **개발 기간:** 2026.01.05 ~ 2026.03.06 (약 2개월)[cite: 1]
- **개발 인원:** 5인 팀 프로젝트[cite: 1]
- **사용 엔진:** Unreal Engine 5.5.4[cite: 1]
- **핵심 기술:** C++, Blueprint, Server-Authoritative Architecture, Event-Driven UI, Sweep Collision
- **담당 역할:** 코어 아키텍처 및 메인 캐릭터(Mage, Paladin) 구현, 네트워크 전투 동기화, 인게임 시스템 연동[cite: 1]

*(💡 본 프로젝트는 현재 라이브 서비스 및 배포가 진행되지 않아 별도의 설치/실행 파일(.exe)은 제공하지 않습니다.)*

---

## 🏗️ 개발 철학 및 코드 컨벤션 (Architecture & Conventions)

### 1. 🎯 C++ ↔ Blueprint 명확한 역할 분리
*   **"Blueprint는 연출을, C++은 규칙을 담당한다."**[cite: 3]
*   **C++ 전담:** 물리 힘 계산, 충돌 판정, 데미지 처리, 상태 전이, 네트워크 권한 판단(Server Authority)[cite: 3].
*   **Blueprint 전담:** UI 시각적 갱신, 이펙트/사운드 재생, 카메라 연출[cite: 3]. (BP 내 물리 수치 직접 계산 엄격히 금지[cite: 3]).

### 2. 💻 Naming & Code Style
*   **엔진 표준 준수:** 멤버 변수와 함수명 등 모든 네이밍은 언리얼 엔진 공식 코딩 표준(`PascalCase`)을 엄격히 따랐습니다[cite: 2].
*   **가독성 중심 설계:** `A`(Actor), `U`(UObject), `F`(Struct) 등의 타입 접두사와 `bIs`/`bCan` 등 bool 변수 접두사를 일관되게 사용했습니다[cite: 2, 3].
*   **RPC 권한 명시:** `Server`, `Multicast`, `Client` 등의 키워드를 함수명에 명시하여 멀티플레이 환경에서의 실행 권한을 명확히 구분했습니다[cite: 2, 3].

---

## 🛠️ 핵심 시스템 및 기술 명세 (Core Systems)

### 1. ⚙️ 데이터 주도형(Data-Driven) 스킬 시스템 및 아키텍처
`BaseCharacter`에 공통 로직을 통합하고, 직업별 기믹만 파생 클래스에서 구현하는 객체지향 설계를 채택했습니다[cite: 1].
*   **FSkillData 연동:** 애니메이션 몽타주, 투사체 클래스, 쿨타임 등을 `FSkillData` 구조체로 캡슐화하여 DataTable과 완벽히 연동했습니다[cite: 1, 2].
*   **협업 효율 극대화:** 프로그래머의 리컴파일 개입 없이, 기획자가 에디터 상에서 실시간으로 밸런스와 리소스를 조절할 수 있습니다[cite: 1].

### 2. ⚡ Event-Driven UI 최적화 및 생명주기 관리
매 프레임 호출되는 무거운 Tick 연산을 배제하고, 리스너 패턴을 도입하여 성능을 극대화했습니다.
*   **멀티캐스트 델리게이트 적용:** 스킬 사용 시점에만 신호를 주고받는 `FOnSkillCooldownChanged` 델리게이트를 구축하여 평상시 UI 연산 비용을 0.1ms 미만으로 억제했습니다[cite: 1, 2].
*   **가비지 컬렉터(GC) 제어:** 전투 중 대량으로 스폰되는 데미지 플로팅 텍스트 액터에 `InitialLifeSpan`을 부여하여 잉여 데이터로 인한 메모리 누수를 원천 차단했습니다[cite: 1, 2].

### 3. 🌐 로컬 예측과 Server Authority 기반 네트워크 무결성
액션 RPG의 쾌적한 조작감과 데이터 무결성을 동시에 확보했습니다.
*   **제로 인풋 랙 (Zero Input Lag):** 클라이언트 화면에서는 서버 응답 대기 없이 로컬에서 즉시 애니메이션(`PlayActionMontage`)을 선행 재생하고, `IsLocallyControlled()` 분기를 통해 서버 방송 수신 시의 이중 재생을 차단했습니다[cite: 1, 2].
*   **서버 권한 검증:** 퍼즐 조작이나 타격/방어 판정 등 주요 엔진 연산은 반드시 `HasAuthority()`를 통과한 서버 엔진 내부에서만 전담하도록 설계하여 해킹과 디싱크(Desync)를 방지했습니다[cite: 1, 2].

### 4. 🎯 정밀 수학(Vector Math) 기반 전투 로직 (Paladin & Mage)
단순 박스 충돌체를 넘어선 수학적 계산으로 전투의 신뢰도를 높였습니다.
*   **고스트 스윙 방지 (Paladin):** 0.015초 주기의 박스형 레이캐스트(`SweepMultiByChannel`)로 무기 궤적의 이전/현재 위치를 촘촘히 스윕하여 프레임 누락에 의한 타격 무시 버그를 제거했습니다[cite: 1, 2].
*   **지향성 방어 (Paladin):** 캐릭터 시선 벡터와 들어오는 타격 방향 벡터의 **내적(Dot Product)**을 계산하여 전방 100도 내외의 유효 공격만 안정적으로 흡수하도록 설계했습니다[cite: 1, 2].
*   **표면 수학 지형 제어 (Mage):** 얼음 투사체 적중 시 표면의 노멀 벡터(`ImpactNormal`)를 추출해 `MakeFromZ` 회전 매트릭스를 구성하여, 지형 경사도에 완벽히 밀착된 장판 스폰을 구현했습니다[cite: 1, 2].

---

## 🔧 트러블슈팅 (Troubleshooting)

*   **패키징 환경 빙의(Possession) 지연 해결:** 
    에디터 플레이와 달리 패키징 빌드 멀티플레이 시, 핑 차이로 인해 캐릭터 `BeginPlay` 시점에 `PlayerController` 빙의가 늦어져 상호작용 타이머가 등록되지 않는 이슈 발생[cite: 1]. 이를 초기 등록 제약에서 벗어나, 판정 함수 내부에서 매 프레임 `GetController()` 유효성을 검증하는 **지연 평가(Lazy Evaluation)** 구조로 리팩토링하여 해결[cite: 1, 2].
*   **방패 외곽 피격 시 벡터 왜곡 현상 수정:** 
    물리 타격점(Hit Location) 기준으로 방향 벡터를 연산하여, 방패 끝단 피격 시 사선 벡터가 형성되어 내적 방어 판정에 실패하는 현상 발생[cite: 1]. 연산 기준점을 '캐릭터의 중심'과 '공격자의 중심'을 잇는 **절대 벡터**로 교체하여 물리적 타격점 오차에 구애받지 않는 안정성을 확보[cite: 1, 2].

---

## 📂 주요 소스 코드 구조 (Directory Structure)
*(💡 아래 링크를 클릭하시면 본인이 직접 구현한 C++ 파일로 이동합니다.)*

* 🔗 [**`BaseCharacter.h`**](Source/Project_Bang_Squad/Character/Base/BaseCharacter.h) & 🔗 [**`BaseCharacter.cpp`**](Source/Project_Bang_Squad/Character/Base/BaseCharacter.cpp) 
  : 데이터 주도형(Data-Driven) 스킬 프레임워크 설계 및 로컬 예측 기반 애니메이션 네트워크 동기화
* 🔗 [**`PaladinCharacter.cpp`**](Source/Project_Bang_Squad/Character/PaladinCharacter.cpp) 
  : Sweep Collision을 활용한 정밀 타격 궤적 판정 및 벡터 내적(Dot Product) 기반 지향성 방어 로직
* 🔗 [**`MageCharacter.cpp`**](Source/Project_Bang_Squad/Character/MageCharacter.cpp) 
  : 표면 노멀(Normal) 벡터 회전 매트릭스 적용 투사체 구현 및 인터페이스 다형성 기반 염력 상호작용
* 🔗 [**`StageMainWidget.cpp`**](Source/Project_Bang_Squad/UI/Stage/StageMainWidget.cpp) 
  : Tick을 완벽히 배제하고 멀티캐스트 Delegate를 활용한 Event-Driven UI 최적화 로직
