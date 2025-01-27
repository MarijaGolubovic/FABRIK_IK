cmake_minimum_required(VERSION 3.8)
project(fabrik_ik)

# Opcije za kompajler
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# Pronađi potrebne zavisnosti
find_package(ament_cmake REQUIRED)
find_package(Eigen3 REQUIRED) # Dodaj Eigen ako ga koristiš
find_package(rclcpp REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(moveit_core REQUIRED)
find_package(rsl REQUIRED)
find_package(pluginlib REQUIRED)


# Dodaj direktorijum sa zaglavljima
include_directories(include)

generate_parameter_library(
  fabrik_ik_parameters
  src/fabrik_ik_parameters.yaml
)

add_library(fabrik_ik_plugin SHARED
  src/fk_moveit.cpp
  src/forward_kinematics.cpp
  src/fabrik_ik_plugin.cpp
  src/goal.cpp
  src/ik_memetic.cpp
  src/ik_gradient.cpp
  src/robot.cpp
)

pluginlib_export_plugin_description_file(
  moveit_core
  fabrik_ik_kinematics_description.xml
)

# Poveži potrebne biblioteke
target_link_libraries(fabrik_ik_plugin
  Eigen3::Eigen
  rclcpp::rclcpp
  tf2_geometry_msgs::tf2_geometry_msgs
  moveit_core::moveit_robot_model
  moveit_core::moveit_kinematics_base
  moveit_core::moveit_robot_state
  rsl::rsl
  pluginlib::pluginlib
  fabrik_ik_parameters
)

install(
  TARGETS fabrik_ik_plugin fabrik_ik_parameters
  EXPORT export_fabrik_ik
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(
  DIRECTORY include/
  DESTINATION include/${PROJECT_NAME}
)

# Export zavisnosti
ament_export_dependencies(
  rclcpp
  tf2_geometry_msgs
  Eigen3
  moveit_core
  rsl
  pluginlib
)

ament_package()
