CXXFLAGS ?= -std=gnu++17 -g -Wall -Wextra -Wno-deprecated-declarations
CXXFLAGS += -DADB_HOST=1 -D_GNU_SOURCE

INCLUDES := -I. -I.. -I../base/include -I../adf/libadf/include
LIBS := -lpthread -lcrypto -lssl -lusb-1.0

SRCS := $(wildcard *.cpp */*.cpp */*/*.cpp)
SRCS := $(filter-out daemon/% sysdeps/win32/% development/% tests/% out/%, $(SRCS))

OBJS := $(patsubst %.cpp,obj/%.o,$(patsubst %.c,obj/%.o,$(SRCS)))

TARGET := adb

.PHONY: all clean

all: $(TARGET)

# Link all objects into the adb binary
$(TARGET): $(OBJS)
	@echo "Linking $@"
	$(CXX) $(CXXFLAGS) $(INCLUDES) -o $@ $^ $(LIBS)

# Compile rules (objects are created under obj/ preserving directory structure)
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
