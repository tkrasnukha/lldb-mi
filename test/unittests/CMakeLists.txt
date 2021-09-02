cmake_minimum_required(VERSION 3.11)

include(FetchContent)
FetchContent_Declare(gtest
  QUIET
  URL https://github.com/google/googletest/archive/refs/tags/release-1.11.0.zip
)

# Prevent overriding the parent project's compiler/linker settings, for Windows.
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

FetchContent_MakeAvailable(gtest)

enable_testing()
include(GoogleTest)

include_directories(../../src)

set(UNITTESTS_ROOT ${CMAKE_CURRENT_LIST_DIR})
set(SOURCE_DIR ${UNITTESTS_ROOT}/../../src)

function(add_lldb_mi_test NAME TEST)
  cmake_parse_arguments(
    ARG
    ""
    ""
    "SOURCES"
    ${ARGN}
  )

  set(SOURCES_RELATIVE "")
  foreach(SRC ${ARG_SOURCES})
    list(APPEND SOURCES_RELATIVE ${SOURCE_DIR}/${SRC})
  endforeach(SRC)

  add_executable(${NAME} ${TEST} ${SOURCES_RELATIVE})
  target_link_libraries(${NAME} gtest_main)
  set_target_properties(${NAME} PROPERTIES FOLDER "unittests")

  gtest_discover_tests(${NAME})
endfunction(add_lldb_mi_test)

add_subdirectory(arguments)
