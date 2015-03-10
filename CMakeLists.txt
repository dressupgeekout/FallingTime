project(ativayeban)
cmake_minimum_required(VERSION 2.6)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")

if(CMAKE_CROSSCOMPILING)
    # check for gcw0
    if(CMAKE_C_COMPILER MATCHES ".*gcw0-linux.*")
        add_definitions(-D__GCW0__)
        set(GCW0 1)
    endif()
endif()

# Default definitions
if(MSVC)
    add_definitions(-Wall -W4 -wd"4996")
else()
    add_definitions(-Wall -Wextra -Wunused -Wshadow -Wunreachable-code -std=c99)
    add_definitions(-fno-math-errno -fno-exceptions)
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Release")
    set(CMAKE_C_FLAGS_RELEASE "-O2 -g0")
elseif(CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
    set(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")
elseif(CMAKE_BUILD_TYPE STREQUAL "MinSizeRel")
    set(CMAKE_C_FLAGS_MINSIZEREL "-Os -g0")
elseif(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(CMAKE_C_FLAGS_DEBUG "-O0 -g3 -pg")
    set(CMAKE_EXE_LINKER_FLAGS_DEBUG "-pg")
    set(CMAKE_SHARED_LINKER_FLAGS_DEBUG "-pg")
    set(CMAKE_MODULE_LINKER_FLAGS_DEBUG "-pg")
endif()

# Check for SDL
find_package(SDL REQUIRED)
if(NOT SDL_FOUND)
  message(FATAL_ERROR "Couldn't find SDL development files. On Debian-based systems (such as Ubuntu) you should install the 'libsdl1.2-dev' package.")
else()
  include_directories(${SDL_INCLUDE_DIR})
endif()
find_package(SDL_image REQUIRED)
if(NOT SDLIMAGE_FOUND)
  message(FATAL_ERROR "Couldn't find SDL-image development files. On Debian-based systems (such as Ubuntu) you should install the 'libsdl-image1.2-dev' package.")
else()
  include_directories(${SDLIMAGE_INCLUDE_DIR})
endif()
find_package(SDL_mixer REQUIRED)
if(NOT SDLMIXER_FOUND)
  message(FATAL_ERROR "Couldn't find SDL-mixer development files. On Debian-based systems (such as Ubuntu) you should install the 'libsdl-mixer1.2-dev' package.")
else()
  include_directories(${SDLMIXER_INCLUDE_DIR})
endif()

include_directories(.)

file(GLOB SOURCES *.c)
if(GCW0)
    set(SOURCES ${SOURCES} platform/opendingux.c)
else()
    set(SOURCES ${SOURCES} platform/general.c)
endif()
file(GLOB HEADERS *.h)

add_executable(ativayeban ${SOURCES} ${HEADERS})

# libSDLMain comes with libSDL if needed on certain platforms
if(NOT SDLMAIN_LIBRARY)
  set(SDLMAIN_LIBRARY "")
endif()

target_link_libraries(ativayeban ${CMAKE_LD_FLAGS} ${SDL_LIBRARY} ${SDLIMAGE_LIBRARY} ${SDLMIXER_LIBRARY} ${SDLMAIN_LIBRARY})