# GO语言基础

## go语言的诞生
2007 年 9 月 20 日的下午，在谷歌山景城总部的一间办公室里，谷歌的大佬级程序员 Rob Pike在等待一个 C++ 项目构建的过程中和谷歌的另外两个大佬级程序员 Robert Griesemer和 Ken Thompson进行了一次有关设计一门新编程语言的讨论。

![](https://img1.sycdn.imooc.com/5f51b6dd0001e4ef06280355.png)

Go语言之父(从左到右分别是Robert Griesemer、Rob Pike和Ken Thompson)

当时的谷歌内部主要使用 C++ 语言构建各种系统，但 C++ 的巨大复杂性、编译构建速度慢以及在编写服务端程序时对并发支持的不便等让三位大佬产生了重新设计一门新编程语言的想法。在他们的初步构想中，这门新语言应该是能够给程序员带来快乐、匹配未来硬件发展趋势并适合用来开发谷歌内部大规模程序的。

2009 年 11 月 10 日，Go 语言正式对外发布并开源。

Go语言具有支持高并发的特性，可以很方便地实现多线程运算，充分利用多核心 cpu 的性能。

众所周知服务器的处理器大都是单核频率较低而核心数较多，对于支持高并发的程序语言，可以充分利用服务器的多核优势，从而降低单核压力，减少性能浪费。

## go语言有什么特点？
1. 高性能、并发
2. 语法简单、学习曲线平缓
3. 丰富的标准库
4. 完善的工具链
5. 静态链接
6. 快速编译
7. 跨平台交叉编译
8. 垃圾回收
9. cgo支持
   
## 简单例子
**使用go语言简单实现一个web服务**
``` go
package main

import (
 "net/http"
)

func main() {
 http.Handle("/", http.FileServer(http.Dir(".")))
 http.ListenAndServe("localhost:8080", nil)
}
```

## 使用go的公司
国内:哔哩哔哩(Kratos)、字节跳动(KiteX)、七牛云(goproxy.cn).
国外:google(Kubernetes)、uber(zap)、IBM(fabric,istio)
开源项目:Docker、Kubernetes、Istio、Etcd、prometheus、influxdb、sealos

## 开发环境搭建-安装Golang
|                        https://go.dev                        |
| :----------------------------------------------------------: |
| ![](https://cdn.jsdelivr.net/gh/nateshao/images/20220507163032.png) |
|                   https://studygolang.com                    |
| ![](https://cdn.jsdelivr.net/gh/nateshao/images/20220507165640.png) |
|                     https://goproxy.cn/                      |
| ![](https://cdn.jsdelivr.net/gh/nateshao/images/20220507165655.png) |
|              [Go Packages](https://pkg.go.dev/)              |
|                 ![](./image/pkg.go.dev.png)                  |
|               https://github.com/voidint/g.git               |
|                      ![](./image/g.png)                      |

## 开发环境搭建-ide

|                      GoLand   开发环境                       |
| :----------------------------------------------------------: |
| ![](https://cdn.jsdelivr.net/gh/nateshao/images/20220507180156.png) |
|            **Visual Studio Code **  **开发环境**             |
| ![](https://cdn.jsdelivr.net/gh/nateshao/images/20220507180333.png) |

## vscode扩展推荐

|                 Go                 |
| :--------------------------------: |
|     ![](./image/vscode-go.png)     |
|      x86 and x86_64 Assembly       |
|   ![](./image/vscode-x86-64.png)   |
|            Code Runner             |
| ![](./image/vscode-coderunner.png) |

## go通用项目结构

### 最小标准布局

``` shell
// 在Go项目仓库根路径下

- go.mod
- LICENSE
- xx.go
- yy.go

// 或
- go.mod
- LICENSE
- package1
        - package1.go
- package2
        - package2.go
...
```

### 以构建二进制可执行文件为目的的Go项目布局

#### `/cmd`

项目的主干。main包入口

#### `/internal`

私有应用程序和库代码。这是你不希望其他人在其应用程序或库中导入代码。

#### `/pkg`

存放项目自身要使用并且同样也是可执行文件对应main包要依赖的库文件。该目录下的包可以被外部项目引用，算是项目导出包的一个聚合。

### 服务应用程序目录

#### `/api`

OpenAPI/Swagger 规范，JSON 模式文件，协议定义文件。

#### `/web`

静态文件目录

#### `/configs`

配置文件模板或默认配置

#### `/init`

系统初始化配置目录

#### `/build`

持续集成目录

#### `/test`

集成测试目录

#### `/examples`

项目使用示例目录



## Go命名规范

1. 包

   建议以小写形式的单词命名
