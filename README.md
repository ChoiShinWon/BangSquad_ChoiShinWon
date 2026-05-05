# BangSquad

> Unreal Engine 5.5 C++ 기반 팀 멀티플레이 액션 프로젝트  
> 본 문서는 팀 프로젝트 전체 소개가 아닌, 제 개인 기여 중심 포트폴리오 README입니다.

---

## 프로젝트 개요
BangSquad는 로비에서 직업을 선택하고, 스테이지 메인 구간과 미니게임, 보스 구간을 진행하는 멀티플레이 프로젝트입니다.  
저는 캐릭터 전투 구조 설계와 전투 시스템 구현, 전투 UI 연동, 일부 맵 패턴 및 스폰 시스템, 네트워크 동기화를 담당했습니다.

- 엔진: Unreal Engine 5.5
- 언어: C++
- 플랫폼: PC (Windows)
- 개발 형태: 팀 프로젝트

---

## 내가 담당한 핵심 구현

### 1. 캐릭터 전투 구조 설계
`BaseCharacter`를 기반으로 공통 전투 구조를 설계하고,  
`PaladinCharacter`, `MageCharacter`에 직업별 전투 특성을 구현했습니다.

- 공통 전투 상태 및 흐름 설계
- 직업별 공격, 스킬, 방패 로직 분리
- 전투 판정과 애니메이션 이벤트 연동
- 멀티플레이 환경을 고려한 전투 구조 설계

관련 코드:
- `Source/Project_Bang_Squad/Character/Base/BaseCharacter.h`
- `Source/Project_Bang_Squad/Character/Base/BaseCharacter.cpp`
- `Source/Project_Bang_Squad/Character/PaladinCharacter.h`
- `Source/Project_Bang_Squad/Character/PaladinCharacter.cpp`
- `Source/Project_Bang_Squad/Character/MageCharacter.h`
- `Source/Project_Bang_Squad/Character/MageCharacter.cpp`

---

### 2. Mage 인터페이스 및 상호작용 확장
Mage 전용 상호작용을 인터페이스로 분리해, 기능 확장성과 유지보수성을 확보했습니다.

- Mage 관련 상호작용 규약 정의
- 캐릭터와 상호작용 액터 간 결합도 감소
- 확장 시 파급 범위를 줄인 구조 설계

관련 코드:
- `Source/Project_Bang_Squad/Character/Player/Mage/MagicInteractableInterface.h`

---

### 3. 공격, 스킬, Projectile 구현
Paladin과 Mage의 전투 루프를 구성하는 핵심 전투 요소를 구현했습니다.

- 기본 공격 및 스킬 발동 흐름 구현
- Projectile 생성, 이동, 충돌 처리
- 전투 효과와 판정 연결

관련 코드:
- `Source/Project_Bang_Squad/Projectile/MageProjectile.h`
- `Source/Project_Bang_Squad/Projectile/MageProjectile.cpp`
- `Source/Project_Bang_Squad/Projectile/MageIceArrow.h`
- `Source/Project_Bang_Squad/Projectile/MageIceArrow.cpp`
- `Source/Project_Bang_Squad/Character/Player/Paladin/PaladinSkill2Hammer.cpp`
- `Source/Project_Bang_Squad/Character/Player/Mage/Stage2MagicBall.cpp`
- `Source/Project_Bang_Squad/Character/Player/Mage/MagicBoat.cpp`
- `Source/Project_Bang_Squad/Character/Player/Mage/IcePad.cpp`

---

### 4. 전투 UI 피드백 연동
전투 가시성 향상을 위해 데미지 피드백과 스킬 쿨타임 UI를 연동했습니다.

- `DamageText` 출력 로직 구현
- 스킬 쿨타임 상태와 위젯 연동
- 전투 이벤트와 UI 반영 타이밍 정리

관련 코드:
- `Source/Project_Bang_Squad/Character/Damage/DamageTextActor.cpp`
- `Source/Project_Bang_Squad/UI/Stage/SkillSlotWidget.h`
- `Source/Project_Bang_Squad/UI/Stage/SkillSlotWidget.cpp`

---

### 5. 몬스터 스폰 및 맵 패턴 일부 구현
전투 흐름과 맵 상호작용을 위해 스폰 및 환경 오브젝트를 구현했습니다.

- 몬스터 스폰 제어
- 환경 패턴 오브젝트 동작 구현
- 전투 시스템과 맵 오브젝트 연동

관련 코드:
- `Source/Project_Bang_Squad/Character/Enemy/EnemySpawner.h`
- `Source/Project_Bang_Squad/Character/Enemy/EnemySpawner.cpp`
- `Source/Project_Bang_Squad/Game/MapPattern/WindZone.h`
- `Source/Project_Bang_Squad/Game/MapPattern/WindZone.cpp`

---

### 6. 멀티플레이 네트워크 처리
전투 결과가 클라이언트 간 일관되게 보이도록 네트워크 로직을 반영했습니다.

- 서버 권한 기반 전투 처리
- 공격, 스킬, 상태 변화 동기화
- 전투 피드백의 네트워크 환경 안정성 개선

핵심 방향:
- 판정은 서버 기준으로 처리
- 클라이언트는 동기화된 결과를 표현
- 상태 전파와 표현 로직을 분리해 안정성 확보

---

## 트러블슈팅 경험

### 전투 판정 불일치 문제
- 문제: 클라이언트마다 전투 결과 체감이 달라지는 이슈
- 원인: 판정 시점과 동기화 타이밍 차이
- 해결: 서버 권한 기준으로 판정을 통일하고, 상태 전파 흐름 정리
- 결과: 멀티플레이에서 전투 일관성 개선

### 쿨타임 UI 갱신 타이밍 문제
- 문제: 스킬 사용 직후 위젯 갱신 지연
- 해결: 전투 이벤트 발생 지점과 위젯 업데이트 지점을 명확히 연결
- 결과: 사용자 피드백 즉시성 개선

---

## 기술적으로 강조하고 싶은 점
- 공통 구조는 `BaseCharacter`에 모으고 직업 고유 로직은 파생 클래스로 분리
- Mage 기능은 인터페이스 기반으로 분리해 확장성 확보
- Projectile, 스킬, UI, 네트워크를 하나의 전투 시스템으로 통합 설계
- 멀티플레이 기준에서 판정 신뢰성과 동기화 안정성을 우선 고려
