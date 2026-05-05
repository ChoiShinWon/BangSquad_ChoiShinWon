# 🛡️ Project: BANG-SQUAD

![메인 전투 연출 및 UI](./Images/BangSquad_Combat.gif)
![멀티플레이어 기믹 상호작용](./Images/BangSquad_Multiplay.gif)

> "Unreal Engine 5 기반 4인 협동 3D 멀티플레이 액션 RPG 팀 프로젝트입니다.  
> 본 README는 팀 전체 소개가 아니라 **개인 기여 포트폴리오 목적**의 문서입니다."

---

## 📖 프로젝트 개요 (Overview)
* **개발 기간:** 2026.01.05 ~ 2026.03.06
* **개발 인원:** 5인 팀 프로젝트
* **사용 엔진:** Unreal Engine 5.5.4
* **핵심 기술:** C++, Blueprint, Server-Authoritative 처리, Event-Driven UI, Sweep Collision
* **담당 역할:** 전투 캐릭터 구조 설계, Mage 인터페이스, 전투 네트워크 동기화, 전투 UI 연동, 몬스터 스폰 및 맵 기믹 일부 구현

---

## 🏗️ 아키텍처 및 컨벤션 (Architecture & Conventions)

### 🎯 C++과 Blueprint 역할 분리
* **C++ 전담:** 전투 규칙, 충돌 판정, 데미지 처리, 상태 전이, 네트워크 권한 처리
* **Blueprint 전담:** 연출, 이펙트, 사운드, UI 표현

### 💻 코드 스타일
* **Unreal 표준 네이밍** 사용
* 타입 접두사와 `bool` 접두사 일관 적용
* RPC 함수에 `Server`, `Client`, `Multicast` 의도를 명시

---

## 🚀 주요 기여 내용 (My Contributions)

### 1. ⚔️ 전투 캐릭터 구조 설계
공통 전투 로직을 `BaseCharacter`에 두고, 직업별 전투 특성을 `PaladinCharacter`, `MageCharacter`로 분리했습니다.
* 공통 전투 상태와 액션 흐름 설계
* 직업별 공격, 스킬, 방어 동작 분리
* 애니메이션 재생과 판정 로직 연동
* 네트워크 환경에서의 동작 일관성 고려

**🔗 관련 코드**
* [`BaseCharacter.h`](Source/Project_Bang_Squad/Character/Base/BaseCharacter.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Base/BaseCharacter.cpp)
* [`PaladinCharacter.h`](Source/Project_Bang_Squad/Character/PaladinCharacter.h) / [`.cpp`](Source/Project_Bang_Squad/Character/PaladinCharacter.cpp)
* [`MageCharacter.h`](Source/Project_Bang_Squad/Character/MageCharacter.h) / [`.cpp`](Source/Project_Bang_Squad/Character/MageCharacter.cpp)

### 2. 🧙 Mage 상호작용 인터페이스 설계
Mage 전용 퍼즐 및 기믹 상호작용을 인터페이스로 추상화해 결합도를 낮췄습니다.
* 상호작용 규약 표준화
* 하이라이트와 입력 처리 인터페이스 제공
* 기믹 액터 확장 시 안정성 확보

**🔗 관련 코드**
* [`MagicInteractableInterface.h`](Source/Project_Bang_Squad/Character/Player/Mage/MagicInteractableInterface.h)

### 3. 🎯 공격, 스킬, 방패, 투사체 처리
직업 전투 루프의 핵심인 공격 및 스킬 발동 흐름과 투사체 처리 로직을 구현했습니다.
* Mage 투사체 발사 및 충돌 판정
* Paladin 방패와 스킬 망치 처리
* 스킬 액션 지연 처리와 이펙트 연계
* 기믹형 오브젝트와 전투 흐름 연결

**🔗 관련 코드**
* **Mage 투사체:** [`MageProjectile.h`](Source/Project_Bang_Squad/Projectile/MageProjectile.h) / [`.cpp`](Source/Project_Bang_Squad/Projectile/MageProjectile.cpp) | [`MageIceArrow.h`](Source/Project_Bang_Squad/Projectile/MageIceArrow.h) / [`.cpp`](Source/Project_Bang_Squad/Projectile/MageIceArrow.cpp)
* **Mage 스킬/기믹:** [`Stage2MagicBall.h`](Source/Project_Bang_Squad/Character/Player/Mage/Stage2MagicBall.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/Stage2MagicBall.cpp) | [`MagicBoat.h`](Source/Project_Bang_Squad/Character/Player/Mage/MagicBoat.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/MagicBoat.cpp) | [`IcePad.h`](Source/Project_Bang_Squad/Character/Player/Mage/IcePad.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Player/Mage/IcePad.cpp)
* **Paladin 스킬:** [`PaladinSkill2Hammer.h`](Source/Project_Bang_Squad/Character/Player/Paladin/PaladinSkill2Hammer.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Player/Paladin/PaladinSkill2Hammer.cpp)

### 4. 🖥️ 전투 UI 연동
전투 피드백 즉시성을 위해 데미지 텍스트와 스킬 쿨타임 UI를 연동했습니다.
* `DamageText` 액터 생성, 표시, 수명 관리
* 쿨타임 델리게이트 기반 위젯 업데이트
* Stage UI 슬롯과 쿨타임 상태 연계

**🔗 관련 코드**
* **Damage Text:** [`DamageTextActor.h`](Source/Project_Bang_Squad/Character/Damage/DamageTextActor.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Damage/DamageTextActor.cpp)
* **Skill UI:** [`SkillSlotWidget.h`](Source/Project_Bang_Squad/UI/Stage/SkillSlotWidget.h) / [`.cpp`](Source/Project_Bang_Squad/UI/Stage/SkillSlotWidget.cpp) | [`StageMainWidget.h`](Source/Project_Bang_Squad/UI/Stage/StageMainWidget.h) / [`.cpp`](Source/Project_Bang_Squad/UI/Stage/StageMainWidget.cpp)

### 5. 🌀 몬스터 스폰 및 맵 패턴 구현
몬스터 스폰 로직과 맵 패턴 오브젝트를 구현했습니다.
* 지형 라인트레이스로 스폰 지점 보정
* 캡슐 높이 기반 Z 오프셋 적용
* 동시 스폰 수와 총 스폰 수 제한 관리
* `WindZone`의 상태 동기화와 이동 상태 복구 처리

**🔗 관련 코드**
* **Spawner:** [`EnemySpawner.h`](Source/Project_Bang_Squad/Character/Enemy/EnemySpawner.h) / [`.cpp`](Source/Project_Bang_Squad/Character/Enemy/EnemySpawner.cpp)
* **Wind Zone:** [`WindZone.h`](Source/Project_Bang_Squad/Game/MapPattern/WindZone.h) / [`.cpp`](Source/Project_Bang_Squad/Game/MapPattern/WindZone.cpp)

### 6. 🌐 멀티플레이 네트워크 처리
전투 결과와 상태 전파의 일관성을 위해 서버 권한 기반 구조를 적용했습니다.
* 서버 권한 기반 데미지 처리
* 스킬 실행 RPC 분리
* 상태 복제와 OnRep 기반 동기화
* 멀티캐스트 기반 액션 및 VFX 동기화

**💡 적용 예시:**
* `HasAuthority` 체크
* `Server_*`, `Client_*`, `Multicast_*` RPC
* `Replicated`, `ReplicatedUsing`, `GetLifetimeReplicatedProps`

---

## 🧾 저장소 정보 (Repository Info)
* **저장소 성격:** 팀 프로젝트 결과물을 정리한 개인 포트폴리오 문서 저장소
* **공개 여부:** Public Repository
* **프로젝트 상태:** 개발 완료, 비배포 포트폴리오 단계
* **대상 플랫폼:** PC (Windows)
* **대상 OS 버전:** Windows 10/11

---

## 🗂️ 프로젝트 구조 (Directory Structure)
```text
Source/Project_Bang_Squad/
├─ Character/      # 플레이어, 적, 전투 관련 핵심 로직
├─ Projectile/     # 투사체 및 충돌 처리
├─ Game/           # 게임 흐름, 맵 패턴, 상태 관리
├─ UI/             # HUD, 스킬 슬롯, 전투 피드백 UI
├─ Data/           # 데이터 테이블 및 데이터 에셋
└─ ...
Content/
Config/

---

## Note: 본 프로젝트는 현재 배포 및 라이브 서비스 단계가 아니므로 실행 파일 배포는 제공하지 않습니다.
