cmake_minimum_required(VERSION 3.7)

if (DEFINED NNP_ARCH)
	if (NNP_ARCH STREQUAL "NNP_ARCH_ARM" OR NNP_ARCH STREQUAL "NNP_ARCH_ARM64")
		add_definitions(-D${NNP_ARCH})
		message(STATUS "NNP_ARCH set to ${NNP_ARCH}")
	else()
		unset(${NNP_ARCH})
	endif()
endif()

if (NOT DEFINED NNP_ARCH)
	message(FATAL_ERROR "NNP_ARCH must be set to either NNP_ARCH_ARM or NNP_ARCH_ARM64")
endif()

if (NOT DEFINED CLANGCC)
	message(FATAL_ERROR "CLANGCC must be set to the clang binary")
endif()

include_directories(include)
include_directories(${CMAKE_SOURCE_DIR}/src)
add_subdirectory(src)

install(TARGETS nnpack
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
