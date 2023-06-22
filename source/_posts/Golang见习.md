---
title: Golang见习
date: 2023-03-20 14:23:20
tags: Golang
categories: 语言
---

# Golang

[参考视频集](https://www.bilibili.com/video/BV1Pg41187AS/?spm_id_from=333.1007.top_right_bar_window_default_collection.content.click&vd_source=fa75ef5fbdcca2eda7172e1304b8da8e)

## 变量

### 变量名、函数名、常量名的大小写可以控制跨包访问权限

如果首字母大写，则可以被其他的包访问；如果首字母小写，则只能在本包中使用。

实际上不建议多用跨包（变量）访问？各包之间应尽量独立，以单独函数作为调用接口。

### 空值

在 Golang 中 数值类型 的零值为 0， 布尔类型 的零值（初始值）为 false， 字符串类型 的零值为空字符串 "" ，而指针、 切片 、映射、通道、 函数 和接口等引用类型的零值则是 nil。

```go
var v []int
fmt.Println(len(v), v, v == nil)
```

## 算术运算符

### 整除、取模

```go
fmt.Println("-10 % 3=", -10%3, "; -10 / 3=", -10/3)
/* 
整除的值是用去尾得到的
% 取模  等价公式： a%b=a-a/b*b
*/
```

### 自增、自减

++ 自增 加1操作，--自减，减1操作

go语言里，++，--操作非常简单，只能单独使用，不能参与到运算中去

go语言里，++，--只能在变量的后面，不能写在变量的前面 --a  ++a  错误写法

## 字符串

### 中文编码

GO语言中 在UTF-8编码下 中文占**3个字节**

```go
package main
import "fmt"
func main(){
        //定义一个字符串：
        var str string = "hello golang你好"
        //方式1：普通for循环：按照字节进行遍历输出的 （暂时先不使用中文）
        // for i := 0;i < len(str);i++ {//i:理解为字符串的下标
        // 	fmt.Printf("%c \n",str[i])
        // }
        //方式2：for range
        for i , value := range str {
                fmt.Printf("索引为：%d,具体的值为：%c \n",i,value)
        }
        //对str进行遍历，遍历的每个结果的索引值被i接收，每个结果的具体数值被value接收
        //遍历对字符进行遍历的
}
```

![img](/images/Golang见习.assets/309B49DC-B872-4BFA-B8D8-EB38A2AF1397.png)

## 循环结构

### 循环块标签

#### break

```go
package main
import "fmt"
func main(){
        //双重循环：
        label2:
        for i := 1; i <= 5; i++ {
                for j := 2; j <= 4; j++ {
                        fmt.Printf("i: %v, j: %v \n",i,j)
                        if i == 2 && j == 2 {
                                break label2   //结束指定标签对应的循环
                        }
                }
        }
        fmt.Println("-----ok")
}
// 如果那个标签没有使用到的话，那么标签不用加，否则报错：定义未使用
```

![img](/images/Golang见习.assets/img.png)

#### continue

```go
package main
import "fmt"
func main(){
        //双重循环：
        label:
        for i := 1; i <= 5; i++ {
                for j := 2; j <= 4; j++ {			
                        if i == 2 && j == 2 {
                                continue label
                        }
                        fmt.Printf("i: %v, j: %v \n",i,j)
                }
        }
        fmt.Println("-----ok")
}
```

![img](/images/Golang见习.assets/img1.png)

## 函数

### 函数重载

Golang中函数**不支持重载**

Golang中**支持可变参数**

```go
package main
import "fmt"
//定义一个函数，函数的参数为：可变参数 ...  参数的数量可变
//args...int 可以传入任意多个数量的int类型的数据  传入0个，1个，，，，n个
func test (args...int){
        //函数内部处理可变参数的时候，将可变参数当做切片来处理
        //遍历可变参数：
        for i := 0; i < len(args); i++ {
                fmt.Println(args[i])
        }
}
func main(){	
        test()
        fmt.Println("--------------------")
        test(3)
        fmt.Println("--------------------")
        test(37,58,39,59,47)
}
```

**基本数据类型和数组默认都是值传递**的，即进行值拷贝。在函数内修改，不会影响到原来的值。

在Go中，**函数也是一种数据类型**，可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变量可以对函数调用。

函数既然是一种数据类型，因此在Go中，函数可以作为形参，并且调用。（把函数本身当做一种数据类型）

### init函数

执行过程

![img](/images/Golang见习.assets/img2.png)

### defer函数

应用场景： 	

比如你想关闭某个使用的资源，在使用的时候直接随手defer，因为defer有延迟执行机制（函数执行完毕再执行defer压入栈的语句），所以你用完随手写了关闭，比较省心，省事

![image-20230421162553501](/images/Golang见习.assets/image-20230421162553501.png)

![image-20230421162647972](/images/Golang见习.assets/image-20230421162647972.png)

## 数组

### 初始化方式

```go
package main
import "fmt"
func main(){
        //第一种：
        var arr1 [3]int = [3]int{3,6,9}
        fmt.Println(arr1)
        //第二种：
        var arr2 = [3]int{1,4,7}
        fmt.Println(arr2)
        //第三种：
        var arr3 = [...]int{4,5,6,7}
        fmt.Println(arr3)
        //第四种：
        var arr4 = [...]int{2:66,0:33,1:99,3:88}
        fmt.Println(arr4)
}
```

### 数组传参

Go中数组属值类型，在默认情况下是值传递，因此会进行值拷贝。

![image-20230501180351019](/images/Golang见习.assets/image-20230501180351019.png)

如想在其它函数中，去修改原来的数组，可以使用引用传递（指针方式）。

![image-20230501180605124](/images/Golang见习.assets/image-20230501180605124.png)
