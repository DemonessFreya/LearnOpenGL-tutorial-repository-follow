cmake_minimum_required(VERSION 3.22)
project(main)

# Set source files
set(SOURCES main.cpp glad.c)

# Create executable
add_executable(main ${SOURCES})

# Find and include GLFW
find_package(glfw3 REQUIRED)
target_link_libraries(main PRIVATE glfw)

# Find and include Assimp
find_package(assimp REQUIRED)
include_directories(${ASSIMP_INCLUDE_DIRS})
target_link_libraries(main PRIVATE ${ASSIMP_LIBRARIES})

# Configure root_directory.h
configure_file(config/root_directory.h.in ../h/root_directory.h)
include_directories(${CMAKE_BINARY_DIR}/config)

# Add glad.c to GLAD
add_library(GLAD "glad.c")
target_link_libraries(main PRIVATE GLAD)

# Set the output directory for the executable
set_target_properties(main PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR})
