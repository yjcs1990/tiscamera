
set(lib_aravis
  AravisDevice.cpp
  aravis_utils.cpp
  aravislibrary.cpp)

include_directories("${CMAKE_SOURCE_DIR}/src")

add_definitions(-DHAVE_ARAVIS)

find_package(GObject REQUIRED QUIET) # dependency
find_package(GLIB2 REQUIRED QUIET)   # dependency
find_package(GIO REQUIRED QUIET)   # dependency

if (TCAM_INTERNAL_ARAVIS)

  include_directories("${GIO_INCLUDE_DIR}")
  include_directories("${CMAKE_SOURCE_DIR}/dependencies/aravis/src")
  include_directories("${CMAKE_BINARY_DIR}/dependencies/aravis-prefix/src/aravis-build/src/")

else (TCAM_INTERNAL_ARAVIS)

  find_package(Aravis REQUIRED QUIET)
  include_directories(${aravis_INCLUDE_DIR})

endif(TCAM_INTERNAL_ARAVIS)

include_directories(${GOBJECT_INCLUDE_DIR})
include_directories(${GLIB2_INCLUDE_DIR})
include_directories("${CMAKE_SOURCE_DIR}/tools/gige-daemon")

add_library(tcam-aravis SHARED ${lib_aravis} $<TARGET_OBJECTS:tcam-base>)

TARGET_LINK_LIBRARIES(tcam-aravis ${GOBJECT_LIBRARIES})
TARGET_LINK_LIBRARIES(tcam-aravis ${GLIB2_LIBRARIES})

if (TCAM_INTERNAL_ARAVIS)
  add_dependencies(tcam-aravis aravis)
  set(ARAVIS_LIB "${CMAKE_BINARY_DIR}/dependencies/aravis-prefix/src/aravis-build/src/.libs/libaravis-0.6.a")
  #add_dependencies(tcam_aravis aravis)
  find_package(LibXml2 REQUIRED QUIET)
  find_package(PCAP REQUIRED QUIET)

  target_link_libraries(tcam-aravis "${GIO_LIBRARIES}")
  target_link_libraries(tcam-aravis "${PCAP_LIBRARIES}")

  target_link_libraries(tcam-aravis "${LIBXML2_LIBRARIES}")
  target_link_libraries(tcam-aravis "${ARAVIS_LIB}")

else (TCAM_INTERNAL_ARAVIS)

  target_link_libraries(tcam-aravis ${aravis_LIBRARIES})

endif (TCAM_INTERNAL_ARAVIS)


set_property(TARGET tcam-aravis PROPERTY VERSION ${TCAM_VERSION})
set_property(TARGET tcam-aravis PROPERTY SOVERSION ${TCAM_VERSION_MAJOR})

install(TARGETS tcam-aravis
  LIBRARY
  DESTINATION "${TCAM_INSTALL_LIB}/tcam-${TCAM_VERSION_MAJOR}"
  COMPONENT tcam-aravis)
