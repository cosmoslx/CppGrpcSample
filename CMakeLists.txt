cmake_minimum_required(VERSION 3.1)

# example setting VCPKG_ROOT:
#   $env:VCPKG_ROOT="g:\github\vcpkg"
if(DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
      CACHE STRING "")
endif()

project(cpp_grpc_sample)

find_package(gRPC CONFIG REQUIRED)
find_package(protobuf CONFIG REQUIRED)

# Proto file
get_filename_component(hw_proto "proto/helloworld.proto" ABSOLUTE)
get_filename_component(hw_proto_path "${hw_proto}" PATH)

# Generated sources
set(hw_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/helloworld.pb.cc")
set(hw_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/helloworld.pb.h")
set(hw_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/helloworld.grpc.pb.cc")
set(hw_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/helloworld.grpc.pb.h")

find_program(_PROTOBUF_PROTOC protoc)
find_program(_GRPC_CPP_PLUGIN_EXECUTABLE grpc_cpp_plugin)
add_custom_command(
  OUTPUT "${hw_proto_srcs}" "${hw_proto_hdrs}" "${hw_grpc_srcs}" "${hw_grpc_hdrs}"
  COMMAND ${_PROTOBUF_PROTOC}
  ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
    --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
    -I "${hw_proto_path}"
    --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
    "${hw_proto}"
  DEPENDS "${hw_proto}"
)

add_executable(server src/greeter_server.cc ${hw_proto_srcs} ${hw_grpc_srcs})
target_include_directories(server PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")
target_link_libraries(server PRIVATE
  gRPC::grpc++ gRPC::gpr gRPC::grpc gRPC::grpc++_alts gRPC::grpc++_reflection
  protobuf::libprotoc protobuf::libprotobuf protobuf::libprotobuf-lite
) 

add_executable(client src/greeter_client.cc ${hw_proto_srcs} ${hw_grpc_srcs})
target_include_directories(client PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")
target_link_libraries(client PRIVATE
  gRPC::grpc++ gRPC::gpr gRPC::grpc gRPC::grpc++_alts
  protobuf::libprotoc protobuf::libprotobuf protobuf::libprotobuf-lite
)

add_executable(inproc src/greeter_inproc.cc ${hw_proto_srcs} ${hw_grpc_srcs})
target_include_directories(inproc PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")
target_link_libraries(inproc PRIVATE
  gRPC::grpc++ gRPC::gpr gRPC::grpc gRPC::grpc++_alts gRPC::grpc++_reflection
  protobuf::libprotoc protobuf::libprotobuf protobuf::libprotobuf-lite
)
