cmake_minimum_required(VERSION 3.11)
project(ReMania-GameBox VERSION 0.0.1 LANGUAGES CXX)

option(USE_OPENGL "Build RMGB-Graphics with OpenGL support" ON)
option(USE_MULTITHREADING "Build RMGB with support for using multiple CPU cores" ON)

set(SRC_DIR src/RMGB)
set(INC_DIR include/RMGB)

add_library(RMGB-Graphics ${SRC_DIR}/Graphics/graphics.cpp)
add_library(RMGB-Physics ${SRC_DIR}/Physics/physics.cpp)
add_library(RMGB-Assets ${SRC_DIR}/Assets/assets.cpp)

target_include_directories(RMGB-Graphics PUBLIC ${CMAKE_SOURCE_DIR}/${INC_DIR})
target_include_directories(RMGB-Physics PUBLIC ${CMAKE_SOURCE_DIR}/${INC_DIR})
target_include_directories(RMGB-Assets PUBLIC ${CMAKE_SOURCE_DIR}/${INC_DIR})

if(USE_OPENGL MATCHES "ON")
    find_package(OpenGL 4.3 REQUIRED)
    find_package(GLEW 1.9)
    find_package(SDL2 QUIET)
    target_compile_definitions(RMGB-Graphics PUBLIC OPENGL)
    if (NOT GLEW_FOUND)
        target_link_libraries(RMGB-Graphics PUBLIC glew)
    else()
        message(" -- Building GLEW from scratch")
        add_subdirectory(lib/glew)
        target_link_libraries(RMGB-Graphics glew)
    endif()
    if (NOT SDL2_FOUND)
        target_link_libraries(RMGB-Graphics PUBLIC sdl2)
    else()
        message(" -- Building SDL2 from scratch")
        add_subdirectory("lib/sdl2")
    endif()
endif()

add_custom_target(ReMania-GameBox)
add_dependencies(ReMania-GameBox RMGB-Graphics RMGB-Physics RMGB-Assets)