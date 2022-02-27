FLAGS    = -z execstack -fno-stack-protector
FLAGS_32 = -m32
TARGET   = stack-L1 stack-L1-dbg

L1 = 100
 
all: $(TARGET)

stack-L1: main.c
	gcc -DBUF_SIZE=$(L1) $(FLAGS) $(FLAGS_32) -o $@ main.c
	gcc -DBUF_SIZE=$(L1) $(FLAGS) $(FLAGS_32) -g -o $@-dbg main.c
	sudo chown root $@ && sudo chmod 4755 $@

clean:
	rm -f badfile $(TARGET) peda-session-stack*.txt .gdb_history
