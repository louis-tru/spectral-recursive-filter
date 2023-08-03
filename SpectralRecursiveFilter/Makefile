# This is a -*- Makefile -*- 
# Warning: this file contains tabs which cannot be converted to spaces

-include config$(CONFIG).mk

CXX			?=	$(CROSS)g++
LINK		?=	$(CXX)

# but here's a reasonable default value
# add -ftree-vectorizer-verbose=3 to get information about vectorized loops
OPENCV_CPPFLAGS = $(shell pkg-config opencv4 --cflags)
OPENCV_LDFLAGS = $(shell pkg-config opencv4 --libs-only-L)
OPENCV_LIBS = $(shell pkg-config opencv4 --libs-only-l) $(shell pkg-config opencv4 --libs-only-other)

# CFLAGS= -O3 -Wall -DCPU_BASELINE=NEON
CFLAGS= -Wall
CXXFLAGS=$(OPENCV_CPPFLAGS) $(CFLAGS) -std=c++11
LDFLAGS=$(OPENCV_LDFLAGS)

# for gcov profiling add:
#-fprofile-arcs -ftest-coverage

COMPILE.cpp=$(CXX) -c $(CXXFLAGS)

PROGRAMS = srf

.PHONY: all

all: $(PROGRAMS)

# for each program, give list of sources and required libs
HEADERS = \
	convolution.hpp \
	spectral_recursive_filter.hpp
SOURCES = \
	main.cpp
OBJS = $(SOURCES:.cpp=.o)
LIBS = $(OPENCV_LIBS)

# link command for each program
srf: $(OBJS)
	$(LINK) -o $@ $^ $(LDFLAGS) $(LIBS)

.SUFFIXES: .o .cpp

## gcc-only version:

%.o : %.cpp
	$(COMPILE.cpp) -MD -o $@ $<
	@cp $*.d $*.P; \
	    sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\$$//' \
	        -e '/^$$/ d' -e 's/$$/ :/' < $*.d >> $*.P; \
	    rm -f $*.d

## general version:

# %.o : %.cpp
# 	@$(MAKEDEPEND); \
# 	    cp $*.d $*.P; \
# 	    sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\$$//' \
# 		-e '/^$$/ d' -e 's/$$/ :/' < $*.d >> $*.P; \
# 	    rm -f $*.d
# 	$(COMPILE.cpp) -o $@ $<

.PHONY: clean distclean
clean:
	-rm -f $(PROGRAMS) *.o  *~
	-rm -rf *.dSYM

distclean: clean
	-rm -f $(SOURCES:.cpp=.P)

count:
	wc -l $(SOURCES) $(HEADERS)

-include $(SOURCES:.cpp=.P)
