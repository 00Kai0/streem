YACC = bison -y -d
LEX = flex
CC = gcc
TOP = ..
TARGET = $(TOP)/bin/streem
CDEFS = -DNO_LOCKFREE_QUEUE
CFLAGS += -std=gnu99 -g -ggdb -Wall $(CDEFS)
LIBS = -lpthread -lm

ifeq (Windows_NT,$(OS))
TARGET:=$(TARGET).exe
LIBS += -lws2_32
endif

TESTS=$(wildcard $(TOP)/examples/*.strm)

SRCS=$(filter-out y.tab.c lex.yy.c, $(wildcard *.c))
OBJS:=$(SRCS:.c=.o) parse.o
DEPS:=$(OBJS:.o=.d)

all : $(TARGET)

.PHONY : all

test : all
	$(MAKE) -C $(TOP) $@

%.o: %.c
	$(CC) $(CFLAGS) -MMD -MP -c -o $@ $<

.PHONY : test

y.tab.c y.tab.h : parse.y lex.yy.c
	$(YACC) -o y.tab.c parse.y

lex.yy.c lex.yy.h : lex.l
	$(LEX) --header-file=lex.yy.h -olex.yy.c lex.l

parse.o : y.tab.c lex.yy.c
	$(CC) -g -MMD -MP -c y.tab.c -o parse.o

node.o : y.tab.h lex.yy.h

$(TARGET) : $(OBJS)
	mkdir -p "$$(dirname $(TARGET))"
	$(CC) $(CFLAGS) $(LDFLAGS) $(OBJS) -o $(TARGET) $(LIBS)

clean :
	rm -f y.output y.tab.c y.tab.h
	rm -f lex.yy.c lex.yy.h
	rm -f *.d *.o $(TARGET)
.PHONY : clean

-include $(DEPS)
