1. = 和 := 的区别？
:= 声明+赋值\
= 仅赋值 

Q2 指针的作用？
指针用来保存变量的地址。

Q3 Go 允许多个返回值吗？
允许
func swap(x, y string) (string, string) {
   return y, x
}

Q4 Go 有异常类型吗？
Go 没有异常类型，只有错误类型（Error），通常使用返回值来表示异常状态。

Q5 什么是协程（Goroutine）
Goroutine 是与其他函数或方法同时运行的函数或方法。 Goroutines 可以被认为是轻量级的线程。
与线程相比，创建 Goroutine 的开销很小。 Go应用程序同时运行数千个 Goroutine 是非常常见的做法。

Q6 如何高效地拼接字符串?
Go 语言中，字符串是只读的，也就意味着每次修改操作都会创建一个新的字符串。如果需要拼接多次，应使用 strings.Builder，最小化内存拷贝次数。

Q7 什么是 rune 类型
ASCII 码只需要 7 bit 就可以完整地表示，但只能表示英文字母在内的128个字符，为了表示世界上大部分的文字系统，发明了 Unicode，
它是ASCII的超集，包含世界上书写系统中存在的所有字符，并为每个代码分配一个标准编号（称为Unicode CodePoint），在 Go 语言中称
之为 rune，是 int32 类型的别名。

Go 语言中，字符串的底层表示是 byte (8 bit) 序列，而非 rune (32 bit) 序列。例如下面的例子中 语 和 言 使用 UTF-8 编码后各
占 3 个 byte，因此 len("Go语言") 等于 8，当然我们也可以将字符串转换为 rune 序列。

Q8 如何判断 map 中是否包含某个 key ？
if val, ok := dict["foo"]; ok {
    //do something here
}

Q9 Go 支持默认参数或可选参数吗？
Go 语言不支持可选参数（python 支持），也不支持方法重载（java支持）。

Q10 defer 的执行顺序
多个 defer 语句，遵从后进先出(Last In First Out，LIFO)的原则，最后声明的 defer 语句，最先得到执行。
defer 在 return 语句之后执行，但在函数退出之前，defer 可以修改返回值。

Q11 如何交换 2 个变量的值？
a, b := "A", "B"
a, b = b, a
fmt.Println(a, b) // B A

Q12 Go 语言 tag 的用处？
tag 可以理解为 struct 字段的注解，可以用来定义字段的一个或多个属性。框架/工具可以通过反射获取到某个字段定义
的属性，采取相应的处理方式。tag 丰富了代码的语义，增强了灵活性。

package main

import "fmt"
import "encoding/json"

type Stu struct {
	Name string `json:"stu_name"`
	ID   string `json:"stu_id"`
	Age  int    `json:"-"`
}

func main() {
	buf, _ := json.Marshal(Stu{"Tom", "t001", 18})
	fmt.Printf("%s\n", buf)
}

Q13 如何判断 2 个字符串切片（slice) 是相等的？
go 语言中可以使用反射 reflect.DeepEqual(a, b) 判断 a、b 两个切片是否相等，但是通常不推荐这么做，使用反射非常影响性能。
通常采用的方式如下，遍历比较切片中的每一个元素.

Q14 字符串打印时，%v 和 %+v 的区别
%v 和 %+v 都可以用来打印 struct 的值，区别在于 %v 仅打印各个字段的值，%+v 还会打印各个字段的名称。

Q15 Go 语言中如何表示枚举值(enums)
通常使用常量(const) 来表示枚举值。

type StuType int32

const (
	Type1 StuType = iota
	Type2
	Type3
	Type4
)

func main() {
	fmt.Println(Type1, Type2, Type3, Type4) // 0, 1, 2, 3
}

Q16 空 struct{} 的用途
使用空结构体 struct{} 可以节省内存，一般作为占位符使用，表明这里并不需要一个值。

Q1 init() 函数是什么时候执行的？
init() 函数是 Go 程序初始化的一部分。Go 程序初始化先于 main 函数，由 runtime 初始化每个导入的包，初始化顺序不是
按照从上到下的导入顺序，而是按照解析的依赖关系，没有依赖的包最先初始化。每个包首先初始化包作用域的常量和变量（常量优
先于变量），然后执行包的 init() 函数。同一个包，甚至是同一个源文件可以有多个 init() 函数。init() 函数没有入参和
返回值，不能被其他函数调用，同一个包内多个 init() 函数的执行顺序不作保证。
一句话总结： import –> const –> var –> init() –> main()

Q2 Go 语言的局部变量分配在栈上还是堆上？
由编译器决定。Go 语言编译器会自动决定把一个变量放在栈还是放在堆，编译器会做逃逸分析(escape analysis)，当发现变量
的作用域没有超出函数范围，就可以在栈上，反之则必须分配在堆上。
func foo() *int {
	v := 11
	return &v
}

func main() {
	m := foo()
	println(*m) // 11
}
foo() 函数中，如果 v 分配在栈上，foo 函数返回时，&v 就不存在了，但是这段函数是能够正常运行的。Go 编译器发现 v 的引
用脱离了 foo 的作用域，会将其分配在堆上。因此，main 函数中仍能够正常访问该值。

Q3 2 个 interface 可以比较吗？
Go 语言中，interface 的内部实现包含了 2 个字段，类型 T 和 值 V，interface 可以使用 == 或 != 比较。2 个 interface
相等有以下 2 种情况

两个 interface 均等于 nil（此时 V 和 T 都处于 unset 状态）
类型 T 相同，且对应的值 V 相等。

Q4 两个 nil 可能不相等吗？
可能。

接口(interface) 是对非接口值(例如指针，struct等)的封装，内部实现包含 2 个字段，类型 T 和 值 V。一个接口等于 nil，当且仅当 T 和 V 处于 unset 状态（T=nil，V is unset）。

两个接口值比较时，会先比较 T，再比较 V。
接口值与非接口值比较时，会先将非接口值尝试转换为接口值，再比较。