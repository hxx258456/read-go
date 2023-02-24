# 你有仔细研究过go build命令吗？编译过程中生成的.a,.h,.o文件的作用？

管中窥豹，可见一斑

大多数语言的编译过程受到编译器架构的影响，基本都是相似的，但是如果你想精通一门语言，你只知道编译原理是不够的，你需要观察调试他的编译过程，甚至你可能需要重构编译器去进行尝试

以一个hello world程序为例，下面我们来探索go在进行编译时究竟会进行哪些工作

首先你需要知道一些go编译工具相关的知识点

1. go编译器分为两部分compile编译go文件
2. asm编译器编译汇编文件
3. link链接器
4. pack静态库生成，将.o文件打包成.a静态链接库

```shell
go run -x -work -a main.go
```

输入以上命令观察打印，从乱序打印结果可以看出go在编译过程中使用了并发编译

编译器前端递归检索AST中的import关键字，查找依赖包源码路径，并将其编译为.a文件，即静态依赖库

以go源码中的reflect包为例：

```bash
#
# reflect
#
reflect
mkdir -p $WORK\b016\ // 创建临时工作目录
cat >$WORK\b016\go_asm.h << 'EOF' # internal // 创建类型定义头文件.h
EOF
cd C:\Users\txhy\.g\go\src\reflect
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\asm.exe" -p reflect -trimpath "$WORK\\b016=>" -I "$WORK\\b016\\" -I "C:\\Users\\txhy\\.g\\go\\pkg\\include" -D GOOS_windows -D GOARCH_amd64 -compiling-runtime -
-o "$WORK\\b016\\symabis" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\asm_amd64.s" // 生成abi符号表
cat >$WORK\b016\importcfg << 'EOF' # internal // 依赖整理go
```

头文件的作用提取类型定义，提高代码复用率和可维护性。

我们重点关注asm汇编器的工作过程

- `-p reflect`：指定该文件所属的Go package为`reflect`。
- `-trimpath "$WORK\\b016=>" -I "$WORK\\b016\\"`：指定`$WORK\\b016`为工作目录，并添加该目录和`$WORK\\b016\\`到include路径中。
- `-I "C:\\Users\\txhy\\.g\\go\\pkg\\include"`：添加Go语言标准库头文件所在的目录到include路径中。
- `-D GOOS_windows -D GOARCH_amd64`：定义宏`GOOS`和`GOARCH`，分别表示编译目标平台为Windows和目标架构为AMD64。
- `-compiling-runtime`：指定编译器需要编译的是Go语言运行时函数。
- `-o "$WORK\\b016\\symabis"`：指定输出文件路径。
- `"C:\\Users\\txhy\\.g\\go\\src\\reflect\\asm_amd64.s"`：要编译的汇编代码文件。

该命令将`reflect/asm_amd64.s`文件编译成机器码，并生成一个ABI sym符号文件`symabis`，其中包含了`reflect/asm_amd64.s`文件中所定义的所有符号和对应的地址。这个符号文件会在链接阶段被用到，用于确定汇编代码中的符号在可执行文件中的地址。

```bash
# import config
packagefile internal/abi=$WORK\b008\_pkg_.a
packagefile internal/goexperiment=$WORK\b011\_pkg_.a
packagefile internal/itoa=$WORK\b017\_pkg_.a
packagefile internal/unsafeheader=$WORK\b005\_pkg_.a
packagefile math=$WORK\b018\_pkg_.a
packagefile runtime=$WORK\b007\_pkg_.a
packagefile strconv=$WORK\b020\_pkg_.a
packagefile sync=$WORK\b022\_pkg_.a
packagefile unicode=$WORK\b025\_pkg_.a
packagefile unicode/utf8=$WORK\b021\_pkg_.a
EOF
cd E:\studygo\hello
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\compile.exe" -o "$WORK\\b016\\_pkg_.a" -trimpath "$WORK\\b016=>" -p reflect -std -buildid L6aKv5TVgW8e4_lJnfln/L6aKv5TVgW8e4_lJnfln -goversion go1.17.5 -symabis "$WORK\\b016\\symabis" -importcfg "$WORK\\b016\\importcfg" -pack -asmhdr "$WORK\\b016\\go_asm.h" -c=4 "C:\\Users\\txhy\\.g\\go\\src\\reflect\\abi.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\deepequal.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\makefunc.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\swapper.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\type.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\value.go" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\visiblefields.go" // 编译go文件到.a
cd C:\Users\txhy\.g\go\src\reflect
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\asm.exe" -p reflect -trimpath "$WORK\\b016=>" -I "$WORK\\b016\\" -I "C:\\Users\\txhy\\.g\\go\\pkg\\include" -D GOOS_windows -D GOARCH_amd64 -compiling-runtime -o "$WORK\\b016\\asm_amd64.o" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\asm_amd64.s" // 编译.s文件到.o
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\pack.exe" r "$WORK\\b016\\_pkg_.a" "$WORK\\b016\\asm_amd64.o" # internal // 将.o文件打包成.a静态链接库
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\buildid.exe" -w "$WORK\\b016\\_pkg_.a" # internal

生成编译配置文件主要为程序中使用到的外部依赖。
部分示例:
cat >$WORK\b001\importcfg.link << 'EOF' # internal
packagefile command-line-arguments=$WORK\b001\_pkg_.a
packagefile fmt=$WORK\b002\_pkg_.a
packagefile runtime=$WORK\b007\_pkg_.a
packagefile errors=$WORK\b003\_pkg_.a
packagefile internal/fmtsort=$WORK\b015\_pkg_.a
packagefile io=$WORK\b027\_pkg_.a
packagefile math=$WORK\b018\_pkg_.a
packagefile os=$WORK\b028\_pkg_.a
packagefile reflect=$WORK\b016\_pkg_.a
packagefile strconv=$WORK\b020\_pkg_.a
packagefile sync=$WORK\b022\_pkg_.a
packagefile unicode/utf8=$WORK\b021\_pkg_.a
packagefile internal/abi=$WORK\b008\_pkg_.a
packagefile internal/bytealg=$WORK\b009\_pkg_.a
packagefile internal/cpu=$WORK\b010\_pkg_.a
packagefile internal/goexperiment=$WORK\b011\_pkg_.a
packagefile runtime/internal/atomic=$WORK\b012\_pkg_.a
packagefile runtime/internal/math=$WORK\b013\_pkg_.a
packagefile runtime/internal/sys=$WORK\b014\_pkg_.a
packagefile internal/reflectlite=$WORK\b004\_pkg_.a
packagefile sort=$WORK\b026\_pkg_.a
packagefile math/bits=$WORK\b019\_pkg_.a
packagefile internal/itoa=$WORK\b017\_pkg_.a
packagefile internal/oserror=$WORK\b029\_pkg_.a
packagefile internal/poll=$WORK\b030\_pkg_.a
packagefile internal/syscall/execenv=$WORK\b037\_pkg_.a
packagefile internal/syscall/windows=$WORK\b031\_pkg_.a
packagefile internal/testlog=$WORK\b038\_pkg_.a
packagefile internal/unsafeheader=$WORK\b005\_pkg_.a
packagefile io/fs=$WORK\b039\_pkg_.a
packagefile sync/atomic=$WORK\b024\_pkg_.a
packagefile syscall=$WORK\b033\_pkg_.a
packagefile time=$WORK\b035\_pkg_.a
packagefile unicode/utf16=$WORK\b034\_pkg_.a
packagefile unicode=$WORK\b025\_pkg_.a
packagefile internal/race=$WORK\b023\_pkg_.a
packagefile internal/syscall/windows/sysdll=$WORK\b032\_pkg_.a
packagefile path=$WORK\b040\_pkg_.a
packagefile internal/syscall/windows/registry=$WORK\b036\_pkg_.a
EOF
mkdir -p $WORK\b001\exe\
cd .
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\link.exe" -o "$WORK\\b001\\exe\\a.out.exe" -importcfg "$WORK\\b001\\importcfg.link" -buildmode=pie -buildid=0uSC1O8FPghyNfAIMJM1/R4RJU_oaaUMb-FdM3OD0/R4RJU_oaaUMb-FdM3OD0/0uSC1O8FPghyNfAIMJM1 -extld=gcc "$WORK\\b001\\_pkg_.a"
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\buildid.exe" -w "$WORK\\b001\\exe\\a.out.exe" # internal
mv $WORK\b001\exe\a.out.exe main.exe

```

