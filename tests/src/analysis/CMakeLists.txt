# Standard includes and utils to compile into all tests.
set (util_SRCS)


#####################################################
# Don't forget to include output directory, otherwise
# the UI file won't be wrapped!
include_directories(${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}
  ${CMAKE_SOURCE_DIR}/src/core
  ${CMAKE_SOURCE_DIR}/src/core/auth
  ${CMAKE_SOURCE_DIR}/src/core/expression
  ${CMAKE_SOURCE_DIR}/src/core/dxf
  ${CMAKE_SOURCE_DIR}/src/core/geometry
  ${CMAKE_SOURCE_DIR}/src/core/labeling
  ${CMAKE_SOURCE_DIR}/src/core/layout
  ${CMAKE_SOURCE_DIR}/src/core/mesh
  ${CMAKE_SOURCE_DIR}/src/core/metadata
  ${CMAKE_SOURCE_DIR}/src/core/numericformats
  ${CMAKE_SOURCE_DIR}/src/core/processing
  ${CMAKE_SOURCE_DIR}/src/core/processing/models
  ${CMAKE_SOURCE_DIR}/src/core/providers/meshmemory
  ${CMAKE_SOURCE_DIR}/src/core/raster
  ${CMAKE_SOURCE_DIR}/src/core/symbology
  ${CMAKE_SOURCE_DIR}/src/core/effects
  ${CMAKE_SOURCE_DIR}/src/core/layertree
  ${CMAKE_SOURCE_DIR}/src/core/textrenderer
  ${CMAKE_SOURCE_DIR}/src/analysis
  ${CMAKE_SOURCE_DIR}/src/analysis/interpolation
  ${CMAKE_SOURCE_DIR}/src/analysis/network
  ${CMAKE_SOURCE_DIR}/src/analysis/processing
  ${CMAKE_SOURCE_DIR}/src/analysis/vector
  ${CMAKE_SOURCE_DIR}/src/analysis/raster
  ${CMAKE_SOURCE_DIR}/src/analysis/mesh
  ${CMAKE_SOURCE_DIR}/src/test
  ${CMAKE_SOURCE_DIR}/external
  ${CMAKE_SOURCE_DIR}/external/nlohmann

  ${CMAKE_BINARY_DIR}/src/core
  ${CMAKE_BINARY_DIR}/src/analysis
)
include_directories(SYSTEM
  ${QT_INCLUDE_DIR}
  ${GDAL_INCLUDE_DIR}
  ${GEOS_INCLUDE_DIR}
  ${SQLITE3_INCLUDE_DIR}
)

if(HAVE_OPENCL)
    include_directories(SYSTEM ${OpenCL_INCLUDE_DIRS})
endif()

#note for tests we should not include the moc of our
#qtests in the executable file list as the moc is
#directly included in the sources
#and should not be compiled twice. Trying to include
#them in will cause an error at build time

#No relinking and full RPATH for the install tree
#See: http://www.cmake.org/Wiki/CMake_RPATH_handling#No_relinking_and_full_RPATH_for_the_install_tree

macro (ADD_QGIS_TEST TESTSRC)
  set (TESTNAME  ${TESTSRC})
  string(REPLACE "test" "" TESTNAME ${TESTNAME})
  string(REPLACE "qgs" "" TESTNAME ${TESTNAME})
  string(REPLACE ".cpp" "" TESTNAME ${TESTNAME})
  set (TESTNAME  "qgis_${TESTNAME}test")

  set(${TESTNAME}_SRCS ${TESTSRC} ${util_SRCS})
  set(${TESTNAME}_MOC_CPPS ${TESTSRC})
  add_executable(${TESTNAME} ${${TESTNAME}_SRCS})
  target_link_libraries(${TESTNAME}
    ${Qt5Core_LIBRARIES}
    ${Qt5Test_LIBRARIES}
    qgis_analysis)
  add_test(${TESTNAME} ${CMAKE_BINARY_DIR}/output/bin/${TESTNAME} -maxwarnings 10000)
  #set_target_properties(qgis_${testname} PROPERTIES
  #  INSTALL_RPATH ${CMAKE_INSTALL_PREFIX}/${QGIS_LIB_DIR}
  #  INSTALL_RPATH_USE_LINK_PATH true )
endmacro (ADD_QGIS_TEST)

#############################################################
# Tests:
set(TESTS
 testqgsgeometrysnapper.cpp
 testqgsinterpolator.cpp
 testqgsprocessing.cpp
 testqgsprocessingalgs.cpp
 testqgszonalstatistics.cpp
 testqgsrastercalculator.cpp
 testqgsreclassifyutils.cpp
 testqgsalignraster.cpp
 testqgsnetworkanalysis.cpp
 testqgsninecellfilters.cpp
 testqgsmeshcalculator.cpp
 testqgsmeshcontours.cpp
 testqgstriangulation.cpp
)

foreach(TESTSRC ${TESTS})
    ADD_QGIS_TEST(${TESTSRC})
endforeach(TESTSRC)
