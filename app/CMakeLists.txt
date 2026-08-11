cmake_minimum_required(VERSION 3.22.1)
project(moteur LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)

# Chemins vers SDL2 précompilé (optionnel)
set(SDL_PATH ${CMAKE_SOURCE_DIR}/../jniLibs/SDL2)
set(SDL_INCLUDE_DIR ${SDL_PATH}/include)
set(SDL_LIBRARY_DIR ${SDL_PATH}/lib/${ANDROID_ABI})

# Créer la lib native
add_library(moteur SHARED
    src/main/cpp/main.cpp
)

# Si des libs précompilées sont présentes dans jniLibs, on les utilise d'abord
if(EXISTS "${SDL_LIBRARY_DIR}")
  message(STATUS "Using prebuilt SDL2 from ${SDL_LIBRARY_DIR}")
  include_directories(${SDL_INCLUDE_DIR})
  find_library(SDL2_LIB SDL2 PATHS ${SDL_LIBRARY_DIR} NO_DEFAULT_PATH)
  find_library(SDL2MAIN_LIB SDL2main PATHS ${SDL_LIBRARY_DIR} NO_DEFAULT_PATH)
  if(SDL2_LIB)
    target_link_libraries(moteur PRIVATE ${SDL2_LIB} android log)
    return()
  endif()
endif()

# Si pas de libs précompilées, essayer de récupérer et compiler SDL2 via FetchContent
include(FetchContent)
message(STATUS "Prebuilt SDL2 not found — fetching and building SDL2 from source (may take a while)...")

FetchContent_Declare(
  SDL2
  GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
  # Pin to a stable release tag for reproducible builds
  GIT_TAG release-2.26.11
)

# Optionally configure SDL build for Android
# Let SDL's CMake detect ANDROID/NDK from the toolchain used by Gradle
FetchContent_MakeAvailable(SDL2)

if(TARGET SDL2::SDL2)
  message(STATUS "Built SDL2 from source via FetchContent")
  target_link_libraries(moteur PRIVATE SDL2::SDL2 android log)
  return()
endif()

message(FATAL_ERROR "SDL2 non trouvé — ni libs précompilées dans jniLibs, ni compilation via FetchContent." )
