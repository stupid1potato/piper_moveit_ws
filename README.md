# piper_moveit_ws

**AgileX Piper 6-DOF 로봇 팔**을 위한 ROS 2 / MoveIt 2 모션 플래닝 워크스페이스입니다.
MoveIt Setup Assistant로 생성한 `piper_moveit_config` 패키지와, 로봇 모델(`piper_description`)을 포함하고 있으며,
`topic_based_ros2_control`을 통해 **NVIDIA Isaac Sim**과 연동하도록 구성되어 있습니다.

---

## 개요

이 워크스페이스는 Piper 6축 로봇 팔(+ 2지 그리퍼)을 MoveIt 2로 제어·플래닝하기 위한 설정을 모아 둔 colcon 워크스페이스입니다. 주요 특징은 다음과 같습니다.

- **로봇**: Piper 6-DOF 매니퓰레이터 + 2지 병렬 그리퍼 (joint1~joint6 팔, joint7·joint8 그리퍼)
- **플래닝 프레임워크**: MoveIt 2 (OMPL / Pilz / CHOMP 등 기본 플래너 설정 포함)
- **역기구학(IK)**: KDL Kinematics Plugin
- **제어 인터페이스**: `ros2_control` + `topic_based_ros2_control/TopicBasedSystem`
  - 명령 토픽: `/isaac_joint_commands`
  - 상태 토픽: `/isaac_joint_states`
- **시뮬레이션 연동**: NVIDIA Isaac Sim (USD 씬 파일 포함)

> 하드웨어 인터페이스가 `TopicBasedSystem`으로 설정되어 있어, 실제 하드웨어가 아니라 지정된 토픽으로 관절 명령/상태를 주고받는 외부 시뮬레이터(Isaac Sim)와 통신하도록 되어 있습니다. 실제 로봇이나 mock 하드웨어로 바꾸려면 `config/piper.ros2_control.xacro`의 `<hardware>` 플러그인을 수정하면 됩니다.

---

## 디렉터리 구조

```
piper_moveit_ws/
├── .gitignore                     # build / install / log 제외
└── src/
    ├── piper_moveit_config/       # MoveIt 2 설정 패키지 (핵심)
    │   ├── config/                # SRDF, kinematics, controllers, joint_limits, URDF/xacro, USD 등
    │   ├── launch/                # demo / move_group / rviz / rsp 등 런치 파일
    │   ├── configuration/         # Isaac Sim USD (piper_simpleRoom 씬)
    │   └── package.xml
    └── moveit_resources/          # MoveIt 예제 리소스 모음 + Piper 로봇 모델
        ├── piper_description/     # Piper URDF / xacro / meshes / MuJoCo / USD
        ├── panda_description/     # (참고용 예제)
        ├── fanuc_description/     # (참고용 예제)
        └── pr2_description/       # (참고용 예제)
```

### 주요 패키지

| 패키지 | 설명 |
| --- | --- |
| `piper_moveit_config` | MoveIt Setup Assistant로 생성된 Piper용 MoveIt 2 설정·런치 패키지 (v0.3.0) |
| `piper_description` | Piper 로봇의 URDF/xacro, 메쉬(STL/DAE), MuJoCo·USD 모델을 담은 description 패키지 |
| `moveit_resources` 외 | Fanuc / Panda / PR2 등 MoveIt 표준 예제 리소스 (참고 및 의존성 용도) |

---

## 플래닝 그룹 & 명명된 상태

`config/piper.srdf`에 정의된 플래닝 그룹과 사전 정의된 자세입니다.

| 그룹 | 포함 조인트 | 명명된 상태 |
| --- | --- | --- |
| `arm` | joint1 ~ joint6 (+ joint6_to_gripper_base) | `piper_home`, `piper_ready` |
| `gripper` | joint7, joint8 | `gripper_open`, `gripper_close` |

- **엔드 이펙터**: `endeffector` (parent link: `gripper_base`)
- **가상 조인트**: `virtual_joint` (world → base_link, fixed)

---

## 요구 사항

- **OS**: Ubuntu 22.04 권장
- **ROS 2**: Humble (MoveIt 2 지원 배포판)
- **MoveIt 2** 및 관련 패키지
- **NVIDIA Isaac Sim** (시뮬레이션 연동 시)
- 주요 ROS 의존 패키지 (`package.xml` 기준):
  `moveit_ros_move_group`, `moveit_kinematics`, `moveit_planners`,
  `moveit_simple_controller_manager`, `moveit_configs_utils`,
  `controller_manager`, `joint_state_publisher(_gui)`, `robot_state_publisher`,
  `rviz2`, `xacro`, `warehouse_ros_mongo`,
  그리고 `topic_based_ros2_control` (Isaac 연동용)

의존성은 `rosdep`으로 한 번에 설치하는 것을 권장합니다.

```bash
cd ~/piper_moveit_ws
rosdep install --from-paths src --ignore-src -r -y
```

> `topic_based_ros2_control`는 배포판에 따라 별도 설치가 필요할 수 있습니다.
> `sudo apt install ros-humble-topic-based-ros2-control`

---

## 빌드

```bash
# 워크스페이스 클론
git clone https://github.com/stupid1potato/piper_moveit_ws.git
cd piper_moveit_ws

# 의존성 설치
rosdep install --from-paths src --ignore-src -r -y

# 빌드
colcon build

# 환경 소싱
source install/setup.bash
```

---

## 실행

### 1. MoveIt 데모 (RViz)

RViz 상에서 인터랙티브 마커로 목표 자세를 지정하고 플래닝/실행을 확인할 수 있는 기본 데모입니다.

```bash
ros2 launch piper_moveit_config demo.launch.py
```

### 2. move_group 단독 실행

```bash
ros2 launch piper_moveit_config move_group.launch.py
```

### 3. RViz만 실행

```bash
ros2 launch piper_moveit_config moveit_rviz.launch.py
```

### Isaac Sim 연동 워크플로

1. Isaac Sim에서 Piper USD 씬(`config/piper/piper.usd` 또는 `configuration/piper_simpleRoom_*.usd`)을 로드합니다.
2. Isaac Sim이 아래 토픽으로 ROS 2와 통신하도록 브리지를 설정합니다.
   - 구독: `/isaac_joint_commands` (MoveIt → Isaac)
   - 발행: `/isaac_joint_states` (Isaac → MoveIt)
3. `move_group`을 실행하면 MoveIt에서 계획한 궤적이 Isaac Sim의 Piper로 전달됩니다.

> 실제 하드웨어나 `mock_components/GenericSystem`으로 전환하려면
> `config/piper.ros2_control.xacro`의 `<hardware>` 플러그인 항목을 수정하세요.

---

## 주요 설정 파일

| 파일 | 역할 |
| --- | --- |
| `config/piper.srdf` | 플래닝 그룹, 명명된 상태, 충돌 무시 규칙 |
| `config/kinematics.yaml` | IK 솔버 설정 (KDL) |
| `config/joint_limits.yaml` | 조인트 속도/가속 제한 (기본 속도·가속 스케일 0.1) |
| `config/moveit_controllers.yaml` | MoveIt 컨트롤러 매핑 (arm / gripper `FollowJointTrajectory`) |
| `config/ros2_controllers.yaml` | `ros2_control` 컨트롤러 정의 (100Hz 업데이트) |
| `config/piper.ros2_control.xacro` | 하드웨어 인터페이스 (`topic_based_ros2_control`) |
| `config/initial_positions.yaml` | 초기 관절 위치 |

---

## 참고

- MoveIt 공식 문서: <https://moveit.ai/>
- 이 설정은 MoveIt Setup Assistant로 생성되었습니다.
- 유지관리자: **anchanbin** (dkscksqls07@naver.com)

## 라이선스

`piper_moveit_config` 및 `piper_description` 패키지는 **BSD 라이선스**를 따릅니다.
`moveit_resources` 하위 예제 패키지들은 각 패키지의 원 라이선스를 따릅니다.
