TARGET=../bin
LIB_INST=../lib

YUMA_HOME = /home/compila/app/poc2-md/open-yuma/netconf

CINC = -I../include \
		-I../../MicrowaveModel-ObjectClasses-AirInterface/src \
		-I../../MicrowaveModel-ObjectClasses-PureEthernetStructure/src \
		-I../../MicrowaveModel-ObjectClasses-EthernetContainer/src \
		-I../../CoreModel-CoreNetworkModule-ObjectClasses/src \
		-I../../DVM_utils/src \
		-I$(YUMA_HOME)/src/agt -I$(YUMA_HOME)/src/ncx \
		-I/usr/include -I/usr/include/libxml2 \
     	-I/usr/include/libxml2/libxml \
     	-I/usr/include/yuma/platform

CWARN=-Wall -Wno-long-long -Wformat-y2k -Winit-self \
	-Wswitch-default \
	-Wextra -Wundef -Wshadow -Wpointer-arith \
	-Wwrite-strings -Wbad-function-cast -Wcast-qual -Wcast-align \
	-Waggregate-return -Wstrict-prototypes -Wold-style-definition \
	-Wmissing-prototypes -Wmissing-declarations \
	-Wpacked -Winvalid-pch \
	-Wredundant-decls -Wnested-externs -Winline -std=gnu99 $(WERROR)

CDEFS=-DDEBUG=1 -DLINUX=1 -DGCC=1
CFLAGS += $(CDEFS) $(CWARN)
CFLAGS += -fPIC -ggdb3

SUBDIR_NM=Default_values_mediator_utils
LIBSUFFIX=a

CC?=gcc
LINK?=$(CC)
LINT=splint
LINTFLAGS= '-weak -macrovarprefix "m_"'
LIBTOOL=ar
LFLAGS=-lm
LPATH=-L$(LIB_INST)

CEES = $(wildcard *.c)
HEES = $(wildcard *.h)
OBJS = $(patsubst %.c,$(TARGET)/%.o,$(CEES))


#################### MAKE RULES ########################

all: lib

######################### MAKE DEPENDENCIES ###############
COMPILE.c= $(CC) $(CFLAGS)\
           $(CINC) -c

$(TARGET)/%.o: %.c
	$(CC) $(CFLAGS) \
        $(CINC) -c -o $@ $< 

# Common library rule

$(LIB_INST)/lib%.a: $(OBJS)
	$(LIBTOOL) cr $@ $(OBJS)
	ranlib $@


lib: $(LIB_INST)/lib$(SUBDIR_NM).$(LIBSUFFIX)

clean:
	rm -f $(OBJS) $(LIB_INST)/lib$(SUBDIR_NM).* $(TARGET)/*.o


.PHONY: all clean


# prevent the make program from choking on all the symbols
# that get generated from autogenerated make rules
.NOEXPORT: