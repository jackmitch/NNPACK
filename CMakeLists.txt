cmake_minimum_required(VERSION 3.7)

#add_definitions(-DNNP_ARCH_PSIMD)
add_definitions(-DNNP_ARCH_ARM)

include_directories(include)
include_directories(${CMAKE_SOURCE_DIR}/src)
add_subdirectory(src)
