cmake_minimum_required(VERSION 3.14)
project(cpp_build)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)


if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/include")
    file(GLOB_RECURSE INC_FILES "${CMAKE_CURRENT_SOURCE_DIR}/include/*")
    list(APPEND SRCS "${INC_FILES}")
    list(APPEND INCS "${INC_FILES}")
endif()

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/src")
    file(GLOB_RECURSE SRC_FILES "${CMAKE_CURRENT_SOURCE_DIR}/src/*")
    list(APPEND SRCS "${SRC_FILES}")
endif()

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/proto")
    find_package(protobuf CONFIG REQUIRED)
    list(APPEND Protobuf_IMPORT_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/proto")
    file(GLOB_RECURSE PROTO_FILES "${CMAKE_CURRENT_SOURCE_DIR}/proto/*.proto")
    protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS "${PROTO_FILES}")
    list(APPEND SRCS "${PROTO_SRCS}" "${PROTO_HDRS}")
    set(INC_FOLDERS ${CMAKE_BINARY_DIR})
endif()

add_executable(cpp_build main.cpp ${SRCS})
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/include")
    list(APPEND INC_FOLDERS "${CMAKE_CURRENT_SOURCE_DIR}/include")
endif()
target_include_directories(cpp_build PRIVATE "${INC_FOLDERS}")
target_link_libraries(cpp_build PRIVATE protobuf::libprotobuf)

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/test")
    find_package(GTest REQUIRED)
    file(GLOB_RECURSE TEST_FILES "${CMAKE_CURRENT_SOURCE_DIR}/src/*")
    add_executable(cpp_test ${SRCS} ${TEST_FILES})
    target_include_directories(cpp_test PRIVATE include)
    target_link_libraries(cpp_test PRIVATE GTest::gtest_main GTest::gtest protobuf::libprotobuf)
endif()

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/docs")
    find_package(Doxygen)
    if (DOXYGEN_FOUND)
        add_custom_target(doc_doxygen ALL
            COMMAND ${DOXYGEN_EXECUTABLE} "${CMAKE_CURRENT_SOURCE_DIR}/docs/Doxyfile"
            WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/docs"
            COMMENT "Generating documentation with Doxygen..."
            VERBATIM
        )
    else()
        message(WARNING "Doxygen was not found. Documentation won't be generated!")
    endif()
endif()
