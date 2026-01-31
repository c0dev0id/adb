# Minimal Makefile to build host 'adb' on OpenBSD (fixed to find sources in repo root)
# Usage:
#   cd adb && make
#   make clean
#
# Overrides:
#   CXX, CXXFLAGS, EXTRA_INCLUDES, EXTRA_LIBS, EXTRA_LDFLAGS
#
# Notes:
# - This builds the host adb only (ADB_HOST=1)
# - It searches for .c/.cpp files in the parent directory (repo root).
# - Excludes daemon/, sysdeps/win32/, development/, tests/, out/ and the adb/ dir itself.

CXX ?= c++
AR  ?= ar
RM  ?= rm -f
MKDIR_P ?= mkdir -p

CXXFLAGS ?= -std=gnu++17 -O2 -g -Wall -Wextra -Wno-deprecated-declarations
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

EXTRA_INCLUDES ?=
EXTRA_CXXFLAGS ?=
EXTRA_LDFLAGS ?=
EXTRA_LIBS ?=

PKG_CFLAGS := $(shell pkg-config --cflags libusb-1.0 2>/dev/null || echo)
PKG_LIBS  := $(shell pkg-config --libs libusb-1.0 2>/dev/null || echo)

# On OpenBSD, avoid -lrt
LIBS ?= -lpthread -lcrypto -lssl $(PKG_LIBS) $(EXTRA_LIBS)

# Include current directory (adb/) and repo root (..), plus any extras
INCLUDES := -I. -I.. $(PKG_CFLAGS) $(EXTRA_INCLUDES)

# Find sources in repo root (parent dir). Strip leading "../" for internal paths.
SRCS_RAW := $(shell find .. \( -name '*.c' -o -name '*.cpp' \) \
    -not -path '../daemon/*' \
    -not -path '../sysdeps/win32/*' \
    -not -path '../development/*' \
    -not -path '../tests/*' \
    -not -path '../out/*' \
    -not -path '../adb/*' 2>/dev/null | sort)

SRCS := $(patsubst ../%,%,$(SRCS_RAW))

# Map sources to object files under obj/
OBJS := $(patsubst %.cpp,obj/%.o,$(patsubst %.c,obj/%.o,$(SRCS)))

TARGET := adb
STATIC_LIB := obj/libadb.a

.PHONY: all clean distclean install

all: $(TARGET)

$(STATIC_LIB): $(OBJS)
	@echo "AR $@"
	@$(MKDIR_P) $(dir $@)
	$(AR) rcs $@ $(OBJS)

# Link the adb executable
$(TARGET): $(STATIC_LIB)
	@echo "Linking $@"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -o $@ $^ $(EXTRA_LDFLAGS) $(LIBS)

# Compile rules: sources are referenced relative to repo root (../)
obj/%.o: ../%.cpp
	@$(MKDIR_P) $(dir $@)
	@echo "CXX $<"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -c $< -o $@

obj/%.o: ../%.c
	@$(MKDIR_P) $(dir $@)
	@echo "CC $<"
	$(CXX) $(CXXFLAGS) $(EXTRA_CXXFLAGS) $(INCLUDES) -c $< -o $@

clean:
	@echo "Cleaning..."
	$(RM) -r obj $(TARGET)

distclean: clean

install: $(TARGET)
	@echo "Installing $(TARGET) to /usr/local/bin"
	install -d $(DESTDIR)/usr/local/bin
	install -m 0755 $(TARGET) $(DESTDIR)/usr/local/bin/$(TARGET)

help:
	@printf "Targets:\n"
	@printf "  make           Build $(TARGET)\n"
	@printf "  make clean\n\n"
	@printf "Environment/override variables:\n"
	@printf "  CXX, CXXFLAGS, EXTRA_INCLUDES, EXTRA_CXXFLAGS\n"
	@printf "  EXTRA_LDFLAGS, EXTRA_LIBS\n"
	@printf "\nExample to add vendor includes and libs:\n"
	@printf "  make EXTRA_INCLUDES='-I../base/include' EXTRA_LIBS='-L/path -lbase -lcutils -llog'\n"