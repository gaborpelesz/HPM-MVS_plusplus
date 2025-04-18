cmake_minimum_required (VERSION 3.22)
project (HPM-MVS_plusplus)

set(CMAKE_CXX_STANDARD 20)

include(CheckLanguage)
check_language(CUDA)
if (CMAKE_CUDA_COMPILER)
    enable_language(CUDA)
    message(" -- CUDA FOUND")
    set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -O3 --use_fast_math --maxrregcount=128 --ptxas-options=-v --compiler-options -Wall")
    # set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -lineinfo") # for CUDA profiling!
else()
    message(FATAL_ERROR "-- CUDA NOT FOUND: Fatal error")
endif()

find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)

if(CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-pthread)
    add_definitions(-Wall)
    add_definitions(-Wextra)
    add_definitions(-pedantic)
    add_definitions(-Wno-unused-function)
    add_definitions(-Wno-switch)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3 -ffast-math -march=native") # extend release-profile with fast-math
endif()

find_package(OpenMP)
if (OPENMP_FOUND)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()


# For compilation ...
# Specify target & source files to compile it from
add_executable(
    HPM-MVS_plusplus
    main.h
    HPM.h
    HPM.cpp
    HPM.cu
    main.cpp
    )

target_link_libraries(HPM-MVS_plusplus
    ${OpenCV_LIBS}
    )

set_target_properties(HPM-MVS_plusplus
    PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
target_include_directories(HPM-MVS_plusplus
    PUBLIC
    ${OpenCV_INCLUDE_DIRS}
    ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}
)
