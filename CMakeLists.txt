if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "" FORCE)
endif()

cmake_minimum_required(VERSION 3.25)
set(CMAKE_OSX_ARCHITECTURES arm64 x86_64)
set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT Embedded)
project(thirdparty-ceres C CXX)

set(BUILD_SHARED_LIBS OFF)
set(BUILD_TESTING OFF)
set(BUILD_EXAMPLES OFF)

set(NO_CBLAS ON)
set(C_LAPACK ON)
set(USE_LOCKING ON)
set(USE_THREAD OFF)
set(NOFORTRAN ON)

set(SUITESPARSE_ENABLE_PROJECTS cholmod)
set(SUITESPARSE_USE_CUDA OFF)
set(SUITESPARSE_USE_PYTHON OFF)
set(CHOLMOD_GPL OFF)
if(NOT APPLE)
    set(BLA_VENDOR OpenBLAS)
endif()
if(MSVC)
    set(SUITESPARSE_USE_FORTRAN OFF)
endif()

set(EIGEN_BUILD_CMAKE_PACKAGE ON)

set(WITH_GFLAGS OFF)
set(WITH_GTEST OFF)

set(BUILD_BENCHMARKS OFF)
set(MINIGLOG ON)
set(PROVIDE_UNINSTALL_TARGET OFF)
set(SUITESPARSE OFF)
# We don't use the SPARSE_SCHUR solver, so the generated code is just file size bloat
set(SCHUR_SPECIALIZATIONS OFF)
include(FetchContent)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/cmake)
if(NOT APPLE)
    fetchcontent_declare(
        BLAS
        GIT_REPOSITORY https://github.com/OpenMathLib/OpenBLAS.git
        GIT_TAG v0.3.29
        OVERRIDE_FIND_PACKAGE
    )
endif()

fetchcontent_declare(
    SuiteSparse
    GIT_REPOSITORY https://github.com/DrTimothyAldenDavis/SuiteSparse.git
    GIT_TAG v7.8.3
    OVERRIDE_FIND_PACKAGE
)

fetchcontent_declare(
    Eigen3
    GIT_REPOSITORY https://gitlab.com/libeigen/eigen.git
    GIT_TAG 0fb2ed140d4fc0108553ecfb25f2d7fc1a9319a1
    OVERRIDE_FIND_PACKAGE 
    PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/0003-Make-assignment-constexpr.patch
)

fetchcontent_declare(
    Ceres
    GIT_REPOSITORY https://github.com/ceres-solver/ceres-solver.git
    GIT_TAG 2.2.0
)
if(NOT APPLE)
    fetchcontent_makeavailable(BLAS)
endif()
fetchcontent_makeavailable(SuiteSparse Eigen3 Ceres)
