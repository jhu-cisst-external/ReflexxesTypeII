# (c) Anton Deguet, Johns Hopkins University

cmake_minimum_required (VERSION 2.8.3)
project (ReflexxesTypeII)

# RML original Makefiles had these options, check if they work
include (CheckCXXCompilerFlag)
check_cxx_compiler_flag ("-std=c++11" CXX_SUPPORTS_STDCPP11)
if (CXX_SUPPORTS_STDCPP11)
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
endif ()
check_cxx_compiler_flag ("-fno-unsafe-math-optimizations" CXX_SUPPORTS_NOUNSAFEMATHOPT)
if (CXX_SUPPORTS_NOUNSAFEMATHOPT)
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-unsafe-math-optimizations")
endif ()

# To allow mixing static and dynamic libraries
if (CMAKE_COMPILER_IS_GNUCXX AND NOT MINGW)
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif (CMAKE_COMPILER_IS_GNUCXX AND NOT MINGW)

# list of source files
file (GLOB ReflexxesTypeII_SOURCES
      RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}"
      "${CMAKE_CURRENT_SOURCE_DIR}/src/TypeIIRML/*.cpp")

# list of header files
file (GLOB ReflexxesTypeII_HEADERS
      RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}"
      "${CMAKE_CURRENT_SOURCE_DIR}/include/*.h")

# to find ReflexxesTypeII headers
include_directories (include)

# library itself
add_library (ReflexxesTypeII SHARED
             ${ReflexxesTypeII_SOURCES}
             ${ReflexxesTypeII_HEADERS})

# install 
install (TARGETS ReflexxesTypeII
         LIBRARY DESTINATION lib)

install (FILES ${ReflexxesTypeII_HEADERS}
         DESTINATION "include/ReflexxesTypeII") 


# compile examples, or not...
option (ReflexxesTypeII_COMPILE_EXAMPLES "Compile examples for ReflexxesTypeII" ON)

if (ReflexxesTypeII_COMPILE_EXAMPLES)
  set (TYPEIIRML_Position_EXAMPLES
       01_RMLPositionSampleApplication
       02_RMLPositionSampleApplication
       03_RMLPositionSampleApplication
       07_RMLPositionSampleApplication)

  set (TYPEIIRML_Velocity_EXAMPLES
       04_RMLVelocitySampleApplication
       05_RMLVelocitySampleApplication
       06_RMLVelocitySampleApplication
       08_RMLVelocitySampleApplication)

  set (TYPEIIRML_EXAMPLE_TYPES Position Velocity)

  foreach (type ${TYPEIIRML_EXAMPLE_TYPES})
    foreach (example ${TYPEIIRML_${type}_EXAMPLES})
      add_executable (${example}
                      "src/RML${type}SampleApplications/${example}.cpp")
      target_link_libraries (${example} ReflexxesTypeII)
    endforeach ()
  endforeach ()

endif ()
