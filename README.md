<div align="center">

# Piper Robot Arm with MoveIt2 & Isaac Sim

**Piper 로봇팔의 URDF를 기반으로 MoveIt2와 Isaac Sim을 연동한 프로젝트입니다.**

[![ROS2](https://img.shields.io/badge/ROS2-Humble-blue?logo=ros&logoColor=white)](https://docs.ros.org/en/humble/)
[![MoveIt2](https://img.shields.io/badge/MoveIt-2-green)](https://moveit.ai/)
[![Isaac Sim](https://img.shields.io/badge/NVIDIA-Isaac%20Sim-76B900?logo=nvidia&logoColor=white)](https://developer.nvidia.com/isaac-sim)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](#)

[![Using MoveIt with a piper robot arm in Isaac Sim](https://img.youtube.com/vi/dGvGAYixpRw/maxresdefault.jpg)](https://youtu.be/dGvGAYixpRw)

</div>

<br>

## 소개

본 Repository는 **Piper Robot Arm**의 URDF 파일을 활용하여 **MoveIt2**와 **NVIDIA Isaac Sim**을 연동하는 예제 및 설정 파일을 제공합니다. Isaac Sim에서 시뮬레이션된 환경을 MoveIt2를 통해 모션 플래닝하고 제어할 수 있습니다.

<br>

## 목차

- [초기 설정](#-초기-설정)
- [Isaac Sim 실행](#-isaac-sim-실행)
- [트러블슈팅](#-트러블슈팅)

<br>

## 초기 설정

### 1. Repository 클론

```bash
git clone https://github.com/stupid1potato/Using-MoveIt-with-a-piper-robot-arm-in-Isaac-Sim.git
```

### 2. 빌드

```bash
colcon build
```

빌드가 정상적으로 완료되면 다음 단계로 진행합니다.

<br>

## Isaac Sim 실행

1. Isaac Sim에서 아래 USD 파일을 불러옵니다.

   ```
   src/piper_moveit_config/piper_simpleRoom.usd
   ```

2. 다음 명령어로 Piper MoveIt 데모를 실행합니다.

   ```bash
   ros2 launch piper_moveit_config demo.launch.py
   ```

<br>

## 트러블슈팅

### Action Graph 오류 발생 시

<img width="719" height="217" alt="Action Graph Error" src="https://github.com/user-attachments/assets/92f78f8b-11cb-4bdb-8c7c-4ccbd5150e4e" />

위 이미지에 표시된 `topicName`을 자신의 환경에 맞는 토픽 이름으로 수정하면 해결됩니다.

<br>
<br>
<div align="center">

made by stupid1potato

</div>
