add_library(GLFW STATIC "../include/GLFW/glfw3.h"
    "../include/GLFW/glfw3native.h"
    internal.h
    platform.h
    mappings.h
    context.c
    init.c
    input.c
    monitor.c
    platform.c
    vulkan.c
    window.c
    egl_context.c
    osmesa_context.c
    null_platform.h
    null_joystick.h
    null_init.c
    null_monitor.c
    null_window.c
    null_joystick.c)

# The time, thread and module code is shared between all backends on a given OS,
# including the null backend, which still needs those bits to be functional
if(APPLE)
    target_sources(GLFW PRIVATE cocoa_time.h cocoa_time.c posix_thread.h
        posix_module.c posix_thread.c)
elseif(WIN32)
    target_sources(GLFW PRIVATE win32_time.h win32_thread.h win32_module.c
        win32_time.c win32_thread.c)
else()
    target_sources(GLFW PRIVATE posix_time.h posix_thread.h posix_module.c
        posix_time.c posix_thread.c)
endif()

if(GLFW_BUILD_COCOA)
    target_compile_definitions(GLFW PRIVATE _GLFW_COCOA)
    target_sources(GLFW PRIVATE cocoa_platform.h cocoa_joystick.h cocoa_init.m
        cocoa_joystick.m cocoa_monitor.m cocoa_window.m
        nsgl_context.m)
endif()

if(GLFW_BUILD_WIN32)
    target_compile_definitions(GLFW PRIVATE _GLFW_WIN32)
    target_sources(GLFW PRIVATE win32_platform.h win32_joystick.h win32_init.c
        win32_joystick.c win32_monitor.c win32_window.c
        wgl_context.c)
endif()

if(GLFW_BUILD_X11)
    target_compile_definitions(GLFW PRIVATE _GLFW_X11)
    target_sources(GLFW PRIVATE x11_platform.h xkb_unicode.h x11_init.c
        x11_monitor.c x11_window.c xkb_unicode.c
        glx_context.c)
endif()

if(GLFW_BUILD_WAYLAND)
    target_compile_definitions(GLFW PRIVATE _GLFW_WAYLAND)
    target_sources(GLFW PRIVATE wl_platform.h xkb_unicode.h wl_init.c
        wl_monitor.c wl_window.c xkb_unicode.c)
endif()

if(GLFW_BUILD_X11 OR GLFW_BUILD_WAYLAND)
    if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
        target_sources(GLFW PRIVATE linux_joystick.h linux_joystick.c)
    endif()

    target_sources(GLFW PRIVATE posix_poll.h posix_poll.c)
endif()

set_target_properties(GLFW PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    DEFINE_SYMBOL _GLFW_BUILD_DLL)

target_include_directories(GLFW PUBLIC
    "$<BUILD_INTERFACE:${GLFW_SOURCE_DIR}/include>"
    "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>")
target_include_directories(GLFW PRIVATE
    "${GLFW_SOURCE_DIR}/src"
    "${GLFW_BINARY_DIR}/src")