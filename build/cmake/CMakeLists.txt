
set(ALL_LINGUAS nl de eo pl da es zh_TW ru fr pt_BR nb lt cs he eu ca sv ja bg hu tr el sk zh_CN it sl ro)
set(SOUND_THEMES subtle default)

# Set Workrave version
set(VERSION_MAJOR   "1")
set(VERSION_MINOR   "9")
set(VERSION_RELEASE "2")
set(VERSION_PATCH   "0")
if (VERSION_PATCH)
    set(VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_RELEASE}-${VERSION_PATCH}" )
    set(RESOURCE_VERSION "${VERSION_MAJOR},${VERSION_MINOR},${VERSION_RELEASE},${VERSION_PATCH}" )
else (VERSION_PATCH)
    set(VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_RELEASE}" )
    set(RESOURCE_VERSION "${VERSION_MAJOR},${VERSION_MINOR},${VERSION_RELEASE},0" )
endif (VERSION_PATCH)

# CMake
CMAKE_MINIMUM_REQUIRED(VERSION 2.6.2 FATAL_ERROR)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/modules")

project(Workrave)

if(MSVC)
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_DEPRECATE -D_CRT_NONSTDC_NO_DEPRECATE)
  
  SET(MSVC_WARNINGS "/W3 /wd4127 /wd4250 /wd4413 /wd4800")
  
  SET(CMAKE_CXX_FLAGS_DEBUG   "${CMAKE_C_FLAGS_DEBUG}   /FIconfig.h /vd2 ${MSVC_WARNINGS}")
  SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /FIconfig.h /vd2 ${MSVC_WARNINGS}")
endif(MSVC)

if(WIN32)
  set (CMAKE_DEBUG_POSTFIX "d")

  add_definitions( 
    -DWIN32
    -D_WINDOWS
    -D_WIN32_WINNT=0x0600
  ) 

endif(WIN32)
 

if (WIN32)
  set (LIBRARY_OUTPUT_PATH  ${CMAKE_BINARY_DIR}/bin)
else (WIN32)
  set (LIBRARY_OUTPUT_PATH  ${CMAKE_BINARY_DIR}/lib)
endif (WIN32)

set (EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

# for including config.h and for includes like <dir/foo.h>
include_directories(${CMAKE_SOURCE_DIR}/.. ${CMAKE_BINARY_DIR} ${CMAKE_INCLUDE_PATH})

# linker search directories
link_directories(${LIBRARY_OUTPUT_PATH})
include_directories(${CMAKE_LIBRARY_PATH})

######################################################################
## Win32
######################################################################

if (WIN32)
  set(PLATFORM_OS_WIN32 1)
  if (MSVC)
    set(PLATFORM_OS_WIN32_NATIVE 1)
  endif (MSVC)
endif (WIN32)

######################################################################
## Options
######################################################################

option(DEVELOPMENT_MODE "Development" OFF)
if(DEVELOPMENT_MODE)
  set(DATA_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../../)
endif(DEVELOPMENT_MODE)

######################################################################
## Find packages
######################################################################

include(FindGlib)
include(FindGtkmm)
include(FindDbus)
include(FindGettext)
#include(FindGnet)

FIND_PROGRAM(GETTEXT_XGETTEXT_EXECUTABLE xgettext)
FIND_PROGRAM(INTLTOOL_UPDATE_EXECUTABLE intltool-update)
FIND_PROGRAM(INTLTOOL_MERGE_EXECUTABLE intltool-merge)
MARK_AS_ADVANCED(INTLTOOL_UPDATE_EXECUTABLE)

if (NOT GETTEXT_FOUND)
  message("=== GetText not found - disabling i18n")
endif (NOT GETTEXT_FOUND)


set(HAVE_DBUS ${DBUS_FOUND})
if (HAVE_DBUS)
  include (FindPythonInterp)
  if (NOT PYTHONINTERP_FOUND)
    message("=== Python not found - disabling dbus")
    set (HAVE_DBUS FALSE)
  endif (NOT PYTHONINTERP_FOUND)
endif (HAVE_DBUS)

set(HAVE_GNET ${GNET_FOUND})
set (HAVE_DISTRIBUTION FALSE)
if (HAVE_GNET OR HAVE_GIO_NET)
  set (HAVE_DISTRIBUTION TRUE)
  set (HAVE_TESTS TRUE)
endif (HAVE_GNET OR HAVE_GIO_NET)

if (WIN32)
  include(FindPerl)
  if (NOT PERL_FOUND)
    message("=== Perl not found")
  else (NOT PERL_FOUND)
    if (INTLTOOL_MERGE_EXECUTABLE)
      set (INTLTOOL_MERGE_EXECUTABLE ${PERL_EXECUTABLE} ${INTLTOOL_MERGE_EXECUTABLE})
    endif(INTLTOOL_MERGE_EXECUTABLE)
  endif (NOT PERL_FOUND)

  include(FindDirectX)
  if (NOT DIRECTX_FOUND)
    message(FATAL_ERROR "Could not find DirectX SDK")
  endif(NOT DIRECTX_FOUND)
  set(HAVE_DIRECTX ${DIRECTX_FOUND})

endif (WIN32)

if (INTLTOOL_MERGE_EXECUTABLE AND PERL_FOUND)
  set (HAVE_EXERCISES TRUE)
else (INTLTOOL_MERGE_EXECUTABLE AND PERL_FOUND)
  message("=== Perl/Intltool not found - disabling exercises")
  set (HAVE_EXERCISES FALSE)
endif (INTLTOOL_MERGE_EXECUTABLE AND PERL_FOUND)


######################################################################
## Create config.h
######################################################################

if (MINGW)
  set (HAVE_GNUC_VARARGS 1)
endif(MINGW)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/workrave.rc.cmake ${CMAKE_CURRENT_BINARY_DIR}/workrave.rc )

# compiler definitions 
add_definitions(-DHAVE_CONFIG_H=1)

######################################################################
## Subdirectories
######################################################################

add_subdirectory(harpoon)
add_subdirectory(common)
add_subdirectory(backend)
add_subdirectory(frontend)

