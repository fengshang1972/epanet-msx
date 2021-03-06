# Cygwin Makefile for EPANET-MSX

# This will build EPANET-MSX as a cygwin DLL and import
# library (cygepanetmsx.dll/libcygepanetmsx.dll.a) under Cygwin/gcc,
# and a standalone executable (cygepanetmsx.exe) that is linked
# to this import library.
# The current release of the Cygwin environment can be
# obtained from http://www.cygwin.com

# The following targets are defined (for execution in
# the build directory under the Cygwin environment):
# make
#     -Builds cygepanetmsx.dll, cygepanetmsx.def, libcygepanetmsx.dll.a,
#      cygepanetmsx.exe
# make install
#     -Creates shell wrappers runcygepanetmsx.sh that
#      executes cygepanetmsx.exe.
#      The runcygepanetmsx.sh wrapper simply exports
#      environment variables so that the DLL is found at runtime,
#      allowing you to specify your own locations for installing
#      the DLL.
#     -Installs cygepanetmsx.dll, cygepanetmsx.exe, and runcygepanetmsx.sh
#      in <prefix>/bin, where <prefix> defaults to ~ (and can be set
#      below to something different - see "Install directories")
#     -Installs libcygepanetmsx.dll.a (import library), cygepanetmsx.def,
#      in <prefix>/lib
#     -Installs epanetmsx.h in <prefix>/include.  This is the required
#      header file for the EPANET programmer's toolkit, and thus
#      <prefix>/include should be on your CPP include search path
#      for subsequent use of the toolkit and linking with the import
#      library libcygepanetmsx.dll.a.
# make clean
#     -Removes object and library files, returning the build directory
#      to its pristine state.

SHELL = /bin/sh

# C H A N G E  H E R E  A S  N E E D E D
# You may wish to change the install path 'prefix',
# or the compiler flags, but these defaults should be fine.

# Target filenames
# svnname.sh constructs a name: <prefix><root><Rev><postfix>
# where <Rev> is the atomic revision number of the svn repo.
epanetmsxrootname := cygepanetmsx
dllname := $(epanetmsxrootname).dll
defname := $(epanetmsxrootname).def
implibname = lib$(dllname).a
exename := $(epanetmsxrootname).exe
# Shell wrapper
runcmdtemplate = runepanetmsx.sh.template
runcmdname = runcygepanetmsx.sh
# Location of EPANET-MSX toolkit includes
epanetmsxincludedir = ../../include
# Search path for sources
epanetmsxsrcdir = ../../src
# dependent libraries
epanetlibname := cygepanet2.dll
VPATH = $(epanetmsxsrcdir)

# Install directories
prefix = ~
exec_prefix = $(prefix)
srcdir = .
libdir = $(exec_prefix)/lib
bindir = $(exec_prefix)/bin
includedir = $(prefix)/include
datarootdir = $(prefix)/share
docdir = $(datarootdir)/doc/epanet

# Compiler and flags
CC = /bin/gcc
dlltool = /bin/dlltool
CFLAGS = -g -O3
CPPFLAGS = -I $(srcdir) -I $(epanetmsxincludedir) -I $(includedir)
LDFLAGS = -L . -L $(libdir) -W1,-rpath,$(bindir) -l$(epanetlibname) -ldl

# Installer
INSTALL = install
INSTALL_PROGRAM = $(INSTALL)
INSTALL_DATA = $(INSTALL) -m 644

# Files for the shared object library
epanetmsx_objs=hash.o mempool.o mathexpr.o msxchem.o msxfile.o msxinp.o msxout.o msxproj.o \
	              msxqual.o msxrpt.o msxtank.o msxtoolkit.o msxutils.o newton.o rk5.o ros2.o \
	              msxcompiler.o msxfuncs.o
# Epanetmsx main program
epanetmsx_main=msxmain
# Epanetmsx header files
epanetmsx_heads=hash.h mempool.h mathexpr.h msxdict.h msxtypes.h msxutils.h \
	               newton.h rk5.h ros2.h msxfuncs.h $(epanetmsxincludedir)/epanetmsx.h
# Epanetmsx main program header files
epanetmsx_main_heads = $(epanetmsxincludedir)/epanetmsx.h

.PHONY: all
all: $(dllname) $(exename)

$(dllname): $(epanetmsx_objs)
#	$(dlltool) -z $(defname) --dllname $(dllname) --output-lib $(implibname) $^
#	$(CC) $(CFLAGS) $(LDFLAGS) -shared -o $@ $(defname) $^
	$(CC) $(CFLAGS) -shared -o $@ $^ -Wl,--output-def,$(defname) $(LDFLAGS)
	$(CC) $(CFLAGS) -shared -o $@ $^ -Wl,--kill-at $(LDFLAGS)
	$(dlltool) -d $(defname) --dllname $@ --output-lib $(implibname) --kill-at

$(exename): $(dllname) $(epanetmsx_main_heads)
	$(CC) $(CFLAGS) $(CPPFLAGS) -c $(epanetmsxsrcdir)/$(epanetmsx_main).c
	$(CC) $(CFLAGS) -o $@ $(epanetmsx_main).o -l$(dllname) $(LDFLAGS)

$(epanetmsx_objs): $(epanetmsx_heads)

.PHONY: install
install:
	cat $(runcmdtemplate) | sed 's|libdir|$(bindir)|' \
                                | sed 's|exename|$(bindir)/$(exename)|' \
                                > $(runcmdname)
	$(INSTALL_PROGRAM) -D $(exename) $(bindir)/$(exename)
	$(INSTALL_PROGRAM) -D $(dllname) $(bindir)/$(dllname)
	$(INSTALL_PROGRAM) -D $(defname) $(libdir)/$(defname)
	$(INSTALL_PROGRAM) -D $(implibname) $(libdir)/$(implibname)
	$(INSTALL_DATA) -D $(epanetmsxincludedir)/epanetmsx.h $(includedir)/epanetmsx.h
	$(INSTALL_PROGRAM) -D $(runcmdname) $(bindir)/$(runcmdname)

.PHONY: uninstall
uninstall:

.PHONY: check
check:

.PHONY: clean
clean:
	-/bin/rm *.o $(dllname) $(defname) $(implibname) $(exename) $(runcmdname)
