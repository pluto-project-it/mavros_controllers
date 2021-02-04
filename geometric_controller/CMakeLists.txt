cmake_minimum_required(VERSION 2.8.12)
project(geometric_controller)

add_definitions(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  dynamic_reconfigure
  tf
  mavros
  mavros_extras
  mavros_msgs
  mavlink
)

generate_dynamic_reconfigure_options(
  cfg/GeometricController.cfg
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES geometric_controller
  CATKIN_DEPENDS roscpp rospy std_msgs mavros_msgs geometry_msgs sensor_msgs tf
)

#############
# LIBRARIES #
#############
include_directories(
  include
  ${Boost_INCLUDE_DIR}
  ${catkin_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME}
  src/geometric_controller.cpp 
)
add_dependencies(geometric_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES})

############
# BINARIES #
############
add_executable(geometric_controller_node
  src/geometric_controller_node.cpp
)
add_dependencies(geometric_controller_node geometric_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(geometric_controller_node ${PROJECT_NAME} ${catkin_LIBRARIES})
##########
# EXPORT #
##########
# cs_install()
# cs_export()

#############
## Install ##
#############
#catkin_install_python(PROGRAMS 
#
#  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#)

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# catkin_install_python(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

#catkin_install_python(PROGRAMS src/navigation.py
#  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#)
 
## Mark executables for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_executables.html
 install(TARGETS ${PROJECT_NAME}_node
  
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )

## Mark libraries for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_libraries.html
 install(TARGETS ${PROJECT_NAME}
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
 )

## Mark cpp header files for installation
 install(DIRECTORY include/${PROJECT_NAME}/
   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
   FILES_MATCHING PATTERN "*.h"
   PATTERN ".svn" EXCLUDE
 )

## Mark other files for installation (e.g. launch and bag files, etc.)
 install(FILES
   launch/config_file.rviz
   launch/mav_trajectory_track_circle.launch
   launch/sitl_multi_vehicle_track_circle.launch
   launch/sitl_trajectory_track_circle.launch
   launch/sitl_trajectory_track_lamniscate.launch
   launch/trajectory_controller.launch  
   cfg/GeometricController.cfg
   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
 )


#############
## Testing ##
#############

if(CATKIN_ENABLE_TESTING)
    # Add gtest based cpp test target and link libraries
    catkin_add_gtest(${PROJECT_NAME}-test test/main.cpp
                                          test/test_example.cpp
                                          test/geometric_controller-test.cpp)

    if(TARGET ${PROJECT_NAME}-test)
        target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME}
                                                 ${catkin_LIBRARIES}
                                                 ${YAML_CPP_LIBRARIES})
    endif()

endif()
