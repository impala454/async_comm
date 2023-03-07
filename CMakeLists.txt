cmake_minimum_required(VERSION 3.5)
project(async_comm)

if (NOT DEFINED CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif (NOT DEFINED CMAKE_BUILD_TYPE)

find_package(ament_cmake REQUIRED)
find_package(Boost REQUIRED COMPONENTS system)
find_package(Threads)

include_directories(include)

add_library(async_comm SHARED
  src/comm.cpp
  src/udp.cpp
  src/serial.cpp
  src/tcp_client.cpp
)

target_include_directories(async_comm
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
ament_target_dependencies(async_comm
  Boost
)
target_link_libraries(async_comm ${Boost_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})
ament_export_targets(async_commTargets HAS_LIBRARY_TARGET)

# examples
option(ASYNC_COMM_BUILD_EXAMPLES "Build examples" OFF)

if(ASYNC_COMM_BUILD_EXAMPLES)
  add_executable(udp_hello_world examples/udp_hello_world.cpp)
  target_link_libraries(udp_hello_world async_comm)

  add_executable(serial_loopback examples/serial_loopback.cpp)
  target_link_libraries(serial_loopback async_comm)

  add_executable(serial_protocol examples/serial_protocol.cpp)
  target_link_libraries(serial_protocol async_comm)

  add_executable(tcp_client_hello_world examples/tcp_client_hello_world.cpp)
  target_link_libraries(tcp_client_hello_world async_comm)
endif(ASYNC_COMM_BUILD_EXAMPLES)

install(TARGETS async_comm
  EXPORT async_commTargets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  INCLUDES DESTINATION include
)

ament_export_include_directories(include)
ament_export_libraries(async_comm)
ament_export_dependencies(Boost)

ament_package()
