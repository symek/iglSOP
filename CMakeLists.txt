# Specify the minimum required version of CMake to build the project.
cmake_minimum_required( VERSION 3.4 )
project( edgyeggs )

option(WITH_IGL     "Build with LIBIGL SOPs" OFF)
option(WITH_SHAPEOP "Build ShapeOp SOP" OFF)
option(WITH_PCALIGN "Build PCAlign SOP" OFF)
option(WITH_ELTOPO  "Build Eltopo SOP" ON)

list( APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules")
list( APPEND CMAKE_PREFIX_PATH "$ENV{HT}/cmake")

find_package( Houdini REQUIRED )
message (STATUS "Houdini HDK: ${Houdini_VERSION}")

if (NOT CMAKE_BUILD_TYPE)
    set (CMAKE_BUILD_TYPE Debug
         CACHE STRING "Choose the type of build, options are: ${CMAKE_CONFIGURATION_TYPES}" FORCE)
    set_property (CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS ${CMAKE_CONFIGURATION_TYPES})
endif ()

set (CMAKE_BUILD_TYPE DEBUG)
if (CMAKE_BUILD_TYPE MATCHES DEBUG)
    message("Buiding in debug mode")
endif (CMAKE_BUILD_TYPE MATCHES DEBUG)


# PCL time
if (WITH_PCALIGN)
    find_package(PCL 1.3 REQUIRED COMPONENTS common features)
    if (PCL_FOUND)
        message(STATUS "PCL found. Building PCAlign SOP with Intel Fast Global Registration.")
        add_definitions(-DBUILD_WITH_INTEL_FGR)
        set(BUILD_WITH_INTEL_FGR TRUE)
    endif()
endif()


# list( APPEND CMAKE_PREFIX_PATH "${CMAKE_SOURCE_DIR}/install/cpd/lib/cmake/cpd")
# 	list( APPEND CMAKE_PREFIX_PATH "${CMAKE_SOURCE_DIR}/install/fgt/lib/cmake/fgt")

# 	find_package(Fgt REQUIRED)
# 	find_package(Cpd REQUIRED)
# Fgt
# include(ExternalProject)
# ExternalProject_Add(Fgt
#   SOURCE_DIR        "${CMAKE_SOURCE_DIR}/external/fgt"
#   BINARY_DIR        "${CMAKE_BINARY_DIR}/external/fgt/build"
#   INSTALL_DIR       "${CMAKE_BINARY_DIR}/external/install"
#   CMAKE_ARGS        "-DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/external/install"
#   BUILD_COMMAND     $(MAKE)
#   INSTALL_COMMAND   $(MAKE) install
#   TEST_COMMAND      ""
# )

# # Cpd
# ExternalProject_Add(Cpd
#   SOURCE_DIR        "${CMAKE_SOURCE_DIR}/external/cpd"
#   BINARY_DIR        "${CMAKE_BINARY_DIR}/external/cpd/build"
#   INSTALL_DIR       "${CMAKE_BINARY_DIR}/external/install"
#   CMAKE_ARGS        "-DCMAKE_PREFIX_PATH=${CMAKE_BINARY_DIR}/external/install -DWITH_FGT=ON  -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/external/install"
#   BUILD_COMMAND     $(MAKE)
#   INSTALL_COMMAND   $(MAKE) install
#   TEST_COMMAND      ""
# )

set (edgyeggs_version_major       0)
set (edgyeggs_version_minor       2)
set (edgyeggs_version_patch       0)

if(WITH_IGL)
    message(STATUS "Building with LIBIGL nodes")
    add_subdirectory (src/SOP_IGLDeform)
    add_subdirectory (src/SOP_IGLDiscreteGeo)
    add_subdirectory (src/SOP_IGLUVproject)
endif()

if(WITH_SHAPEOP)
    message(STATUS "Building SOP_ShapeOp")
    add_subdirectory (src/SOP_ShapeOp)
endif()

message(STATUS "Building SOP_PCAlign")
add_subdirectory (src/SOP_PCAlign)

if(WITH_ELTOPO)
    message(STATUS "Building SOP_Eltopo")
    add_subdirectory (src/SOP_Eltopo)
endif()
# add_subdirectory (src/SOP_GUItest)

