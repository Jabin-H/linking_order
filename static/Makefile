all: libsquare.a libadd.a

add.o: add.c

libadd.a: add.o
	ar cr $@ $^

square.o: square.c

libsquare.a: square.o
	ar cr $@ $^


link_add_square_main:
	gcc -L. -ladd -lsquare main.c

link_square_add_main:
	gcc -L. -lsquare -ladd main.c

link_main_add_square: main.c
	gcc -L. $^ -ladd -lsquare
link_main_square_add: main.c
	gcc -L. $^ -lsquare -ladd

clean:
	rm *.a *.o