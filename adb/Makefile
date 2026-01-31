CXXFLAGS ?= -std=gnu++17 -g -Wall -Wextra -Wno-deprecated-declarations
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

INCLUDES := -I. -I.. -I../include -I../base/include -I../adf/libadf/include
LIBS := -lpthread -lcrypto -lssl -lusb-1.0
SRCS := \
    adb.cpp \
    adb_client.cpp \
    adb_io.cpp \
    adb_utils.cpp \
    adb_trace.cpp \
    fdevent.cpp \
    socket_spec.cpp \
    transport.cpp \
    transport_local.cpp \
    file_sync_client.cpp \
    console.cpp \
    commandline.cpp \
    line_printer.cpp \
    services.cpp \
    shell_service_protocol.cpp \
    adb_auth_host.cpp \
    transport_mdns.cpp \
    bugreport.cpp \
    adb_unique_fd.cpp \
    client/main.cpp \
    client/usb_dispatch.cpp \
    client/usb_libusb.cpp

OBJS := $(patsubst %.cpp,obj/%.o,$(patsubst %.c,obj/%.o,$(SRCS)))

TARGET := adb

.PHONY: all clean

all: $(TARGET)

$(TARGET): $(OBJS)
	@echo "Linking $@"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -o $@ $^ $(LIBS)

# Compile rules (create obj/ subdirs as needed)
obj/%.o: %.cpp
	@mkdir -p $(dir $@)
	@echo "CXX $<"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -c $< -o $@

obj/%.o: %.c
	@mkdir -p $(dir $@)
	@echo "CC $<"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -c $< -o $@

clean:
	@echo "Cleaning..."
	-rm -rf obj $(TARGET)
