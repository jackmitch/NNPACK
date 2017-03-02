PROJECT(NNPACK
	LANGUAGES C
	VERSION 1
)

file(GLOB sources *.c)
file(GLOB sources_psimd psimd/*.c)
file(GLOB sources_neon neon/blas/*.c)

message(${sources_psimd})

list(APPEND sources_psimd
	${CMAKE_SOURCE_DIR}/src/psimd/blas/shdotxf.c
)

set(CLANGSPLIT ${CLANGCC})
separate_arguments(CLANGSPLIT)

foreach(SRC_FILE_FULL ${sources_psimd})
	get_filename_component(SRC_FILE ${SRC_FILE_FULL} NAME)
	string(REPLACE "\.c" "\.o" SRC_OBJECT ${SRC_FILE})
	list(APPEND CLANG_OBJECTS "${CMAKE_CURRENT_BINARY_DIR}/${SRC_OBJECT}")
endforeach(SRC_FILE_FULL ${sources_psimd})

SET_SOURCE_FILES_PROPERTIES(
	${CLANG_OBJECTS}
	PROPERTIES
	EXTERNAL_OBJECT true
	GENERATED true
)

add_custom_target(
	clang_psimd
	COMMAND ${CLANGSPLIT}
	-fPIC
	-c
	-I ${CMAKE_SOURCE_DIR}/include
	-I ${CMAKE_SOURCE_DIR}/src
	${sources_psimd}
)

add_library(nnpack ${sources} ${sources_neon} ${CLANG_OBJECTS})
add_dependencies(nnpack clang_psimd)
target_link_libraries(nnpack -lpthreadpool)

target_compile_options(nnpack PUBLIC -fPIC)
set_target_properties(nnpack PROPERTIES PUBLIC_HEADER "../include/nnpack.h")

include(GNUInstallDirs)

install(TARGETS nnpack
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
