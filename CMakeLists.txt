cmake_minimum_required(VERSION 3.2.2)
project(Emojicode)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_C_STANDARD 11)

if(heapSize)
  add_definitions(-DheapSize=${heapSize})
endif()

if(defaultPackagesDirectory)
  add_definitions(-DdefaultPackagesDirectory="${defaultPackagesDirectory}")
endif()

file(GLOB EMOJICODE_SOURCES "EmojicodeReal-TimeEngine/*")
add_executable(emojicode ${EMOJICODE_SOURCES} utf8.c utf8.h)
target_compile_options(emojicode PUBLIC -Ofast -Wall -Wno-unused-result -Wno-missing-braces -pedantic)
target_link_libraries(emojicode dl pthread)

file(GLOB EMOJICODEC_SOURCES "EmojicodeCompiler/*")
add_executable(emojicodec ${EMOJICODEC_SOURCES} utf8.c utf8.h)
target_compile_options(emojicodec PUBLIC -Ofast -Wall -Wno-unused-result -Wno-missing-braces -pedantic)

if(APPLE)
  set(CMAKE_SHARED_MODULE_CREATE_CXX_FLAGS "${CMAKE_SHARED_MODULE_CREATE_CXX_FLAGS} -undefined dynamic_lookup")
endif()

function(package name)
  file(GLOB FILES_SOURCES "DefaultPackages/${name}/*")
  add_library(${name} MODULE ${FILES_SOURCES})
  set_target_properties(${name} PROPERTIES PREFIX "")
  set_target_properties(${name} PROPERTIES SUFFIX ".so")
endfunction()

package(files)
package(sockets)

add_custom_target(dist python3 ${PROJECT_SOURCE_DIR}/dist.py ${PROJECT_SOURCE_DIR})
add_custom_target(tests python3 ${PROJECT_SOURCE_DIR}/tests.py ${PROJECT_SOURCE_DIR} DEPENDS dist)
