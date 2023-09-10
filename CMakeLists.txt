cmake_minimum_required(VERSION 3.22 FATAL_ERROR)
include(CMakeDependentOption)

if(POLICY CMP0054)
    cmake_policy(SET CMP0054 NEW)
endif()

if(POLICY CMP0069)
    cmake_policy(SET CMP0069 NEW)
endif()

if(POLICY CMP0077)
    cmake_policy(SET CMP0077 NEW)
endif()

set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)

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

# --------------------------------------------------------------------
# Apply Microsoft C runtime library option
# This is here because it also applies to tests and examples
# --------------------------------------------------------------------
if(MSVC AND NOT USE_MSVC_RUNTIME_LIBRARY_DLL)
    if(CMAKE_VERSION VERSION_LESS 3.15)
        foreach(flag CMAKE_C_FLAGS
            CMAKE_C_FLAGS_DEBUG
            CMAKE_C_FLAGS_RELEASE
            CMAKE_C_FLAGS_MINSIZEREL
            CMAKE_C_FLAGS_RELWITHDEBINFO)
            if(flag MATCHES "/MD")
                string(REGEX REPLACE "/MD" "/MT" ${flag} "${${flag}}")
            endif()

            if(flag MATCHES "/MDd")
                string(REGEX REPLACE "/MDd" "/MTd" ${flag} "${${flag}}")
            endif()
        endforeach()
    else()
        set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    endif()
endif()

add_subdirectory(src)