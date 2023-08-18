cmake_minimum_required(VERSION 3.22 FATAL_ERROR)
include(CMakeDependentOption)

cmake_dependent_option(GLFW_BUILD_WIN32 "Build support for Win32" ON "WIN32" OFF)
cmake_dependent_option(GLFW_BUILD_COCOA "Build support for Cocoa" ON "APPLE" OFF)
cmake_dependent_option(GLFW_BUILD_X11 "Build support for X11" ON "UNIX;NOT APPLE" OFF)
cmake_dependent_option(GLFW_BUILD_WAYLAND "Build support for Wayland"
    "${GLFW_USE_WAYLAND}" "UNIX;NOT APPLE" OFF)

cmake_dependent_option(GLFW_USE_HYBRID_HPG "Force use of high-performance GPU on hybrid systems" OFF
    "WIN32" OFF)
cmake_dependent_option(USE_MSVC_RUNTIME_LIBRARY_DLL "Use MSVC runtime library DLL" ON
    "MSVC" OFF)

# --------------------------------------------------------------------
# Report backend selection
# --------------------------------------------------------------------
if(GLFW_BUILD_WIN32)
    message(STATUS "Including Win32 support")
endif()

if(GLFW_BUILD_COCOA)
    message(STATUS "Including Cocoa support")
endif()

if(GLFW_BUILD_WAYLAND)
    message(STATUS "Including Wayland support")
endif()

if(GLFW_BUILD_X11)
    message(STATUS "Including X11 support")
endif()

if(MSVC)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

add_subdirectory(src)