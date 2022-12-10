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

