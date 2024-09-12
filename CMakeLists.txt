cmake_minimum_required(VERSION 3.11)
project(rmagine_viewer)

add_compile_options(-std=c++17)
set(CMAKE_CXX_STANDARD 17)

include(FetchContent)

set(FETCHCONTENT_QUIET FALSE)

FetchContent_Declare(
  polyscope
  GIT_REPOSITORY https://github.com/nmwsharp/polyscope.git
  GIT_TAG        v2.3.0
  GIT_PROGRESS   TRUE
)

FetchContent_Declare(
  portable_file_dialogs
  GIT_REPOSITORY https://github.com/samhocevar/portable-file-dialogs.git
  GIT_TAG        0.1.0
  GIT_PROGRESS   TRUE
)

FetchContent_Declare(
  rmagine
  GIT_REPOSITORY https://github.com/uos/rmagine.git
  GIT_TAG        v2.2.7
  GIT_PROGRESS   TRUE
)

FetchContent_MakeAvailable(rmagine polyscope portable_file_dialogs)

add_executable(rmagine_viewer src/rmagine_viewer.cpp)
target_link_libraries(rmagine_viewer
    polyscope
    rmagine::core
    rmagine::embree
    rmagine::ouster
    portable_file_dialogs)
