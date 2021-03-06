cmake_minimum_required(VERSION 3.8)
project(POPLAR_TRIE)

if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif ()

# C++17 compiler check
if (CMAKE_CXX_COMPILER MATCHES ".*clang.*" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  set(CMAKE_COMPILER_IS_CLANGXX 1)
endif ()
if (CMAKE_CXX_COMPILER_ID STREQUAL "Intel")
  set(CMAKE_COMPILER_IS_INTEL 1)
endif ()
if ((CMAKE_COMPILER_IS_GNUCXX AND ${CMAKE_CXX_COMPILER_VERSION} VERSION_LESS 7.0) OR (CMAKE_COMPILER_IS_CLANGXX AND ${CMAKE_CXX_COMPILER_VERSION} VERSION_LESS 4.0))
  message(FATAL_ERROR "Your C++ compiler does not support C++17. Please install g++ 7.0 (or greater) or clang 4.0 (or greater)")
else ()
  message(STATUS "Compiler is recent enough to support C++17.")
endif ()

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(POPLAR_ENABLE_EX_STATS
  "Enable extra stats."
  OFF)

option(POPLAR_USE_POPCNT
  "Use popcount intrinsic. Available on x86-64 since SSE4.2."
  ON)

if (POPLAR_USE_POPCNT)
  if (UNIX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4.2")
  endif ()
endif ()

message(STATUS "BUILD_TYPE is ${CMAKE_BUILD_TYPE}")
message(STATUS "CXX_FLAGS are ${CMAKE_CXX_FLAGS}")
message(STATUS "CXX_FLAGS_DEBUG are ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "CXX_FLAGS_RELEASE are ${CMAKE_CXX_FLAGS_RELEASE}")

configure_file(
  ${POPLAR_TRIE_SOURCE_DIR}/include/poplar/poplar_config.hpp.in
  ${POPLAR_TRIE_SOURCE_DIR}/include/poplar/poplar_config.hpp
)

#file(GLOB_RECURSE INCLUDE_FILES "include/*.hpp")
#add_executable(sample sample/sample.cpp ${INCLUDE_FILES})

include_directories(include)

add_subdirectory(benchmark)
add_subdirectory(sample)

enable_testing()
add_subdirectory(test)

file(GLOB INTERNAL_HEADERS "${POPLAR_TRIE_SOURCE_DIR}/include/poplar/*.hpp")
install(FILES include/poplar.hpp DESTINATION include)
install(FILES ${INTERNAL_HEADERS} DESTINATION include/poplar)

