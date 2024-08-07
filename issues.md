# linking order

运行环境：WSL Ubuntu 22.04.4

GCC 版本：gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0

ld 版本：GNU ld (GNU Binutils for Ubuntu) 2.38





问题1：

dynamic_object 中只有 `make main` 能正确生成可执行文件 main，

```bash
make main

gcc -fpic -shared add.c -o libadd.so
gcc -fpic -shared square.c -o libsquare.so
gcc -fpic -shared dummy.c -o libdummy.so
gcc -L. -o main main.c -lsquare -ladd -ldummy
```

`make main1` 和 `make main2` 均报错：

`make main1`:

```bash
make main1

gcc -L. -o main1 main.c -ladd -ldummy -lsquare
/usr/bin/ld: ./libsquare.so: undefined reference to `add'
collect2: error: ld returned 1 exit status
make: *** [Makefile:16: main1] Error 1
```

`make main2`:

```bash
make main2

gcc -L. -o main2 -ladd -lsquare -ldummy main.c
/usr/bin/ld: /tmp/ccQ8stIv.o: in function `main':
main.c:(.text+0x13): undefined reference to `square'
collect2: error: ld returned 1 exit status
make: *** [Makefile:19: main2] Error 1
```

似乎按照 `gcc -L. -o main main.c -lsquare -ladd -ldummy` 的链接命令格式依然必须是正确的依赖关系顺序才能链接？与视频 p5 24:56 的演示有出入。





问题2：

dynamic_object 中，查看 `make main` 生成的 main 文件的依赖关系如下：

```bash
ldd main

linux-vdso.so.1 (0x00007ffc771ba000)
libsquare.so => not found
libadd.so => not found
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9a3e173000)
/lib64/ld-linux-x86-64.so.2 (0x00007f9a3e3a7000)
```

查看 `make main_needed1` 生成的 main_needed1 文件的依赖关系：

```bash
ldd main_needed1

linux-vdso.so.1 (0x00007ffc0d7c3000)
libsquare.so => not found
libadd.so => not found
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd103e90000)
/lib64/ld-linux-x86-64.so.2 (0x00007fd1040c4000)
```

对比看到，在命令行加入 `-as-needed` 之前就没有 libdummy.so 的依赖关系了，与视频 p5 33:28 有出入。





问题3：

dynamic_lib 中，查看两个动态库文件的依赖：

```bash
ldd libsquare.so

linux-vdso.so.1 (0x00007ffc965ac000)
libadd.so => not found
```



```bash
ldd libadd.so

statically linked
```

均与视频 p5 37:15 处有出入， 



指定链接路径后， 即 `LD_LIBRARY_PATH=. make main` 依然报错：

```bash
LD_LIBRARY_PATH=. make main

gcc -L. -o main -lsquare main.c
/usr/bin/ld: /tmp/ccGz4uet.o: in function `main':
main.c:(.text+0x13): undefined reference to `square'
collect2: error: ld returned 1 exit status
make: *** [Makefile:10: main] Error 1
```

改为正确依赖顺序可以生成可执行文件 main1

```bash
LD_LIBRARY_PATH=. make main1

gcc -L. -o main1 main.c -lsquare
```

加入路径后，可以正确执行 main1

```bash
LD_LIBRARY_PATH=. ./main1

result 9
```

