cmake_minimum_required(VERSION 3.14)
project(autoware_gyro_odometer)

find_package(autoware_cmake REQUIRED)
find_package(autoware_agnocast_wrapper REQUIRED)
find_package(agnocastlib REQUIRED)

autoware_package()

ament_auto_add_library(${PROJECT_NAME} SHARED
  src/gyro_odometer_core.cpp
)

target_link_libraries(${PROJECT_NAME} fmt)

autoware_agnocast_wrapper_setup(autoware_gyro_odometer)
ament_target_dependencies(autoware_gyro_odometer autoware_agnocast_wrapper)
target_include_directories(autoware_gyro_odometer PUBLIC ${autoware_agnocast_wrapper_INCLUDE_DIRS})

if(BUILD_TESTING)
  ament_add_ros_isolated_gtest(test_gyro_odometer
    test/test_main.cpp
    test/test_gyro_odometer_pubsub.cpp
    test/test_gyro_odometer_helper.cpp
  )
  ament_target_dependencies(test_gyro_odometer
    rclcpp
  )
  target_link_libraries(test_gyro_odometer
    ${PROJECT_NAME}
  )
  target_include_directories(test_gyro_odometer PRIVATE
    src
  )
endif()

rclcpp_components_register_nodes(${PROJECT_NAME}
  PLUGIN "autoware::gyro_odometer::GyroOdometerNode"
)

autoware_ament_auto_package(INSTALL_TO_SHARE
  launch
  config
)
