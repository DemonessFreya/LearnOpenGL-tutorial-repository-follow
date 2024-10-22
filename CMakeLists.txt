cmake_minimum_required(VERSION 3.22)
project(main)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)

# Set source files
set(SOURCES main.cpp glad.c)

# Define the executable
add_executable(main ${SOURCES})

# Add libraries
find_package(glfw3 REQUIRED)
find_package(assimp REQUIRED)

# Add glad.c to GLAD
add_library(GLAD "glad.c")

# Link libraries (including GLAD)
target_link_libraries(main PRIVATE glfw assimp GLAD)

# Define the output directory for the executable
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/dist)

# List of DLLs to copy (adjust paths as needed)
set(DLLS_TO_COPY
    "C:/msys64/mingw64/bin/libgcc_s_seh-1.dll"
    "C:/msys64/mingw64/bin/libstdc++-6.dll"
    "C:/msys64/mingw64/bin/libassimp-5.dll"
    "C:/msys64/mingw64/bin/glfw3.dll"
)

# Create a directory for the executable and copy the DLLs
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:main>/dlls"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${DLLS_TO_COPY}
        "$<TARGET_FILE_DIR:main>/dlls"
)

# Configure root_directory.h
configure_file(config/root_directory.h.in ../h/root_directory.h)
include_directories(${CMAKE_BINARY_DIR}/config)
