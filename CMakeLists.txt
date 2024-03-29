cmake_minimum_required( VERSION 3.12 )

message( STATUS "Parallel Programming Course" )

include(cmake/configure.cmake)
include(cmake/gtest.cmake)

################################ MPI ################################
option(USE_MPI OFF)
if( USE_MPI )
    include(cmake/boost.cmake)
    find_package( MPI )
    if( MPI_FOUND )
        include_directories( ${MPI_INCLUDE_PATH} )
    else( MPI_FOUND )
        set( USE_MPI OFF )
    endif( MPI_FOUND )
endif( USE_MPI )

############################### OpenMP ##############################
option(USE_OMP OFF)
if( USE_OMP OR USE_SEQ )
    find_package( OpenMP )
    if( OpenMP_FOUND )
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
    else( OpenMP_FOUND )
        message(FATAL_ERROR "OpenMP NOT FOUND")
    endif( OpenMP_FOUND )
endif( USE_OMP OR USE_SEQ )

############################ std::thread ############################
option(USE_STD OFF)
if( USE_STD )
    find_package( Threads )
endif( USE_STD )

################################ TBB ################################
option(USE_TBB OFF)
if( USE_TBB )
    include(cmake/onetbb.cmake)
endif( USE_TBB )

############################## CPPLINT ##############################
find_package(Python3 REQUIRED)
enable_testing()
if( NOT (APPLE OR WIN32) )
    include(cmake/cpplint.cmake)
    cpplint_add_subdirectory(modules)
endif( NOT (APPLE OR WIN32) )

############################## Modules ##############################
include_directories(3rdparty/unapproved)
add_subdirectory(modules)
