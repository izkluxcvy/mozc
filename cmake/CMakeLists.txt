cmake_minimum_required(VERSION 3.12)
project(mozc)

option(ENABLE_SERVER "Build Mozc with server" On)
option(BUILD_SERVER "Build mozc_server" On)
option(BUILD_TOOL "Build mozc_tool" On)
option(BUILD_FCITX "Build fcitx5-mozc" On)
option(BUILD_IBUS "Build ibus_mozc" On)
option(MOZC_USE_SYSTEM_DEPS
    "Use system-installed abseil-cpp/protobuf instead of fetching them at configure time (required for sandboxed builds such as OpenBSD ports)"
    Off)

find_package(PkgConfig REQUIRED)
find_package(Python3 REQUIRED COMPONENTS Interpreter)

if(BUILD_FCITX)
  find_package(Fcitx5Core REQUIRED)
  pkg_check_modules(Fcitx5Module REQUIRED "Fcitx5Module" IMPORTED_TARGET)
endif()
if(BUILD_IBUS)
  pkg_check_modules(Ibus REQUIRED IMPORTED_TARGET
      glib-2.0 gobject-2.0 gio-2.0 ibus-1.0)
endif()
if(BUILD_TOOL)
  find_package(Qt6 REQUIRED COMPONENTS Gui Widgets)
endif()

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(MOZC_USE_SYSTEM_DEPS)
  find_package(absl CONFIG REQUIRED)
  find_package(protobuf CONFIG REQUIRED)
else()
  # Fetch dependencies
  include(FetchContent)

  set(ABSL_PROPAGATE_CXX_STD ON)
  set(ABSL_ENABLE_INSTALL OFF)
  FetchContent_Declare(
    absl
    GIT_REPOSITORY https://github.com/abseil/abseil-cpp.git
    GIT_TAG 20260107.1
    GIT_SHALLOW TRUE
  )

  set(protobuf_BUILD_TESTS OFF)
  set(protobuf_INSTALL OFF)
  set(protobuf_ABSL_PROVIDER "package")
  FetchContent_Declare(
    protobuf
    GIT_REPOSITORY https://github.com/protocolbuffers/protobuf.git
    GIT_TAG v34.1
    GIT_SHALLOW TRUE
  )

  FetchContent_MakeAvailable(absl protobuf)

  include(${protobuf_SOURCE_DIR}/cmake/protobuf-generate.cmake)
endif()

include(cmake/Macros.cmake)

include(GNUInstallDirs)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_FLAGS "-funsigned-char ${CMAKE_CXX_FLAGS}")
add_definitions(-DMOZC_BUILD)

set(MOZC_SOURCE_DIR ${PROJECT_SOURCE_DIR}/../src)

include_directories(${MOZC_SOURCE_DIR})
set(MOZC_LIBRARY_TYPE STATIC)
# set(MOZC_LIBRARY_TYPE SHARED) # Uncomment to test dependency and missing symbols.

add_subdirectory(protocol)
add_subdirectory(base)
add_subdirectory(config)
add_subdirectory(composer)
add_subdirectory(converter)
add_subdirectory(dictionary)
add_subdirectory(data_manager)
add_subdirectory(rewriter)
add_subdirectory(storage)
add_subdirectory(ipc)
add_subdirectory(client)
add_subdirectory(transliteration)
add_subdirectory(session)
add_subdirectory(prediction)
add_subdirectory(engine)
add_subdirectory(renderer)

if (BUILD_TOOL)
add_subdirectory(gui)
endif()

if (BUILD_SERVER AND ENABLE_SERVER)
add_subdirectory(server)
endif()

if (BUILD_FCITX)
add_subdirectory(fcitx)
endif()

if (BUILD_IBUS)
add_subdirectory(unix/ibus)
endif()
