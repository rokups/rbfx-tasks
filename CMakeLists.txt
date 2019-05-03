#
# Copyright (c) 2017-2019 Rokas Kupstys.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
#

# Set CMake minimum version and CMake policy required by UrhoCommon module
cmake_minimum_required (VERSION 3.4.3)

# Set project name
project (rbfx-tasks)

# Dependencies
add_subdirectory(ThirdParty/sc)

# Library
add_library(rbfx-tasks STATIC Source/rbfx-tasks/Tasks.cpp Source/rbfx-tasks/Tasks.h)
target_link_libraries(rbfx-tasks PUBLIC sc)
target_include_directories(rbfx-tasks PUBLIC Source)

include(CheckIncludeFile)
check_include_file("valgrind/valgrind.h" HAVE_VALGRIND)
if (HAVE_VALGRIND)
    target_compile_definitions(rbfx-tasks PRIVATE -DHAVE_VALGRIND=${HAVE_VALGRIND})
endif ()

# Samples
if ("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")
    set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

    # Build samples and pull in engine dependency only when this library is built
    # independently. These are not necessary when user is consuming library in their
    # own project.
    if (NOT EXISTS "${CMAKE_CURRENT_BINARY_DIR}/rbfx.zip")
        file(DOWNLOAD "https://github.com/rokups/rbfx/archive/master.zip" ${CMAKE_CURRENT_BINARY_DIR}/rbfx.zip)
    endif ()
    if (NOT EXISTS "${CMAKE_CURRENT_BINARY_DIR}/rbfx-master")
        execute_process(COMMAND ${CMAKE_COMMAND} -E tar xzf ${CMAKE_CURRENT_BINARY_DIR}/rbfx.zip)
    endif ()
    set (URHO3D_URHO2D OFF CACHE BOOL "" FORCE)
    set (URHO3D_IK OFF CACHE BOOL "" FORCE)
    set (URHO3D_NAVIGATION OFF CACHE BOOL "" FORCE)
    set (URHO3D_NETWORK OFF CACHE BOOL "" FORCE)
    set (URHO3D_PHYSICS OFF CACHE BOOL "" FORCE)
    set (URHO3D_SAMPLES OFF CACHE BOOL "" FORCE)
    set (URHO3D_WEBP OFF CACHE BOOL "" FORCE)
    set (URHO3D_PROFILING OFF CACHE BOOL "" FORCE)
    set (URHO3D_CSHARP OFF CACHE BOOL "" FORCE)
    set (URHO3D_EXTRAS OFF CACHE BOOL "" FORCE)
    set (URHO3D_TOOLS OFF CACHE BOOL "" FORCE)
    set (URHO3D_PLAYER OFF CACHE BOOL "" FORCE)
    add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/rbfx-master)
    target_link_libraries(rbfx-tasks PUBLIC Urho3D)
    add_subdirectory(Samples/01_Tasks)
endif ()
