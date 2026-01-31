# Minimal, explicit Makefile for building host 'adb'
# Usage:
#   cd adb && make
# Overrides: CXX, CXXFLAGS, EXTRA_INCLUDES, EXTRA_LIBS, EXTRA_LDFLAGS

CXX ?= c++
RM  ?= rm -f
MKDIR_P ?= mkdir -p

# Defaults (override from environment)
CXXFLAGS ?= -std=gnu++17 -O2 -g -Wall -Wextra -Wno-deprecated-declarations
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

PKG_CFLAGS := $(shell pkg-config --cflags libusb-1.0 2>/dev/null || echo)
PKG_LIBS  := $(shell pkg-config --libs libusb-1.0 2>/dev/null || echo)

INCLUDES ?= -I. -I.. -I../adf/libadf/include $(PKG_CFLAGS)
LIBS ?= -lpthread -lcrypto -lssl $(PKG_LIBS)

# Explicit list of sources (surgically selected — no find/scan)
SRCS := \
    ../adb.cpp \
    ../adb_io.cpp \
    ../adb_listeners.cpp \
    ../adb_trace.cpp \
    ../adb_utils.cpp \
    ../fdevent.cpp \
    ../sockets.cpp \
    ../socket_spec.cpp \
    ../sysdeps/errno.cpp \
    ../transport.cpp \
    ../transport_local.cpp \
    ../transport_usb.cpp \
    ../sysdeps_unix.cpp \
    ../sysdeps/posix/network.cpp \
    ../client/usb_dispatch.cpp \
    ../client/usb_libusb.cpp \
    ../client/usb_linux.cpp

OBJS := $(patsubst ../%.cpp,obj/%.o,$(SRCS))

TARGET := adb

.PHONY: all clean

all: $(TARGET)

# Link
$(TARGET): $(OBJS)
	@echo "Linking $@"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -o $@ $^ $(EXTRA_LDFLAGS) $(LIBS)

# Compile (sources live in repo root: ../)
obj/%.o: ../%.cpp
	@$(MKDIR_P) $(dir $@)
	@echo "CXX $<"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -c $< -o $@

clean:
	$(RM) $(TARGET)
	$(RM) -r obj