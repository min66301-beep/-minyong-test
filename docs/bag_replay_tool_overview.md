# Bag Replay Tool ROS2 - 개요

## 소개

ROS2 Bag 파일을 재생하기 위한 GUI 기반 도구입니다. ROS1 버전에서 ROS2로 변환되었습니다.

## 주요 기능

### 1. 파일 관리
- **파일 선택**: GUI를 통해 ROS2 bag 디렉토리 찾기 및 선택
- **최근 파일 메뉴**: 최대 10개의 최근 사용 파일에 빠르게 접근
  - File 메뉴에서 접근 가능
  - 세션 간에도 유지됨

### 2. 토픽 관리
- **토픽 선택**: Bag 파일의 모든 토픽을 확인하고 발행할 토픽 선택
- **토픽 정보**: 각 토픽의 타입 및 메시지 정보 표시

### 3. 재생 제어
- **재생 제어**: Play, Pause, Stop 버튼
- **루프 재생**: 배그 파일을 반복 재생하는 옵션
- **재생 속도**: 0.1배속 ~ 10배속 조정 가능
- **시간 발행**: Clock 메시지 발행 옵션

### 4. 정보 표시
- **Bag 정보**: 선택된 bag 파일의 상세 정보 표시
  - 메시지 개수
  - 시간 범위
  - 토픽 목록 등

## 기술 스택

- **언어**: Python 3
- **UI 프레임워크**: PyQt5
- **ROS2 패키지**: rclpy
- **데이터베이스**: SQLite3

## 파일 구조

```
bag_replay_tool_ros2/
├── __init__.py                    # 패키지 초기화
├── bag_replay_tool_node.py        # 메인 GUI 애플리케이션 (655줄)
├── ../ui/
│   └── bag_replay_tool.ui        # PyQt5 UI 정의 파일
└── ../docs/
    └── (문서 파일들)
```

## 클래스 구조

### BagReplayTool (메인 윈도우)
- **부모 클래스**: `QtWidgets.QMainWindow`
- **역할**: GUI 창의 메인 윈도우 관리

#### 주요 속성
- `playback_thread`: 재생 스레드
- `stop_playback_flag`: 재생 중지 플래그
- `pause_playback_flag`: 일시 중지 플래그
- `current_bag_file`: 현재 선택된 bag 파일
- `bag_topics`: 토픽 이름 → 토픽 타입 매핑
- `publishers`: 토픽 이름 → 발행자 매핑
- `is_paused`: 일시 중지 상태 추적
- `recent_files`: 최근 파일 목록
- `recent_files_config_path`: 최근 파일 설정 경로

#### 주요 메서드
- `__init__()`: 초기화
- `set_default_values()`: UI 기본값 설정
- `connect_signals()`: UI 신호 연결
- `setup_menu_actions()`: 메뉴 액션 설정
- `load_recent_files()`: 최근 파일 로드
- `browse_bag_file()`: 파일 브라우저 열기
- `load_bag_file()`: Bag 파일 로드
- `play_bag()`: Bag 재생
- `pause_playback()`: 일시 중지
- `stop_playback()`: 중지
- `update_ui_from_bag()`: Bag 정보로 UI 업데이트

## 기본값 설정

앱 시작 시 다음 기본값이 설정됩니다:
- **루프 재생**: 체크됨 (ON)
- **재생 속도**: 1.0배속
- **시간 발행**: 체크 해제됨 (OFF)

## 설정 파일

- **경로**: `~/.ros2/bag_replay_tool_recent.json`
- **용도**: 최근 사용한 파일 목록 저장
- **관리**: 자동으로 최대 10개 파일 유지

## 종속성

- `rclpy`: ROS2 Python 클라이언트 라이브러리
- `PyQt5`: GUI 프레임워크
- `rosidl_runtime_py`: ROS2 메시지 타입 유틸리티
- `ament_index_python`: 패키지 위치 찾기
- `sqlite3`: Bag 파일 데이터베이스 접근
