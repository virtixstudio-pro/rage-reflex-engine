cmake_minimum_required(VERSION 3.22.1)
project(moteur LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)

# Chemins vers SDL2
set(SDL_PATH ${CMAKE_SOURCE_DIR}/../jniLibs/SDL2)
set(SDL_INCLUDE_DIR ${SDL_PATH}/include)
set(SDL_LIBRARY_DIR ${SDL_PATH}/lib/${ANDROID_ABI})

# Inclure les headers de SDL2
include_directories(${SDL_INCLUDE_DIR})

# Ajouter la librairie SDL2
find_library(SDL2_LIB SDL2 PATHS ${SDL_LIBRARY_DIR} NO_DEFAULT_PATH)
find_library(SDL2MAIN_LIB SDL2main PATHS ${SDL_LIBRARY_DIR} NO_DEFAULT_PATH)

# Créer la lib native
add_library(moteur SHARED
    src/main/cpp/main.cpp
)

# Lier SDL2
target_link_libraries(moteur
    ${SDL2_LIB}
    android
    log
)
