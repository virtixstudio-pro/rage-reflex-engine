cmake_minimum_required(VERSION 3.14)
project(moteur LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)

# chemin du main existant dans le repo
set(PROJECT_MAIN "${CMAKE_SOURCE_DIR}/app/src/main/cpp/main.cpp")
if(NOT EXISTS "${PROJECT_MAIN}")
  message(FATAL_ERROR "Fichier principal introuvable: ${PROJECT_MAIN}. Vérifie l'arborescence ou adapte la variable PROJECT_MAIN.")
endif()

if(ANDROID)
  message(STATUS "Cible Android détectée — utiliser app/CMakeLists.txt et fournir jniLibs/SDL2")
  return()
endif()

# 1) Try CMake config package
find_package(SDL2 CONFIG QUIET)
if(TARGET SDL2::SDL2)
  message(STATUS "Found SDL2 via CMake package")
  add_executable(moteur "${PROJECT_MAIN}")
  target_link_libraries(moteur PRIVATE SDL2::SDL2)
  return()
endif()

# 2) Try pkg-config
find_package(PkgConfig REQUIRED)
pkg_check_modules(SDL2_PKG QUIET sdl2)
if(SDL2_PKG_FOUND)
  message(STATUS "Found SDL2 via pkg-config")
  add_executable(moteur "${PROJECT_MAIN}")
  target_include_directories(moteur PRIVATE ${SDL2_PKG_INCLUDE_DIRS})
  target_link_libraries(moteur PRIVATE ${SDL2_PKG_LIBRARIES})
  return()
endif()

message(FATAL_ERROR "SDL2 non trouvé. Installe SDL2 sur ton système (Termux) ou adapte le CMakeLists.")
