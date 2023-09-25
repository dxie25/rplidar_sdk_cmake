# Get sdk sources

# Common sources
set(SDK_SRC
    src/sl_lidar_driver.cpp
    src/hal/thread.cpp
    src/sl_crc.cpp
    src/sl_serial_channel.cpp
    src/sl_tcp_channel.cpp
    src/sl_udp_channel.cpp
)

# System-specific sources
if(WIN32)
    # Target system is Windows
    list(APPEND SDK_SRC
        src/arch/win32/net_serial.cpp
        src/arch/win32/net_socket.cpp
        src/arch/win32/timer.cpp
    )
elseif(APPLE)
    # Target system is Apple, hopefully macOS
    add_compile_definitions("_MACOS")
    list(APPEND SDK_SRC
        src/arch/macOS/net_serial.cpp
        src/arch/macOS/net_socket.cpp
        src/arch/macOS/timer.cpp
    )
elseif(UNIX)
    # Target system is UNIX or UNIX-like, hopefully linux
    list(APPEND SDK_SRC
        src/arch/linux/net_serial.cpp
        src/arch/linux/net_socket.cpp
        src/arch/linux/timer.cpp
    )
else()
    message(FATAL_ERROR "${CMAKE_SYSTEM} is unsupported.")
endif()


add_library(rplidar_driver ${SDK_SRC})
target_include_directories(rplidar_driver
    PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include"
    PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src"
)


# Add threads because Linux wants pthread
set(THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package(Threads REQUIRED)
target_link_libraries(rplidar_driver PRIVATE Threads::Threads)
