cmake_minimum_required(VERSION 2.8.12)

project(nsjail)
SET(CMAKE_FIND_LIBRARY_SUFFIXES ".a")
SET(BUILD_SHARED_LIBS OFF)
SET(CMAKE_EXE_LINKER_FLAGS "-static")
add_subdirectory(kafel)

find_package(PkgConfig REQUIRED)
find_package(Protobuf REQUIRED)

pkg_check_modules(LIBNL3 REQUIRED IMPORTED_TARGET libnl-route-3.0)

include_directories(${Protobuf_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kafel/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kafel/src)

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS config.proto)

add_executable(${CMAKE_PROJECT_NAME} caps.cc cgroup.cc cgroup2.cc cmdline.cc config.cc contain.cc cpu.cc logs.cc mnt.cc net.cc nsjail.cc pid.cc
    sandbox.cc subproc.cc uts.cc user.cc util.cc ${PROTO_SRCS} ${PROTO_HDRS})

target_link_libraries(${CMAKE_PROJECT_NAME} protobuf::libprotobuf ${Protobuf_LIBRARIES} PkgConfig::LIBNL3 kafel)
