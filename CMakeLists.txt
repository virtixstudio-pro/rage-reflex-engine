cmake_minimum_required(VERSION 3.10)
project(moteur LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)

find_package(PkgConfig REQUIRED)
pkg_check_modules(SDL2 REQUIRED sdl2)

add_executable(moteur src/main.cpp)

target_link_libraries(moteur ${SDL2_LIBRARIES})
target_include_directories(moteur PRIVATE ${SDL2_INCLUDE_DIRS})
