cmake_minimum_required(VERSION 2.8.3)
project(capra_landolt_ros)

add_compile_options(-std=c++11)

find_package(Boost REQUIRED COMPONENTS thread)
find_package(OpenCV REQUIRED)
find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  image_transport
  roscpp
  sensor_msgs
  std_msgs
  image_publisher
  roslint
  capra_landolt_msgs
)

catkin_package(
  CATKIN_DEPENDS 
    nodelet 
    cv_bridge 
    roscpp 
    sensor_msgs
    capra_landolt_msgs
    std_msgs 
    image_transport
  DEPENDS
    Boost
)

include_directories(
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

add_library(capra_landolt_nodelet src/LandoltNodelet.cpp)
target_link_libraries(capra_landolt_nodelet ${catkin_LIBRARIES})
add_dependencies(capra_landolt_nodelet capra_landolt_msgs_gencpp)

if(catkin_EXPORTED_LIBRARIES)
  add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_LIBRARIES})
endif()

if(CATKIN_ENABLE_TESTING)
  find_package(rostest REQUIRED)
  catkin_add_executable_with_gtest(landolt_test test/landolt_test.cpp)
  add_rostest(test/capra_detection.test ARGS datapath:=${PROJECT_SOURCE_DIR}/test/sample/)
  target_link_libraries(landolt_test ${catkin_LIBRARIES})
endif()

roslint_cpp()

install(TARGETS capra_landolt_nodelet
	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
	LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
	ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(FILES nodelet.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
