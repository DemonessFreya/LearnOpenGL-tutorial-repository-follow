cmake_minimum_required(VERSION 3.22)
project(main)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)

# Set source files
set(SOURCES src/main.cpp src/glad.c)

# Define include directories
include_directories(include)

# Define the output directory for the executable
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Define the executable
add_executable(main ${SOURCES})

# Find and link libraries
find_package(glfw3 REQUIRED)
find_package(assimp REQUIRED)

# Link libraries to the executable
target_link_libraries(main PRIVATE glfw assimp)

# Set the output directory for the libraries
set(LIB_OUTPUT_DIR "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/lib")

# Set RPATH for the executable
set_target_properties(main PROPERTIES
    BUILD_WITH_INSTALL_RPATH TRUE
    INSTALL_RPATH "${LIB_OUTPUT_DIR}"
    INSTALL_RPATH_USE_LINK_PATH TRUE
)

# List of DLLs to copy (adjust paths as needed)
set(DLLS_TO_COPY
    "C:/msys64/mingw64/bin/libgcc_s_seh-1.dll"
    "C:/msys64/mingw64/bin/libstdc++-6.dll"
    "C:/msys64/mingw64/bin/libassimp-5.dll"
    "C:/msys64/mingw64/bin/glfw3.dll"
    "C:/msys64/mingw64/bin/libwinpthread-1.dll"
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

# Create a directory for the executable and copy the DLLs to the bin/ directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:main>/lib"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${DLLS_TO_COPY}
        "$<TARGET_FILE_DIR:main>/lib"
)

# Copy shader files to the bin/ directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:main>/shaders"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${SHADERS_TO_COPY}
        "$<TARGET_FILE_DIR:main>/shaders/"
)

# Copy the resources directory to the bin/ directory
add_custom_command(TARGET main POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${RESOURCE_DIR_TO_COPY}
        "$<TARGET_FILE_DIR:main>/resources"
)

# Configure root_directory.h
configure_file(config/root_directory.h.in ${CMAKE_BINARY_DIR}/include/h/root_directory.h)
configure_file(config/root_directory.h.in ${CMAKE_BINARY_DIR}/../include/h/root_directory.h)
include_directories(${CMAKE_BINARY_DIR}/include)
