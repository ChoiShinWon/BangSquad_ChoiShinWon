# 🛡️ BANG-SQUAD : 4인 협동 3D 멀티플레이어 액션 RPG
> **최신원 (Gameplay Programmer)**  
> 팀 프로젝트 중 **코어 아키텍처 설계, 네트워크 전투 동기화, 인게임 시스템 및 UI 통합 연동**을 전담하여 구현한 포트폴리오용 레포지토리입니다.

*(🔗 [YouTube 플레이 영상 링크 삽입 시 이곳에 추가])*

---

## 📌 1. 프로젝트 소개
*   **개발 기간:** 2026.01.05 ~ 2026.03.06 (약 2개월)
*   **개발 인원:** 5인 팀 프로젝트
*   **사용 엔진:** Unreal Engine 5 (5.5.4)
*   **주요 언어:** C++, Blueprints
*   **대상 플랫폼:** PC (Windows)
*   **담당 역할:** 코어 아키텍처 및 메인 캐릭터(Mage, Paladin) 구현, 네트워크 전투 동기화, 인게임 시스템 연동

*(💡 본 프로젝트는 현재 라이브 서비스 및 배포가 진행되지 않아 별도의 설치/실행 파일(.exe)은 제공하지 않습니다.)*

---

## 🏗️ 2. 개발 철학 및 코드 컨벤션
> 4인 멀티플레이와 물리 기반 상호작용의 안정성을 확보하기 위해, 엄격한 실무 기준의 공통 개발 컨벤션을 수립하여 개발했습니다.

### 🎯 핵심 설계 사상 (C++ ↔ Blueprint 역할 분리)
1. **"가독성은 축약보다 우선한다."**
2. **"Blueprint는 연출을, C++은 규칙을 담당한다."**
   * **C++ 전담:** 물리 힘 계산, 충돌 판정, 데미지 처리, 상태 전이, 네트워크 권한 판단(Server Authority)
   * **Blueprint 전담:** UI 표시, 이펙트 재생, 사운드, 카메라 연출 (BP 내 물리 수치 직접 계산 엄격히 금지)
3. **"한 클래스(혹은 BP)는 하나의 책임만 가진다."**

### 💻 Naming & Code Style
*   **언리얼 엔진 표준 준수:** 멤버 변수와 함수명 등 모든 네이밍은 언리얼 엔진 공식 코딩 표준인 `PascalCase`를 엄격히 따랐습니다.
*   **타입 및 상태 명시:** `A`(Actor), `U`(UObject/Component), `F`(Struct), `E`(Enum), `I`(Interface) 등의 타입 접두사와, `bool` 변수의 `bIs`/`bCan` 접두사를 일관되게 사용하여 가독성을 높였습니다.
*   **네트워크 동기화 (RPC):** `Server`, `Multicast`, `Client` 등의 키워드를 함수명에 명시하여 멀티플레이 환경에서의 권한과 역할을 직관적으로 파악할 수 있도록 설계했습니다.

### 📁 디렉토리 구조 및 협업 컨벤션
*   **Asset:** `Content/` 하위에 `Characters`, `Physics`, `Stage`, `UI`, `FX` 등으로 모듈화하였으며, `SM_`, `SK_`, `M_`, `NS_` 등 명확한 에셋 접두사를 사용했습니다.
*   **Git Branch:** `main` (배포), `develop` (통합), `feature/기능명` (개별 단위) 구조를 사용했습니다.

---

## 🚀 3. 핵심 기능 구현 및 코드 리다이렉션
> 각 항목의 링크를 클릭하면 본인이 직접 구현한 C++ 상세 코드를 확인할 수 있습니다.

### ⚔️ [1] 데이터 주도형(Data-Driven) 스킬 시스템 및 캐릭터 아키텍처
*   **내용:** `BaseCharacter` 내에 공통 로직을 집중하고, 애니메이션 몽타주, 투사체, 쿨타임 등을 하드코딩 없이 `FSkillData` 구조체로 캡슐화하여 DataTable과 연동.
*   **성과:** 프로그래머의 개입(리컴파일) 없이 기획자가 에디터에서 즉각적인 밸런싱 및 리소스 교체가 가능한 협업 최적화 환경 구축.
*   🔗 **[BaseCharacter.h 및 관련 코드 확인하기](Source/Project_Bang_Squad/Character/Base/BaseCharacter.h)**

### 🌐 [2] 멀티플레이 조작감 최적화 (Zero Input Lag)
*   **내용:** 서버 멀티캐스트 응답을 기다리지 않고 로컬에서 즉각 애니메이션을 선행 재생(`PlayActionMontage`)하는 로컬 예측 적용 및 `IsLocallyControlled()`를 통한 이중 재생 방지.
*   **성과:** 조작 지연을 제거하고 송수신 대역폭을 초당 3.5KB 수준으로 안정적으로 방어.
*   🔗 **[BaseCharacter.cpp 네트워크 로직 확인하기](Source/Project_Bang_Squad/Character/Base/BaseCharacter.cpp)**

### 🛡️ [3] 팔라딘(Paladin): 정밀 근접 판정 및 지향성 방어 (Server Authority)
*   **내용:** 
    *   0.015초 주기의 박스형 레이캐스트(`SweepMultiByChannel`)를 통한 고스트 스윙(Ghost Swing) 방지.
    *   캐릭터 시선 벡터와 공격 방향 벡터의 내적(Dot Product)을 서버 내부에서만 계산하여 핑(Ping) 조작이나 클라이언트 변조를 원천 차단.
*   🔗 **[PaladinCharacter.cpp 확인하기](Source/Project_Bang_Squad/Character/PaladinCharacter.cpp)**

### 🧙 [4] 메이지(Mage): 인터페이스 다형성 및 표면 수학 연산
*   **내용:**
    *   염력 액터들을 `IMagicInteractableInterface`로 추상화하여 결합도를 낮춤.
    *   스킬 적중 시 레이캐스트의 노멀 벡터(`ImpactNormal`)를 추출하여 경사로에 완벽히 밀착되는 3D 회전 매트릭스 구현.
*   🔗 **[MageCharacter.cpp 확인하기](Source/Project_Bang_Squad/Character/MageCharacter.cpp)**

---

## 🛠️ 4. 트러블슈팅 및 최적화

### 1. 패키징 환경 빙의(Possession) 지연과 생명주기 제어
*   **문제:** 에디터와 달리 패키징 빌드 환경에서 상호작용 기믹이 전혀 작동하지 않는 치명적 버그 발생.
*   **원인:** 네트워크 환경의 특성상 캐릭터 스폰(`BeginPlay`) 후 네트워크 지연을 거쳐 컨트롤러가 빙의되므로, 초기화 시점에 `IsLocallyControlled()`가 false를 반환하여 타이머가 유실됨.
*   **해결:** `BeginPlay`의 조건문을 제거하고, 매 프레임 `GetController()` 유효성을 검증하는 지연 평가(Lazy Evaluation) 구조로 변경하여 네트워크 동기화가 완료되는 즉시 활성화되도록 로직 수정.

### 2. 방패 외곽 피격 시 방어 실패 현상 (벡터 왜곡 롤백)
*   **문제:** 팔라딘이 정면 가드 중임에도, 방패의 끝단에 공격이 적중하면 데미지가 흡수되지 않는 버그 발생.
*   **원인:** 실제 물리 타격점(Hit Location) 기준으로 방향 벡터를 연산한 탓에, 방패 외곽 피격 시 사선 벡터가 형성되어 허용 내적각(-0.2f)을 초과함.
*   **해결:** 물리 타격점 의존성을 버리고 '내 캐릭터의 중심 좌표'와 '공격자의 중심 좌표'를 잇는 절대 벡터 기준으로 연산식을 교체. 어떠한 물리적 오차에도 의도한 정면 방어각이 유지되도록 신뢰성 확보.

---
*README Generated for Portfolio Purpose. Check the original branch for full team collaboration history.*
