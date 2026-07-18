cmake_minimum_required(VERSION 3.10.2)

project(piper_description)

find_package(ament_cmake REQUIRED)

ament_package()

install(DIRECTORY launch meshes mujoco_model rviz urdf DESTINATION share/${PROJECT_NAME})
