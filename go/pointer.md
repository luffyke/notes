## 定义
所谓指针其实你可以把它想像成一个箭头，这个箭头指向（存储）一个变量的地址。

因为这个箭头本身也需要变量来存储，所以也叫做指针变量。

Go的指针就表示一个变量的地址。看看这个例子：
```
package main

import (
    "fmt"
)

func main() {
    var i int
    var pointer *int
    i = 10
    pointer = &i
    fmt.Println(i)
    fmt.Println(pointer)
    fmt.Println(*pointer)
}
```
输出
```
10
0xc420058190
10
```
pointer输出就是i的地址（0xc420058190，一个16进制的值），*pointer输出就是这个地址指向的值（就是10）。

& 取一个变量的地址
\* 取一个指针变量所指向的地址的值

而且*&这两个运算符在一起就相互抵消作用了。

## 用途
Go的变量传递都是值传递，指针的一大用途就是可以将变量的指针作为实参传递给函数，从而在函数内部能够直接修改实参所指向的变量值。

```
package main

import (
    "fmt"
)

func change(x *int) {
    *x = 20
}
func main() {
    var x int = 10
    fmt.Println(x)
    change(&x)
    fmt.Println(x)
}
```
输出
```
10
20
```
