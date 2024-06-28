################################################################################
## Basic Setup
cmake_minimum_required (VERSION 3.1)
project(ParallelMatrixMarket)
set(CMAKE_VERBOSE_MAKEFILE ON)

set(src
    Source/pmm_constants.c
    Source/pmm_data.c Source/pmm_data.h
    Source/pmm_header.h
    Source/pmm_read_routines.h
    Source/pmm_process_data.c
    Source/pmm_process_header.c
    Source/pmm_write_routines.c 
    Source/pmm_write_routines.h
)

add_library(ParallelMatrixMarket SHARED ${src})
target_include_directories(ParallelMatrixMarket PUBLIC ${CMAKE_SOURCE_DIR}/Source)

#### MPI
find_package(MPI REQUIRED)
if (MPI_FOUND)
  message(STATUS "Found MPI")
  message(STATUS "Run: ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} ${MPIEXEC_MAX_NUMPROCS} ${MPIEXEC_PREFLAGS} EXECUTABLE ${MPIEXEC_POSTFLAGS} ARGS")
  target_include_directories(${PROJECT_NAME} PUBLIC ${MPI_INCLUDE_PATH})
  target_link_libraries(${PROJECT_NAME} PUBLIC MPI::MPI_CXX)
endif()

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE RELEASE)
endif()

################################################################################
find_package(PythonInterp)
if (PYTHONINTERP_FOUND)
  message("testing enabled")
  include(CTest)
  enable_testing()
  file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/scratch)
else()
  message("No testing")
endif (PYTHONINTERP_FOUND)

################################################################################
## Output Locations
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

################################################################################
## Documentation
# find_package(Doxygen REQUIRED)
# configure_file(${CMAKE_SOURCE_DIR}/Doxyfile.in
#                ${CMAKE_BINARY_DIR}/Doxyfile @ONLY)
# add_custom_target(doc ${DOXYGEN_EXECUTABLE} ${CMAKE_BINARY_DIR}/Doxyfile)

################################################################################
## Get at the source files
subdirs(Source)
subdirs(UnitTests)

