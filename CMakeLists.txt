#=============================================================================
#  MuseScore
#  Music Composition & Notation
#  $Id:$
#
#  Copyright (C) 2011-2014 Werner Schweer
#
#  This program is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License version 2
#  as published by the Free Software Foundation and appearing in
#  the file LICENSE.GPL
#=============================================================================

project(sf3convert)
CMAKE_MINIMUM_REQUIRED(VERSION 2.6.0)
include (GNUInstallDirs)
include (${PROJECT_SOURCE_DIR}/build/FindQt5.cmake)
include (${PROJECT_SOURCE_DIR}/build/UsePkgConfig1.cmake)
# set(CMAKE_VERBOSE_MAKEFILE ON)

set(QT_MIN_VERSION    "5.6")
set(QT_USE_QTXML         TRUE)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")

# distributions might need to override these
if (MINGW)
      set(CMAKE_CXX_FLAGS   "${CMAKE_CXX_FLAGS} -g")
else (MINGW)
      set(CMAKE_CXX_FLAGS   "${CMAKE_CXX_FLAGS} -g -fPIC -fPIE")
endif (MINGW)

add_executable(sf3convert
   sfconvert.cpp sfont.cpp xml.cpp
   )

if (MINGW)
   target_link_libraries(sf3convert
      ${QT_LIBRARIES}
      vorbis
      vorbisenc
      vorbisfile
      ogg
      sndfile-1
   )

   install( TARGETS sf3convert RUNTIME DESTINATION . )

   install_files ( . .dll
      ${CROSS}/bin/libgcc_s_dw2-1.dll
      ${CROSS}/bin/libstdc++-6.dll
      ${CROSS}/bin/libwinpthread-1.dll
      ${CROSS}/lib/libsndfile-1.dll
      ${CROSS}/lib/libogg.dll
      ${CROSS}/lib/libvorbis.dll
      ${CROSS}/lib/libvorbisenc.dll
      ${CROSS}/lib/libvorbisfile.dll
      ${CROSSQT}/bin/Qt5Core.dll
      ${CROSSQT}/bin/Qt5Xml.dll
      ${CROSSQT}/bin/icuin51.dll
      ${CROSSQT}/bin/icuuc51.dll
      ${CROSSQT}/bin/icudt51.dll
      )
else (MINGW)

   ##
   ## libsndfile
   ##

   PKGCONFIG1 (sndfile 1.0.25 SNDFILE_INCDIR SNDFILE_LIBDIR SNDFILE_LIB SNDFILE_CPP)
   if (SNDFILE_INCDIR)
       message("libsndfile detected ${SNDFILE_INCDIR} ${SNDFILE_LIBDIR} ${SNDFILE_LIB}")
   else (SNDFILE_INCDIR)
      message("libsndfile not found\n")
   endif (SNDFILE_INCDIR)

   ##
   ## libvorbis
   ##

   PKGCONFIG1 (vorbis 1.3.3 VORBIS_INCDIR VORBIS_LIBDIR VORBIS_LIB VORBIS_CPP)
   if (VORBIS_INCDIR)
       message("libvorbis detected ${VORBIS_INCDIR} ${VORBIS_LIBDIR} ${VORBIS_LIB}")
   else (VORBIS_INCDIR)
       message("libvorbis not found\n")
   endif (VORBIS_INCDIR)

  ##
   ## libvorbisenc
   ##

   PKGCONFIG1 (vorbisenc 1.3.3 VORBISENC_INCDIR VORBISENC_LIBDIR VORBISENC_LIB VORBISENC_CPP)
   if (VORBISENC_INCDIR)
       message("libvorbisenc detected ${VORBISENC_INCDIR} ${VORBISENC_LIBDIR} ${VORBISENC_LIB}")
   else (VORBISENC_INCDIR)
       message("libvorbisenc not found\n")
   endif (VORBISENC_INCDIR)

   ##
   ## libvorbisfile
   ##

   PKGCONFIG1 (vorbisfile 1.3.3 VORBISFILE_INCDIR VORBISFILE_LIBDIR VORBISFILE_LIB VORBISFILE_CPP)
   if (VORBISFILE_INCDIR)
       message("libvorbisfile detected ${VORBISFILE_INCDIR} ${VORBISFILE_LIBDIR} ${VORBISFILE_LIB}")
   else (VORBISFILE_INCDIR)
       message("libvorbisfile not found\n")
   endif (VORBISFILE_INCDIR)


   ##
   ## libogg
   ##

   PKGCONFIG1 (ogg 1.3.0 OGG_INCDIR OGG_LIBDIR OGG_LIB OGG_CPP)
   if (OGG_INCDIR)
       message("libogg detected ${OGG_INCDIR} ${OGG_LIBDIR} ${OGG_LIB}")
   else (OGG_INCDIR)
       message("libogg not found\n")
   endif (OGG_INCDIR)

   ##
   ## Includes
   ##
   include_directories(
      ${PROJECT_SOURCE_DIR}
      ${PROJECT_BINARY_DIR}
      ${SNDFILE_INCDIR}
      ${OGG_INCDIR}
      ${VORBIS_INCDIR}
      ${VORBISENC_INCDIR}
      ${VORBISFILE_INCDIR}
   )

   target_link_libraries(sf3convert
      ${QT_LIBRARIES}
      ${OGG_LIB}
      ${VORBIS_LIB}
      ${VORBISENC_LIB}
      ${VORBISFILE_LIB}
      ${SNDFILE_LIB}
   )

   install(TARGETS sf3convert RUNTIME DESTINATION bin)
   install(FILES sf3convert.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1 COMPONENT doc)
endif (MINGW)
