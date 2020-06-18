cmake_minimum_required(VERSION 2.8.12)

project(nsjail)
set(CMAKE_EXE_LINKER_FLAGS "-static -static-libstdc++ -static-libgcc")
set(CMAKE_FIND_LIBRARY_SUFFIXES .a)
set(CMAKE_EXE_LINK_DYNAMIC_C_FLAGS)       # remove -Wl,-Bdynamic
set(CMAKE_EXE_LINK_DYNAMIC_CXX_FLAGS)
set(CMAKE_SHARED_LIBRARY_C_FLAGS)         # remove -fPIC
set(CMAKE_SHARED_LIBRARY_CXX_FLAGS)
set(CMAKE_SHARED_LIBRARY_LINK_C_FLAGS)    # remove -rdynamic
set(CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS)

find_package(PkgConfig REQUIRED)
find_package(Protobuf REQUIRED)
find_package(Threads REQUIRED)

add_subdirectory(kafel)

pkg_check_modules(LIBNL3 REQUIRED IMPORTED_TARGET libnl-route-3.0)

include_directories(${Protobuf_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kafel/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kafel/src)
# ???
include_directories(${LIBNL3_INCLUDE_DIRS})

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS config.proto)

add_executable(${CMAKE_PROJECT_NAME}  caps.cc cgroup.cc cgroup2.cc cmdline.cc config.cc contain.cc cpu.cc logs.cc mnt.cc net.cc nsjail.cc pid.cc
    sandbox.cc subproc.cc uts.cc user.cc util.cc ${PROTO_SRCS} ${PROTO_HDRS})
target_link_libraries(${CMAKE_PROJECT_NAME} -pthread  ${LIBNL3_LIBRARIES} ${Protobuf_LIBRARIES} kafel -static)
