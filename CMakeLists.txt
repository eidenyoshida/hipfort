CMAKE_MINIMUM_REQUIRED(VERSION 3.0.2)
# Default compiler gfortran, otherwise cmake Fortran package will set it to f95
# You can override the compiler with cmake -DHIPFORT_COMPILER=
set(HIPFORT_COMPILER "/usr/bin/gfortran" CACHE STRING "Fortran Compiler to build HIPFORT")
set(HIPFORT_AR "/usr/bin/gcc-ar" CACHE STRING "Static archive command")
set(HIPFORT_RANLIB "/usr/bin/gcc-ranlib" CACHE STRING "ranlib used to create Static archive")
set(HIPFORT_COMPILER_FLAGS "-std=f2003 -ffree-form -cpp" CACHE STRING "Compiler flags to build HIPFORT")
set(HIPFORT_BUILD_TYPE "RELEASE"  CACHE STRING "Set to RELEASE TESTING or DEBUG")
set(HIPFORT_INSTALL_DIR  "/tmp/hipfort"  CACHE STRING "Install directory for hipfort")
message("-- HIPFORT -------------  cmake START -------------------")
message("-- HIPFORT_COMPILER:       ${HIPFORT_COMPILER}")
message("-- HIPFORT_AR:             ${HIPFORT_AR}")
message("-- HIPFORT_RANLIB:         ${HIPFORT_RANLIB}")
message("-- HIPFORT_COMPILER_FLAGS: ${HIPFORT_COMPILER_FLAGS}")
message("-- HIPFORT_BUILD_TYPE:     ${HIPFORT_BUILD_TYPE}")
message("-- HIPFORT_INSTALL_DIR:    ${HIPFORT_INSTALL_DIR}")
message("-- HIPFORT ----------------------------------------------")

set(CMAKE_Fortran_COMPILER_INIT ${HIPFORT_COMPILER})
PROJECT(hipfort Fortran C)

SET(CMAKE_BUILD_TYPE ${HIPFORT_BUILD_TYPE})
SET(VERSION 0.1-0)
SET(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules/")
SET(CMAKE_AR ${HIPFORT_AR})
SET(CMAKE_RANLIB ${HIPFORT_RANLIB})
SET(CMAKE_INSTALL_PREFIX ${HIPFORT_INSTALL_DIR})
message("-- CMAKE_INSTALL_PREFIX:${CMAKE_INSTALL_PREFIX}")

INCLUDE(FortranCInterface)
FortranCInterface_VERIFY()
IF(NOT FortranCInterface_VERIFIED_C)
	MESSAGE(FATAL_ERROR "Fortran compiler must support C Interface")
ENDIF(NOT FortranCInterface_VERIFIED_C)
	
IF(NOT CMAKE_Fortran_COMPILER_SUPPORTS_F90)
    MESSAGE(FATAL_ERROR "Fortran compiler does not support F90")
ENDIF(NOT CMAKE_Fortran_COMPILER_SUPPORTS_F90)

#OPTION(USE_MPI "Use the MPI library for parallelization" OFF)
#OPTION(USE_OPENMP "Use OpenMP for parallelization" OFF)

# Set compile flags for DEBUG, # RELEASE, or TESTING.  
INCLUDE(${CMAKE_MODULE_PATH}/SetFortranFlags.cmake) 
message("-- Done seFortranFlags")

# There is an error in CMAKE with this flag for pgf90.  Unset it
GET_FILENAME_COMPONENT(FCNAME ${CMAKE_Fortran_COMPILER} NAME)
IF(FCNAME STREQUAL "pgf90")
    UNSET(CMAKE_SHARED_LIBRARY_LINK_Fortran_FLAGS)
ENDIF(FCNAME STREQUAL "pgf90")

ADD_SUBDIRECTORY(${CMAKE_SOURCE_DIR}/lib)
ADD_SUBDIRECTORY(${CMAKE_SOURCE_DIR}/test)
ADD_SUBDIRECTORY(${CMAKE_SOURCE_DIR}/bin)

message("-- HIPFORT -------------  cmake DONE --------------------")
