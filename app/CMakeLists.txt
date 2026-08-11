cmake_minimum_required(VERSION 3.10)
project(moteur LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)

# Trouver SDL2
find_library(SDL2_LIB SDL2)
find_path(SDL2_INCLUDE_DIR SDL2/SDL.h)

add_library(moteur SHARED
    src/main/cpp/main.cpp
)

target_include_directories(moteur PRIVATE
    ${SDL2_INCLUDE_DIR}
)

target_link_libraries(moteur
    ${SDL2_LIB}
    android
    log
)
