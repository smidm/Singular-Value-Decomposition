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
    Singular_Value_Decomposition_Streaming_Test.cpp
    Singular_Value_Decomposition_Unit_Test.cpp
)

ADD_LIBRARY(svd STATIC ${SVD_SRCS})

add_definitions(-DUSE_SSE_IMPLEMENTATION)

find_package(Threads)
IF(NOT CMAKE_USE_PTHREADS_INIT)
    MESSAGE(FATAL_ERROR "Pthreads library not found.") # not tested
ENDIF(NOT CMAKE_USE_PTHREADS_INIT)
target_link_libraries (svd ${CMAKE_THREAD_LIBS_INIT})

IF(MSVC) 
  SET_TARGET_PROPERTIES(svd PROPERTIES COMPILE_FLAGS "/arch:SSE") # not tested
ELSE(MSVC)   
  SET_TARGET_PROPERTIES(svd PROPERTIES COMPILE_FLAGS "-march=native")
ENDIF(MSVC)




