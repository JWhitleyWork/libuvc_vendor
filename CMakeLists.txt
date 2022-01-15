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

set(libuvc_DESTDIR ${CMAKE_CURRENT_BINARY_DIR}/libuvc_install)

include(ExternalProject)
externalproject_add(libuvc_project
  GIT_REPOSITORY https://github.com/libuvc/libuvc
  GIT_TAG v0.0.6
  GIT_SHALLOW TRUE
  BUILD_IN_SOURCE TRUE
  INSTALL_DIR ${libuvc_DESTDIR}
  CONFIGURE_COMMAND ${CMAKE_COMMAND} -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> -DCMAKE_POSITION_INDEPENDENT_CODE=ON
)

find_library(libuvc_PATH libuvc.so
  HINTS ${libuvc_DESTDIR}/lib
  REQUIRED
)

add_library(libuvc SHARED IMPORTED)
set_property(TARGET libuvc PROPERTY
  IMPORTED_LOCATION ${libuvc_PATH})
add_dependencies(libuvc libuvc_project)

ament_export_include_directories(include)
ament_export_libraries(libuvc)

install(DIRECTORY ${libuvc_DESTDIR}/include/
  DESTINATION include
)

install(DIRECTORY ${libuvc_DESTDIR}/lib/
  DESTINATION lib
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
