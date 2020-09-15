# Atleast this version of CMake is needed
# to compile and run the project
cmake_minimum_required(VERSION 3.10.2)

# Set the C++ standard we will use
set(CMAKE_CXX_STANDARD 17)

# Add the path of the cmake files to the CMAKE_MODULE_PATH
# Allows to find the third part libraries (googletest and pugixml libraries)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

# Project name
project(OSM_A_star_search)

# Set library output path to /lib
set(LIBRARY_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/lib")

# Locate project prerequisites
# Uses the CMAKE_MODULE_PATH to find them
find_package(io2d REQUIRED)
find_package(Cairo)
find_package(GraphicsMagick)

# Set IO2D flags
set(IO2D_WITHOUT_SAMPLES 1)
set(IO2D_WITHOUT_TESTS 1)

# Add the pugixml and GoogleTest library subdirectories
add_subdirectory(thirdparty/pugixml)
add_subdirectory(thirdparty/googletest)

# Add project executable
# Mentioned cpp files are used to create the executable
add_executable(OSM_A_star_search src/main.cpp src/model.cpp src/render.cpp src/route_model.cpp src/route_planner.cpp)

target_link_libraries(OSM_A_star_search
    PRIVATE io2d::io2d
    PUBLIC pugixml
) # google test is not needed

# Add the testing executable
# main and render is not need for testing
add_executable(test_executable test/utest_rp_a_star_search.cpp src/route_planner.cpp src/model.cpp src/route_model.cpp)

target_link_libraries(test_executable
    gtest_main 
    pugixml
) # because rendering is not needed, io2d is not required

# Set options for Linux or Microsoft Visual C++
if( ${CMAKE_SYSTEM_NAME} MATCHES "Linux" )
    target_link_libraries(OSM_A_star_search PUBLIC pthread)
endif()

if(MSVC)
	target_compile_options(OSM_A_star_search PUBLIC /D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING /wd4459)
endif()
