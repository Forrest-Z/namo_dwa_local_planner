cmake_minimum_required(VERSION 2.8.3)
project(namo_dwa_local_planner)

find_package(catkin REQUIRED
        COMPONENTS
            base_local_planner
            cmake_modules
            costmap_2d
            dynamic_reconfigure
            nav_core
            nav_msgs
            pluginlib
            pcl_conversions
            roscpp
            tf
            actionlib_msgs
        )

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
remove_definitions(-DDISABLE_LIBUSB-1.0)
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    )
add_definitions(${EIGEN3_DEFINITIONS})

# dynamic reconfigure
generate_dynamic_reconfigure_options(
    cfg/NAMODWAPlanner.cfg
)

add_action_files(
    DIRECTORY action
    FILES
    FollowPath.action
)

generate_messages(
  DEPENDENCIES actionlib_msgs std_msgs nav_msgs geometry_msgs # Or other packages containing msgs
)

catkin_package(
    INCLUDE_DIRS include
    LIBRARIES namo_dwa_local_planner
    CATKIN_DEPENDS
        dynamic_reconfigure
        nav_msgs
        pluginlib
        roscpp
        actionlib_msgs
)

add_library(namo_dwa_local_planner src/namo_dwa_planner.cpp src/namo_dwa_planner_ros.cpp)
add_dependencies(namo_dwa_local_planner ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(namo_dwa_local_planner ${catkin_LIBRARIES})

install(TARGETS namo_dwa_local_planner
       ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       )

install(FILES blp_plugin.xml
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)
