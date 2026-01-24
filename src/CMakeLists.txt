#
# Copyright (c) 2026      Kingshuk Haldar.
#                         All rights reserved.
#
# Authors: Kingshuk Haldar <haldar.kingshuk@gmail.com>
#

# libparaver.a
add_library(paraver STATIC
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver/paraver.c
)
target_include_directories(paraver PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver
  ${CMAKE_CURRENT_SOURCE_DIR}
)
target_link_libraries(paraver PUBLIC m)

# libclocks.a
add_library(clocks STATIC
  ${CMAKE_CURRENT_SOURCE_DIR}/clocks/clocks.c
)
target_include_directories(clocks PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}/clocks
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver
  ${CMAKE_CURRENT_SOURCE_DIR}
)
target_link_libraries(clocks PUBLIC paraver m)

# libmonitoring.a
add_library(monitoring STATIC
  ${CMAKE_CURRENT_SOURCE_DIR}/monitoring.c
)
target_include_directories(monitoring PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver
  ${CMAKE_CURRENT_SOURCE_DIR}/clocks
)
target_link_libraries(monitoring PUBLIC m)

# libreplay.a
file(GLOB REPLAY_SOURCES CONFIGURE_DEPENDS
  "${CMAKE_CURRENT_SOURCE_DIR}/*.c"
)
list(FILTER REPLAY_SOURCES EXCLUDE REGEX ".*/replay\\.c$")
list(FILTER REPLAY_SOURCES EXCLUDE REGEX ".*/monitoring\\.c$")

add_library(replay STATIC ${REPLAY_SOURCES})
target_include_directories(replay PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver
  ${CMAKE_CURRENT_SOURCE_DIR}/clocks
)
target_link_libraries(replay PUBLIC paraver clocks monitoring m)

add_executable(clocktalk
  ${CMAKE_CURRENT_SOURCE_DIR}/replay.c
)

target_include_directories(clocktalk PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/paraver
  ${CMAKE_CURRENT_SOURCE_DIR}/clocks
)

# Link order matters for static linkage; keep dependencies to the right.
target_link_libraries(clocktalk PRIVATE
  replay
  monitoring
  clocks
  paraver
  m
)
