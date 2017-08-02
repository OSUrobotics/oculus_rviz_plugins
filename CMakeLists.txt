cmake_minimum_required(VERSION 2.8.3)
project(oculus_rviz_plugins)

# Qt
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)
set(QT_LIBRARIES Qt5::Widgets)
set(QTVERSION ${Qt5Widgets_VERSION})
add_definitions(-DQT_NO_KEYWORDS -fPIC)

# Catkin
find_package(catkin REQUIRED COMPONENTS 
  rviz
  roscpp
  tf
)

# Oculus SDK

FIND_PACKAGE( ros_ovr_sdk REQUIRED)

# Run OVR off of a shared library

if(NOT ros_ovr_sdk_FOUND)
    message("ros_ovr_sdk Library cannot be found.")
endif(NOT ros_ovr_sdk_FOUND)

# Boost
find_package(Boost REQUIRED)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES oculus_rviz_plugins
  CATKIN_DEPENDS rviz
  DEPENDS ros_ovr_sdk
)

find_package(PkgConfig)
pkg_check_modules(OGRE OGRE)

include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${ros_ovr_sdk_INCLUDE_DIRS}
  ${OGRE_INCLUDE_DIRS}
  ${BOOST_INCLUDE_DIRS}
)

# run Qt moc generator
qt5_wrap_cpp(MOC_FILES
  include/oculus_rviz_plugins/oculus_display.h
  include/oculus_rviz_plugins/fixed_view_controller.h
)

add_library(oculus_rviz_plugins
  src/ogre_oculus.cpp
  src/oculus_display.cpp
  src/fixed_view_controller.cpp
  ${MOC_FILES}
)

target_link_libraries(oculus_rviz_plugins
  ${catkin_LIBRARIES}
  ${QT_LIBRARIES}
  ${ros_ovr_sdk_LIBRARIES}
  ${BOOST_LIBRARIES}
)


#############
## Install ##
#############

install(TARGETS oculus_rviz_plugins
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY ogre_media
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(FILES
  plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY
  icons
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
