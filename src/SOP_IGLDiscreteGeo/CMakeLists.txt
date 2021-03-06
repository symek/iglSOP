# Includes headers based projects
include_directories(${CMAKE_SOURCE_DIR}/external/eigen)
include_directories(${CMAKE_SOURCE_DIR}/external/libigl/include)

# Add a library with two source files.
set( library_name SOP_IGLDiscreteGeometry )

add_library( ${library_name} SHARED
    ../converters.cpp
    ../converters.hpp
    discretegeometry.hpp
    discretegeometry.cpp
    SOP_IGLDiscreteGeo.cpp
    SOP_IGLDiscreteGeo.hpp
)

# Link against the Houdini libraries, and add required include directories and compile definitions.
target_link_libraries( ${library_name} Houdini )

# Configure several common target properties, such as its output directory.
houdini_configure_target( ${library_name} )