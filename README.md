# 🛡️ BANG-SQUAD : 4인 협동 3D 멀티플레이어 액션 RPG
> **최신원 (Gameplay Programmer)**  
> 팀 프로젝트 중 **코어 아키텍처 설계, 네트워크 전투 동기화, 인게임 시스템 및 UI 통합 연동**을 전담하여 구현한 포트폴리오용 Fork 레포지토리입니다.

![커버 이미지 플레이스홀더: 발표자료 4p의 게임 로고나 멋진 스크린샷 삽입]
*(🔗 [YouTube 플레이 영상 링크 삽입 시 이곳에 추가])*

---

## 📌 1. 프로젝트 소개
*   **개발 기간:** 2026.01.05 ~ 2026.03.06 (약 2개월)
*   **개발 인원:** 5인 팀 프로젝트
*   **사용 엔진:** Unreal Engine 5 (5.5.4)[cite: 1]
*   **주요 언어:** C++, Blueprints[cite: 1]
*   **대상 플랫폼:** PC (Windows)
*   **담당 역할:** 코어 아키텍처 및 메인 캐릭터(Mage, Paladin) 구현, 네트워크 전투 동기화, 인게임 시스템 연동[cite: 1]

---

## 🏗️ 2. 프로젝트 구조 및 코드 컨벤션

### 아키텍처 설계 사상
본 프로젝트는 유지보수성과 확장성을 극대화하기 위해 **객체지향 설계(OOP) 및 데이터 주도(Data-Driven) 아키텍처**를 채택했습니다.

*   **중앙 집중화 및 컴포넌트 분리:** `ACharacter`를 상속받은 `BaseCharacter`에 공통 로직을 집중하고, 직업별 기믹은 가상 함수 오버라이드로 구현했습니다. 또한, 비대해지기 쉬운 체력 및 데미지 로직은 `UHealthComponent`로 분리하여 단일 책임 원칙(SRP)을 준수했습니다[cite: 1].
*   **UI 이벤트 기반(Event-Driven) 동기화:** 틱(Tick)을 통한 상태 검사를 배제하고 멀티캐스트 델리게이트(`FOnSkillCooldownChanged`)를 구축하여 로직과 뷰(View)의 의존성을 완전히 분리했습니다[cite: 1].

### 코드 및 협업 컨벤션
*(팀에서 사용한 폴더 구조나 PR 가이드가 있다면 이곳에 요약해서 적어주세요. 없다면 아래 예시를 참고하여 수정하세요.)*
*   **Directory Structure:** `Source/Project_Bang_Squad/Character`, `UI`, `MapPuzzle`, `Projectile` 등으로 기능별 모듈화
*   **Commit Message:** `[Feat]`, `[Fix]`, `[Refactor]`, `[Chore]` 접두사 사용

---

## 🚀 3. 핵심 기능 구현 및 코드 리다이렉션 (Key Features)
> 각 항목의 링크를 클릭하면 상세 코드를 확인할 수 있습니다.

### ⚔️ [1] 데이터 주도형(Data-Driven) 스킬 시스템 및 캐릭터 아키텍처
*   **내용:** 애니메이션 몽타주, 투사체 클래스, 쿨타임 등의 수치를 하드코딩하지 않고 `FSkillData` 구조체로 캡슐화하여 DataTable과 연동[cite: 1].
*   **성과:** 프로그래머의 리컴파일 없이 기획자가 에디터에서 즉각적인 캐릭터 밸런싱 및 리소스 교체가 가능한 협업 환경 구축[cite: 1].
*   🔗 **[BaseCharacter.h 확인하기 (경로 삽입)](#)**

### 🌐 [2] 네트워크 대역폭 및 조작감 최적화 (제로 인풋 랙)
*   **내용:** 통합 몽타주 제어 시스템(`PlayActionMontage`) 구현. 본인 화면에서는 즉각 애니메이션을 실행하고 멀티캐스트 수신 시 `IsLocallyControlled()`로 이중 재생을 방지[cite: 1].
*   **성과:** 액션 게임 특유의 조작 지연(Input Lag)을 제거하고, 송수신 대역폭을 초당 3.5KB 수준으로 안정적으로 방어[cite: 1].
*   🔗 **[BaseCharacter.cpp 확인하기 (경로 삽입)](#)**

### 🛡️ [3] 팔라딘(Paladin): 정밀한 근접 판정 및 지향성 방어 시스템
*   **내용:** 
    *   연속 충돌 검사: 0.015초 주기의 박스형 레이캐스트(`SweepMultiByChannel`)를 통해 고스트 스윙(Ghost Swing) 방지[cite: 1].
    *   지향성 방어: 캐릭터 시선 벡터와 공격 방향 벡터의 내적(Dot Product)을 계산해 전방 공격만 정밀하게 방어[cite: 1].
*   **성과:** 핑(Ping) 차이로 인한 디싱크를 방지하기 위해 Server-Authoritative 기반으로 물리 연산 무결성 확보[cite: 1].
*   🔗 **[PaladinCharacter.cpp 확인하기 (경로 삽입)](#)**

### 🧙 [4] 메이지(Mage): 인터페이스 기반 오브젝트 제어 및 표면 수학 연산
*   **내용:**
    *   염력 스킬: 퍼즐 오브젝트를 `IMagicInteractableInterface`로 추상화하여 결합도를 최소화하고 다형성 부여[cite: 1].
    *   얼음 화살: 적중 표면의 노멀 벡터를 추출하여 경사로에 밀착되는 3D 회전 매트릭스 계산 및 스폰[cite: 1].
*   🔗 **[MageCharacter.cpp 확인하기 (경로 삽입)](#)**

### 🖥️ [5] 인게임 시스템 (UI 및 환경 제어)
*   **내용:** 매 프레임 UI 연산을 0.1ms 미만으로 극소화한 쿨타임 UI 연동, 가비지 컬렉터 메모리 누수를 제어한 데미지 플로팅, CDO(Class Default Object)를 활용해 동적 Z축 오프셋을 적용한 몬스터 스포너 개발[cite: 1].
*   🔗 **[StageMainWidget.cpp 확인하기 (경로 삽입)](#)**
*   🔗 **[EnemySpawner.cpp 확인하기 (경로 삽입)](#)**

---

## 🛠️ 4. 트러블슈팅 (Troubleshooting)

### 1. 패키징 환경 빙의(Possession) 지연과 생명주기 불일치 문제[cite: 1]
*   **문제:** 에디터에서는 정상 작동하는 상호작용 기믹이 패키징 빌드 후 멀티플레이 접속 시 작동하지 않는 문제 발생[cite: 1].
*   **원인:** 네트워크 환경에서는 캐릭터 스폰(`BeginPlay`) 이후 네트워크 지연을 거쳐 `PlayerController`가 빙의되므로, 초기화 시점에 `IsLocallyControlled()`가 false를 반환하여 타이머가 등록되지 않음[cite: 1].
*   **해결:** `BeginPlay`에서의 타이머 등록 조건을 제거하고, 실제 상호작용 판정 함수 내부에서 `GetController()` 유효성을 매 프레임 검증하는 지연 평가(Lazy Evaluation) 구조로 변경하여 해결[cite: 1].

### 2. 방패 외곽 피격 시 지향성 방어 실패 버그 (벡터 연산 왜곡)[cite: 1]
*   **문제:** 팔라딘이 정면 방어 중임에도 방패 끝단(가장자리)에 피격 시 데미지가 들어오는 문제[cite: 1].
*   **원인:** 실제 물리 타격점(Hit Location) 기준으로 방향 벡터를 계산하여, 방패 외곽 타격 시 사선 벡터가 형성되어 허용 내적각을 벗어남[cite: 1].
*   **해결:** 타격점이 아닌 '캐릭터의 중심(`GetActorLocation()`)'과 '공격자 액터의 중심'을 잇는 절대 벡터 기준으로 연산식을 교체하여, 물리적 오차 없이 완벽한 정면 방어 시스템 구현[cite: 1].

---
*README Generated for Portfolio Purpose. For full team credits, please refer to `Team_README.md`.*
