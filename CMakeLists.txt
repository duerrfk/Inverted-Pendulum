cmake_minimum_required (VERSION 3.5)

# Set project name
project(inverted_pendulum)

# Required to plot states
add_definitions(-std=c++17)

# Add library
add_subdirectory(src)

# Add executable to plot states
find_package(sciplot)
if(sciplot_FOUND)
  add_executable(plot src/main.cpp)
  target_link_libraries(plot sciplot::sciplot source)
else()
  message("sciplot need to be installed to generate the plot executable")
endif()

# Add executable to run unit tests
find_package(GTest)
if(GTest_FOUND)
  add_executable(test tests/main.cpp tests/inverted_pendulum_test.cpp)
  target_link_libraries(test source ${GTEST_LIBRARIES} pthread)
else()
  message("gloogletest need to be installed to generate the test executable")
endif()

# Additional resources like images and fonts
configure_file(res/Roboto-Regular.ttf Roboto-Regular.ttf COPYONLY)

# Add executable to open game environment
find_package(SFML COMPONENTS graphics window system REQUIRED)
add_executable(cartpole game/main.cpp)
target_link_libraries(cartpole source sfml-graphics sfml-window sfml-system)

# Add executable of networked control system
find_package(Threads REQUIRED)
find_package(SFML COMPONENTS graphics window system REQUIRED)
add_executable(plant networked_control_system/plant.cpp networked_control_system/socket_utils.cpp networked_control_system/marshaling.cpp)
add_executable(controller_pid networked_control_system/controller_pid.cpp networked_control_system/socket_utils.cpp networked_control_system/marshaling.cpp)
target_link_libraries(plant source sfml-graphics sfml-window sfml-system Threads::Threads)
target_link_libraries(controller_pid source)

# Add executable to run examples
add_executable(example examples/lqr_design.cpp)
target_link_libraries(example source)
