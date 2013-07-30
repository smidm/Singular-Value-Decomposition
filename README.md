# Computing the Singular Value Decomposition of 3x3 matrices with minimal branching and elementary floating point operations

A. McAdams, A. Selle, R. Tamstorf, J. Teran and E. Sifakis

## Abstract

A numerical method for the computation of the Singular Value Decomposition of 3 × 3 matrices is presented. 
The proposed methodology robustly handles rank-deficient matrices and guarantees orthonormality of the 
computed rotational factors. The algorithm is tailored to the characteristics of SIMD or vector processors. 
In particular, it does not require any explicit branching beyond simple conditional assignments 
(as in the C++ ternary operator ?:, or the SSE4.1 instruction VBLENDPS), enabling trivial data-level 
parallelism for any number of operations. Furthermore, no trigonometric or other expensive operations 
are required; the only floating point operations utilized are addition, multiplication, and an inexact 
(yet fast) reciprocal square root which is broadly available on current SIMD/vector architectures. 
The performance observed approaches the limit of making the 3 × 3 SVD a memory-bound (as opposed to 
CPU-bound) operation on current SMP platforms.

Technical report: http://pages.cs.wisc.edu/~sifakis/papers/SVD_TR1690.pdf

Original sources available at http://pages.cs.wisc.edu/~sifakis/project_pages/svd.html

# Compilation

CMake creates library target "svd" and a optionally set of benchmarks executables.

    $ cd <downloaded / cloned source directory>
    $ mkdir _build
    $ cd _build
    $ cmake ..
    $ make
    
By default SSE implementation is used. To select AVX or scalar implementation use:

    $ cmake -D IMPLEMENTATION=AVX ..
    
   or 
   
    $ cmake -D IMPLEMENTATION=SCALAR ..
    
To build benchmarks use:

    $ cmake -D BUILD_BENCHMARKS=TRUE ..
       
Tested on Ubuntu 12.10 only.

# Original README

A Makefile is provided. Due to the use of the AVX instruction set on some of
the included benchmarks, the Intel C++ Composer (icc) version 12.X is used as
the default and tested compiler. It is possible to use the g++ compiler instead
for those versions of the benchmarks that are limited to Scalar or SSE-only
implementations.

The executables built by this Makefile include:

## Singular_Value_Decomposition_Streaming_Test_XXX
  where XXX=Scalar,SSE,AVX

  *Description*: This benchmark initializes a large number of random matrices
  (16M by default, statically set in code) normalized to unit Frobenius norm.
  The Singular Value Decomposition is currently computed on this entire stream
  of 3x3 matrices, using a Scalar implementation, or SSE/AVX versions using
  explicit intrinsics.

  The number of threads to be run concurrently is supplied as the sole
  argument to these benchmarks.

## Singular_Value_Decomposition_Correctness_Test_XXX
  where XXX=Scalar,SSE,AVX

  *Description*: These benchmarks generate a random dataset, as the streaming
  tests above, but also report a number of accuracy metrics, such as the
  reconstruction error and the maximum off-diagonal magnitude of the resulting
  near-diagonal factor.

 
## Singular_Value_Decomposition_Unit_Test_XXX
  where XXX=SSE,AVX

  *Description*: This is a debugging trace of the algorithm, run on a single 3x3
  matrix. The scalar implementation is compared against the result of the
  SSE/AVX vectorized implementation. Intermediate results of the decomposition
  algorithm are reported, for both scalar and vector variants.

  The command line argument is an integer seed, used to initialize the random
  number generator used in creating the test matrix.
