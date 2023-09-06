cmake_minimum_required(VERSION 3.15)
project(foxglove_websocket CXX)

set(CMAKE_CXX_STANDARD 17)
set(CXX_STANDARD_REQUIRED ON)

find_package(Protobuf REQUIRED)

add_subdirectory(dependencies/json)

FILE(GLOB all_protos "${CMAKE_CURRENT_SOURCE_DIR}/foxglove_types/proto/foxglove/*.proto")

FOREACH(f ${all_protos})
    file(RELATIVE_PATH f ${CMAKE_CURRENT_SOURCE_DIR}/foxglove_types/proto ${f})
    STRING(REGEX REPLACE "\\.proto$" "" f ${f})
    LIST(APPEND proto_sources "generated/foxglove_types/${f}.pb.h")
    LIST(APPEND proto_sources "generated/foxglove_types/${f}.pb.cc")
ENDFOREACH(f)

add_custom_command(
    OUTPUT ${proto_sources}
    COMMAND ${CMAKE_COMMAND} -E make_directory generated/foxglove_types
    COMMAND ${Protobuf_PROTOC_EXECUTABLE} --proto_path=${CMAKE_CURRENT_SOURCE_DIR}/foxglove_types/proto --cpp_out=generated/foxglove_types ${all_protos}
)

add_library(${PROJECT_NAME}
    src/parameter.cpp 
    src/serialization.cpp
    ${proto_sources})
target_include_directories(${PROJECT_NAME} 
    PUBLIC 
    ${CMAKE_CURRENT_BINARY_DIR}/generated/foxglove_types
    include)
target_link_libraries(${PROJECT_NAME}
    nlohmann_json websocketpp asio z)

install(TARGETS foxglove_websocket)
INSTALL (DIRECTORY 
    ${CMAKE_SOURCE_DIR}/include/ 
    ${CMAKE_BINARY_DIR}/generated/
    DESTINATION include)
