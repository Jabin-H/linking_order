all: main

libadd.so: add.c
	gcc -fpic -shared $^ -o $@

libsquare.so: square.c libadd.so
	gcc -fpic -shared $^ -o $@ -L. -ladd

main: main.c libsquare.so
	gcc -L. -o $@ -lsquare main.c

main1: main.c libsquare.so
	gcc -L. -o $@ main.c -lsquare

main_needed: main.c libsquare.so
	gcc -Wl,-as-needed -o $@ -L.-lsquare main.c

main_needed1: main.c libsquare.so
	gcc -Wl,-as-needed -o $@ -L. main.c -lsquare

clean:
	rm -rf *.so main main1 main_needed*