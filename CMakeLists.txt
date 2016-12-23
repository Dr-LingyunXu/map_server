cmake_minimum_required(VERSION 2.8)
project(map_server)

set(PACKAGE_DEPENDENCIES
    roscpp visualization_msgs sensor_msgs pcl_ros pcl_conversions
    nav_msgs std_msgs std_srvs octomap_ros octomap_msgs
    dynamic_reconfigure nodelet
)


find_package(catkin REQUIRED COMPONENTS ${PACKAGE_DEPENDENCIES})

find_package(PCL 1.7 REQUIRED QUIET COMPONENTS common sample_consensus io segmentation filters)

find_package(octomap REQUIRED)
add_definitions(-DOCTOMAP_NODEBUGOUT)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${OCTOMAP_INCLUDE_DIRS}
)


generate_dynamic_reconfigure_options(cfg/map_server.cfg)

catkin_package()

set(LINK_LIBS
  ${OCTOMAP_LIBRARIES}
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
)

add_executable(server_node src/server_node.cpp 
	include/map_server/map_server.h 
	include/map_server/impl/map_server.hpp)
add_dependencies(server_node ${PROJECT_NAME}_gencfg)
target_link_libraries(server_node ${LINK_LIBS})



