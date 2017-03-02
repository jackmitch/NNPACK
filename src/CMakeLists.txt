file(GLOB sources *.c)
file(GLOB sources_psimd psimd/*.c)
file(GLOB sources_neon neon/blas/*.c)

set(CLANGSPLIT ${CLANGCC})
separate_arguments(CLANGSPLIT)

add_custom_target(
        psimd_clang
	COMMAND ${CLANGSPLIT}
	-c
	-I ${CMAKE_SOURCE_DIR}/include
	-I ${CMAKE_SOURCE_DIR}/src
	${sources_psimd}
)

string(REPLACE "\.c" "\.o" objects_psimd ${sources_psimd})

add_library(nnpack ${sources} ${sources_neon})
add_dependencies(nnpack psimd_clang)
target_link_libraries(nnpack ${objects_psimd})

set_target_properties(nnpack PROPERTIES PUBLIC_HEADER "../include/nnpack.h")

install(TARGETS nnpack
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
