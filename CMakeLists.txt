cmake_minimum_required(VERSION 3.10)
project(libiff VERSION 0.1.1 DESCRIPTION "EA-85 IFF parser library" LANGUAGES C)

include(GNUInstallDirs)
include(CheckIncludeFile)

set(IFF_BIG_ENDIAN 0)
set(VERSION "${PROJECT_VERSION}")
set(prefix "${CMAKE_INSTALL_PREFIX}")
set(exec_prefix "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR}")
set(libdir "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
set(includedir "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR}")

check_include_file(getopt.h HAVE_GETOPT_H)
if(HAVE_GETOPT_H)
  set(HAVE_GETOPT_H 1)
else ()
  set(HAVE_GETOPT_H 0)
endif ()

set(iff_HEADERS
  src/libiff/cat.h
  src/libiff/chunk.h
  src/libiff/error.h
  src/libiff/extension.h
  src/libiff/form.h
  src/libiff/group.h
  src/libiff/id.h
  src/libiff/iff.h
  src/libiff/io.h
  src/libiff/list.h
  src/libiff/prop.h
  src/libiff/rawchunk.h
  src/libiff/util.h
  )

set(iff_SOURCES
  src/libiff/cat.c
  src/libiff/chunk.c
  src/libiff/error.c
  src/libiff/extension.c
  src/libiff/form.c
  src/libiff/group.c
  src/libiff/id.c
  src/libiff/iff.c
  src/libiff/io.c
  src/libiff/list.c
  src/libiff/prop.c
  src/libiff/rawchunk.c
  src/libiff/util.c
  )

set(iff_DATAFILES
  src/Doxyfile
  src/libiff/ifftypes.h.in
  src/libiff/libiff.def
  src/libiff/libiff.vcxproj
  src/libiff/libiff.vcxproj.filters
  src/libiff/Makefile.am
  src/libiff.pc.in
  src/libiff.sln
  src/Makefile.am
  doc/IFF.asc
  doc/Makefile.am
  AUTHORS
  bootstrap
  ChangeLog
  configure.ac
  COPYING
  INSTALL
  Makefile.am
  NEWS
  README.md
  release.nix
  version
  )

set(iff_INCLUDES
  "${CMAKE_CURRENT_BINARY_DIR}"
  "${CMAKE_CURRENT_SOURCE_DIR}/src/libiff"
  )

set(iff_DEFINITIONS
  LIBIFF_EXPORTS
  )

if (WIN32)
  add_definitions(-DWIN32)
endif ()

configure_file(src/libiff/ifftypes.h.in "${CMAKE_CURRENT_BINARY_DIR}/ifftypes.h" @ONLY UNIX)
configure_file(src/libiff.pc.in "${CMAKE_CURRENT_BINARY_DIR}/libiff.pc" @ONLY UNIX)

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${iff_HEADERS} ${iff_SOURCES} ${iff_DATAFILES})
add_library(iff SHARED ${iff_HEADERS} ${iff_SOURCES} ${iff_DATAFILES})
target_include_directories(iff PUBLIC "$<BUILD_INTERFACE:${iff_INCLUDES}>")
target_compile_definitions(iff PRIVATE "${iff_DEFINITIONS}")
set_target_properties(iff PROPERTIES VERSION ${PROJECT_VERSION} SOVERSION ${PROJECT_VERSION_MAJOR})
set_target_properties(iff PROPERTIES PREFIX "lib")
set_property(TARGET iff PROPERTY POSITION_INDEPENDENT_CODE ON)

add_library(IFF::IFF ALIAS iff)


set(iffpp_HEADERS
  src/iffpp/pp.h
  )

set(iffpp_SOURCES
  src/iffpp/pp.c
  src/iffpp/main.c
  )

set(iffpp_DATAFILES
  src/iffpp/iffpp.vcxproj
  src/iffpp/iffpp.vcxproj.filters
  src/iffpp/Makefile.am
  )

set(iffpp_DEFINITIONS
  PACKAGE_NAME=\"${PROJECT_NAME}\"
  PACKAGE_VERSION=\"${PROJECT_VERSION}\"
  HAVE_GETOPT_H=${HAVE_GETOPT_H}
  )

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${iffpp_HEADERS} ${iffpp_SOURCES})
add_executable(iffpp ${iffpp_HEADERS} ${iffpp_SOURCES})
target_compile_definitions(iffpp PRIVATE "${iffpp_DEFINITIONS}")
target_link_libraries(iffpp PRIVATE IFF::IFF)


set(iffjoin_HEADERS
  src/iffjoin/join.h
  )

set(iffjoin_SOURCES
  src/iffjoin/join.c
  src/iffjoin/main.c
  )

set(iffjoin_DATAFILES
  src/iffjoin/iffjoin.vcxproj
  src/iffjoin/iffjoin.vcxproj.filters
  src/iffjoin/Makefile.am
  )

set(iffjoin_DEFINITIONS
  PACKAGE_NAME=\"${PROJECT_NAME}\"
  PACKAGE_VERSION=\"${PROJECT_VERSION}\"
  HAVE_GETOPT_H=${HAVE_GETOPT_H}
  )

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${iffjoin_HEADERS} ${iffjoin_SOURCES})
add_executable(iffjoin ${iffjoin_HEADERS} ${iffjoin_SOURCES})
target_compile_definitions(iffjoin PRIVATE "${iffjoin_DEFINITIONS}")
target_link_libraries(iffjoin PRIVATE IFF::IFF)


install(TARGETS iff iffpp iffjoin
  RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
  LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  )

install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/libiff.pc"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/pkgconfig"
  )

install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/ifftypes.h" ${iff_HEADERS}
  DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/libiff"
  )
