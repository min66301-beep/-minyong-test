# Bag Replay Tool - API 참고

## 클래스 구조

### BagReplayTool

ROS2 bag 재생 도구의 메인 GUI 클래스입니다.

```python
class BagReplayTool(QtWidgets.QMainWindow):
    """GUI tool for replaying ROS2 bag files with playback control"""
```

#### 상속
- 부모: `QtWidgets.QMainWindow`
- PyQt5의 메인 윈도우 기능 활용

---

## 속성(Attributes)

### 파일 관련

#### `current_bag_file`
- **타입**: `str` 또는 `None`
- **설명**: 현재 선택된 bag 파일의 경로
- **초기값**: `None`

#### `recent_files`
- **타입**: `list[str]`
- **설명**: 최근 사용한 bag 파일 경로 목록
- **최대 길이**: 10개

#### `recent_files_config_path`
- **타입**: `str`
- **설명**: 최근 파일 설정 파일 경로
- **기본값**: `~/.ros2/bag_replay_tool_recent.json`

#### `max_recent_files`
- **타입**: `int`
- **설명**: 저장할 최근 파일 최대 개수
- **기본값**: `10`

### 토픽 관련

#### `bag_topics`
- **타입**: `dict[str, str]`
- **설명**: 토픽 이름과 메시지 타입 매핑
- **예시**: `{"/camera/image_raw": "sensor_msgs/Image", "/imu": "sensor_msgs/Imu"}`

#### `publishers`
- **타입**: `dict[str, Publisher]`
- **설명**: 토픽 이름과 ROS2 발행자 매핑
- **예시**: `{"/camera/image_raw": <rclpy.publisher.Publisher>, ...}`

### 재생 제어 관련

#### `playback_thread`
- **타입**: `threading.Thread` 또는 `None`
- **설명**: bag 재생을 위한 스레드
- **초기값**: `None`

#### `stop_playback_flag`
- **타입**: `threading.Event`
- **설명**: 재생 중지 신호 플래그
- **사용**: `set()` - 중지, `clear()` - 초기화, `is_set()` - 상태 확인

#### `pause_playback_flag`
- **타입**: `threading.Event`
- **설명**: 재생 일시중지 신호 플래그
- **사용**: `set()` - 일시중지, `clear()` - 재개, `is_set()` - 상태 확인

#### `is_paused`
- **타입**: `bool`
- **설명**: 현재 일시중지 상태 추적
- **기본값**: `False`

#### `node`
- **타입**: `rclpy.node.Node`
- **설명**: ROS2 노드 인스턴스
- **사용**: 로깅, 서비스 호출 등

---

## 메서드(Methods)

### 초기화 메서드

#### `__init__(node: Node) -> None`
**설명**: 클래스 생성자, UI 초기화

**매개변수**:
- `node`: ROS2 노드 인스턴스

**동작**:
1. PyQt5 메인 윈도우 초기화
2. UI 파일 로드
3. 신호-슬롯 연결
4. 최근 파일 로드
5. 기본값 설정

**예외**:
- `FileNotFoundError`: UI 파일이 없으면 발생

```python
node = rclpy.create_node('bag_replay_tool')
window = BagReplayTool(node)
```

#### `set_default_values() -> None`
**설명**: UI 컨트롤의 기본값 설정

**설정 항목**:
- 루프 재생: 체크됨 (True)
- 재생 속도: 1.0x
- 시간(Clock) 발행: 체크 해제 (False)

```python
window.set_default_values()
```

### 신호 연결 메서드

#### `connect_signals() -> None`
**설명**: UI 버튼과 이벤트 핸들러 연결

**연결되는 신호**:
- Browse 버튼 → `on_browse_clicked()`
- Play 버튼 → `on_play_clicked()`
- Pause 버튼 → `on_pause_clicked()`
- Stop 버튼 → `on_stop_clicked()`

```python
window.connect_signals()
```

#### `setup_menu_actions() -> None`
**설명**: 메뉴 바 액션 설정

**설정 액션**:
- File → Open: `on_browse_clicked()`
- File → Clear Recent: `clear_recent_files()`

```python
window.setup_menu_actions()
```

### 최근 파일 관리 메서드

#### `load_recent_files() -> None`
**설명**: 설정 파일에서 최근 파일 목록 로드

**동작**:
1. 설정 파일 존재 확인
2. JSON 파일 읽기
3. 존재하지 않는 파일 필터링
4. `self.recent_files` 업데이트

**예외 처리**: 오류 발생 시 로그 기록, 빈 목록으로 초기화

```python
window.load_recent_files()
# self.recent_files에 로드됨
```

#### `save_recent_files() -> None`
**설명**: 최근 파일 목록을 설정 파일에 저장

**동작**:
1. `~/.ros2/` 디렉토리 생성 (필요시)
2. JSON 파일로 저장
3. 2칸 들여쓰기로 포맷

```python
window.save_recent_files()
# ~/.ros2/bag_replay_tool_recent.json에 저장됨
```

**저장 형식**:
```json
[
  "/path/to/bag1",
  "/path/to/bag2",
  "/path/to/bag3"
]
```

#### `add_recent_file(file_path: str) -> None`
**설명**: 파일을 최근 파일 목록에 추가

**매개변수**:
- `file_path`: 추가할 bag 파일 경로

**동작**:
1. 이미 존재하면 제거 (중복 방지)
2. 목록 맨 앞에 추가
3. 최대 개수 초과시 끝에서 제거
4. 디스크에 저장
5. 메뉴 업데이트

```python
window.add_recent_file("/home/user/bags/my_recording")
```

#### `update_recent_files_menu() -> None`
**설명**: File 메뉴의 "Recent Files" 서브메뉴 업데이트

**동작**:
1. 기존 최근 파일 액션 제거
2. 현재 최근 파일 목록으로 새 메뉴 생성
3. 각 파일에 대해 액션 생성
4. 클릭 시 `open_recent_file()` 호출

**메뉴 항목 정보**:
- 텍스트: 파일명만 표시
- 툴팁: 전체 경로 표시

```python
window.update_recent_files_menu()
```

#### `open_recent_file(file_path: str) -> None`
**설명**: 최근 파일 목록에서 파일 열기

**매개변수**:
- `file_path`: 열 파일의 경로

**동작**:
1. 파일 존재 여부 확인
2. 존재 안 함: 최근 파일 목록에서 제거
3. 존재: `current_bag_file` 설정
4. UI 업데이트
5. Bag 정보 표시
6. 토픽 로드
7. 최근 파일 목록 업데이트

```python
window.open_recent_file("/home/user/bags/recording_1")
```

#### `clear_recent_files() -> None`
**설명**: 최근 파일 목록 모두 삭제

**동작**:
1. 확인 대화상자 표시
2. 사용자 확인 시:
   - 목록 비우기
   - 디스크 저장
   - 메뉴 업데이트

```python
window.clear_recent_files()
```

### 파일 선택 메서드

#### `on_browse_clicked() -> None`
**설명**: Browse 버튼 클릭 처리

**동작**:
1. 디렉토리 선택 대화상자 표시
2. `metadata.yaml` 파일 검증
3. 유효하지 않으면 경고 표시
4. 유효하면 Bag 정보 표시 및 토픽 로드
5. 최근 파일에 추가

```python
# 사용자가 버튼 클릭 시 자동 호출
# 또는 직접 호출:
window.on_browse_clicked()
```

### Bag 정보 메서드

#### `display_bag_info(bag_file: str) -> None`
**설명**: Bag 파일 정보를 UI에 표시

**매개변수**:
- `bag_file`: Bag 파일 경로

**동작**:
1. `ros2 bag info` 명령 실행
2. 출력을 UI의 텍스트 영역에 표시
3. 오류 발생 시 오류 메시지 표시

**명령 실행**:
```bash
ros2 bag info /path/to/bag
```

**타임아웃**: 10초

```python
window.display_bag_info("/home/user/bags/my_recording")
```

#### `load_bag_topics(bag_file: str) -> None`
**설명**: Bag 파일에서 토픽 목록 로드

**매개변수**:
- `bag_file`: Bag 파일 경로

**동작**:
1. `metadata.yaml` 읽기
2. 토픽 정보 추출
3. UI 리스트 위젯에 추가
4. 모든 토픽 기본 선택

**토픽 정보**:
- 토픽 이름
- 메시지 타입
- 메시지 개수

**디스플레이 형식**:
```
/camera/image_raw (sensor_msgs/Image) - 1245 msgs
/imu (sensor_msgs/Imu) - 2341 msgs
```

```python
window.load_bag_topics("/home/user/bags/my_recording")
# self.bag_topics와 list_topics 위젯 업데이트됨
```

#### `get_selected_topics() -> list[str]`
**설명**: 토픽 리스트에서 사용자가 선택한 토픽 반환

**반환**:
- `list[str]`: 선택된 토픽 이름 목록

**동작**:
1. 토픽 리스트 위젯 반복
2. 체크된 항목 확인
3. 토픽 이름 수집 및 반환

**예시**:
```python
selected = window.get_selected_topics()
# ["/camera/image_raw", "/imu"]
```

### 재생 제어 메서드

#### `on_play_clicked() -> None`
**설명**: Play 버튼 클릭 처리

**동작**:
1. Bag 파일 선택 여부 확인
2. 파일 존재 여부 확인
3. 이미 실행 중인 재생 중지
4. 새 재생 스레드 생성
5. UI 상태 업데이트

**상태 표시**: "Status: Playing" (녹색)

```python
# 사용자 버튼 클릭으로 자동 호출
window.on_play_clicked()
```

#### `on_pause_clicked() -> None`
**설명**: Pause 버튼 클릭 처리

**동작**:
1. 재생 스레드 확인
2. 일시중지 플래그 설정
3. UI 상태 업데이트

**상태 표시**: "Status: Paused" (주황색)

```python
window.on_pause_clicked()
```

#### `on_stop_clicked() -> None`
**설명**: Stop 버튼 클릭 처리

**동작**:
- `stop_playback()` 호출

```python
window.on_stop_clicked()
```

#### `stop_playback() -> None`
**설명**: Bag 재생 중지

**동작**:
1. 중지 플래그 설정
2. 일시중지 플래그 초기화
3. 스레드 종료 대기 (최대 5초)
4. 스레드 살아있으면 경고 로그
5. UI 상태 업데이트

**상태 표시**: "Status: Stopped" (빨강색)

```python
window.stop_playback()
```

### 백그라운드 스레드 메서드

#### `_playback_thread_worker() -> None`
**설명**: 별도 스레드에서 Bag 파일 재생 (백그라운드)

**동작**:
1. 재생 옵션 수집:
   - 루프 재생 여부
   - 재생 속도
   - 시간 발행 여부
   - 선택된 토픽
2. `ros2 bag play` 명령 생성
3. 서브프로세스 시작
4. 재생 중 일시중지/재개 처리:
   - SIGSTOP/SIGCONT 신호 사용
5. 중지 신호 확인
6. 종료 시 정리

**명령 예시**:
```bash
ros2 bag play /path/to/bag --rate 1.0 --topics /camera/image_raw /imu
```

**신호 처리**:
- `SIGSTOP`: 일시중지
- `SIGCONT`: 재개

```python
# 스레드에서 자동으로 호출됨
thread = threading.Thread(target=window._playback_thread_worker)
thread.daemon = True
thread.start()
```

### 윈도우 이벤트 메서드

#### `closeEvent(event: QCloseEvent) -> None`
**설명**: 윈도우 닫기 처리

**동작**:
1. 로그 메시지 출력
2. 실행 중인 재생 중지
3. 이벤트 수락

```python
# 사용자가 윈도우 닫을 때 자동 호출
```

---

## 전역 함수

#### `main(args: list[str] = None) -> None`
**설명**: 프로그램 메인 함수

**동작**:
1. ROS2 초기화
2. 노드 생성
3. PyQt5 애플리케이션 생성
4. BagReplayTool 윈도우 생성
5. ROS2 스핀 타이머 설정 (100ms 간격)
6. 이벤트 루프 실행
7. 정리 작업

```python
if __name__ == '__main__':
    main()
```

---

## UI 요소 참조

### 예상되는 UI 요소

| 위젯 이름 | 타입 | 설명 |
|----------|------|------|
| `btn_browse` | QPushButton | Browse 버튼 |
| `line_edit_file_path` | QLineEdit | 파일 경로 표시 |
| `btn_play` | QPushButton | Play 버튼 |
| `btn_pause` | QPushButton | Pause 버튼 |
| `btn_stop` | QPushButton | Stop 버튼 |
| `list_topics` | QListWidget | 토픽 목록 (체크박스) |
| `checkbox_loop` | QCheckBox | 루프 재생 |
| `spinbox_rate` | QDoubleSpinBox | 재생 속도 |
| `checkbox_clock` | QCheckBox | 시간(Clock) 발행 |
| `text_bag_info` | QPlainTextEdit | Bag 정보 표시 |
| `label_status` | QLabel | 상태 표시 |
| `menu_file` | QMenu | File 메뉴 |
| `action_open` | QAction | Open Bag File 액션 |
| `action_clear_recent` | QAction | Clear Recent Files 액션 |

---

## 로깅 메시지 레퍼런스

### 성공 메시지 (✅)
```
UI loaded from: ...
Bag Replay Tool initialized
Loop checkbox set to checked by default
Playback rate set to 1.0
...
```

### 오류 메시지 (❌)
```
UI file not found: ...
Error loading recent files: ...
Error browsing file: ...
Error starting playback: ...
```

### 정보 메시지 (📋)
```
No recent files config found, starting fresh
Loaded 3 recent topics
Playing 5 selected topics
Playing all topics
Starting playback: ros2 bag play ...
```

### 상태 메시지 (🎬⏸️⏹️)
```
▶️  Starting playback thread
⏸️ Playback paused
🛑 Stopping playback...
✅ Playback stopped
🧹 Playback thread cleaned up
```

---

## 예제 사용

### 기본 사용
```python
import rclpy
from bag_replay_tool_node import BagReplayTool

rclpy.init()
node = rclpy.create_node('example')
window = BagReplayTool(node)
# PyQt5 이벤트 루프에서 실행됨
```

### 프로그래매틱 제어
```python
# Bag 파일 직접 로드
window.current_bag_file = "/path/to/bag"
window.display_bag_info("/path/to/bag")
window.load_bag_topics("/path/to/bag")

# 최근 파일 추가
window.add_recent_file("/path/to/bag")

# 선택된 토픽 확인
topics = window.get_selected_topics()
print(topics)  # ['/topic1', '/topic2']

# 재생 제어
window.on_play_clicked()
window.on_pause_clicked()
window.stop_playback()
```

---

## 스레드 안전성

### 스레드 안전 요소
- `threading.Event()`: 스레드 간 신호 전달
- `playback_thread`: 별도 스레드에서 재생 실행

### 스레드 비안전 요소
- UI 업데이트는 메인 스레드에서만 수행
- 멀티스레드 환경에서 `bag_topics` 수정 시 주의

### 권장 사항
- UI 업데이트는 Qt signals 사용
- 장시간 작업은 별도 스레드에서 실행
