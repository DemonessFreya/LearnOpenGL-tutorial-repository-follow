cmake_minimum_required(VERSION 3.22)
project(main)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)

# Set source files
set(SOURCES src/main.cpp src/glad.c)

# Define include directories
include_directories(include)


# Define directories for outputs
set(BIN_DIR ${CMAKE_BINARY_DIR}/bin)
set(LIBEXEC_DIR ${BIN_DIR}/libexec)

# Define the executable
add_executable(main ${SOURCES})

# Set output directory for the main executable only
set_target_properties(main PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${LIBEXEC_DIR})

# Find and link libraries
find_package(glfw3 REQUIRED)
find_package(assimp REQUIRED)

# Link libraries to the executable
target_link_libraries(main PRIVATE glfw assimp)

# List of DLLs to copy (adjust paths as needed)
set(DLLS_TO_COPY
    "/usr/lib/libglfw.so.3"
    "/usr/lib/libassimp.so.5"
    "/usr/lib/libstdc++.so.6"
    "/usr/lib/libgcc_s.so.1"
    "/usr/lib/libz.so.1"
)

# List of shader files to copy
set(SHADERS_TO_COPY
    "${CMAKE_SOURCE_DIR}/shaders/shader.vert"
    "${CMAKE_SOURCE_DIR}/shaders/shader.frag"
)

# List of resource directories to copy
set(RESOURCE_DIR_TO_COPY
    "${CMAKE_SOURCE_DIR}/resources"
)

# Create a directory for libraries and copy the DLLs to the bin/lib directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "${BIN_DIR}/lib"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${DLLS_TO_COPY}
        "${BIN_DIR}/lib"
)

# Copy shader files to the bin/shaders directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "${BIN_DIR}/shaders"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${SHADERS_TO_COPY}
        "${BIN_DIR}/shaders/"
)

# Copy the resources directory to the bin/resources directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${RESOURCE_DIR_TO_COPY}
        "${BIN_DIR}/resources"
)

# Configure and copy the run script to bin
configure_file(src/run_main.sh.in ${BIN_DIR}/run_main.sh @ONLY)
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E chmod +x "${BIN_DIR}/run_main.sh"
)

# Configure root_directory.h
configure_file(config/root_directory.h.in ${CMAKE_BINARY_DIR}/include/h/root_directory.h)
configure_file(config/root_directory.h.in ${CMAKE_BINARY_DIR}/../include/h/root_directory.h)
include_directories(${CMAKE_BINARY_DIR}/include)
