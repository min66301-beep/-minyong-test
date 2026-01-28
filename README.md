# Bag Replay Tool ROS2 - 문서 색인

이 디렉토리는 **bag_replay_tool_ros2**에 대한 상세 문서를 포함합니다.

## 📚 문서 목록

### 1. [개요 (Overview)](bag_replay_tool_overview.md)
bag_replay_tool_ros2의 전체 개요, 기능, 기술 스택, 파일 구조, 클래스 구조를 설명합니다.

**포함 내용**:
- 소개 및 주요 기능
- 기술 스택
- 파일 구조
- 클래스 구조 및 주요 속성/메서드
- 기본값 설정
- 종속성

**대상**: 프로젝트를 처음 접하는 개발자

---

### 2. [사용 가이드 (User Guide)](bag_replay_tool_guide.md)
실제 사용 방법을 단계별로 설명하는 가이드입니다.

**포함 내용**:
- 설치 방법
- 실행 방법
- GUI 사용법 (단계별)
- 메뉴 구성
- 최근 파일 관리
- 상태 표시
- 로깅 출력
- 일반적인 사용 시나리오 (4가지)
- 문제 해결 (Q&A)
- 팁 및 트릭

**대상**: 최종 사용자 및 운영 담당자

**quick start**:
```bash
# 설치
colcon build --packages-select bag_replay_tool_ros2
source install/setup.bash

# 실행
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py
```

---

### 3. [API 참고 (API Reference)](bag_replay_tool_api.md)
코드 레벨의 상세 API 문서입니다.

**포함 내용**:
- 클래스 구조
- 속성(Attributes) 상세 설명
  - 파일 관련
  - 토픽 관련
  - 재생 제어 관련
- 메서드(Methods) 상세 설명
  - 초기화 메서드
  - 신호 연결 메서드
  - 최근 파일 관리 메서드
  - 파일 선택 메서드
  - Bag 정보 메서드
  - 재생 제어 메서드
  - 백그라운드 스레드 메서드
  - 윈도우 이벤트 메서드
- 전역 함수
- UI 요소 참조
- 로깅 메시지 레퍼런스
- 예제 사용
- 스레드 안전성

**대상**: 개발자 및 기여자

---

## 🎯 빠른 참고

### 주요 기능별 문서

| 기능 | 문서 위치 |
|------|---------|
| **설치 및 실행** | [사용 가이드 - 설치](bag_replay_tool_guide.md#설치) |
| **GUI 사용** | [사용 가이드 - GUI 사용법](bag_replay_tool_guide.md#gui-사용법) |
| **클래스 분석** | [API 참고 - 클래스 구조](bag_replay_tool_api.md#클래스-구조) |
| **메서드 호출** | [API 참고 - 메서드](bag_replay_tool_api.md#메서드methods) |
| **문제 해결** | [사용 가이드 - 문제 해결](bag_replay_tool_guide.md#문제-해결) |
| **개발 팁** | [사용 가이드 - 팁 및 트릭](bag_replay_tool_guide.md#팁-및-트릭) |

---

## 📖 문서 사용 방법

### 처음 사용하는 경우
1. [개요](bag_replay_tool_overview.md) 읽기
2. [사용 가이드](bag_replay_tool_guide.md) - "설치" 섹션 참고
3. [사용 가이드](bag_replay_tool_guide.md) - "사용 방법" 섹션 참고

### 개발/기여하는 경우
1. [개요](bag_replay_tool_overview.md) - "파일 구조" 및 "클래스 구조" 섹션
2. [API 참고](bag_replay_tool_api.md) - 전체 문서
3. 소스 코드 검토

### 문제가 발생한 경우
1. [사용 가이드](bag_replay_tool_guide.md) - "문제 해결" 섹션
2. [API 참고](bag_replay_tool_api.md) - "로깅 메시지 레퍼런스"

---

## 🔍 주요 개념

### Bag 파일 (ROS2 Bag)
- ROS2의 메시지 데이터를 저장하는 형식
- 디렉토리 기반 (ROS1과 다름)
- 최소 필수 파일: `metadata.yaml`, `.db3` 데이터베이스
- 용도: 센서 데이터 기록/재생, 테스트, 분석

### 토픽 (Topic)
- ROS2에서 노드 간 통신하는 채널
- Pub/Sub 패턴 사용
- 각 토픽은 특정 메시지 타입 사용
- 예: `/camera/image_raw` → `sensor_msgs/Image`

### 메시지 타입 (Message Type)
- 토픽을 통해 전송되는 데이터 구조
- ROS2 표준 메시지 (sensor_msgs, geometry_msgs 등)
- 사용자 정의 메시지도 가능

### 재생 (Playback)
- 저장된 bag 파일의 메시지를 원본 속도 또는 조정된 속도로 발행
- 센서 시뮬레이션, 테스트, 분석에 사용

---

## 🛠️ 아키텍처 개요

```
bag_replay_tool_ros2/
├── bag_replay_tool_node.py  ← 메인 GUI 애플리케이션
│   ├── BagReplayTool (메인 클래스)
│   │   ├── UI 초기화 및 관리
│   │   ├── 최근 파일 관리
│   │   ├── 토픽 로드 및 선택
│   │   ├── 재생 제어 (Play, Pause, Stop)
│   │   └── 백그라운드 스레드 관리
│   └── main() (진입점)
│
├── __init__.py
├── ../ui/bag_replay_tool.ui  ← PyQt5 UI 정의
└── ../docs/
    ├── bag_replay_tool_overview.md  ← 개요
    ├── bag_replay_tool_guide.md     ← 사용 가이드
    └── bag_replay_tool_api.md       ← API 참고
```

---

## 📋 기술 스택

| 항목 | 기술 |
|------|------|
| **언어** | Python 3 |
| **UI 프레임워크** | PyQt5 |
| **ROS2 통합** | rclpy |
| **데이터베이스** | SQLite3 |
| **설정 저장** | JSON |
| **명령 실행** | subprocess |
| **멀티스레드** | threading |

---

## 🎬 워크플로우

### 사용자 관점
```
1. GUI 실행
2. Bag 파일 선택 (Browse/Recent Files)
3. 토픽 선택 (체크박스)
4. 재생 옵션 설정 (루프, 속도, 시간)
5. Play/Pause/Stop 제어
6. 결과 확인
```

### 시스템 관점
```
1. UI 로드 (PyQt5)
2. 최근 파일 로드 (JSON)
3. Bag 메타데이터 파싱 (YAML)
4. 토픽 정보 추출
5. 사용자 입력 처리
6. ros2 bag play 서브프로세스 실행
7. 신호 처리 (일시중지/재개)
8. 재생 완료 또는 중지
9. 정리 작업
```

---

## 📝 주요 파일 설명

### bag_replay_tool_node.py (655줄)

**주요 섹션**:
1. **임포트** (1-26줄): 필요한 라이브러리
2. **클래스 정의** (29줄+):
   - `__init__`: 초기화 (76줄)
   - 신호 연결 (connect_signals)
   - 최근 파일 관리
   - 파일 선택 및 로드
   - Bag 정보 표시
   - 재생 제어
   - 스레드 관리

---

## 🚀 성능 및 최적화

### 메모리 관리
- 대용량 bag 파일: 특정 토픽만 로드
- 스트리밍 처리로 메모리 절약

### 응답성
- 재생을 별도 스레드에서 실행
- UI 블로킹 방지
- 주기적 ROS2 스핀 (100ms)

### 속도
- 재생 속도 조절 (0.1배 ~ 10배)
- 빠른 미리보기 가능

---

## 📞 지원 및 연락

### 로그 확인
```bash
# ROS2 logger 확인
ros2 topic echo /rosout

# GUI 콘솔로 확인
ros2 run rqt_console rqt_console
```

### 디버깅
- 로그 메시지 패턴 인식 (✅, ❌, ⚠️, 🎬 등)
- 각 메서드의 entry/exit 로그
- 예외 스택 트레이스 포함

---

## 📚 추가 자료

### ROS2 공식 문서
- [ROS2 메인](https://docs.ros.org/en/humble/)
- [ROS2 Bag](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)
- [rclpy](https://docs.ros.org/en/humble/p/rclpy/)

### PyQt5 문서
- [PyQt5 공식](https://www.riverbankcomputing.com/software/pyqt/)
- [PyQt5 한글 튜토리얼](https://wikidocs.net/book/2165)

---

## 📄 버전 정보

- **프로젝트**: bag_replay_tool_ros2
- **타입**: ROS2 GUI 도구
- **언어**: Python 3
- **라이선스**: (프로젝트에 따라)
- **마지막 업데이트**: 2026-01-28

---

## ✨ 빠른 링크

- 🚀 [빠른 시작](bag_replay_tool_guide.md#실행-방법)
- 📖 [전체 사용 설명](bag_replay_tool_guide.md)
- 🔧 [API 참고](bag_replay_tool_api.md)
- ❓ [FAQ](bag_replay_tool_guide.md#문제-해결)
