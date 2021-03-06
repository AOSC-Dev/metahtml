# Makefile.in: -*- Text -*-  Source for configure to make a Makefile from.
#
# Author: Brian J. Fox (bfox@ai.mit.edu).
#
# This file is part of <Meta-HTML>(tm), a system for the rapid
# deployment of Internet and Intranet applications via the use
# of the Meta-HTML language.
#
# Copyright (c) 1995, 2000, Brian J. Fox (bfox@ai.mit.edu).
#
# Meta-HTML is free software; you can redistribute it and/or modify
# it under the terms of the General Public License as published
# by the Free Software Foundation; either version 1, or (at your
# option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# FSF GPL for more details.
#
# You should have received a copy of the FSF General Public License
# along with this program; if you have not, you may obtain one
# electronically at the following URL:
#
#	 http://www.metahtml.com/COPYING
#

# The version number of this package.
PACKNAME= metahtml
PACKVER = 6.11
SUBVER=01
RPMREL	= 1

# Items that were filled in by configure.
VPATH	= @srcdir@
srcdir	= @srcdir@
DEFS	= -DMHTML_VERSION=$(PACKVER) -DMHTML_MINOR=$(SUBVER)
INSTALL = @INSTALL@
FIND	= @FIND@
PLATFORM= @PLATFORM@
BUILD_CPU= @BUILD_CPU@
BUILD_OS = @BUILD_OS@
EXE	= @EXE@
WEBBASEDIR = @WEBBASEDIR@
MODULES_DIR = @MODULES_DIR@
MHTML_SYSTEM_TYPE = @MHTML_SYSTEM_TYPE@
prefix	= @prefix@
exec_prefix = @exec_prefix@
OS_LIBS	= @OS_LIBS@
DLLDFLAGS = @DLLDFLAGS@
CHECK_ACTIVATION = 
i386_RPM_ARGS = @i386_RPM_ARGS@

# The real locations of programs.
RANLIB	= @RANLIB@
AR	= ar
MKDIR	= mkdir
TAR	= tar
LN	= ln
CP	= cp
RM	= rm -f
MV	= mv
BASH_SHELL = @BASH_SHELL@
bindir	= $(exec_prefix)/bin
libdir	= $(exec_prefix)/lib
docdir	= $(WEBBASEDIR)
DOCPDF	= doc/manual/metahtml.pdf
redhat_bin = /usr/bin
redhat_lib = /usr/lib/metahtml
redhat_sbin= /usr/sbin
redhat_rc  = /etc/rc.d/init.d

# You should not compile with anything other than GCC.
# It is possible to compile with a fully ANSI compliant compiler that
# isn't GCC, but it isn't recommended.

CC		= gcc $(GCC_WARN)
GCC_WARN	= -Wall -Wstrict-prototypes -Wshadow # -fvolatile -fvolatile-global
# PROFILE_FLAGS	= -pg
# DEBUG_FLAGS	= -g $(PROFILE_FLAGS)
OPTIMIZE_FLAGS	= -O69 -finline-functions
INCLUDE_FLAGS	= @EXTRAINC@
CFLAGS		= $(OPTIMIZE_FLAGS) $(DEBUG_FLAGS) $(INCLUDE_FLAGS)
LDFLAGS		= $(STATIC) $(DEBUG_FLAGS) $(DLLDFLAGS)

PROGDEFS	= AR='$(AR)' RANLIB='$(RANLIB)' LN='$(LN)' CP='$(CP)' \
		  RM='$(RM)' CC='$(CC)'

OTHERDEFS	= TOPDIR=$(srcdir) \
		  MHTML_VERSION=$(PACKVER) MHTML_MINOR=$(SUBVER) \
		  EXE=$(EXE) CHECK_ACTIVATION=$(CHECK_ACTIVATION)

PCFLAGS		= $(PROGDEFS) CFLAGS='$(CFLAGS)' LDFLAGS='$(LDFLAGS)' \
		  DEBUG_FLAGS='$(DEBUG_FLAGS)' $(OTHERDEFS) \
		  PROFILE_FLAGS='$(PROFILE_FLAGS)' \
		  OPTIMIZE_FLAGS='$(OPTIMIZE_FLAGS)'

LIBUTILS	= libutils/libutils.a
LIBMHTML	= libmhtml/libmhtml.a
LIBSERVER	= libserver/libserver.a

SUBDIRS		= libutils libmhtml libserver \
		  engine mhc server mdb \
		  dbutils $(MODULES_DIR) doc sessions utilities

SUBDIR_TARGETS  = engine mhc server mdb sessions dbutils utilities \
		  $(MODULES_DIR) doc

TARGETS		= $(LIBUTILS) $(LIBMHTML) $(LIBSERVER) \
		  engine mhc server mdb sessions dbutils utilities \
		  $(MODULES_DIR) doc build-download

ALLMAKEFILES = Makefile mhc/Makefile server/Makefile engine/Makefile \
	       mdb/Makefile mdb/readline/Makefile sessions/Makefile \
	       dbutils/Makefile libmhtml/Makefile libutils/Makefile \
	       libutils/compat/Makefile libserver/Makefile \
	       modules/Makefile modules/libgd/Makefile utilities/Makefile \
	       engine/fcgi/Makefile.in

SPECFILES	= support/metahtml.spec.in support/mysql.spec.in \
		  support/iodbc.spec.in support/odbc.spec.in \
		  support/sybase.spec.in support/msql.spec.in \
		  support/podbc.spec.in

EXECUTABLES	= server/mhttpd mdb/mdb mhc/mhc

all:	$(TARGETS)
	@if [ ! -s libmhtml/bootstrap.lib ]; then \
	  echo ""; \
	  echo " -- BOOTSTRAPPING code can now be built!"; echo ""; \
	  echo " -- Rebuilding to compile libmhtml/bootstrap.lib."; \
	  echo ""; \
	  $(MAKE); \
	fi
	@echo ""
	@echo "Meta-HTML $(PACKVER) is built successfully for $(PLATFORM)"
	@echo " -- Use \"make install\" to install the binaries and modules."
	@echo ""

debug: FORCE
	$(MAKE) OPTIMIZE_FLAGS= DEBUG_FLAGS=-g

purify: FORCE
	$(MAKE) CC='purify $(CC)' OPTIMIZE_FLAGS= DEBUG_FLAGS=-g

all++: FORCE
	$(MAKE) CC=g++ bindir=$(prefix)/bin++ libdir=$(prefix)/lib++ all

debug++: FORCE
	$(MAKE) CC=g++ bindir=$(prefix)/bin++ libdir=$(prefix)/lib++ \
	DEBUG_FLAGS=-g OPTIMIZE_FLAGS=

install++: FORCE
	$(MAKE) CC=g++ bindir=$(prefix)/bin++ libdir=$(prefix)/lib++ install

$(TARGETS): $(ALLMAKEFILES)

$(LIBUTILS): FORCE
	(cd libutils; $(MAKE) $(PCFLAGS))

$(LIBMHTML): FORCE
	(cd libmhtml; $(MAKE) $(PCFLAGS))

$(LIBSERVER): FORCE
	(cd libserver; $(MAKE) $(PCFLAGS))

$(SUBDIR_TARGETS): $(LIBUTILS) $(LIBMHTML)

$(SUBDIR_TARGETS): FORCE
	(cd $@; $(MAKE) $(PCFLAGS))

doc: dbutils $(MODULES_DIR)

static: clean-executables build-download
	$(MAKE) clean-executables
	$(MAKE) STATIC=--static

rebuild: clean-executables no-dot-lib
	 if [ -f modules/compiler/Makefile ]; then \
	    (cd modules/compiler; $(MAKE) clean); \
	 fi
	 if [ -f modules/machine/Makefile ]; then \
	    (cd modules/machine; $(MAKE) clean); \
	 fi
	 $(RM) -f modules/modcompiler.so modules/modmachine.so
	 $(MAKE) debug

build-download: build-download.in Makefile
	cat build-download.in | \
	sed -e 's%@VERSION@%$(PACKVER)%' \
	    -e 's%@BUILD_PLATFORM@%$(PLATFORM)%' \
	    -e 's%@BASH@%$(BASH_SHELL)%' >build-download
	chmod a+x build-download

clean-executables: FORCE
	for dir in mhc server engine mdb sessions ; do \
	   (cd $$dir; $(MAKE) clean-executables); \
	done

no-dot-lib: FORCE
	(cd libmhtml; $(RM) -f *.lib)

install: $(TARGETS) install_force

install_force: FORCE
	support/mkinstalldirs $(bindir)
	support/mkinstalldirs $(libdir)
	(cd mhc; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd engine; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd server; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd mdb; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd sessions; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd dbutils; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	(cd utilities; $(MAKE) install bindir=$(bindir) libdir=$(libdir))
	if [ "$(MODULES_DIR)" ]; then \
	  (cd $(MODULES_DIR); \
	  $(MAKE) install bindir=$(bindir) libdir=$(libdir)); \
	fi
	$(CP) support/install-site .; chmod a+x ./install-site
	@echo "To install the server or the engine, type \`./install-site'"

install-docs:
	(cd doc; $(MAKE) install docdir=$(docdir))

clean:  FORCE
	for dir in $(SUBDIRS); do \
	  (cd $$dir; $(MAKE) $@ ); \
	done
	$(RM) -rf BUILDROOT RPM-RELEASE DISTFILES $(DISTNAME).tgz
	$(RM) -f support/*.spec

configure-nt: configure-nt.in
	autoconf configure-nt.in >configure-nt
	chmod a+x configure-nt

realclean: clean
	for dir in $(SUBDIRS); do (cd $$dir; $(MAKE) $@ ); done
	(cd tagsets; $(RM) -f *.lib)
	(cd applications; $(MAKE) $@ )
	$(RM) -rf downloads =* build-download
	$(FIND) . -name "temp" -exec $(RM) -rf \{\} \;
	$(FIND) . -name "*~" -exec $(RM) \{\} \;
	$(FIND) . -name "*gz" -exec $(RM) \{\} \;
	$(FIND) . -name "foo\.*" -exec $(RM) \{\} \;
	$(FIND) . -name "bar\.*" -exec $(RM) \{\} \;
	$(FIND) . -name "baz\.*" -exec $(RM) \{\} \;
	$(FIND) . -name "core" -exec $(RM) \{\} \;
	$(FIND) . -name "core.*" -exec $(RM) \{\} \;
	$(FIND) . -name "*.core*" -exec $(RM) \{\} \;
	$(FIND) . -name "*.LCK*" -exec $(RM) \{\} \;
	$(FIND) ./release -name "*.db" -exec $(RM) \{\} \;
	$(FIND) . -name "*DISTFILES" -exec $(RM) \{\} \;
	$(RM) -f config.cache config.status config.h *.log Makefile TAGS
	$(RM) -f Install install-bin install-site
	$(RM) -rf $(DISTNAME).tgz
	$(RM) -f doc/manual/metahtml.dvi doc/manual/metahtml.newaux
	$(RM) -f doc/manual/metahtml.pdf doc/manual/metahtml.texi
	$(RM) -f doc/manual/page-counts.db doc/manual/page-counts.db.packed

distclean: realclean
	-$(FIND) . -name "CVS" -exec $(RM) -rf \{\} \;

codedistclean: distclean
	$(RM) -rf release applications

tags TAGS: FORCE
	etags --defines --typedefs --output TAGS \
	--regex='/DEFUNX[ \t]+\([^ \t]+\)/\1/' \
	--regex='/DEFMACRO[ \t]+\([^ \t]+\)/\1/' \
	--regex='/DEFMACROX[ \t]+\([^ \t]+\)/\1/' \
	--regex='/DEFWEAKMACRO[ \t]+\([^ \t]+\)/\1/' \
	--regex='/DEFWEAKMACROX[ \t]+\([^ \t]+\)/\1/' \
	 */*.c */*.c */*.h */*/*.c \
	 --language=none \
	 --regex='/<defun[ \t]+\([^ \t]+\)/\1/' \
	 --regex='/<defmacro[ \t]+\([^ \t]+\)/\1/' \
	 --regex='/<defsubst[ \t]+\([^ \t]+\)/\1/' \
	 --regex='/<define-tag[ \t]+\([^ \t]+\)/\1/' \
	 --regex='/<define-container[ \t]+\([^ \t]+\)/\1/' \
	 libmhtml/*/*.mhtml

Makefile: Makefile.in config.status
	./config.status

mhc/Makefile: mhc/Makefile.in config.status
	./config.status

modules/Makefile: modules/Makefile.in config.status
	./config.status

utilities/Makefile: utilities/Makefile.in config.status
	./config.status

utilities/imagemap/Makefile: utilities/imagemap/Makefile.in config.status
	./config.status

dbutils/Makefile: dbutils/Makefile.in config.status
	./config.status

sessions/Makefile: sessions/Makefile.in config.status
	./config.status

libutils/Makefile: libutils/Makefile.in config.status
	./config.status

libmhtml/Makefile: libmhtml/Makefile.in config.status
	./config.status

libserver/Makefile: libserver/Makefile.in config.status
	./config.status

server/Makefile: server/Makefile.in config.status
	./config.status

engine/Makefile: engine/Makefile.in config.status
	./config.status

engine/fcgi/Makefile: engine/fcgi/Makefile.in config.status
	./config.status

engine/fcgi/libfcgi/Makefile: engine/fcgi/libfcgi/Makefile.in config.status
	./config.status

mdb/Makefile: mdb/Makefile.in config.status
	./config.status

mdb/readline/Makefile: mdb/readline/Makefile.in config.status
	./config.status

config.status: configure
	./config.status --recheck

DISTNAME = $(PACKNAME)-$(PACKVER).$(SUBVER)
CODEDISTNAME = $(PACKNAME)-code-$(PACKVER).$(SUBVER)

$(DISTNAME).tgz: $(EXECUTABLES)
	$(MAKE) dist

$(DOCPDF):
	(cd doc/manual; if [ ! -f ./metahtml.pdf ]; then ./build-pdf; fi)

dist: DISTFILES
	(cd doc; $(MAKE) dist)
	$(RM) -rf $(DISTNAME)
	$(MKDIR) $(DISTNAME)
	for d in `find . -type d -print`; do \
	  if [ "$$d" != "./$(DISTNAME)" -a "$$d" != "." ]; then \
	    echo "Making directory $(DISTNAME)/$$d"; \
	    mkdir $(DISTNAME)/$$d; \
	  fi; \
	done
	for f in `cat DISTFILES`; do \
	   $(LN) $(srcdir)/$$f $(DISTNAME)/$$f || \
		{ echo copying $$f; cp -rp $(srcdir)/$$f $(DISTNAME)/$$f ; } \
	done
	-(cd $(DISTNAME); $(MAKE) -f ../Makefile distclean)
	$(TAR) chvf - $(DISTNAME) | gzip >$(DISTNAME).tgz
	$(RM) -rf $(DISTNAME)

# Gets rid of most of the unwanted files.  Verify manually (if necessary)
# that this produces a list of all the files desired in the distribution. 
DISTFILES: FORCE
	$(RM) -rf $(DISTNAME)
	(cd $(srcdir); find . ! -type d -print) \
	| sed  '/\/RCS\//d; \
		/\/EMACS-BACKUPS\//d; \
		/\/downloads\//d; \
		/\.tar.*/d; \
		/\#*#$$/d; \
		/~$$/d; /\.o$$/d; /\/.*\.a$$/d; \
		/\/\.gdb_history$$/d; \
		/\/\.gdbinit$$/d; \
		/\/\.mdb_init$$/d; \
		/\/\.ps$$/d; \
		/\/\.pdf$$/d; \
		/\/\.dvi$$/d; \
		/\/.*\.BAK$$/d; \
		/\/TAGS$$/d; \
		/\/core$$/d; /\/[a-z]*\.core$$/d; /\/core\.[a-z]*$$/d; \
		/\/a.out$$/d; \
		/\/=/d; \
		/\/conftest\.c$$/d; \
		/\/config\.status$$/d; \
		/\/config\.cache/d; \
		/\/.*DISTFILES$$/d; \
		/\.toc$$/d; \
		/\.aux$$/d; /\.log$$/d; \
		/\.cps$$/d; /\.cp$$/d; \
		/\.fns$$/d; /\.fn$$/d; \
		/\.tps$$/d; /\.tp$$/d; \
		/\.vrs$$/d; /\.vr$$/d; \
		/\.pgs$$/d; /\.pg$$/d; \
		/\.kys$$/d; /\.ky$$/d; \
		s/^.\///; /^\.$$/d;' \
	| sort | uniq > DISTFILES

# Gets rid of most of the unwanted files.  Verify manually (if necessary)
# that this produces a list of all the files desired in the distribution. 
CODEDISTFILES: FORCE
	$(RM) -rf $(CODEDISTNAME)
	(cd $(srcdir); find . ! -type d -print) \
	| sed  '/\/RCS\//d; \
	        /\/CVS\//d; \
	        /\release\//d; \
	        /\applications\//d; \
		/\/EMACS-BACKUPS\//d; \
		/\/downloads\//d; \
		/\.tar.*/d; \
		/\#*#$$/d; \
		/~$$/d; /\.o$$/d; /\/.*\.a$$/d; \
		/\/\.gdb_history$$/d; \
		/\/\.mdb_init$$/d; \
		/\/.*\.BAK$$/d; \
		/\/TAGS$$/d; \
		/\/core$$/d; /\/[a-z]*\.core$$/d; /\/core\.[a-z]*$$/d; \
		/\/a.out$$/d; \
		/\/=/d; \
		/\/conftest\.c$$/d; \
		/\/config\.status$$/d; \
		/\/config\.cache/d; \
		/\/.*DISTFILES$$/d; \
		/\.toc$$/d; \
		/\.aux$$/d; /\.log$$/d; \
		/\.cps$$/d; /\.cp$$/d; \
		/\.fns$$/d; /\.fn$$/d; \
		/\.tps$$/d; /\.tp$$/d; \
		/\.vrs$$/d; /\.vr$$/d; \
		/\.pgs$$/d; /\.pg$$/d; \
		/\.kys$$/d; /\.ky$$/d; \
		s/^.\///; /^\.$$/d;' \
	| sort | uniq > CODEDISTFILES

support/metahtml.spec: $(SPECFILES) Makefile
	MY_BUILD_ROOT=`/bin/pwd`/BUILDROOT/INSTALLROOT; \
	cat support/metahtml.spec.in \
	| sed -e 's/DISTNAME/$(DISTNAME)/g' \
	      -e 's/PACKVER/$(PACKVER)/g' \
	      -e 's/SUBVER/$(SUBVER)/g' \
	      -e "s@BUILDROOT@$$MY_BUILD_ROOT@g" \
	      -e 's/RPMREL/$(RPMREL)/g' >support/metahtml.spec
	@if [ -f ./modules/modmysql.so ]; then \
	  cat support/mysql.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modmsql.so ]; then \
	  cat support/msql.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modpgsql.so ]; then \
	  cat support/pgsql.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modsybaseodbc.so ]; then \
	  cat support/sybase.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modiodbc.so ]; then \
	  cat support/iodbc.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/mododbc.so ]; then \
	  cat support/odbc.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modpodbc.so ]; then \
	  cat support/podbc.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modmachine.so ]; then \
	  cat support/machine.spec.in >>support/metahtml.spec; \
	 fi
	@if [ -f ./modules/modcompiler.so ]; then \
	  cat support/compiler.spec.in >>support/metahtml.spec; \
	 fi

RPM-RELEASE: support/RPM-RELEASE.in
	cat support/RPM-RELEASE.in \
	| sed -e 's/DISTNAME/$(DISTNAME)/g' \
	      -e 's/PACKVER/$(PACKVER)/g' \
	      -e 's/SUBVER/$(SUBVER)/g' >RPM-RELEASE

redhat_install: $(TARGETS) redhat_install_force

redhat_install_force: FORCE
	if [ ! -d "$(redhat_bin)" ]; then mkdir -p $(redhat_bin); fi
	if [ ! -d "$(redhat_lib)" ]; then mkdir -p $(redhat_lib); fi
	(cd mhc; $(MAKE) install bindir=$(redhat_bin) libdir=$(redhat_lib))
	(cd engine; $(MAKE) redhat_install bindir=$(redhat_sbin))
	(cd server; $(MAKE) redhat_install bindir=$(redhat_sbin))
	(cd mdb; $(MAKE) install bindir=$(redhat_bin) libdir=$(redhat_lib))
	(cd dbutils; $(MAKE) install bindir=$(redhat_lib) libdir=$(redhat_lib))
	(cd sessions; \
	   $(MAKE) install bindir=$(redhat_lib) libdir=$(redhat_lib))
	(cd utilities; \
	   $(MAKE) redhat_install bindir=$(redhat_bin) libdir=$(redhat_lib))
	if [ "$(MODULES_DIR)" ]; then \
	  (cd $(MODULES_DIR); \
	    $(MAKE) install bindir=$(redhat_lib) libdir=$(redhat_lib)) \
	fi

BUILDROOT:
	-$(MKDIR) BUILDROOT
	-$(MKDIR) BUILDROOT/SPECS
	-$(MKDIR) BUILDROOT/BUILD
	-$(MKDIR) BUILDROOT/SOURCES
	-$(MKDIR) BUILDROOT/SRPMS
	-$(MKDIR) BUILDROOT/RPMS
	-$(MKDIR) BUILDROOT/RPMS/i386
	-$(MKDIR) BUILDROOT/INSTALLROOT

newrpm new-rpm: FORCE
	$(RM) -rf $(DISTNAME).tgz BUILDROOT support/metahtml.spec
	$(MAKE) rpm

rpm:  $(DISTNAME).tgz BUILDROOT support/metahtml.spec RPM-RELEASE $(DOCPDF)
	$(CP) support/metahtml.spec BUILDROOT/SPECS/$(DISTNAME).spec
	(cd BUILDROOT/SOURCES; \
	 $(RM) -f $(DISTNAME).tgz metahtml.pdf; \
	 $(LN) -s ../../$(DISTNAME).tgz .; \
	 $(LN) -s ../../doc/manual/metahtml.pdf .)
	(topdir=`pwd`/BUILDROOT; export topdir; \
	 cd BUILDROOT; \
	 rpmbuild $(i386_RPM_ARGS) -ba SPECS/$(DISTNAME).spec)

FORCE:
