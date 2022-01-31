cmake_minimum_required(VERSION 3.5)
project(libuvc_vendor)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)

include(ExternalProject)
externalproject_add(libuvc_project
  GIT_REPOSITORY https://github.com/libuvc/libuvc
  GIT_TAG 37f00abd32b083a3d748f2a25c1c794578b51c16
  GIT_SHALLOW TRUE
  BUILD_IN_SOURCE TRUE
  INSTALL_DIR ${CMAKE_INSTALL_PREFIX}
  CONFIGURE_COMMAND ${CMAKE_COMMAND} -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> -DCMAKE_POSITION_INDEPENDENT_CODE=ON
  STEP_TARGETS build
)

externalproject_get_property(libuvc_project SOURCE_DIR)

add_library(libuvc SHARED IMPORTED)
set_target_properties(libuvc PROPERTIES
  IMPORTED_LOCATION "${CMAKE_INSTALL_PREFIX}/lib/libuvc.so"
)
add_dependencies(libuvc libuvc_project-build)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()

# Fix installation location and permissions
install(DIRECTORY ${SOURCE_DIR}/
  DESTINATION lib
  USE_SOURCE_PERMISSIONS
  FILES_MATCHING PATTERN "*.so"
)
