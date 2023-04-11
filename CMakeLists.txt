cmake_minimum_required(VERSION 3.10)
project(lvgl)
set(CMAKE_C_STANDARD 11)#C11
set(CMAKE_CXX_STANDARD 17)#C17
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(ExternalProject)

set_directory_properties(PROPERTIES EP_PREFIX ${CMAKE_BINARY_DIR}/Rust)

message (STATUS "CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")

ExternalProject_Add(
        lvgl_but_rust
        DOWNLOAD_COMMAND ""
        CONFIGURE_COMMAND ""
        BUILD_COMMAND cargo build COMMAND cargo build --all
        BINARY_DIR "${CMAKE_SOURCE_DIR}/lvgl_but_rust"
        INSTALL_COMMAND ""
        LOG_BUILD ON)


INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR})

file(GLOB_RECURSE INCLUDES "lv_drivers/*.h" "lv_demos/*.h"  "lvgl/*.h"  "./*.h" )
file(GLOB_RECURSE SOURCES  "lv_drivers/*.c" "lv_demos/*.c"  "lvgl/*.c" )

SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin) 
SET(CMAKE_CXX_FLAGS "-O3")

find_package(SDL2 REQUIRED SDL2)
message(STATUS "SDL2_INCLUDE_DIRS: ${SDL2_INCLUDE_DIRS}")
message(STATUS "SDL2_LIBRARIES: ${SDL2_LIBRARIES}")
include_directories(${SDL2_INCLUDE_DIRS})
add_executable(main main.c mouse_cursor_icon.c ${SOURCES} ${INCLUDES})
add_dependencies(main lvgl_but_rust)
add_compile_definitions(LV_CONF_INCLUDE_SIMPLE)
target_link_libraries(main
        PRIVATE SDL2
        debug "${CMAKE_SOURCE_DIR}/lvgl_but_rust/target/debug/liblvgl_but_rust.a"
        optimized "${CMAKE_SOURCE_DIR}/lvgl_but_rust/target/release/liblvgl_but_rust.a"
        )
add_custom_target (run COMMAND ${EXECUTABLE_OUTPUT_PATH}/main)
