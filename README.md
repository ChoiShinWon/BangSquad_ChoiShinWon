# 🛡️ Project: Bang-Squad

![메인 전투 연출 및 UI](./Images/BangSquad_Combat.gif)
![멀티플레이어 기믹 상호작용](./Images/BangSquad_Multiplay.gif)

> "Unreal Engine 5 기반의 객체지향/데이터 주도(Data-Driven) 설계와 실무 표준 컨벤션을 엄격히 적용한 4인 협동 3D 멀티플레이어 액션 RPG"

## 📖 프로젝트 개요 (Overview)
- **개발 기간:** 2026.01.05 ~ 2026.03.06 (약 2개월)
- **개발 인원:** 5인 팀 프로젝트
- **사용 엔진:** Unreal Engine 5.5.4
- **핵심 기술:** C++, Blueprint, Server-Authoritative Architecture, Event-Driven UI, Sweep Collision
- **담당 역할:** 코어 아키텍처 및 전투 구조 설계, 상호작용 인터페이스, 네트워크 전투 동기화, UI 연동 및 기믹 구현

*(💡 본 프로젝트는 현재 라이브 서비스 및 배포가 진행되지 않아 별도의 설치/실행 파일(.exe)은 제공하지 않습니다.)*

---

## 🏗️ 개발 철학 및 코드 컨벤션 (Architecture & Conventions)

### 1. 🎯 C++ ↔ Blueprint 명확한 역할 분리
*   **"Blueprint는 연출을, C++은 규칙을 담당한다."**
*   **C++ 전담:** 물리 힘 계산, 충돌 판정, 데미지 처리, 상태 전이, 네트워크 권한 판단(Server Authority).
*   **Blueprint 전담:** UI 시각적 갱신, 이펙트/사운드 재생, 카메라 연출. (BP 내 물리 수치 직접 계산 엄격히 금지).

### 2. 💻 Naming & Code Style
*   **엔진 표준 준수:** 멤버 변수와 함수명 등 모든 네이밍은 언리얼 엔진 공식 코딩 표준(`PascalCase`)을 엄격히 따랐습니다.
*   **가독성 중심 설계:** `A`(Actor), `U`(UObject), `F`(Struct) 등의 타입 접두사와 `bIs`/`bCan` 등 bool 변수 접두사를 일관되게 사용했습니다.
*   **RPC 권한 명시:** `Server`, `Multicast`, `Client` 등의 키워드를 함수명에 명시하여 멀티플레이 환경에서의 실행 권한을 명확히 구분했습니다.

---

## 🚀 핵심 기능 구현 및 코드 리다이렉션 (My Contributions)
> 각 항목의 링크를 클릭하면 본인이 직접 구현한 C++ 상세 코드를 확인할 수 있습니다.

### ⚔️ [1] 캐릭터 전투 구조 설계 및 아키텍처
*   **내용:** 공통 전투 로직의 확장성을 위해 `BaseCharacter` 중심의 상속 구조를 설계하고, 직업별 전투 특성(공격, 스킬, 방패)은 파생 클래스에 분리 구현하여 애니메이션 및 전투 판정 흐름을 유기적으로 연결했습니다.
*   **관련 코드:**
    *   🔗 [`BaseCharacter.h` & `.cpp`](Source/Project_Bang_Squad/Character/Base/BaseCharacter.cpp)
    *   🔗 [`PaladinCharacter.h` & `.cpp`](Source/Project_Bang_Squad/Character/PaladinCharacter.cpp)
    *   🔗 [`MageCharacter.h` & `.cpp`](Source/Project_Bang_Squad/Character/MageCharacter.cpp)

### 🧙 [2] Mage 상호작용 인터페이스 설계
*   **내용:** Mage 전용 퍼즐 및 기믹 오브젝트와의 상호작용 규약을 `MagicInteractableInterface`로 추상화하여, 액터 간 결합도를 낮추고 추후 기능 확장이 안전하게 이루어질 수 있는 구조를 설계했습니다.
*   **관련 코드:**
    *   🔗 [`MagicInteractableInterface.h`](Source/Project_Bang_Squad/Character/Player/Mage/MagicInteractableInterface.h)

### 🎯 [3] 공격, 스킬, 방패 및 투사체(Projectile) 처리
*   **내용:** 직업별 전투 루프의 핵심인 기본 공격과 스킬 발동 흐름을 제어했습니다. Paladin의 방패 기반 물리 전투와 Mage의 투사체 기반 전투에서 발생하는 충돌, 판정, 효과 적용 흐름을 체계적으로 정리했습니다.
*   **관련 코드:**
    *   🔗 [`MageProjectile.h` & `.cpp`](Source/Project_Bang_Squad/Projectile/MageProjectile.cpp)
    *   🔗 [`MageIceArrow.h` & `.cpp`](Source/Project_Bang_Squad/Projectile/MageIceArrow.cpp)
    *   🔗 [`IcePad.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/IcePad.cpp) / 🔗 [`MagicBoat.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/MagicBoat.cpp)
    *   🔗 [`Stage2MagicBall.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/Stage2MagicBall.cpp) / 🔗 [`PaladinSkill2Hammer.cpp`](Source/Project_Bang_Squad/Character/Player/Paladin/PaladinSkill2Hammer.cpp)

### 🖥️ [4] 이벤트 기반 UI 연동 (DamageText & 스킬 쿨타임)
*   **내용:** 전투 피드백의 즉각적인 전달을 위해 스킬 쿨타임 상태와 위젯을 이벤트 기반으로 연동하고, 엔진의 생명주기를 활용해 메모리 누수를 제어한 `DamageText` 액터 표시 로직을 구현했습니다.
*   **관련 코드:**
    *   🔗 [`DamageTextActor.cpp`](Source/Project_Bang_Squad/Character/Damage/DamageTextActor.cpp)
    *   🔗 [`SkillSlotWidget.h` & `.cpp`](Source/Project_Bang_Squad/UI/Stage/SkillSlotWidget.cpp)

### 🌀 [5] 몬스터 스폰 및 맵 패턴 오브젝트 구현
*   **내용:** 지형 데이터를 읽어 동적 Z축 오프셋을 적용하는 몬스터 스폰 제어 로직과, 맵 내 환경 상호작용 오브젝트(`WindZone`)의 동작 및 캐릭터 스킬과의 연동을 구현했습니다.
*   **관련 코드:**
    *   🔗 [`EnemySpawner.h` & `.cpp`](Source/Project_Bang_Squad/Character/Enemy/EnemySpawner.cpp)
    *   🔗 [`WindZone.h` & `.cpp`](Source/Project_Bang_Squad/Game/MapPattern/WindZone.cpp)

### 🌐 [6] 멀티플레이 네트워크 무결성 처리
*   **내용:** 클라이언트 간 전투 결과 불일치를 최소화하기 위해 **서버 권한(Server Authority)** 기반의 전투 처리 구조를 반영했습니다. 공격, 스킬, 상태 변경의 판정은 서버 기준으로 처리하고 클라이언트는 결과를 동기화하며, 전투 이벤트와 상태 전파를 분리해 네트워크 환경에서의 안정성을 확보했습니다.

---

## 🔧 트러블슈팅 (Troubleshooting)

*   **패키징 환경 빙의(Possession) 지연 해결:** 
    에디터 플레이와 달리 패키징 빌드 멀티플레이 시, 핑 차이로 인해 캐릭터 `BeginPlay` 시점에 `PlayerController` 빙의가 늦어져 상호작용 타이머가 등록되지 않는 이슈 발생. 이를 초기 등록 제약에서 벗어나, 판정 함수 내부에서 매 프레임 `GetController()` 유효성을 검증하는 **지연 평가(Lazy Evaluation)** 구조로 리팩토링하여 해결.
*   **방패 외곽 피격 시 벡터 왜곡 현상 수정:** 
    물리 타격점(Hit Location) 기준으로 방향 벡터를 연산하여, 방패 끝단 피격 시 사선 벡터가 형성되어 내적 방어 판정에 실패하는 현상 발생. 연산 기준점을 '캐릭터의 중심'과 '공격자의 중심'을 잇는 **절대 벡터**로 교체하여 물리적 타격점 오차에 구애받지 않는 안정성을 확보.

---
*README Generated for Portfolio Purpose. Check the original branch for full team collaboration history.*
