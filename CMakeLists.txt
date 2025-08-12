cmake_minimum_required(VERSION 3.11)
project(rmagine_viewer)

add_compile_options(-std=c++17)
set(CMAKE_CXX_STANDARD 17)


function(print_link_info tgt)
  message(STATUS "--- ${tgt} ---")
  foreach(prop
    LINK_DIRECTORIES
    INTERFACE_LINK_DIRECTORIES
    LINK_LIBRARIES
    INTERFACE_LINK_LIBRARIES
    IMPORTED_LINK_INTERFACE_LIBRARIES
    )
    get_property(val TARGET ${tgt} PROPERTY ${prop})
    if(val)
      message(STATUS "- ${prop} = ${val}")
    endif()
  endforeach()

  # Per-config (useful for imported targets)
  foreach(cfg Debug Release RelWithDebInfo MinSizeRel)
    get_property(percfg TARGET ${tgt} PROPERTY IMPORTED_LINK_INTERFACE_LIBRARIES_${cfg})
    if(percfg)
      message(STATUS "${tgt} :: IMPORTED_LINK_INTERFACE_LIBRARIES_${cfg} = ${percfg}")
    endif()
  endforeach()
endfunction()


# set(CPM_USE_LOCAL_PACKAGES TRUE)
# This didn't work in my tests:
# when rmagine is already installed on the system,
# CPMAddPackage doesn't return all of rmagine's targets 
# properly. find_package does

include(cmake/CPM.cmake)

find_package(rmagine 2.3.1
    COMPONENTS
        core
        embree
        ouster
)

if(NOT TARGET rmagine::core)

CPMAddPackage(
    NAME rmagine
    GITHUB_REPOSITORY uos/rmagine
    GIT_TAG cpm
    OPTIONS
        "RMAGINE_BUILD_TESTS OFF"
        "RMAGINE_BUILD_TOOLS OFF"
)

endif()

print_link_info(rmagine::embree)

CPMAddPackage(
    NAME polyscope 
    GITHUB_REPOSITORY nmwsharp/polyscope
    VERSION 2.4.0
    OPTIONS
        "POLYSCOPE_BACKEND_OPENGL3_GLFW ON"
        "POLYSCOPE_BACKEND_OPENGL3_EGL OFF"
        "POLYSCOPE_BACKEND_OPENGL_MOCK OFF"
        "BUILD_TESTING OFF"
)

CPMAddPackage(
    NAME portable-file-dialogs
    GITHUB_REPOSITORY samhocevar/portable-file-dialogs
    GIT_TAG 0.1.0
)

add_executable(rmagine_viewer src/rmagine_viewer.cpp)
target_link_libraries(rmagine_viewer
    polyscope
    rmagine::core
    rmagine::embree
    rmagine::ouster
    portable_file_dialogs)

print_link_info(rmagine_viewer)
