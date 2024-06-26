cmake_minimum_required(VERSION 3.5)
project(packml_sm)

set(CMAKE_XX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++")

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# prevents weird Qt error
#set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rqt_gui_cpp REQUIRED)
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)


#include all directories
include_directories(
  include
)

qt5_wrap_cpp(packml_sm_MOCS include/packml_sm/state_machine.hpp
  include/packml_sm/state.hpp
  include/packml_sm/transitions.hpp
  include/packml_sm/events.hpp
  include/packml_sm/common.hpp)

#add libraries
add_library(${PROJECT_NAME} SHARED
  src/state_machine.cpp
  src/state.cpp
  src/transitions.cpp
  ${packml_sm_MOCS})
ament_target_dependencies(${PROJECT_NAME} rclcpp rqt_gui_cpp Qt5)

#install
install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY include/${PROJECT_NAME} DESTINATION include)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()

  find_package(ament_cmake_gtest REQUIRED)
  ament_add_gtest(${PROJECT_NAME}_utest test/utest.cpp)
  ament_target_dependencies(${PROJECT_NAME}_utest rclcpp rqt_gui_cpp Qt5)
  target_link_libraries(${PROJECT_NAME}_utest ${PROJECT_NAME})

endif()


#Substituting the catkin_package () components:
#INCLUDE_DIRS
ament_export_include_directories(include)
#LIBRARIES
ament_export_libraries(${PROJECT_NAME})
#CATKIN_DEPENDS
ament_export_dependencies(rqt_gui_cpp)

ament_package()
