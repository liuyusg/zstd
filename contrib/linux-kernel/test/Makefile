
IFLAGS := -isystem include/ -I ../include/ -I ../lib/zstd/ -isystem googletest/googletest/include -isystem ../../../lib/common/

SOURCES := $(wildcard ../lib/zstd/*.c)
OBJECTS := $(patsubst %.c,%.o,$(SOURCES))

ARFLAGS := rcs
CXXFLAGS += -std=c++11
CFLAGS += -g -O0
CPPFLAGS += $(IFLAGS)

../lib/zstd/libzstd.a: $(OBJECTS)
	$(AR) $(ARFLAGS) $@ $^

DecompressCrash: DecompressCrash.o $(OBJECTS) libFuzzer.a
	$(CXX) $(TEST_CPPFLAGS) $(TEST_CXXFLAGS) $(LDFLAGS) $^ -o $@

RoundTripCrash: RoundTripCrash.o $(OBJECTS) ../lib/xxhash.o libFuzzer.a
	$(CXX) $(TEST_CPPFLAGS) $(TEST_CXXFLAGS) $(LDFLAGS) $^ -o $@

UserlandTest: UserlandTest.cpp ../lib/zstd/libzstd.a ../lib/xxhash.o
	$(CXX) $(CXXFLAGS) $(CFLAGS) $(CPPFLAGS) $^ googletest/build/googlemock/gtest/libgtest.a googletest/build/googlemock/gtest/libgtest_main.a -o $@

XXHashUserlandTest: XXHashUserlandTest.cpp ../lib/xxhash.o ../../../lib/common/xxhash.o
	$(CXX) $(CXXFLAGS) $(CFLAGS) $(CPPFLAGS) $^ googletest/build/googlemock/gtest/libgtest.a googletest/build/googlemock/gtest/libgtest_main.a -o $@

# Install libfuzzer
libFuzzer.a:
	@$(RM) -rf Fuzzer
	@git clone https://chromium.googlesource.com/chromium/llvm-project/llvm/lib/Fuzzer
	@./Fuzzer/build.sh

# Install googletest
.PHONY: googletest
googletest:
	@$(RM) -rf googletest
	@git clone https://github.com/google/googletest
	@mkdir -p googletest/build
	@cd googletest/build && cmake .. && $(MAKE)

clean:
	$(RM) -f *.{o,a} ../lib/zstd/*.{o,a}
	$(RM) -f DecompressCrash RoundTripCrash UserlandTest XXHashUserlandTest
