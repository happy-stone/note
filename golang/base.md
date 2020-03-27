## 基本类型 ##

#### 布尔值 ####

取值范围：true、false

默认值： false

长度：1 字节

注意： true、false 不是关键字，下面的程序可以运行

```go
package main

import "fmt"

func main() {
	true := 1
	false := 2
	fmt.Printf("Hello, %d, %d", true, false)
}
```

#### 数字类型 ####

| 类型       | 长度（字节） | 默认值   | 取值范围                                                     | 说明                                                  |
| :--------- | :----------- | -------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| byte       | 1            | 0        | [0 , 255]                                                    | uint8 的别名                                          |
| int        | 4/8          | 0        | 4:  [ -2147483648 , 2147483647]<br />8: [-9223372036854775808, 9223372036854775807] | 一般应该使用 int， 而不是 int32 / int64               |
| uint       | 4/8          | 0        | 4: [0, 4294967295]<br />8: [0, 18446744073709551615]         |                                                       |
| int8       | 1            | 0        | [-128, 127]                                                  |                                                       |
| uin8       | 1            | 0        | [0 , 255]                                                    |                                                       |
| int16      | 2            | 0        | [-32768, 32767]                                              |                                                       |
| uint16     | 2            | 0        | [0, 65535]                                                   |                                                       |
| int32      | 4            | 0        | [ -2147483648 , 2147483647]                                  |                                                       |
| uint32     | 4            | 0        | [0, 4294967295]                                              |                                                       |
| int64      | 8            | 0        | [-9223372036854775808, 9223372036854775807]                  |                                                       |
| uint64     | 8            | 0        | [0, 18446744073709551615]                                    |                                                       |
| rune       | 4            | 0        | [ -2147483648 , 2147483647]                                  | 1. int32 的别名      <br /> 2. 代表一个Unicode码      |
| float32    | 4            | 0        |                                                              | [IEEE 754](https://zh.wikipedia.org/zh-hans/IEEE_754) |
| float64    | 8            | 0        |                                                              |                                                       |
| complex64  | 8            | 0.0+0.0i |                                                              | 实部、虚部 都是 float32                               |
| complex128 | 16           | 0.0+0.0i |                                                              | 实部、虚部 都是 float64                               |
| uintptr    | 4/8          | 0        |                                                              | 指针类型，其实质是uint；<br />详细见 指针 部分        |

1. rune 类型

   `rune is an alias for int32 and is equivalent to int32 in all ways. It is used, by convention, to distinguish character values from integer values.`

   int32 的别名，通常用做 char 类型，和  int32 做区分。

   ```go
   package main
   
   import "fmt"
   
   func main() {
       s := "abc 汉字"
       for i, c := range s{
   		    fmt.Printf("%d %c %T\n", i, c, c)
       }
   }
   // output
   0 a int32
   1 b int32
   2 c int32
   3   int32
   4 汉 int32
   7 字 int32
   ```

#### [string](https://golang.org/pkg/builtin/#string) ####

​    `字符串是8位字节的所有字符串的集合，通常但不一定表示UTF-8编码的文本。字符串可以为空，但不能为空。字符串类型的值是不可变的。`

#### 数组 ####

​    数组是特定类型的元素组成的固定长度的序列。其类型由元素类型和长度决定的，所以 int[10] 和 int[20] 是不同类型的数组。数组通常用作 切片 的底层。

#### 切片 ####

​    切片是对数组的封装，通过指针确定在数组上的位置。 切片 可以 通过 len 来获取实际长度，cap 获取实际容量（底层数组的长度），当切片长度超过容量，就会分配新的数组。在创建数组时，指定合适的容量，可以减少复制数据的时间。切片也是

#### [指针]()

​    指针表示变量的 内存地址 。空指针 为 nil，但是实际输出值 为 0。当调用函数时，一般传递指针，来避免较大的值拷贝。但是不允许进行指针运算，

```go
package main

import "fmt"

func main() {
	i := 100
	p := &i
	fmt.Printf("%d %p %T\n", p, p)
	var p1 *int
	fmt.Printf("%d %T %t\n", p1, p1, p1 == nil)
}


// output
// 824633811096 0xc000016098 *int
// 0 *int true
```

#### 结构体 ####

​    结构图是字段的结合。定义是如下，`type name struct` 是对结构的别名。

```go
struct {
    a string
    b int
}
```

```go
package main

type A struct {
   a string
   b int
}

func main() {
   var a A
   var b struct {
      a string
      b int
   }
   println(a == b)
}

// output
// true
```



#### 映射 ####

键到值的映射， 键推荐使用 string 类型，值的类型，如果不确定，可以使用 interface{}，map 必须用 make 而不是 new 来创建，因为 new 出来的 nil 不能赋值。