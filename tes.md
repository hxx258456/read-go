# go基础语法介绍

## 变量声明

```go
// 声明一个变量并初始化
var a string = "hello"

// 没有初始化就为零值
var a int

// 自动类型推导
var b = true

// 多变量声明
var c,d,e int = 1,2,3
var (
    test1 int
    test2 string
    test3 [3]int
)

// 空白标识符，忽略返回值
var _ := add(1,2)

// 局部变量，全局变量
```

## 常量声明

```go
const pi = 3.1415926

const (
	x int = 6
    y
)

const (
	man = iota
   	woman
)
```

## go语言的基础类型

- 整数

  int,int8,int16,int32,int64,uint,uint8,uint,16,uint32,uint64

- 浮点数

  float,float32,float64

  ```go
  const e = .71828 // 0.71828 可以只写小数部分
  const f = 1.     // 1 也可以只写整数部分
  const Planck   = 6.62606957e-34 // 普朗克常数（很小或很大的数最好用科学计数法书写，通过 e 或 E 来指定指数部分）
  
  var f float32 = 16777216 // 1 << 24
  fmt.Println(f == f+1)    // "true"!
  
  var f_ float64 = 16777216 // 1 << 24
  fmt.Println(f_ == f_+1)   // "false"!
  ```

- 复数

  complex,complex64,complex128

- bool类型

  true,false

- 字符串类型

  ```go
  var str="hello world"
  
  var str1 = `
  锄禾日当午，
  汗滴禾下土。
  `
  // 字符串不允许修改
  // str[0]='a'这种操作不允许
  ```

- 字符类型(byte&rune)

  ```go
  // go字符类型分为两种byte和rune
  // byte为ascii码
  // rune为utf8编码
  ```

- pointer

  ```go
  // 创建一个指针
  ptr := &v    // v 的类型为 T
  
  // 根据指针取值
  value := *ptr
  ```

## go语言类型别名

```go
package main
import (
    "fmt"
)
// 将NewInt定义为int类型
type NewInt int
// 将int取一个别名叫IntAlias
type IntAlias = int
func main() {
    // 将a声明为NewInt类型
    var a NewInt
    // 查看a的类型名
    fmt.Printf("a type: %T\n", a)
    // 将a2声明为IntAlias类型
    var a2 IntAlias
    // 查看a2的类型名
    fmt.Printf("a2 type: %T\n", a2)
}
/* 
原理
go语言runtime会为自定义类型和内置类型生成全局唯一类型元数据
在上述代码中IntAlias和int共用同一种类型元数据
NewInt则是基于int创建一种新的类型,拥有独立的类型元数据
*/
```

## go语言的容器类型

### 数组

var 数组变量名 [元素数量]Type

```go
[100]int
[200]interface{}
[...]{1,2,3}
[5]{1:2,3:4}
```

### 切片

var name []T

```go
var a = [3]int{1,2,3}
var b = a[start:end:step]

var c = []int{}
var d = make([]int,1,2)
```

### map

var mapname map[keyType]valueType

```go
package main
import "fmt"
func main() {
    var mapLit map[string]int
    //var mapCreated map[string]float32
    var mapAssigned map[string]int
    mapLit = map[string]int{"one": 1, "two": 2}
    mapCreated := make(map[string]float32)
    mapAssigned = mapLit
    mapCreated["key1"] = 4.5
    mapCreated["key2"] = 3.14159
    mapAssigned["two"] = 3
    fmt.Printf("Map literal at \"one\" is: %d\n", mapLit["one"])
    fmt.Printf("Map created at \"key2\" is: %f\n", mapCreated["key2"])
    fmt.Printf("Map assigned at \"two\" is: %d\n", mapLit["two"])
    fmt.Printf("Map literal at \"ten\" is: %d\n", mapLit["ten"])
}
```

### for...range遍历容器类型

```go
// 创建一个整型切片，并赋值
slice := []int{10, 20, 30, 40}
// 迭代每个元素，并显示值和地址
for index, value := range slice {
    fmt.Printf("Value: %d Value-Addr: %X ElemAddr: %X\n", value, &value, &slice[index])
}


// 创建一个整型切片，并赋值
slice := []int{10, 20, 30, 40}
// 迭代每个元素，并显示其值
for _, value := range slice {
    fmt.Printf("Value: %d\n", value)
}

// 创建一个整型切片，并赋值
slice := []int{10, 20, 30, 40}
// 从第三个元素开始迭代每个元素
for index := 2; index < len(slice); index++ {
    fmt.Printf("Index: %d Value: %d\n", index, slice[index])
}
```

### sync.Map

并发安全map

```go
var scene sync.Map
// 将键值对保存到sync.Map
scene.Store("greece", 97)
scene.Store("london", 100)
scene.Store("egypt", 200)
// 从sync.Map中根据键取值
fmt.Println(scene.Load("london"))

// 根据键删除对应的键值对
scene.Delete("london")
// 遍历所有sync.Map中的键值对
scene.Range(func(k, v interface{}) bool {
    fmt.Println("iterate:", k, v)
    return true
})
```

### go中的nil

从go语言源码中可知nil是一个Type类型变量

这里涉及到golang的内存分配原则，变量定义分配的内存一定是置0分配

本质上是一个值为0的变量用于与指针进行对比判断是否为0



## go语言流程控制

- 分支结构

  ```go
  if condition1 {
      // do something
  } else if condition2 {
      // do something else
  }else {
      // catch-all or default
  }
  
  if err := Connect(); err != nil {
      fmt.Println(err)
      return
  }
  ```

- 循环结构

  ```go
  sum := 0
  for i := 0; i < 10; i++ {
      sum += i
  }
  
  for {
      sum++
      if sum > 100 {
          break
      }
  }
  
  i:= 1
  for i <= 10 {
      i++
  }
  ```

  

- switch语句

## go语言的编译过程

go run -x -work -a main.go观察

编译过程

![](https://pic1.zhimg.com/80/v2-727f48ff8ab45505daa3970095bf94e0_720w.webp)

````
WORK=C:\Users\txhy\AppData\Local\Temp\go-build738707198 // 创建临时工作目录
// 从打印观察编译过程应该是并发过程
// 以库为单位进行分段编译
// 编译过程大致分为两中.go文件的编译和.s汇编文件的编译
// 以reflect包为例
#
# reflect
#

reflect
mkdir -p $WORK\b016\ // 创建工作目录
cat >$WORK\b016\go_asm.h << 'EOF' # internal // 创建类型定义头文件.h
EOF
cd C:\Users\txhy\.g\go\src\reflect
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\asm.exe" -p reflect -trimpath "$WORK\\b016=>" -I "$WORK\\b016\\" -I "C:\\Users\\txhy\\.g\\go\\pkg\\include" -D GOOS_windows -D GOARCH_amd64 -compiling-runtime -gensymabis -o "$WORK\\b016\\symabis" "C:\\Users\\txhy\\.g\\go\\src\\reflect\\asm_amd64.s" // 生成符号表?
cat >$WORK\b016\importcfg << 'EOF' # internal
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
"C:\\Users\\txhy\\.g\\go\\pkg\\tool\\windows_amd64\\pack.exe" r "$WORK\\b016\\_pkg_.a" "$WORK\\b016\\asm_amd64.o" # internal // 将.o文件添加到.a静态链接库
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

compile编译.go文件到.a 同时会将go
asm编译.s文件到.o
pack将.o文件打包成静态链接库.a文件

go编译器为了方便汇编中访问`struct`的指定字段，会在编译过程中自动生成一个`go_asm.h`文件，可以通过`#include "go_asm.h"`语言来引用，该文件中会生成该包内全部`struct`的每个字段的偏移量宏定义与结构体大小的宏定义，比如：

```go
type vdsoVersionKey struct {
	version string
	verHash uint32
}
```

会生成宏定义：
```c
#define vdsoVersionKey__size 24
#define vdsoVersionKey_version 0
#define vdsoVersionKey_verHash 16
```

在汇编代码中，我们就可以直接使用这些宏：
```asm
MOVQ vdsoVersionKey_version(DX) AX
MOVQ (vdsoVersionKey_version+vdsoVersionKey_verHash)(DX) AX
```

比如我们在`runtime`包中经常会看见一些代码就是如此：
```asm
    MOVQ    DX, m_vdsoPC(BX)
    LEAQ    ret+0(SP), DX
    MOVQ    DX, m_vdsoSP(BX)
```

我们可以通过命令`go tool compile -S -asmhdr dump.h *.go`来导出相关文件编译过程中会生成的宏定义。
````

链接器的工作原理

```
	链接器之所以存在或者产生，基本上是由于程序开发的模块化。这里讲的模块，主要是编译概念上的模块，通常他们按照功能划分，比如一个.c或者.cpp文件就是一个编译单元，就是一个模块，编译后就产生一个.o目标文件。为了最终生成一个可执行文件、静态库或者动态库，就需要把各个编译单元按照特定的约定组合到一起。这里特定的约定指的就是“目标文件格式”，它定义了目标文件、库文件和可执行文件的格式，这里组合这一过程就叫做链接。
　　一个编译模块中，通常是函数的定义和全局数据的定义，数据类型的定义通常在头文件中，编译时会被包含在编译模块中。函数和数据由符号来标识，一般符号有全局和静态之分，全局符号可以被其他模块引用，而静态符号只能在本模块中引用。编译各个模块时，编译器会解析该模块。重要的一项工作就是建立符号表，符号表中包含了本模块有哪些符号可以被其他模块引用（导出符号），还包括本模块引用（导入符号，即未定义符号）、但在其他模块中定义的符号。每一个符号都关联一个地址，这个地址指明了该符号在本模块中的偏移地址（通常是一个从0开始的地址）。
　　链接器在链接过程中，会扫描各个模块的符号表，得到一个“全局符号表”，链接器由此决定一个符号在哪里被定义，在哪里被引用。并且，将符号引用处替换为定义处的地址，这一过程就叫做符号解析。
　　链接器的一项终极目标就是生成可执行文件。通常，可执行文件和普通目标文件的重要区别就是地址空间的使用。主流操作系统中，可执行文件都是基于虚拟地址空间的，即每个可执行文件都有相同且独立的地址空间，并且文件中各个段（代码段，数据段，以及进程空间中的堆栈段）都有相似的布局。而普通目标文件却使用从零开始的地址空间，这样一来，模块M中的符号m就可能和模块N中的符号n拥有“相同”的地址。在链接器链接各个模块时，会从各个模块中“提取”类型相同的段进行合并，并将合并后的段写入可执行文件中。这一过程被称为存储空间的分配。值得一提的是，栈、堆以及未初始化的数据这些“运行时”需要的空间不会在可执行文件中占据磁盘空间，但它们占用相应的地址空间。
　　由于存在上述“合并”过程，前面提到的符号解析就涉及到另外一个过程：重定位。由于各个模块中的函数/数据地址会被重新排放，那么对这些符号的引用也必须被相应地调整。这一调整过程被称作重定位。
　　符号解析，存储空间分配，还有重定位，这三个过程是一个有机的整体，是“同时”进行的，且这三个过程也是模块化所带来的必须要解决的问题
```

