CXXFLAGS ?= -std=gnu++17 -g -Wall -Wextra -Wno-deprecated-declarations
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

# Map C11 atomic identifiers and nullability annotations to C++ equivalents
# so Android C headers compile cleanly under C++ on OpenBSD.
# - atomic_bool -> std::atomic_bool
# - atomic_load_explicit/atomic_store_explicit -> std::atomic_load_explicit / std::atomic_store_explicit
# - memory_order_* -> std::memory_order_*
# - _Nonnull / _Nullable annotations removed
CXXFLAGS += \
  -Datomic_bool=std::atomic_bool \
  -Datomic_int=std::atomic_int \
  -Datomic_uint=std::atomic_uint \
  -Datomic_load_explicit=std::atomic_load_explicit \
  -Datomic_store_explicit=std::atomic_store_explicit \
  -Dmemory_order_relaxed=std::memory_order_relaxed \
  -Dmemory_order_consume=std::memory_order_consume \
  -Dmemory_order_acquire=std::memory_order_acquire \
  -Dmemory_order_release=std::memory_order_release \
  -Dmemory_order_acq_rel=std::memory_order_acq_rel \
  -Dmemory_order_seq_cst=std::memory_order_seq_cst \
  -D_Nonnull= \
  -D_Nullable=

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
