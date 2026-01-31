# Classic Makefile — attempt to build host 'adb' from this tree
# Usage:
#   make                # build 'adb'
#   make -j4            # parallel build
#   make clean
#
# Common overrides:
#   make EXTRA_INCLUDES="-I/some/path/include" EXTRA_LIBS="-L/some/path/lib -lfoo"
#   or export EXTRA_CXXFLAGS / EXTRA_LDFLAGS before invoking make

EXTRA_INCLUDES="../base/include"

CXX ?= g++
AR  ?= ar
RM  ?= rm -f
NPROC ?= $(shell nproc 2>/dev/null || echo 2)

CXXFLAGS ?= -std=gnu++17 -O2 -g -Wall -Wextra -Wno-deprecated-declarations
# Build for host paths in source
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

# Hooks for the user to provide additional includes / flags
EXTRA_INCLUDES ?=
EXTRA_CXXFLAGS ?=
EXTRA_LDFLAGS ?=
EXTRA_LIBS ?=

# Typical system libraries that adb expects on Linux. Adjust as needed.
LIBS ?= -lpthread -lcrypto -lssl $(EXTRA_LIBS)

# Search for all .cpp and .c files under the tree (recursively)
SRCS := $(shell find . -name '*.cpp' -o -name '*.c' 2>/dev/null | sed 's|^\./||' | sort)

# Convert source filenames to object filenames
OBJS := $(patsubst %.cpp,%.o,$(patsubst %.c,%.o,$(SRCS)))

# final targets
TARGET := adb
STATIC_LIB := libadb.a

# include flags
INCLUDES := -I. $(EXTRA_INCLUDES)

.PHONY: all clean distclean install

all: $(TARGET)

# Archive a static library from all objects (useful if you want to link selectively)
$(STATIC_LIB): $(OBJS)
	@echo "ar rcs $@ (objects: $(words $(OBJS)))"
	$(AR) rcs $@ $(OBJS)

# Link the adb executable from the static library (and system libs)
$(TARGET): $(STATIC_LIB)
	@echo "Linking $@"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -o $@ $^ $(EXTRA_LDFLAGS) $(LIBS)

# Generic rules to compile .cpp and .c to .o
# Uses the same directory layout for objects as sources (object next to source).
# If desired, you can change to an out-of-tree objdir.
%.o: %.cpp
	@mkdir -p $(dir $@)
	@echo "CXX $<"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -c $< -o $@

%.o: %.c
	@mkdir -p $(dir $@)
	@echo "CC $<"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -c $< -o $@

clean:
	@echo "Cleaning objects and binaries..."
	$(RM) $(OBJS) $(STATIC_LIB) $(TARGET)

distclean: clean
	@echo "distclean (no extra artifacts to remove)"

install: $(TARGET)
	@echo "Installing $(TARGET) to /usr/local/bin (use INSTALL_PREFIX to change)"
	install -d $(DESTDIR)/usr/local/bin
	install -m 0755 $(TARGET) $(DESTDIR)/usr/local/bin/$(TARGET)

# Help target
help:
	@printf "Makefile targets:\n"
	@printf "  make           Build $(TARGET)\n"
	@printf "  make -jN       Parallel build\n"
	@printf "  make clean     Remove .o and binaries\n"
	@printf "Environment / make variables you can set:\n"
	@printf "  EXTRA_INCLUDES  - additional -I paths\n"
	@printf "  EXTRA_CXXFLAGS  - extra compiler flags\n"
	@printf "  EXTRA_LDFLAGS   - extra linker flags\n"
	@printf "  EXTRA_LIBS      - extra library flags (-L and -l)\n"
