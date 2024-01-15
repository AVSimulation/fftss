cmake_minimum_required(VERSION 3.21)

project(fftss VERSION 3.0)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

SET(HEADERS include/fftss.h
            include/fftw3compat.h)
            
SET(HEADERS_NO_INSTALL  include/libfftss.h
                        # include/wrap440d.h
                        # include/fftss.f
                        )

option(USE_SSE2 "Use of SSE2 accelerated" ON)
option(USE_SSE3 "Use of SSE3 accelerated" ON)


if(USE_SSE2)
	SET(SSE2_SOURCES 
            libfftss/r4_sse2_1_n.c
            libfftss/r4_sse2_o.c
            libfftss/r4_sse2_u1.c
            libfftss/r4_sse2_u4.c
            libfftss/r8_sse2_n.c
            libfftss/r8_sse2_o.c
            libfftss/r8_sse2_u1.c
	)
endif(USE_SSE2)

if(USE_SSE3)
	SET(SSE3_SOURCES 
            libfftss/r4_sse3_h_n.c
            libfftss/r4_sse3_h_o.c
            libfftss/r4_sse3_h_u1.c
            libfftss/r4_sse3_n.c
            libfftss/r4_sse3_o.c
            libfftss/r4_sse3_u1.c
            libfftss/r4_sse3_u4.c
	)
endif(USE_SSE2)


SET(SOURCES libfftss/fftss.c
            libfftss/fftss_2d.c
            libfftss/fftss_3d.c
            libfftss/fftss_copy.c
            libfftss/fftss_counter.c
            libfftss/fftss_cpuid.c
            libfftss/fftss_destroy_plan.c
            libfftss/fftss_execute.c
            libfftss/fftss_execute_dft.c
            libfftss/fftss_execute_dft_1d.c
            libfftss/fftss_execute_inplace_dft_1d.c
            libfftss/fftss_fma.c
            # libfftss/fftss_fortran.c
            libfftss/fftss_get_wtime.c
            libfftss/fftss_kset.c
            libfftss/fftss_malloc.c
            libfftss/fftss_set.c
            libfftss/fftss_table.c
            libfftss/fftss_test.c
            # libfftss/fftss_threads.c
            libfftss/fftss_version.c
            # libfftss/r4_bgl_n.c
            # libfftss/r4_bgl_o.c
            # libfftss/r4_bgl_pl_n.c
            # libfftss/r4_bgl_u1.c
            # libfftss/r4_cmplx_n.c
            libfftss/r4_fma_n.c
            libfftss/r4_fma_o.c
            libfftss/r4_fma_u1.c
            libfftss/r4_fma_u4.c
            # libfftss/r4_ia64_o.c
            libfftss/r4_n.c
            libfftss/r4_o.c
            libfftss/r4_u1.c
            libfftss/r4_u4.c
            # libfftss/r8_bgl_n.c
            # libfftss/r8_bgl_o.c
            # libfftss/r8_bgl_u1.c
            libfftss/r8_fma_n.c
            libfftss/r8_fma_o.c
            libfftss/r8_fma_u1.c
            # libfftss/r8_ia64_n.c
            libfftss/r8_n.c
            libfftss/r8_o.c
            libfftss/r8_u1.c)

# SET(BASIC_KERNELS   libfftss/r4_n.c
                    # libfftss/r4_o.c
                    # libfftss/r4_u1.c
                    # libfftss/r4_u4.c
                    # libfftss/r4_fma_n.c
                    # libfftss/r4_fma_o.c
                    # libfftss/r4_fma_u1.c
                    # libfftss/r4_fma_u4.c
                    # libfftss/r8_n.c
                    # libfftss/r8_o.c
                    # libfftss/r8_u1.c
                    # libfftss/r8_fma_n.c
                    # libfftss/r8_fma_o.c
                    # libfftss/r8_fma_u1.c )

# SET(BASE    libfftss/fftss.c
            # libfftss/fftss_test.c
            # libfftss/fftss_fma.c
            # libfftss/fftss_malloc.c
            # libfftss/fftss_table.c
            # libfftss/fftss_kset.c
            # libfftss/fftss_set.c
            # libfftss/fftss_execute.c
            # libfftss/fftss_execute_dft.c
            # libfftss/fftss_destroy_plan.c
            # libfftss/fftss_version.c
            # libfftss/fftss_get_wtime.c
            # libfftss/fftss_counter.c
            # libfftss/fftss_cpuid.c
            # libfftss/fftss_execute_dft_1d.c
            # libfftss/fftss_execute_inplace_dft_1d.c
            # libfftss/fftss_2d.c
            # libfftss/fftss_3d.c
            # libfftss/fftss_copy.c
            # libfftss/fftss_threads.c
            # libfftss/fftss_fortran.c)

add_library(fftss
            ${HEADERS}
            ${SOURCES}
	    ${SSE2_SOURCES}
	    ${SSE3_SOURCES}
            # ${BASIC_KERNELS}
            # ${BASE}
            )

if(USE_SSE2)
  target_compile_definitions(fftss PRIVATE USE_SSE2)
endif(USE_SSE2)

if(USE_SSE3)
  target_compile_definitions(fftss PRIVATE USE_SSE3)
endif(USE_SSE3)

target_include_directories(fftss PRIVATE include)

install(TARGETS fftss
        EXPORT fftss-export
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION include)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/cmake/fftssConfig.cmake DESTINATION "share/fftss")

INSTALL (
    FILES ${HEADERS}
    DESTINATION include/fftss
    CONFIGURATIONS Release
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(${CMAKE_BINARY_DIR}/cmake/fftssConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)
install(FILES ${CMAKE_BINARY_DIR}/cmake/fftssConfigVersion.cmake
    DESTINATION "share/fftss")

install(EXPORT fftss-export
  FILE
  	fftssTargets.cmake
  NAMESPACE
  	fftss::
  DESTINATION
    share/fftss
)
