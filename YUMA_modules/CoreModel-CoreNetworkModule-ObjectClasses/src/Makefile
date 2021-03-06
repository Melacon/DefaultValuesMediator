MODULE_NAME=CoreModel-CoreNetworkModule-ObjectClasses

# SIL Makefile for Yuma Server Instrumentation Library
#  

############### SOURCE PROFILE ##############################

SUBDIR_NM=$(MODULE_NAME)

SUBDIR_CPP=

############### TARGET PROFILE ##############################

ifndef PREFIX
    PREFIX=/usr
endif

TARGET=../bin

LIB_INST=../lib

ifdef YUMA_HOME
WORK_INST=$(YUMA_HOME)/target/lib
else
WORK_INST=
endif

ifdef LIB64
REAL_INST=$(DESTDIR)$(PREFIX)/lib64/yuma
REAL_BASE=$(DESTDIR)$(PREFIX)/lib64
else
REAL_INST=$(DESTDIR)$(PREFIX)/lib/yuma
REAL_BASE=$(DESTDIR)$(PREFIX)/lib
endif

BASE_NAME=Default_values_mediator_utils
BASE_COMMON=../../$(BASE_NAME)

#################### MAKE RULES ########################

all: sil_dummy sil_lib

#################### PLATFORM DEFINITIONS ############
CINCBASE=$(DESTDIR)$(PREFIX)/include
CINCCOMMON=$(BASE_COMMON)/include

CINC = -I.

ifdef FORCE_YUMA_HOME
CINC += -I$(YUMA_HOME)/src/platform -I$(YUMA_HOME)/src/ncx \
     -I$(YUMA_HOME)/src/agt 
else
CINC += -I$(CINCBASE)/yuma/platform -I$(CINCBASE)/yuma/ncx \
     -I$(CINCBASE)/yuma/agt 
endif

CINC += -I$(CINCBASE) -I$(CINCBASE)/libxml2 -I$(CINCBASE)/libxml2/libxml -I$(CINCCOMMON)

ifdef FREEBSD
CINC += -I/usr/local/include -I/usr/local/include/libxml2 \
     -I/usr/local/include/libxml2/libxml
endif

# added /sw/include for MacOSX
ifdef MAC
# MACOSX version
  CINC +=-I/sw/include
  CFLAGS += -DMACOSX=1
endif

ifdef DESTDIR
OWNER=
else
ifdef MAC
OWNER=-oroot
else
ifdef CYGWIN
OWNER=
else
OWNER= --owner=root
endif
endif
endif

### GCC + [LINUX or MACOSX]

ifdef USE_WERROR
WERROR= -Werror
else
WERROR=
endif

CWARN=-Wall -Wno-long-long -Wformat-y2k -Winit-self \
	-Wswitch-default \
	-Wextra -Wundef -Wshadow -Wpointer-arith \
	-Wwrite-strings -Wbad-function-cast -Wcast-qual -Wcast-align \
	-Waggregate-return -Wstrict-prototypes -Wold-style-definition \
	-Wmissing-prototypes -Wmissing-declarations \
	-Wpacked -Winvalid-pch \
	-Wredundant-decls -Wnested-externs -Winline -std=gnu99 $(WERROR)


# -Wunreachable-code removed due to -O3
# -O3 changed to -O2 due to code bloat from inline functions

CDEFS=-DDEBUG=1 -DLINUX=1 -DGCC=1

ifndef NOFLOAT
  CDEFS += -DHAS_FLOAT=1
endif

# production (0) or debug (1) build
ifdef DEBUG
  CFLAGS += -ggdb3
else
  CFLAGS += -O2
endif

CFLAGS += $(CDEFS) $(CWARN)

ifndef CYGWIN
   CFLAGS += -fPIC
endif

# memory leak debugging mode
ifdef MEMTRACE
  CFLAGS += -DMEMORY_DEBUG=1
endif

ifdef RELEASE
  CFLAGS += -DRELEASE=$(RELEASE)
endif

ifdef LIB64
   CFLAGS += -DLIB64=1
endif


ifdef MAC
   GRP=
else
ifdef DESTDIR
   GRP=
else
ifdef CYGWIN
   GRP=
else
   GRP=--group=root
endif
endif
endif

ifdef STATIC
LIBSUFFIX=a
else
LIBSUFFIX=so
endif

CC?=gcc
LINK?=$(CC)
LINT=splint
LINTFLAGS= '-weak -macrovarprefix "m_"'
##LIBFLAGS=-lsocket

LIBTOOL=ar
#LFLAGS=-v --no-as-needed
LFLAGS=-lm
LPATH=-L$(LIB_INST)

CEES = $(wildcard *.c)

HEES = $(wildcard *.h)


################ OBJS RULE #############
OBJS = $(patsubst %.c,$(TARGET)/%.o,$(CEES))

################ DEPS RULE #############
DEPS = $(patsubst %.c,%.D,$(wildcard *.c))

######################## PLATFORM DEFINITIONS #############
PLATFORM_CPP=

######################### MAKE DEPENDENCIES ###############
COMPILE.c= $(CC) $(CFLAGS) $(CPPFLAGS) $(PLATFORM_CPP) \
           $(CINC) $(SUBDIR_CPP) $(TARGET_ARCH) -c


$(TARGET)/%.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(PLATFORM_CPP) \
        $(CINC) $(SUBDIR_CPP) $(TARGET_ARCH) -c -o $@ $< 


# Common library rule

$(LIB_INST)/lib%.a: $(OBJS)
	$(LIBTOOL) cr $@ $(OBJS)
	ranlib $@

# dependency rule to make temp .D files from .c sources
# all the .D files are collected and appended to the
# appropriate Makefile when 'make depend' is run
# this rule is kept here to make sure it matches COMPILE.c
%.D: %.c
	$(CC) -MM -MG -MT $(TARGET)/$(patsubst %.c,%.o,$<) \
	-Wall -Wcomment $(CPPFLAGS) $(PLATFORM_CPP) $(CINC) \
	$(SUBDIR_CPP) $(TARGET_ARCH) -c $< > $@


################## MAKE DEPENDENCIES #####################
# following depend rule is the GNU version! Other versions TBD
depend: dependencies

dependencies: $(DEPS)
	@if [ ! -f Makefile ]; then \
	    echo "Error: Makefile missing!"; \
	    exit 1; \
	fi
	@rm -f dependencies
	@for i in $(DEPS); do \
	  if [ -f $$i ] ; then \
	    (cat $$i >> dependencies; echo "" >> dependencies) ; \
	  else \
	    (echo "*** Warning: Dependency file $i.D is missing! (Skipping...) ***"; \
	     echo "# Warning: Missing file $$i !!!") ; \
	  fi; \
	done
	@echo "" >> dependencies
# delete the .D files to force make depend to rebuild them each time
# that target is built
#	@rm -f $(DEPS)


test:

install:
	mkdir -p $(REAL_INST)
	install $(LIB_INST)/lib$(SUBDIR_NM).$(LIBSUFFIX) $(REAL_INST)

work:
ifdef YUMA_HOME
	mkdir -p $(WORK_INST)        
	install $(LIB_INST)/lib$(SUBDIR_NM).$(LIBSUFFIX) $(WORK_INST)
endif

sil_lib: $(LIB_INST)/lib$(SUBDIR_NM).$(LIBSUFFIX)

# this dummy rule keeps make from deleting the $(OBJS) as
# intermediate files
sil_dummy: dependencies $(OBJS)

clean:
	rm -f $(OBJS) $(LIB_INST)/lib$(SUBDIR_NM).*

superclean:
	rm -f *~  $(DEPS) dependencies $(OBJS) \
	$(LIB_INST)/lib$(SUBDIR_NM).*

ifndef MAC
$(LIB_INST)/lib$(SUBDIR_NM).so: $(OBJS)
	$(LINK) $(CFLAGS) $(LDFLAGS) -shared -rdynamic -Wl,-soname,lib$(SUBDIR_NM).so -o $@ $(OBJS) -L$(BASE_COMMON)/lib -l$(BASE_NAME)
else
$(LIB_INST)/lib$(SUBDIR_NM).so: $(OBJS)
	$(LINK) $(CFLAGS) $(LDFLAGS) -shared -bundle -std=gnu99 \
	-current_version 1.0 \
	-undefined dynamic_lookup \
	-o $@ -install_name lib$(SUBDIR_NM).so $(OBJS)
endif

code:
	yangdump format=h indent=4 module=$(SUBDIR_NM) output=$(SUBDIR_NM).h
	yangdump format=c indent=4 module=$(SUBDIR_NM) output=$(SUBDIR_NM).c

splitcode:
	yangdump format=yh indent=4 module=$(SUBDIR_NM) output=y_$(SUBDIR_NM).h
	yangdump format=yc indent=4 module=$(SUBDIR_NM) output=y_$(SUBDIR_NM).c
	yangdump format=uh indent=4 module=$(SUBDIR_NM) output=u_$(SUBDIR_NM).h
	yangdump format=uc indent=4 module=$(SUBDIR_NM) output=u_$(SUBDIR_NM).c


.PHONY: all superclean clean test install uninstall \
        distclean depend lint code splitcode work sil_lib sil_dummy


# prevent the make program from choking on all the symbols
# that get generated from autogenerated make rules
.NOEXPORT:

include dependencies
