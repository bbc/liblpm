#
# This file is in the Public Domain.
#

PROJ=		lpm

SYSNAME:=	$(shell uname -s)
SYSARCH:=	$(shell uname -m)

CFLAGS+=	-std=c99 -O2 -g -Wall -Wextra -Werror
ifneq ($(SYSNAME), FreeBSD)
CFLAGS+=	-D_POSIX_C_SOURCE=200809L
endif
CFLAGS+=	-D_GNU_SOURCE -D_DEFAULT_SOURCE

#
# Extended warning flags.
#
CFLAGS+=	-Wno-unknown-warning-option # gcc vs clang

CFLAGS+=	-Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith
CFLAGS+=	-Wmissing-declarations -Wredundant-decls -Wnested-externs
CFLAGS+=	-Wshadow -Wcast-qual -Wcast-align -Wwrite-strings
CFLAGS+=	-Wold-style-definition
CFLAGS+=	-Wsuggest-attribute=noreturn -Wjump-misses-init
CFLAGS+=	-Wduplicated-cond -Wmisleading-indentation -Wnull-dereference
CFLAGS+=	-Wduplicated-branches -Wrestrict

ifeq ($(MAKECMDGOALS),tests)
DEBUG=		1
endif

ifeq ($(DEBUG),1)
CFLAGS+=	-Og -DDEBUG -fno-omit-frame-pointer
ifeq ($(SYSARCH),x86_64)
CFLAGS+=	-fsanitize=address -fsanitize=undefined
LDFLAGS+=	-fsanitize=address -fsanitize=undefined
endif
else
CFLAGS+=	-DNDEBUG
endif

# Build outside src/ so artifacts never end up in the source tarball.
OUTDIR?=	../build

# C library
INCS=		lpm.h
OBJS=		$(OUTDIR)/lpm.o
LIB=		liblpm

$(OUTDIR)/$(LIB).la:	LDFLAGS+=	-rpath $(LIBDIR) -version-info 1:0:0
install/%.la:	ILIBDIR=	$(DESTDIR)/$(LIBDIR)
install:	IINCDIR=	$(DESTDIR)/$(INCDIR)/
#install:	IMANDIR=	$(DESTDIR)/$(MANDIR)/man3/

obj: $(OBJS)

lib: $(OUTDIR)/$(LIB).la

$(OUTDIR):
	mkdir -p $@

$(OUTDIR)/%.o: %.c | $(OUTDIR)
	$(CC) $(CFLAGS) -c $< -o $@

$(OUTDIR)/%.lo: %.c | $(OUTDIR)
	libtool --mode=compile --tag CC $(CC) $(CFLAGS) -fPIC -c $< -o $@

$(OUTDIR)/$(LIB).la: $(OUTDIR)/lpm.lo
	libtool --mode=link --tag CC $(CC) $(LDFLAGS) -o $@ $^

install/%.la: $(OUTDIR)/%.la
	mkdir -p $(ILIBDIR)
	libtool --mode=install install -c $< $(ILIBDIR)/$(notdir $@)

install: $(addprefix install/,$(LIB).la)
	libtool --mode=finish $(LIBDIR)
	mkdir -p $(IINCDIR) && install -c $(INCS) $(IINCDIR)
	#mkdir -p $(IMANDIR) && install -c $(MANS) $(IMANDIR)

tests: $(OBJS) $(OUTDIR)/t_$(PROJ).o
	$(CC) $(CFLAGS) $^ -o $(OUTDIR)/t_$(PROJ)
	MALLOC_CHECK_=3 $(OUTDIR)/t_$(PROJ)

clean:
	rm -rf $(OUTDIR) .libs *.so *.o *.lo *.la t_$(PROJ)

.PHONY: all obj lib install tests clean
