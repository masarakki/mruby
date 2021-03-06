# makefile discription.
# basic build file for mruby executable

# project-specific macros
# extension of the executable-file is modifiable(.exe .out ...)
BASEDIR = ../../src
TARGET := ../../bin/mruby
LIB := ../../lib/ritevm
ifeq ($(OS),Windows_NT)
EXE := $(TARGET).exe
LIBR := $(LIB).lib
else
EXE := $(TARGET)
LIBR := $(LIB).a
endif
OBJ0 := $(patsubst %.c,%.o,$(wildcard $(BASEDIR)/../tools/mruby/*.c))
#OBJ1 := $(patsubst %.c,%.o,$(filter-out $(EXCEPT1),$(wildcard $(BASEDIR)/*.c)))
#OBJ2 := $(patsubst %.c,%.o,$(wildcard $(BASEDIR)/ext/regex/*.c))
#OBJ3 := $(patsubst %.c,%.o,$(wildcard $(BASEDIR)/ext/enc/*.c))
OBJS := $(OBJ0)

# mruby libraries
EXTC := $(BASEDIR)/../mrblib/mrblib.c
EXTRB := $(wildcard $(BASEDIR)/../mrblib/*.rb)
EXTM := $(patsubst %.c,%.o,$(EXTC))
# ext libraries
#EXT1 := $(patsubst %.c,%.o,$(wildcard $(BASEDIR)/../ext/socket/*.c))
EXTS := $(EXT1)

# libraries, includes
LIBS = -lm
INCLUDES = -I$(BASEDIR) -I$(BASEDIR)/../include

# compiler, linker (gcc)
CC = gcc
LL = gcc
YACC = bison
DEBUG_MODE = 1
ifeq ($(DEBUG_MODE),1)
CFLAGS = -g -O3
else
CFLAGS = -O3
endif
ALL_CFLAGS = -Wall -Werror-implicit-function-declaration $(CFLAGS)
ifeq ($(OS),Windows_NT)
  MAKE_FLAGS = CC=$(CC) LL=$(LL) ALL_CFLAGS="$(ALL_CFLAGS)"
else
  MAKE_FLAGS = CC='$(CC)' LL='$(LL)' ALL_CFLAGS='$(ALL_CFLAGS)'
endif

##############################
# generic build targets, rules

.PHONY : all
all : $(LIBR) $(EXE)
	@echo "make: built targets of `pwd`"

# executable constructed using linker from object files
$(EXE) : $(LIBR) $(OBJS) $(EXTM) $(EXTS)
	$(LL) -o $@ $(CFLAGS) $(OBJS) $(EXTM) $(LIBR) $(EXTS) $(LIBS)

-include $(OBJS:.o=.d)

# objects compiled from source
$(OBJS) : %.o : %.c
	$(CC) $(ALL_CFLAGS) -MMD $(INCLUDES) -c $< -o $@

# C library compile
$(LIBR) :
	@$(MAKE) -C $(BASEDIR) $(MAKE_FLAGS)

# mruby library compile
$(EXTM) : $(EXTRB) $(LIBR)
	@$(MAKE) -C ../mrbc $(MAKE_FLAGS)
	@$(MAKE) -C ../../mrblib $(MAKE_FLAGS)

# extend libraries complile
$(EXTS) : %.o : %.c
	$(CC) $(ALL_CFLAGS) -MMD $(INCLUDES) -c $< -o $@

# clean up
.PHONY : clean #cleandep
clean :
	$(MAKE) clean -C ../../mrblib $(MAKE_FLAGS)
	$(MAKE) clean -C ../mrbc $(MAKE_FLAGS)
	@echo "make: removing targets, objects and depend files of `pwd`"
	-rm -f $(EXE) $(OBJS)
	-rm -f $(OBJS:.o=.d)
