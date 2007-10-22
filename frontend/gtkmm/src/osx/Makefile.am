# Process this file with automake to produce Makefile.in
#
# Copyright (C) 2001, 2002, 2003, 2004, 2005, 2007 Rob Caelers & Raymond Penners
#
# Time-stamp: <2007-09-16 15:42:46 robc>
#
# $Id: Makefile.am 1280 2007-08-21 16:23:07Z rcaelers $
#

MAINTAINERCLEANFILES = 	Makefile.in

EXTRA_DIST = 		$(wildcard *.hh) $(wildcard *.cc) \
			$(wildcard *.c) $(wildcard *.h)  $(wildcard *.icc)

if PLATFORM_OS_OSX

noinst_LIBRARIES = 	libworkrave-frontend-gtkmm-osx.a
libworkrave_frontend_gtkmm_osx_a_SOURCES = \
			OSXUtil.cc \
			sync-menu.c

libworkrave_frontend_gtkmm_osx_a_CFLAGS = \
			@X_CFLAGS@ \
			-W  -I../../../common/include \
			-I../../../../backend/include \
			-I .. @GTK_CFLAGS@ \
                        -I ../../../../common/include/osx \
                        -I ../../../../common/include

libworkrave_frontend_gtkmm_osx_a_CXXFLAGS = \
			@X_CFLAGS@ \
			-W  -I../../../common/include \
			-I../../../../backend/include \
			-I .. @GTK_CFLAGS@ \
                        -I ../../../../common/include/osx \
                        -I ../../../../common/include

endif