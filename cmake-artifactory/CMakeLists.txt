cmake_minimum_required(VERSION 3.10)
project(MyProject)

# Add source files
add_executable(my_project src/main.cpp src/my_lib.cpp)

# Include directories
target_include_directories(my_project PUBLIC include)

# Link libraries
# target_link_libraries(my_project PUBLIC my_lib)  # If you had a library