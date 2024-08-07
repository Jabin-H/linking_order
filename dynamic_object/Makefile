all: main

libadd.so: add.c
	gcc -fpic -shared $^ -o $@

libsquare.so: square.c
	gcc -fpic -shared $^ -o $@

libdummy.so: dummy.c
	gcc -fpic -shared $^ -o $@

main: main.c libadd.so libsquare.so libdummy.so
	gcc -L. -o $@ main.c -lsquare -ladd -ldummy

main1: main.c libadd.so libsquare.so libdummy.so
	gcc -L. -o $@ main.c -ladd -ldummy -lsquare

main2: main.c libadd.so libsquare.so libdummy.so
	gcc -L. -o $@ -ladd -lsquare -ldummy main.c

main_needed: main.c libadd.so libsquare.so libdummy.so
	gcc -Wl,-as-needed -o $@ -L. -ladd -lsquare -ldummy main.c

main_needed1: main.c libadd.so libsquare.so libdummy.so
	gcc -Wl,-as-needed -o $@ -L. main.c -lsquare -ladd -ldummy

clean:
	rm -rf *.so main main_needed*