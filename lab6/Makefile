
LAB_NO=06
AUTHOR=GrzesikBartłomiej

FILES=\
	Makefile            	\
				\
	chat/chat.c		\
	chat/chat.h		\
	chat/client.c		\
	chat/server.c		\




all: executables
.PHONY: all executables

# =============================================================================

O ?= 3

CC := gcc
AR := ar
LD := $(CC)
GDB := gdb
CFLAGS := -Wall -c -g -I ./lib
OBJ := ./obj
LDFLAGS := -L $(OBJ)/ -lm -lrt


define solution

$(1)_obj := $(addprefix $(OBJ)/$(1).out/, $(2:%.c=%.o))
$(foreach src, $(2), 
$(addprefix $(OBJ)/$(1).out/, $(src:%.c=%.o)): $(src)
	    @mkdir -p $$(dir $$@)
	    @printf "%10s %s\n" CC $$<
	    @$(CC) $(CFLAGS) $$< -o $$@ $3
)

$(1): $(OBJ)/$(1)
$(OBJ)/$(1): $$($(1)_obj)
	@printf "%10s %s\n" LD $(1)
	@$$(LD) -o $$@ $$(LDFLAGS) $$($(1)_obj)


run_$(1): $(OBJ)/$(1)
	@printf "%10s %s\n" RUN $(1)
	@LD_LIBRARY_PATH=$(OBJ) $$< $$(ARGS)

gdb_$(1): $(OBJ)/$(1)
	@printf "%10s %s\n" GDB $(1)
	@LD_LIBRARY_PATH=./obj/ $(GDB) $$< $(ARGS)

valgrind_$(1): $(OBJ)/$(1)
	@printf "%10s %s\n" VALGRIND $(1)
	@LD_LIBRARY_PATH=./obj/ valgrind                                \
			--leak-check=full				\
			-q						\
			--trace-children=yes			        \
			$(OBJ)/$(1) $(ARGS)

callgrind_$(1): $(OBJ)/$(1)
	@printf "%10s %s\n" CALLGRIND $(1)
	@LD_LIBRARY_PATH=./obj/ valgrind 			        \
			--tool=callgrind                                \
			--callgrind-out-file=$(OBJ)/$(1).callgrind      \
			$$< $(ARGS)

	callgrind_annotate $(OBJ)/$(1).callgrind

executables: $(1)

.PHONY: $(1) run_$(1) gdb_$(1) valgrind_$(1) callgrind_$(1)

endef


# =============================================================================

$(eval $(call solution,zad1_server, chat/server.c chat/chat.c, -DUSE_SYSV_QUEUE))
$(eval $(call solution,zad1_client, chat/client.c chat/chat.c, -DUSE_SYSV_QUEUE))

$(eval $(call solution,zad2_server, chat/server.c chat/chat.c, -DUSE_POSIX_QUEUE))
$(eval $(call solution,zad2_client, chat/client.c chat/chat.c, -DUSE_POSIX_QUEUE))

# =============================================================================

.PHONY: clean
clean:
	@printf "%10s %s\n" RM $(ARCHIVE)
	@rm -f $(ARCHIVE)

	@printf "%10s %s\n" RM ./obj/
	@rm -rf ./obj/

# =============================================================================

ARCHIVE=$(AUTHOR)-cw$(LAB_NO).tar.gz
ARCHIVE_TOP=$(AUTHOR)/cw$(LAB_NO)
ARCHIVE_CONTENTS=$(addprefix $(ARCHIVE_TOP)/, $(FILES))

$(ARCHIVE_TOP)/%: %
	@printf "%10s %s\n" INSTALL $(@:$(ARCHIVE_TOP)/%=%)
	@install -C -D $(@:$(ARCHIVE_TOP)/%=%) ./$@

$(ARCHIVE): $(ARCHIVE_CONTENTS)
	@printf "%10s %s\n" TAR $(ARCHIVE)
	@tar -czf $(ARCHIVE) $(AUTHOR)

	@printf "%10s %s\n" RM $(AUTHOR)
	@rm -rf $(AUTHOR)

.PHONY: achive
archive: $(ARCHIVE)
