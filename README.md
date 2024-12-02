# External Ceres repo
This is the repo that builds ceres and its dependents for for use in wpical. It uses vcpkg to build all the dependencies and Gradle to package it for use in allwpilib.

## Important notes
### Overlay ports
There are two overlay ports for blas and lapack. By default on x86-64 Windows, vcpkg will use lapack-reference and cblas, which is an issue because it requires shared libraries (the list is libblas.dll, libgcc_s_seh-1.dll, libgfortran-5.dll, liblapack.dll, libquadmath-0.dll, libwinpthread-1.dll) for the gfortran dependency, which we can't use for a static build.

To solve this, overlay ports were used to override the blas port to use openblas and the lapack port to use clapack, avoiding the gfortran dependency and allowing a completely static build. 

### Triplets
The vcpkg triplets used for Windows end in `-static-md`. This enables static library building, but dynamic linkage to the CRT, which [matches the way WPILib builds](https://github.com/wpilibsuite/native-utils/blob/main/src/main/java/edu/wpi/first/nativeutils/WPINativeUtilsExtension.java#L45).