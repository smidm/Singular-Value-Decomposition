CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
PROJECT(SVD)

SET(SVD_SRCS
    POINTER_QUEUE.h
    PTHREAD_QUEUE.cpp
    PTHREAD_QUEUE.h
    Singular_Value_Decomposition_Givens_QR_Factorization_Kernel.hpp
    Singular_Value_Decomposition_Helper.cpp
    Singular_Value_Decomposition_Helper.h
    Singular_Value_Decomposition_Jacobi_Conjugation_Kernel.hpp
    Singular_Value_Decomposition_Kernel_Declarations.hpp
    Singular_Value_Decomposition_Main_Kernel_Body.hpp
    Singular_Value_Decomposition_Preamble.hpp
)

# IMPLEMENTATION exclusive options
# credits http://clauandr.sdf-eu.org/blog/2012/04/06/cmake-select-among-exclusive-multiple-options/
SET(IMPLEMENTATION "SSE" CACHE STRING "Choose routines implementation.")
SET(IMPLEMENTATION_VALUES "SSE" "AVX" "SCALAR")
SET_PROPERTY(CACHE IMPLEMENTATION PROPERTY STRINGS ${IMPLEMENTATION_VALUES})
LIST(FIND IMPLEMENTATION_VALUES ${IMPLEMENTATION} IMPLEMENTATION_INDEX)
IF(${IMPLEMENTATION_INDEX} EQUAL -1)
    MESSAGE(FATAL_ERROR "Option ${IMPLEMENTATION} not supported, valid entries are ${IMPLEMENTATION_VALUES}")
ENDIF()

IF(${IMPLEMENTATION} STREQUAL "SSE")
    MESSAGE(STATUS "SSE implmentation selected.")
    ADD_DEFINITIONS(-DUSE_SSE_IMPLEMENTATION)
ENDIF()

IF(${IMPLEMENTATION} STREQUAL "AVX")
    MESSAGE(STATUS "AVX implmentation selected.")
    ADD_DEFINITIONS(-DUSE_AVX_IMPLEMENTATION)
ENDIF()

IF(${IMPLEMENTATION} STREQUAL "SCALAR")
    MESSAGE(STATUS "Scalar implmentation selected.")
    ADD_DEFINITIONS(-DUSE_SCALAR_IMPLEMENTATION)
ENDIF()

ADD_LIBRARY(svd STATIC ${SVD_SRCS})

ADD_EXECUTABLE(Singular_Value_Decomposition_Streaming_Test Singular_Value_Decomposition_Streaming_Test.cpp)
TARGET_LINK_LIBRARIES(Singular_Value_Decomposition_Streaming_Test svd)

IF((${IMPLEMENTATION} STREQUAL "SSE") OR (${IMPLEMENTATION} STREQUAL "AVX"))
    ADD_DEFINITIONS(-DPERFORM_CORRECTNESS_TEST)
    ADD_EXECUTABLE(Singular_Value_Decomposition_Correctness_Test Singular_Value_Decomposition_Streaming_Test.cpp)
    TARGET_LINK_LIBRARIES(Singular_Value_Decomposition_Correctness_Test svd)    
    ADD_EXECUTABLE(Singular_Value_Decomposition_Unit_Test Singular_Value_Decomposition_Unit_Test.cpp)
    TARGET_LINK_LIBRARIES(Singular_Value_Decomposition_Unit_Test svd)
ENDIF()

FIND_PACKAGE(Threads)
IF(NOT CMAKE_USE_PTHREADS_INIT)
    MESSAGE(FATAL_ERROR "Pthreads library not found.") # not tested
ENDIF(NOT CMAKE_USE_PTHREADS_INIT)
TARGET_LINK_LIBRARIES(svd ${CMAKE_THREAD_LIBS_INIT})

IF((${IMPLEMENTATION} STREQUAL "SSE") OR (${IMPLEMENTATION} STREQUAL "SCALAR"))
    IF(MSVC)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:SSE") # not tested
    ENDIF(MSVC)
    IF(CMAKE_COMPILER_IS_GNUCC)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native") 
    ENDIF()
ENDIF()

IF(${IMPLEMENTATION} STREQUAL "AVX")
    IF(MSVC)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:AVX") # not tested
    ENDIF()
    IF(CMAKE_COMPILER_IS_GNUCC)
        MESSAGE(FATAL_ERROR "AVX not supported with gcc. Try Intel compiler.")
    ENDIF()
ENDIF()

