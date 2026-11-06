#
# This file is in the Public Domain.
#

PROJ=		lpm

CFLAGS+=	-std=c99 -O2 -g -Wall -Wextra -Werror
CFLAGS+=	-D_POSIX_C_SOURCE=200809L
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

# New GCC 6/7 flags:
#CFLAGS+=	-Wduplicated-cond -Wmisleading-indentation -Wnull-dereference
#CFLAGS+=	-Wduplicated-branches -Wrestrict

ifeq ($(DEBUG),1)
CFLAGS+=	-Og -DDEBUG -fno-omit-frame-pointer
CFLAGS+=	-fsanitize=address
LDFLAGS+=	-fsanitize=address
else
CFLAGS+=	-DNDEBUG
endif

# Lua module
LUA_CFLAGS?=	$(shell pkg-config --cflags lua)
LUA_LDFLAGS?=	$(shell pkg-config --cflags --libs lua)
CFLAGS+=	$(LUA_CFLAGS) -I.. -fPIC
LDFLAGS+=	$(LUA_LDFLAGS)
# Build outside src/ so artifacts never end up in the source tarball.
OUTDIR?=	../../build/lua
LIBLPM_OUTDIR?=	../../build
LDFLAGS+=	-L$(LIBLPM_OUTDIR)/.libs -llpm

OBJS=		$(OUTDIR)/lpm_lua.o
LIB=		lpm

install:	LUA_ILIBDIR=	$(DESTDIR)/$(LUA_LIBDIR)/

obj: $(OBJS)

lib: $(OUTDIR)/$(LIB).so

$(OUTDIR):
	mkdir -p $@

$(OUTDIR)/%.o: %.c | $(OUTDIR)
	$(CC) $(CFLAGS) -c $< -o $@

$(OUTDIR)/$(LIB).so: $(OBJS)
	$(CC) $(LDFLAGS) -fPIC -shared -o $@ $^

install:
	mkdir -p $(LUA_ILIBDIR) && install -c $(OUTDIR)/lpm.so $(LUA_ILIBDIR)

clean:
	rm -rf $(OUTDIR) .libs *.so *.o *.lo *.la t_$(PROJ)

.PHONY: all obj lib install clean
