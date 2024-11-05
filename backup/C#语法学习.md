# 简介
记录一下C#基本语法的学习
# HelloWorld
一个 C# 程序主要包括以下部分：
- 命名空间声明（Namespace declaration）
- 一个 class
- Class 方法
- Class 属性
- 一个 Main 方法
- 语句（Statements）& 表达式（Expressions）
- 注释
C# 文件的后缀为 .cs
```C#
using System;
namespace HelloWorldApplication
{
   class HelloWorld
   {
      static void Main(string[] args)
      {
         /* 我的第一个 C# 程序*/
         Console.WriteLine("Hello World");
         Console.ReadKey();
      }
   }
}
```
# 基本语法
C#也是一种面向对象的编程语言。在面向对象的程序设计方法中，程序由各种相互交互的对象组成。相同种类的对象通常具有相同的类型，或者说，是在相同的 class 中。下面是一个Rectangle类：
```C#
using System;
namespace RectangleApplication
{
    class Rectangle
    {
        // 成员变量
        double length;
        double width;
        public void Acceptdetails()
        {
            length = 4.5;    
            width = 3.5;
        }
        public double GetArea()
        {
            return length * width;
        }
        public void Display()
        {
            Console.WriteLine("Length: {0}", length);
            Console.WriteLine("Width: {0}", width);
            Console.WriteLine("Area: {0}", GetArea());
        }
    }
    
    class ExecuteRectangle
    {
        static void Main(string[] args)
        {
            Rectangle r = new Rectangle();
            r.Acceptdetails();
            r.Display();
            Console.ReadLine();
        }
    }
}
```
## using关键字
表示在程序中包含命名空间，类似于C++的头文件、Java的导包
## 标识符
注意：如果要将关键字作为标识符，需要在前面加上@
## 顶级语句
可以在文件的顶层直接编写代码，无需封装方法或类
特点：
- 无需类或方法：顶级语句允许你直接在文件的顶层编写代码，无需定义类或方法。
- 文件作为入口点：包含顶级语句的文件被视为程序的入口点，类似于 C# 之前的 Main 方法。
- 自动 Main 方法：编译器会自动生成一个 Main 方法，并将顶级语句作为 Main 方法的主体。
- 支持局部函数：尽管不需要定义类，但顶级语句的文件中仍然可以定义局部函数。
- 更好的可读性：对于简单的脚本或工具，顶级语句提供了更好的可读性和简洁性。
- 适用于小型项目：顶级语句非常适合小型项目或脚本，可以快速编写和运行代码。
- 与现有代码兼容：顶级语句可以与现有的 C# 代码库一起使用，不会影响现有代码。
传统C#代码：
```C#
using System;

namespace MyApp
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
        }
    }
}
```
使用顶级语句的C#代码：
```C#
using System;

Console.WriteLine("Hello, World!");
```
需要注意：
- 文件限制：顶级语句只能在一个源文件中使用。如果在一个项目中有多个使用顶级语句的文件，会导致编译错误。
- 程序入口：如果使用顶级语句，则该文件会隐式地包含 Main 方法，并且该文件将成为程序的入口点。
- 作用域限制：顶级语句中的代码共享一个全局作用域，这意味着可以在顶级语句中定义的变量和方法可以在整个文件中访问。
# 数据类型
C#中变量分为：值类型、引用类型、指针类型
## 值类型
派生自System.ValueType类，使用sizeof方法可以获得某个变量的存储大小
## 引用类型
引用类型指的是一个内存位置，内置的引用类型有：object、dynamic 和 string；用户自定义引用类型：class、interface 或 delegate
### 对象Object类型
对象（Object）类型 是 C# 通用类型系统（Common Type System - CTS）中所有数据类型的终极基类。Object 是 System.Object 类的别名。所以对象（Object）类型可以被分配任何其他类型（值类型、引用类型、预定义类型或用户自定义类型）的值。但是，在分配值之前，需要先进行类型转换。

当一个值类型转换为对象类型时，则被称为 装箱；另一方面，当一个对象类型转换为值类型时，则被称为 拆箱。
```C#
object obj;
obj = 100; // 这是装箱
int a = (int)obj;//这是拆箱
```
### 动态Dynamic类型
可以存储任意类型的变量，在运行时进行类型检查，所以会降低运行效率，且不易维护代码:
`dynamic <variable_name> = value;`
### 字符串String类型
字符串（String）类型是 System.String 类的别名。它是从对象（Object）类型派生的。字符串（String）类型的值可以通过两种形式进行分配：引号和 @引号
C# string 字符串的前面可以加 @（称作"逐字字符串"）将转义字符（\）当作普通字符对待，比如：
`string str = @"C:\Windows";`
等价于：
`string str = "C:\\Windows";`
### 指针类型
与C++的指针一样
# 类型转换
## 隐式转换
隐式转换是指将一个较小范围的数据类型转换为较大范围的数据类型时，编译器会自动完成类型转换，这些转换是 C# 默认的以安全方式进行的转换, 不会导致数据丢失。
## 显式转换
显式类型转换，即强制类型转换，需要程序员在代码中明确指定。
显式转换是指将一个较大范围的数据类型转换为较小范围的数据类型时，或者将一个对象类型转换为另一个对象类型时，需要使用强制类型转换符号进行显示转换，强制转换会造成数据丢失
## 转换方法
| 序号 | 方法 & 描述                                                                                  |
|------|---------------------------------------------------------------------------------------------|
| 1    | ToBoolean 如果可能的话，把类型转换为布尔类型。                                                  |
| 2    | ToByte 把类型转换为字节字符类型。                                                             |
| 3    | ToChar 如果可能的话，把类型转换为单个 Unicode 字符类型。                                       |
| 4    | ToDateTime 把类型（或表示日期和时间的字符串）转换为 日期-时间 值。                             |
| 5    | ToDecimal 把类型或变量的值转换为十进制类型。                                                  |
| 6    | ToDouble 把类型转换为双精度浮点类型。                                                         |
| 7    | ToInt16 把类型转换为 16 位整数类型。                                                          |
| 8    | ToInt32 把类型转换为 32 位整数类型。                                                          |
| 9    | ToInt64 把类型转换为 64 位整数类型。                                                          |
| 10   | ToSbyte 把类型转换为有符号字节字符类型。                                                      |
| 11   | ToSingle 把类型转换为小浮点精度类型。                                                         |
| 12   | ToString 把类型转换为字符串类型。                                                            |
| 13   | ToType 把类型转换为指定类型。                                                                |
| 14   | ToUInt16 把类型转换为 16 位无符号整数类型。                                                   |
| 15   | ToUInt32 把类型转换为 32 位无符号整数类型。                                                   |
| 16   | ToUInt64 把类型转换为 64 位无符号整数类型。                                                   |
这些方法都定义在 System.Convert 类中，可以处理 null值，并且转换失败的情况会抛出异常，例如：
```C#
using System;

namespace TypeConversionApplication
{
    class StringConversion
    {
        static void Main(string[] args)
        {
            // 定义一个整型变量
            int i = 75;
            
            // 定义一个浮点型变量
            float f = 53.005f;
            
            // 定义一个双精度浮点型变量
            double d = 2345.7652;
            
            // 定义一个布尔型变量
            bool b = true;

            // 将整型变量转换为字符串并输出
            Console.WriteLine(i.ToString());
            
            // 将浮点型变量转换为字符串并输出
            Console.WriteLine(f.ToString());
            
            // 将双精度浮点型变量转换为字符串并输出
            Console.WriteLine(d.ToString());
            
            // 将布尔型变量转换为字符串并输出
            Console.WriteLine(b.ToString());

            // 等待用户按键后关闭控制台窗口
            Console.ReadKey();
        }
    }
}
```
## 类转换方法
### 使用 Convert 类
Convert类的静态方法用于基本数据类型的转换
### 使用 Parse 方法
Parse 方法用于将字符串转换为对应的数值类型，如果转换失败会抛出异常
```C#
string str = "123.45";
double d = double.Parse(str);
```
### 使用 TryParse 方法
TryParse 方法类似于 Parse，但它不会抛出异常，而是返回一个布尔值指示转换是否成功。
```C#
string str = "123.45";
double d;
bool success = double.TryParse(str, out d);

if (success) {
    Console.WriteLine("转换成功: " + d);
} else {
    Console.WriteLine("转换失败");
}
```
## 自定义类型转换
```C#
using System;

public class Fahrenheit
{
    public double Degrees { get; set; }

    public Fahrenheit(double degrees)
    {
        Degrees = degrees;
    }

    // 隐式转换从Fahrenheit到double
    public static implicit operator double(Fahrenheit f)
    {
        return f.Degrees;
    }

    // 显式转换从double到Fahrenheit
    public static explicit operator Fahrenheit(double d)
    {
        return new Fahrenheit(d);
    }
}

public class Program
{
    public static void Main()
    {
        Fahrenheit f = new Fahrenheit(98.6);
        Console.WriteLine("Fahrenheit object: " + f.Degrees + " degrees");

        double temp = f; // 隐式转换
        Console.WriteLine("After implicit conversion to double: " + temp + " degrees");

        Fahrenheit newF = (Fahrenheit)temp; // 显式转换
        Console.WriteLine("After explicit conversion back to Fahrenheit: " + newF.Degrees + " degrees");
    }
}
```
# 变量
除了基本数据类型，还可以定义enum、class等变量
# 接收输入的值
System 命名空间中的 Console 类提供了一个函数 ReadLine()，用于接收来自用户的输入（只接受字符串类型），并把它存储到一个变量中
```C#
int num;
num = Convert.ToInt32(Console.ReadLine());
```
# 常量
## 整数常量
整数常量可以是十进制、八进制或十六进制的常量。
前缀指定基数：0x 或 0X 表示十六进制，0 表示八进制，没有前缀则表示十进制。
整数常量也可以有后缀，可以是 U 和 L 的组合，其中，U 和 L 分别表示 unsigned 和 long。后缀可以是大写或者小写，多个后缀以任意顺序进行组合
```C#
212         /* 合法 */
215u        /* 合法 */
0xFeeL      /* 合法 */
078         /* 非法：8 不是一个八进制数字 */
032UU       /* 非法：不能重复后缀 */
```
## 浮点常量
一个浮点常量是由整数部分、小数点、小数部分和指数部分组成，可以用小数形式或指数形式来表示：
```C#
3.14159       /* 合法 */
314159E-5L    /* 合法 */
510E          /* 非法：不完全指数 */
210f          /* 非法：没有小数或指数 */
.e55          /* 非法：缺少整数或小数 */
```
## 字符常量
字符常量包括普通字符、转义序列、通用字符；需要用时再查即可
## 字符串常量
字符串常量是括在双引号`""` 里，或者是括在`@""`里
## 定义常量
使用 const 关键字定义常量
```C#
using System;

public class ConstTest 
{
    class SampleClass
    {
        public int x;
        public int y;
        public const int c1 = 5;
        public const int c2 = c1 + 5;

        public SampleClass(int p1, int p2) 
        {
            x = p1; 
            y = p2;
        }
    }

    static void Main()
    {
        SampleClass mC = new SampleClass(11, 22);
        Console.WriteLine("x = {0}, y = {1}", mC.x, mC.y);
        Console.WriteLine("c1 = {0}, c2 = {1}", 
                          SampleClass.c1, SampleClass.c2);
    }
}
```
# 运算符
## 算术运算符
+、-、*、/、%、++、--
## 关系运算符
==、!=、>、<、>=、<=
## 逻辑运算符
&&、 ||、 !
## 位运算符
逐位执行操作
逻辑与：&
逻辑或：|
逻辑异或：^，相同为0，不同为1，
- 例如：A = 0011 1100；B = 0000 1101
                  A^B = 0011 0001
按位取反：~
二进制左移：<<
二进制右移：>>
```C#
            int a = 60;            /* 60 = 0011 1100 */  
            int b = 13;            /* 13 = 0000 1101 */
            int c = 0;          

             c = a & b;           /* 12 = 0000 1100 */
             Console.WriteLine("Line 1 - c 的值是 {0}", c );

             c = a | b;           /* 61 = 0011 1101 */
             Console.WriteLine("Line 2 - c 的值是 {0}", c);

             c = a ^ b;           /* 49 = 0011 0001 */
             Console.WriteLine("Line 3 - c 的值是 {0}", c);

             c = ~a;               /*-61 = 1100 0011 */
             Console.WriteLine("Line 4 - c 的值是 {0}", c);

             c = a << 2;     /* 240 = 1111 0000 */
             Console.WriteLine("Line 5 - c 的值是 {0}", c);

             c = a >> 2;     /* 15 = 0000 1111 */
```
## 其它运算符
- sizeof：返回数据类型的大小
- typeof：返回class的类型
- &：返回变量的地址
- *：变量的指针
- ?：条件表达式
- is：判断对象是否为某种类型
- as：强制转换，失败也不会抛出异常
# 循环
只介绍与其它语言不同的地方
## foreach
语法：
```C#
foreach (var item in collection)
{
    // 循环
}
```
# 封装
成员函数、成员变量不指定权限，默认是private
C#的访问修饰符如下：
- public：所有对象都可以访问；
- private：对象本身在对象内部可以访问；
- protected：只有该类对象及其子类对象可以访问
- internal：同一个程序集的对象可以访问；
- protected internal：访问限于当前程序集或派生自包含类的类型。
![image](https://github.com/user-attachments/assets/5d71b77f-2d4f-4784-b9cc-ad04c48c1ba9)
## Protected
Proteced 允许子类访问其积累的成员变量和成员函数
## internal
internal允许一个类将其成员变量和成员函数暴露给当前程序中的其它函数对象，与public的区别在于如果是程序被其它程序引用，internal类型不可以被直接访问，public可以直接访问
# 方法
## 参数传递
方法的参数传递分为值参数、引用参数、输出参数三种；值参数和C++的值传递是一样的
### 引用传参
通过 ref 关键字声明引用参数：
```C#
class NumberManipulator
{
    public void swap(ref int x, ref int y)
    {
        int temp;
        temp = x;
        x = y;
        y = temp;
    }

    static void Main(string[] args)
    {
        NumberManipulator n = new NumberManipulator();
        int a = 50;
        int b = 100;
        Console.WriteLine("在交换之前，a 的值： {0}", a);
        Console.WriteLine("在交换之前，b 的值： {0}", b);
        n.swap(ref a, ref b);
        Console.WriteLine("在交换之后，a 的值： {0}", a);
        Console.WriteLine("在交换之后，b 的值： {0}", b);

        Console.ReadLine();
    }
}
```
### 输出参数
输出参数和返回值的区别是，return语句的返回值只能有一个，输出参数可以返回多个，思路和引用参数差不多，在定义和调用方法的时候，参数都要加上out关键字
```C#
 public void GetValue(out int x)
 {
     int temp = 54;
     x = temp; ;
 }

 static void Main(string[] args)
 {
     NumberManipulator n = new NumberManipulator();
     /* 局部变量定义 */
     int a = 100;

     Console.WriteLine("在方法调用之前，a 的值： {0}", a);

     /* 调用函数来获取值 */
     n.GetValue(out a);

     Console.WriteLine("在方法调用之后，a 的值： {0}", a);
     Console.ReadLine();
 }
```
# 可空类型

C# 中的`?`关键字用于对int、double、bool等无法直接赋值为null的数据类型进行null赋值；

```C#
int? i = 3;
// 等价于
Nullable<int> i = new Nullable<int>(3);

// 默认值0
int i ;
// 默认值null
int? ii;
```

其中，Nullable为可空类型，表示在基础类型的范围内，再加上一个null值：

```C#
static void Main(string[] args)
      {
         int? num1 = null;
         int? num2 = 45;
         double? num3 = new double?();
         double? num4 = 3.14157;       
         bool? boolval = new bool?();
     
         Console.WriteLine("显示可空类型的值： {0}, {1}, {2}, {3}",
                            num1, num2, num3, num4);
         Console.WriteLine("一个可空的布尔值： {0}", boolval);
         Console.ReadLine();
      }
```

??运算符：

如果可空类型的值为null，那么就会返回第二个操作数的值：

```c#
int? num1 = null;
           
double num3;
num3 = num1 ?? 5.34;
```

# 数组

声明：

`dataType[] arrayName;`

初始化：

`double[] balance = new double[10];`

赋值：

1. 通过索引赋值：`balance[0] = 1;`

2. 声明的时候赋值：`double[] balance = {23.4,12.3,54.1};`

3. 也可以创建并初始化：`int [] marks = new int[]  { 99,  98, 92, 97, 95};`

4. 不同数组之间赋值，会指向**相同的内存位置**：

   ```c#
   int [] marks = new int[]  { 99,  98, 92, 97, 95};
   int[] score = marks;
   ```

5. 使用foreach遍历：`foreach(int j in marks)`

## 多维数组

C# 中的多维数组在元素访问和初始化上更像是普通的矩阵，不需要连续调用`[]`运算符

二维数组：`string [,] names;`；三维数组：`int[ , , ]m;`

二维数组初始化：

```c#
int [,] a = new int [3,4] {
 {0, 1, 2, 3} ,   /*  初始化索引号为 0 的行 */
 {4, 5, 6, 7} ,   /*  初始化索引号为 1 的行 */
 {8, 9, 10, 11}   /*  初始化索引号为 2 的行 */
};
```

访问：

```c#
int[,] a = new int[5, 2] { { 0, 0 }, { 1, 2 }, { 2, 4 }, { 3, 6 }, { 4, 8 } };
int i, j;
for(i=0;i < 5; i++)
{
    for(j = 0; j < 2; j++)
    {
        Console.Write(a[i,j] + "\t");
    }
    Console.WriteLine() ;
}
```

## 交错数组

交错数组是数组的数组，交错数组是一维数组，声明：

```c#
int [][] socres;
```

创建上面的数组：

```c#
int[][] scores = new int[5][];
for (int i = 0; i < scores.Length; i++) 
{
   scores[i] = new int[4];
}
```

上述代码创建了长度为5的一维数组，每个元素的类型是int类型的数组，**长度不一定一样**

初始化交错数组：

```c#
int[][] scores = new int[2][]{new int[]{92,93,94},new int[]{85,66,87,88}};
```

下面这个例子简单使用了交错数组：

```C#
int[][] a = new int[][] { new int[] { 0, 0 }, new int[] { 1, 2 }, new int[] { 2, 4, 6 }, new int[] { 3, 6 }, new int[] { 4, 8 } };
for (int i = 0; i < a.Length; i++)
{
    int[] temp = a[i]; 
    for(int j = 0;j < temp.Length; j++)
    {
        Console.Write(temp[j] + "\t");
    }
    Console.WriteLine() ;
}
```

## 多维数组和交错数组的区别

1. 多维数组是一个固定的矩阵结构，其行和列都是不变的，每一行都有相同的列数交错数组的每一行都是独立的数组，所以各行可以拥有不同的长度
2. 多维数组的内存是连续分布的，C#中通常先按照行优先（先分配第一行的所有列、再分配第二行的所有列、以此类推）将数组的元素分配到内存中；交错数组由于每一行是独立的数组，所以在内存中可能是分散分布的；因为可以根据需要来分配内存，使用时优先用交错数组
3. 多维数组的内存是连续的，所以访问速度较快；交错数组需要先找到行的地址，再寻找列的地址，访问速度相对较慢
4. 多维数组多用在适合表示规则的矩阵、数据结构大小固定且所有行长度相同；交错数组用在不规则的数据结构、数据大小不确定的情况

## 传递数组给函数

通过不带索引的数组名给函数传递一个指向数组的**指针**，在函数中的任何操作都会直接影响到原数组

```C#
void GetAverage(int[] arr, out double res)
{
    int sum = 0;
    for (int i = 0; i < arr.Length; i++)
    {
        sum += arr[i];
    }
    res = (double)sum / arr.Length;
}
void RedefineArr(int[] arr)
{
    for (int i = 0; i < arr.Length; i++)
    {
        arr[i] += 1000;
    }
}
static void Main(string[] args)
{
    MyArray app = new MyArray(); 
    int[] arr = { 1000, 2, 3, 17, 50 };
    double avg;
    app.GetAverage(arr,out avg);
    Console.WriteLine(avg);
    app.RedefineArr(arr);
 	Array.ForEach(arr, a => { Console.Write(a + "\t"); });
}
```

最后的输出中可以看出，数组arr中的每个元素都增加了1000

## 参数数组

`params`关键字，可以使调用方法时既可以传入数组实参，也可以传递一组数组元素：

使用格式：`返回类型 方法名称( params 类型名称[] 数组名称 )`

```C#
public int AddElements(params int[] arr)
{
    int sum = 0;
    foreach (int i in arr)
    {
       sum += i;
    }
    return sum;
}
static void Main(string[] args)
{
	ParamArray app = new ParamArray();
	int sum = app.AddElements(512, 720, 250, 567, 889);
	Console.WriteLine("总和是： {0}", sum);
	int[] arr = { 1000, 2, 3, 17, 50 };
	sum = app.AddElements(arr);
	Console.WriteLine("总和是： {0}", sum);
}
```

## Array类

Array类是所有数组的基类

特性：

- **类型安全**：数组只能存储指定类型的元素，类型在声明时确定。
- **固定长度**：数组的长度在创建后不可更改。
- **索引访问**：数组中的元素通过索引访问，索引从 0 开始。
- **多维支持**：C# 支持一维、多维和交错数组（数组的数组）。

| 属性名         | 说明                                       |
| :------------- | ------------------------------------------ |
| Length         | 元素个数                                   |
| Rank           | 获取数组的维数                             |
| IsFixedSize    | 判断数组大小是否固定                       |
| IsReadOnly     | 判断数组是否为只读                         |
| IsSynchronized | 判断数组是否线程安全                       |
| SyncRoot       | 获取用于同步数组访问的对象，用于多线程操作 |

常用方法略，详见官方API文档[.NET API browser | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/api/?view=net-8.0&term=Array)

# 字符串

C#一般不用char型数组表示字符串；更多的是用string来声明

创建string对象的几个方法：

```c#
            // 字符拼接
            string fname, lname;
            fname = "Roa";
            lname = "ss";
            string fullname = fname + lname;
            Console.WriteLine("Full Name: {0}", fullname);
            //通过使用 string 构造函数
            char[] letters = { 'H', 'e', 'l', 'l', 'o' };
            string greetings = new string(letters);
            Console.WriteLine("Greetings: {0}", greetings);
            //方法返回字符串
            string[] sarray = { "Hello", "From", "Tutorials", "Point" };
            string message = String.Join(" ", sarray);
            Console.WriteLine("Message: {0}", message);

            //用于转化值的格式化方法
            DateTime waiting = new DateTime();
            waiting = DateTime.Now;
            string chat = String.Format("Message sent at {0:t} on {0:D}",
            waiting);
            Console.WriteLine("Message: {0}", chat);
```

常用方法查询官方API文档：[.NET API browser | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/api/?view=net-8.0&term=Array)

# 结构体

C#中，结构体是值类型数据结构，可以使得单一变量存储多种数据类型的数据

定义：

```c#
struct Books
{
   public string title;
   public string author;
   public string subject;
   public int book_id;
};  
```

特点：

- 可以有方法、字段、索引、属性、运算符方法和事件，适用于表示轻量级数据的情况，如坐标、范围、日期、时间等
- 结构可定义构造函数，但不能定义析构函数；并且，**不要自定义无参构造**
- 结构体不支持多态
- 结构体成员不可以声明为abstract、virtual或protected
- 结构体创建可以不用new，但是必须初始化所有字段
- 结构体通常分配在栈上，但是如果作为类的成员，且类是引用类型，结构体就在堆上存储
- 结构体可以定义为只读，字段就不可变

# 枚举

枚举是一组命名整型常量，是值类型

声明：

```c#
enum <enum_name>
{ 
    enumeration list 
};
```

# 类

定义：

```c#
<access specifier> class  class_name
{
    // member variables
    <access specifier> <data type> variable1;
    ...
    <access specifier> <data type> variableN;
    // member methods
    <access specifier> <return type> method1(parameter_list)
    {
        // method body
    }
    ...
    <access specifier> <return type> methodN(parameter_list)
    {
        // method body
    }
}
```

- 类的访问标识符 <access specifier> 默认是 internal，成员的默认访问标识符是private

## 构造函数

和C++的构造函数一样，可以提供多个

## 析构函数

和C++的析构函数一样

## 静态成员

`static`关键字定义的成员，表示只会有一个该静态成员的副本，可以直接通过类名调用。静态成员可以在成员函数或类的外部进行初始化，也可以内部初始化

静态成员函数只能访问静态成员变量

# 继承

C#不支持多重继承，但是可以实现多个接口（Interface），继承语法和C++一样

```c#
<访问修饰符> class <基类>
{
 ...
}
class <派生类> : <基类>
{
 ...
}
```

## 基类的初始化

派生类继承了基类的成员变量和方法， 所以父类对象在子类对象之前被创建，可以通过初始化列表的方式创建

```c#
using System;
namespace RectangleApplication
{
   class Rectangle
   {
      // 成员变量
      protected double length;
      protected double width;
      public Rectangle(double l, double w)
      {
         length = l;
         width = w;
      }
      public double GetArea()
      {
         return length * width;
      }
      public void Display()
      {
         Console.WriteLine("长度： {0}", length);
         Console.WriteLine("宽度： {0}", width);
         Console.WriteLine("面积： {0}", GetArea());
      }
   }//end class Rectangle  
   class Tabletop : Rectangle
   {
      private double cost;
      public Tabletop(double l, double w) : base(l, w)
      { }
      public double GetCost()
      {
         double cost;
         cost = GetArea() * 70;
         return cost;
      }
      public void Display()
      {
         base.Display();
         Console.WriteLine("成本： {0}", GetCost());
      }
   }
   class ExecuteRectangle
   {
      static void Main(string[] args)
      {
         Tabletop t = new Tabletop(4.5, 7.5);
         t.Display();
         Console.ReadLine();
      }
   }
}
```

## 实现接口

一个接口可以继承自一个或多个接口

```c#
interface IBaseInterface
{
    void Method1();
}

interface IDerivedInterface : IBaseInterface
{
    void Method2();
}
```

例子：

```c#
using System;

// 定义一个基接口
interface IBaseInterface
{
    void Method1();
}

// 定义一个派生接口，继承自基接口
interface IDerivedInterface : IBaseInterface
{
    void Method2();
}

// 实现派生接口的类
class MyClass : IDerivedInterface
{
    public void Method1()
    {
        Console.WriteLine("Method1 implementation");
    }

    public void Method2()
    {
        Console.WriteLine("Method2 implementation");
    }
}

class Program
{
    static void Main(string[] args)
    {
        // 创建 MyClass 类的实例
        MyClass obj = new MyClass();

        // 调用继承自基接口的方法
        obj.Method1();

        // 调用派生接口新增的方法
        obj.Method2();
    }
}
```

## 继承多个接口

```C#
using System;
namespace InheritanceApplication
{
   class Shape 
   {
      public void setWidth(int w)
      {
         width = w;
      }
      public void setHeight(int h)
      {
         height = h;
      }
      protected int width;
      protected int height;
   }

   // 基类 PaintCost
   public interface PaintCost 
   {
      int getCost(int area);

   }
   // 派生类
   class Rectangle : Shape, PaintCost
   {
      public int getArea()
      {
         return (width * height);
      }
      public int getCost(int area)
      {
         return area * 70;
      }
   }
   class RectangleTester
   {
      static void Main(string[] args)
      {
         Rectangle Rect = new Rectangle();
         int area;
         Rect.setWidth(5);
         Rect.setHeight(7);
         area = Rect.getArea();
         // 打印对象的面积
         Console.WriteLine("总面积： {0}",  Rect.getArea());
         Console.WriteLine("油漆总成本： ${0}" , Rect.getCost(area));
         Console.ReadKey();
      }
   }
}
```

# 多态

在编译时，函数和对象的连接机制被称为早期绑定，也被称为静态绑定。C# 提供了两种技术来实现静态多态性。分别为：

- 函数重载
- 运算符重载

## 函数重载

同名的函数，参数类型不同、参数个数不同，满足其一就叫函数重载

## 动态多态性

C#中可以使用`abstract`创建抽象类，包含抽象方法；可以被派生类实现，抽象类的特性：

- 不可实例化
- 抽象方法只能在抽象类内部声明，但抽象类内部也可以定义普通的方法
- 类前使用关键字`sealed`，可以将类声明为密封类，不可被继承；抽象类不可以使用`sealed`

```c#
using System;
namespace PolymorphismApplication
{
   abstract class Shape
   {
       abstract public int area();
   }
   class Rectangle:  Shape
   {
      private int length;
      private int width;
      public Rectangle( int a=0, int b=0)
      {
         length = a;
         width = b;
      }
      public override int area ()
      { 
         Console.WriteLine("Rectangle 类的面积：");
         return (width * length); 
      }
   }

   class RectangleTester
   {
      static void Main(string[] args)
      {
         Rectangle r = new Rectangle(10, 7);
         double a = r.area();
         Console.WriteLine("面积： {0}",a);
         Console.ReadKey();
      }
   }
}
```

虚方法：需要在派生类重写的方法，使用`virtual`声明为虚方法

```c#
public class Shape
{
    public int X { get; private set; }
    public int Y { get; private set; }
    public int Height { get; set; }
    public int Width { get; set; }
   
    // 虚方法
    public virtual void Draw()
    {
        Console.WriteLine("执行基类的画图任务");
    }
}

class Circle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("画一个圆形");
        base.Draw();
    }
}
class Rectangle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("画一个长方形");
        base.Draw();
    }
}
class Triangle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("画一个三角形");
        base.Draw();
    }
}

class Program
{
    static void Main(string[] args)
    {
        // 创建一个 List<Shape> 对象，并向该对象添加 Circle、Triangle 和 Rectangle
        var shapes = new List<Shape>
        {
            new Rectangle(),
            new Triangle(),
            new Circle()
        };

        // 使用 foreach 循环对该列表的派生类进行循环访问，并对其中的每个 Shape 对象调用 Draw 方法
        foreach (var shape in shapes)
        {
            shape.Draw();
        }

        Console.WriteLine("按下任意键退出。");
        Console.ReadKey();
    }
}
```

## 运算符重载

下面是一个实现运算符重载的案例：

```C#
class Box
   {
      private double length;      // 长度
      private double breadth;     // 宽度
      private double height;      // 高度

      public double getVolume()
      {
         return length * breadth * height;
      }
      public void setLength( double len )
      {
         length = len;
      }

      public void setBreadth( double bre )
      {
         breadth = bre;
      }

      public void setHeight( double hei )
      {
         height = hei;
      }
      // 重载 + 运算符来把两个 Box 对象相加
      public static Box operator+ (Box b, Box c)
      {
         Box box = new Box();
         box.length = b.length + c.length;
         box.breadth = b.breadth + c.breadth;
         box.height = b.height + c.height;
         return box;
      }
   }
```

# 接口

接口定义了属性、方法、事件，且只包括成员的声明；实现由每个继承接口的类完成

```c#
using System;

interface IMyInterface
{
        // 接口成员
    void MethodToImplement();
}

class InterfaceImplementer : IMyInterface
{
    static void Main()
    {
        InterfaceImplementer iImp = new InterfaceImplementer();
        iImp.MethodToImplement();
    }

    public void MethodToImplement()
    {
        Console.WriteLine("MethodToImplement() called.");
    }
}
```

# 预处理指令

*预处理指令指导编译器在实际编译之前对信息进行预处理；*

*通过这些指令，可以控制编译器如何编译文件或编译哪些部分。常见的预处理器指令包括条件编译、宏定义等。*

*所有的预处理器指令都是以 **#** 开始，且在一行上，只有空白字符可以出现在预处理器指令之前。*

*预处理器指令不是语句，所以它们不以分号 **;** 结束。*

预处理指令列表：

| 指令         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| `#define`    | 定义一个符号，可以用于条件编译。                             |
| `#undef`     | 取消定义一个符号。                                           |
| `#if`        | 开始一个条件编译块，如果符号被定义则包含代码块。             |
| `#elif`      | 如果前面的 `#if` 或 `#elif` 条件不满足，且当前条件满足，则包含代码块。 |
| `#else`      | 如果前面的 `#if` 或 `#elif` 条件不满足，则包含代码块。       |
| `#endif`     | 结束一个条件编译块。                                         |
| `#warning`   | 生成编译警告信息。                                           |
| `#error`     | 生成编译错误信息。                                           |
| `#region`    | 标记一段代码区域，可以在 IDE 中折叠和展开这段代码，便于代码的组织和阅读。 |
| `#endregion` | 结束一个代码区域。                                           |
| `#line`      | 更改编译器输出中的行号和文件名，可以用于调试或生成工具的代码。 |
| `#pragma`    | 用于编译器发送特定指令，例如禁用或恢复特定的警告。           |
| `#nullable`  | 控制可空性上下文注释，允许启用或禁用对不可空引用类型的编译器检查。 |

```c#
#define DEBUG

#if DEBUG
    Console.WriteLine("Debug mode");
#elif RELEASE
    Console.WriteLine("Release mode");
#else
    Console.WriteLine("Other mode");
#endif

#warning This is a warning message
#error This is an error message

#region MyRegion
    // Your code here
#endregion

#line 100 "MyFile.cs"
    // The next line will be reported as line 100 in MyFile.cs
    Console.WriteLine("This is line 100");
#line default
    // Line numbering returns to normal

#pragma warning disable 414
    private int unusedVariable;
#pragma warning restore 414

#nullable enable
    string? nullableString = null;
#nullable disable
```

条件指令的使用：

```C#
#define DEBUG
#define VC_V10
using System;
public class TestClass
{
   public static void Main()
   {

      #if (DEBUG && !VC_V10)
         Console.WriteLine("DEBUG is defined");
      #elif (!DEBUG && VC_V10)
         Console.WriteLine("VC_V10 is defined");
      #elif (DEBUG && VC_V10)
         Console.WriteLine("DEBUG and VC_V10 are defined");
      #else
         Console.WriteLine("DEBUG and VC_V10 are not defined");
      #endif
      Console.ReadKey();
   }
}
```

注意：

- 使用`#region`可以分隔代码块，提高可读性
- `#if`条件编译可以分隔开发环境和生产环境的代码
- `#warning`和`#error`可以在编译时提示开发人员注意特定问题

# 正则表达式

知道有个`Regex`类就行了，用的时候问AI或者查API文档

# 异常处理

异常处理的关键词：***try***、***catch***、***finally*** *和* ***throw***

- **try**：一个 try 块标识了一个将被激活的特定的异常的代码块。后跟一个或多个 catch 块。
- **catch**：程序通过异常处理程序捕获异常。catch 关键字表示异常的捕获。
- **finally**：finally 块用于执行给定的语句，不管异常是否被抛出都会执行。例如，如果您打开一个文件，不管是否出现异常文件都要被关闭。
- **throw**：当问题出现时，程序抛出一个异常。使用 throw 关键字来完成。

异常类：C#中的异常类基本上都是派生于**System.Exception**类；

**System.ApplicationException** 类支持由应用程序生成的异常。所以程序员定义的异常都应派生自该类。

**System.SystemException** 类是所有预定义的系统异常的基类。

## 自定义异常类

派生自 **ApplicationException**类：

```c#
using System;
namespace UserDefinedException
{
   class TestTemperature
   {
      static void Main(string[] args)
      {
         Temperature temp = new Temperature();
         try
         {
            temp.showTemp();
         }
         catch(TempIsZeroException e)
         {
            Console.WriteLine("TempIsZeroException: {0}", e.Message);
         }
         Console.ReadKey();
      }
   }
}
public class TempIsZeroException: ApplicationException
{
   public TempIsZeroException(string message): base(message)
   {
   }
}
public class Temperature
{
   int temperature = 0;
   public void showTemp()
   {
      if(temperature == 0)
      {
         throw (new TempIsZeroException("Zero Temperature found"));
      }
      else
      {
         Console.WriteLine("Temperature: {0}", temperature);
      }
   }
}
```

# 文件操作

C#的文件操作也是通过***输入流***和***输出流***来实现。***输入流***用于从文件读取数据（读操作），***输出流***用于向文件写入数据（写操作）

## I/O类

下表列出了一些 System.IO 命名空间中常用的非抽象类：

| I/O 类         | 描述                               |
| -------------- | ---------------------------------- |
| BinaryReader   | 从二进制流读取/恢复数据。          |
| BinaryWriter   | 以二进制格式写入原始数据。         |
| BufferedStream | 字节流的临时存储。                 |
| Directory      | 有助于操作目录结构。               |
| DirectoryInfo  | 用于对目录执行操作。               |
| DriveInfo      | 提供驱动器的信息。                 |
| File           | 有助于处理文件。                   |
| FileInfo       | 用于对文件执行操作。               |
| FileStream     | 用于文件中任何位置的读写。         |
| MemoryStream   | 用于随机访问存储在内存中的数据流。 |
| Path           | 对路径信息执行操作。               |
| StreamReader   | 用于从字节流中读取字符串。         |
| StreamWriter   | 用于向一个流中写入字符串。         |
| StringReader   | 用于读取字符串缓冲区。             |
| StringWriter   | 用于写入字符串缓冲区。             |

## FileStream类

派生自抽象类Stream，使用语法：

```C#
FileStream <object_name> = new FileStream( <file_name>,
<FileMode Enumerator>, <FileAccess Enumerator>, <FileShare Enumerator>);

FileStream F = new FileStream("sample.txt", FileMode.Open, FileAccess.Read, FileShare.Read);
```

参数介绍：

**FileMode：**

- **Append**：打开一个已有的文件，并将光标放置在文件的末尾。如果文件不存在，则创建文件。
- **Create**：创建一个新的文件。如果文件已存在，则删除旧文件，然后创建新文件。
- **CreateNew**：指定操作系统应创建一个新的文件。如果文件已存在，则抛出异常。
- **Open**：打开一个已有的文件。如果文件不存在，则抛出异常。
- **OpenOrCreate**：指定操作系统应打开一个已有的文件。如果文件不存在，则用指定的名称创建一个新的文件打开。
- **Truncate**：打开一个已有的文件，文件一旦打开，就将被截断为零字节大小。然后我们可以向文件写入全新的数据，但是保留文件的初始创建日期。如果文件不存在，则抛出异常。

**FileAccess：** **Read**、**ReadWrite** 和 **Write**。

**FileShare：**

- **Inheritable**：允许文件句柄可由子进程继承。Win32 不直接支持此功能。
- **None**：谢绝共享当前文件。文件关闭前，打开该文件的任何请求（由此进程或另一进程发出的请求）都将失败。
- **Read**：允许随后打开文件读取。如果未指定此标志，则文件关闭前，任何打开该文件以进行读取的请求（由此进程或另一进程发出的请求）都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
- **ReadWrite**：允许随后打开文件读取或写入。如果未指定此标志，则文件关闭前，任何打开该文件以进行读取或写入的请求（由此进程或另一进程发出）都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
- **Write**：允许随后打开文件写入。如果未指定此标志，则文件关闭前，任何打开该文件以进行写入的请求（由此进程或另一进过程发出的请求）都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
- **Delete**：允许随后删除文件。

使用：

```c#
using System;
using System.IO;

namespace FileIOApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            FileStream F = new FileStream("test.dat", 
            FileMode.OpenOrCreate, FileAccess.ReadWrite);

            for (int i = 1; i <= 20; i++)
            {
                F.WriteByte((byte)i);
            }

            F.Position = 0;

            for (int i = 0; i <= 20; i++)
            {
                Console.Write(F.ReadByte() + " ");
            }
            F.Close();
            Console.ReadKey();
        }
    }
}
```

## 文本文件的读写

使用StreamReader类读取文件：

```c#
void readTextFile()
{
    try
    {
        using (StreamReader sr = new StreamReader
             ("E:\\Code\\C#Learn\\Basic\\HelloWorld\\HelloWorld\\Text.txt", System.Text.Encoding.UTF8))
        {
            ;
            string line;
            while ((line = sr.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }

        }
    }
    catch (Exception e)
    {
        Console.WriteLine("The file could not be read:");
        Console.WriteLine(e.Message);
    }
}
```

使用StreamWriter类写入文件：

```c#
void writeTextFile()
{
    string[] names = new string[] { "Zara Ali", "Nuha Ali" };
    using (StreamWriter sw = new StreamWriter("names.txt"))
    {
        foreach (string s in names)
        {
            sw.WriteLine(s);

        }
    }

    // 从文件中读取并显示每行
    string line = "";
    using (StreamReader sr = new StreamReader("names.txt"))
    {
        while ((line = sr.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }
}
```

## 二进制文件的读写

使用**BinaryReader**类和**BinaryWriter**类进行二进制文件的读写

```c#
BinaryWriter bw;
BinaryReader br;
int i = 25;
double d = 3.14157;
bool b = true;
string s = "i am happy";
// 创建文件
try
{
    bw = new BinaryWriter(new FileStream("bindata", FileMode.OpenOrCreate, FileAccess.Write));
}
catch (IOException e)
{
    Console.WriteLine(e.Message + "\n Cannot create file.");
    return;
}
// 写入文件
try
{
    bw.Write(i);
    bw.Write(d);
    bw.Write(b);
    bw.Write(s);
}
catch (IOException e)
{
    Console.WriteLine(e.Message + "\n Cannot write to file.");
    return;
}
bw.Close();
// 读取文件
try
{
    br = new BinaryReader(new FileStream("bindata", FileMode.Open, FileAccess.Read));
}
catch (IOException e) {
    Console.WriteLine(e.Message + "\n Cannot open file.");
    return;
}
try
{
    i = br.ReadInt32();
    Console.WriteLine("Integer data: {0}", i);
    d = br.ReadDouble();
    Console.WriteLine("Double data: {0}", d);
    b = br.ReadBoolean();
    Console.WriteLine("Boolean data: {0}", b);
    s = br.ReadString();
    Console.WriteLine("String data: {0}", s);
}
catch (IOException e)
{
    Console.WriteLine(e.Message + "\n Cannot read from file.");
    return;
}
br.Close();
```
# 特性

特性（Attribute）用于添加元数据，如编译器指令和注释、描述、方法、类等其他信息。.Net 框架提供了两种类型的特性：**预定义**特性和**自定义**特性。

## 规定特性

规定特性的语法：

```C#
[attribute(positional_parameters, name_parameter = value, ...)]
element
```

特性（Attribute）的名称和值是在方括号内规定的，放置在它所应用的元素之前。positional_parameters 规定必需的信息，name_parameter 规定可选的信息。

## 预定义特性

.Net 框架提供了三种预定义特性：

- AttributeUsage
- Conditional
- Obsolete

### AttrubuteUsage

预定义特性 **AttributeUsage** 描述了如何使用一个自定义特性类。它规定了特性可应用到的项目的类型；语法如下：

```c#
[AttributeUsage(
   validon,
   AllowMultiple=allowmultiple,
   Inherited=inherited
)]
```

其中：

- 参数 validon 规定特性可被放置的语言元素。它是枚举器 *AttributeTargets* 的值的组合。默认值是 *AttributeTargets.All*。
- 参数 *allowmultiple*（可选的）为该特性的 *AllowMultiple* 属性（property）提供一个布尔值。如果为 true，则该特性是多用的。默认值是 false（单用的）。
- 参数 *inherited*（可选的）为该特性的 *Inherited* 属性（property）提供一个布尔值。如果为 true，则该特性可被派生类继承。默认值是 false（不被继承）。

### Conditional

**Conditional** 标记了一个条件方法，执行依赖于指定的预处理标识符。它会引起方法调用的条件编译，取决于指定的值，比如 **Debug** 或 **Trace**。例如，当调试代码时显示变量的值，语法如下：

```C#
[Conditional(
   conditionalSymbol
)]
```

例如：

```c#
[Conditional("DEBUG")]
```

### Obsolete

该预定义特性可以将一个目标元素标记为过时，不再使用，语法如下：

```c#
[Obsolete(
   message
)]
[Obsolete(
   message,
   iserror
)]
```

其中：

- 参数 *message*，是一个字符串，描述项目为什么过时以及该替代使用什么。
- 参数 *iserror*，是一个布尔值。如果该值为 true，编译器应把该项目的使用当作一个错误。默认值是 false（编译器生成一个警告）。

例如：

```c#
using System;
public class MyClass
{
   [Obsolete("Don't use OldMethod, use NewMethod instead", true)]
   static void OldMethod()
   { 
      Console.WriteLine("It is the old method");
   }
   static void NewMethod()
   { 
      Console.WriteLine("It is the new method"); 
   }
   public static void Main()
   {
      OldMethod();
   }
}
```

编译上述代码时，编译器会给出错误消息： Don't use OldMethod, use NewMethod instead

## 自定义特性

创建并使用自定义特性包含四个步骤：

- 声明自定义特性
- 构建自定义特性
- 在目标程序元素上应用自定义特性
- 通过反射访问特性

### 声明自定义特性

自定义特性要派生自 **System.Attribute** 类；例如：

```c#
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Constructor |AttributeTargets.Field |AttributeTargets.Method |AttributeTargets.Property,AllowMultiple = true)]
class DebugInfo : System.Attribute
{

}
```

### 构建自定义特性

DebugInfo 存储debug的调试信息：

- bug 的代码编号
- 辨认该 bug 的开发人员名字
- 最后一次审查该代码的日期
- 一个存储了开发人员标记的字符串消息

```c#
[AttributeUsage(AttributeTargets.Class |
AttributeTargets.Constructor |
AttributeTargets.Field |
AttributeTargets.Method |
AttributeTargets.Property,
AllowMultiple = true)]

public class DeBugInfo : System.Attribute
{
  private int bugNo;
  private string developer;
  private string lastReview;
  public string message;

  public DeBugInfo(int bg, string dev, string d)
  {
      this.bugNo = bg;
      this.developer = dev;
      this.lastReview = d;
  }

  public int BugNo
  {
      get
      {
          return bugNo;
      }
  }
  public string Developer
  {
      get
      {
          return developer;
      }
  }
  public string LastReview
  {
      get
      {
          return lastReview;
      }
  }
  public string Message
  {
      get
      {
          return message;
      }
      set
      {
          message = value;
      }
  }
}
```

### 应用自定义特性

```c#
[DebugInfo(45, "ME", "29292/12/2", Message = "Rectangle")]
class Rectangle
{
    [DebugInfo(43, "ME", "29292/12/2", Message = "成员变量")]
    protected double length;
    protected double width;
    public Rectangle(double l, double w)
    {
        length = l;
        width = w;
    }
    [DebugInfo(55, "Zara Ali", "19/10/2012", Message = "Return type mismatch")]
    public double GetArea()
    {
        return length * width;
    }
    [DebugInfo(56, "Zara Ali", "19/10/2012")]
    public void Display()
    {
        Console.WriteLine("Length: {0}", length);
        Console.WriteLine("Width: {0}", width);
        Console.WriteLine("Area: {0}", GetArea());
    }
}
```

要查看特性信息，需要使用反射

# 反射

反射是获取运行时类型信息的方式，c#应用程序由：`程序集(Assembly)`、`模块(Module)`、`类型(class)`组成，反射可以让程序员可以在程序运行期获得这几个组成部分的相关信息

用途：

- 它允许在运行时查看特性（attribute）信息。
- 它允许审查集合中的各种类型，以及实例化这些类型。
- 它允许延迟绑定的方法和属性（property）。
- 它允许在运行时创建新类型，然后使用这些类型执行一些任务。

## 查看元数据

使用MemberInfo类查看特性：

```c#
System.Reflection.MemberInfo info = typeof(Rectangle);
object[] attributes = info.GetCustomAttributes(true);
for (int i = 0; i < attributes.Length; i++)
{
    System.Console.WriteLine(attributes[i]);
}
```

使用反射查看特性：

```c#
static void Main(string[] args)
{
    Rectangle r = new Rectangle(4.5, 7.5);
    //r.Display();
    Type type = typeof(Rectangle); 
    // 遍历类特性
    foreach(Object attributes in type.GetCustomAttributes(false)){
            DebugInfo dbi = (DebugInfo)attributes;
        if (dbi != null)
        {
            Console.WriteLine("Bug no: {0}", dbi.BugNo);
            Console.WriteLine("Developer: {0}", dbi.Developer);
            Console.WriteLine("Last Reviewed: {0}",
                                     dbi.LastReview);
            Console.WriteLine("Remarks: {0}", dbi.Message);
        }
    }

    // 遍历方法特性
    foreach (MethodInfo m in type.GetMethods())
    {
        foreach (Attribute a in m.GetCustomAttributes(true))
        {
            DebugInfo dbi = a as DebugInfo;
            if (null != dbi)
            {
                Console.WriteLine("Bug no: {0}, for Method: {1}",
                                              dbi.BugNo, m.Name);
                Console.WriteLine("Developer: {0}", dbi.Developer);
                Console.WriteLine("Last Reviewed: {0}",
                                              dbi.LastReview);
                Console.WriteLine("Remarks: {0}", dbi.Message);
            }
        }
    }

    // 遍历字段特性
    foreach (FieldInfo f in type.GetFields(BindingFlags.NonPublic | BindingFlags.Instance))
    {
        foreach (Attribute a in f.GetCustomAttributes(true))
        {
            DebugInfo dbi = a as DebugInfo;
            if (dbi != null)
            {
                Console.WriteLine("Bug no: {0}, for Field: {1}", dbi.BugNo, f.Name);
                Console.WriteLine("Developer: {0}", dbi.Developer);
                Console.WriteLine("Last Reviewed: {0}", dbi.LastReview);
                Console.WriteLine("Remarks: {0}", dbi.Message);
            }
        }
    }
}
```

# 属性

属性可以看作对类中**字段**的包装器，给私有字段提供外界访问的方法，通常由`get`和`set`访问器组成；例如：

```C#
public class Person
{
    private string name;

    public string Name
    {
        get { return name; }
        set { name = value; }
    }
}
```

自动实现：

```c#
public class Person
{
    public string Name { get; set; }
}
```

> 希望字段只读就只用`get`访问器，只写就用`set`访问器；get和set访问器中的逻辑可以自定义

# 索引器

索引器允许一个对象可以像数组一样使用下标`[]`的方式来访问。

一维索引器的语法如下：

```C#
element-type this[int index]
{
   // get 访问器
   get
   {
      // 返回 index 指定的值
   }

   // set 访问器
   set
   {
      // 设置 index 指定的值
   }
}
```

索引器的行为的声明在某种程度上类似于属性（property）。就像属性（property），可使用 **get** 和 **set** 访问器来定义索引器。但是，属性返回或设置一个特定的数据成员，而索引器返回或设置对象实例的一个特定值。换句话说，它把实例数据分为更小的部分，并索引每个部分，获取或设置每个部分。

定义一个属性（property）包括提供属性名称。索引器定义的时候不带有名称，但带有 **this** 关键字，它指向对象实例。例：

```c#
    class IndexedNames
    {
        private string[] namelist = new string[size];
        static public int size = 10;
        public IndexedNames()
        {
            for (int i = 0; i < size; i++)
                namelist[i] = "N. A.";
        }

        public string this[int index]
        {
            get
            {
                string temp;
                if (index >= 0 && index < size)
                {
                    temp = namelist[index];
                }
                else
                {
                    temp = "";
                }
                return temp;
            }
            set
            {
                if (index >= 0 && index < size) { namelist[index] = value; }
            }
        }
    }
```

重载索引器：索引器声明的时候也可带有多个参数，且每个参数可以是不同的类型。没有必要让索引器必须是整型的。C# 允许索引器可以是其他类型，例如，字符串类型

```c#
public string this[int index]
{
    get
    {
        string temp;
        if (index >= 0 && index < size)
        {
            temp = namelist[index];
        }
        else
        {
            temp = "";
        }
        return temp;
    }
    set
    {
        if (index >= 0 && index < size) { namelist[index] = value; }
    }
}

public int this[string name]
{
    get
    {
        int index = 0;
        while (index < size)
        {
            if (namelist[index] == name)
            {
                return index;
            }
            index++;
        }
        return index == size ? 0 : index;
    }
}
```

# 委托

委托(Delegate)类似于指针，是存有对某个方法的引用的一种引用类型变量，特别用于实现事件和回调方法

## 声明委托

```c#
delegate <return type> <delegate-name> <parameter list>
```

## 实例化委托

委托对象必须用new关键字创建，参数传递方法名：

```c#
public delegate void printString(string s);
...
printString ps1 = new printString(WriteToScreen);
printString ps2 = new printString(WriteToFile);
```

例子：

```c#
class TestDelegate
{
    static int num = 10;
    public static int AddNum(int p)
    {
        num += p;
        return num; 
    }

    public static int MultNum(int q)
    {
        num *= q;
        return num;
    }
    public static int getNum()
    {
        return num;
    }

    static void Main(string[] args)
    {
        // 创建委托实例
        NumberChanger nc1 = new NumberChanger(AddNum);
        NumberChanger nc2 = new NumberChanger(MultNum);
        // 使用委托对象调用方法
        nc1(25);
        Console.WriteLine("Value of Num: {0}", getNum());
        nc2(5);
        Console.WriteLine("Value of Num: {0}", getNum());
    }
}
```

## 委托的多播

委托对象可以用`+`运算符进行合并，`-`运算符移除；可以利用这个特性创建一个委托被调用时调用方法的列表：

```c#
        static void Main(string[] args)
        {
            // 多播
            NumberChanger nc;
            NumberChanger nc1 = new NumberChanger(AddNum);
            NumberChanger nc2 = new NumberChanger(MultNum);
            nc = nc1;
            nc += nc2;
            nc(5);
            Console.WriteLine("Value of Num: {0}", getNum());
        }
```

## 委托的用途

利用一个委托，调用多个方法：

```c#
using System;
using System.IO;

namespace DelegateAppl
{
   class PrintString
   {
      static FileStream fs;
      static StreamWriter sw;
      // 委托声明
      public delegate void printString(string s);

      // 该方法打印到控制台
      public static void WriteToScreen(string str)
      {
         Console.WriteLine("The String is: {0}", str);
      }
      // 该方法打印到文件
      public static void WriteToFile(string s)
      {
         fs = new FileStream("c:\\message.txt", FileMode.Append, FileAccess.Write);
         sw = new StreamWriter(fs);
         sw.WriteLine(s);
         sw.Flush();
         sw.Close();
         fs.Close();
      }
      // 该方法把委托作为参数，并使用它调用方法
      public static void sendString(printString ps)
      {
         ps("Hello World");
      }
      static void Main(string[] args)
      {
          // 可以用多播
         printString ps1 = new printString(WriteToScreen);
         printString ps2 = new printString(WriteToFile);
         sendString(ps1);
         sendString(ps2);
         Console.ReadKey();
      }
   }
}
```

# 事件

事件是类与类之间进行通信的一种方式，与委托绑定使用

## 通过事件使用委托

事件在类中声明且生成，且通过使用同一个类或其他类中的委托与事件处理程序关联。

事件使用的是**发布-订阅（publisher-subscriber** 模型；包含事件的类用于发布事件，这种类叫 **发布器（publisher）类**，接收事件的类叫 **订阅器（subscriber）类**

**发布器（publisher）** 是一个包含事件和委托定义的对象。事件和委托之间的联系也定义在这个对象中。发布器（publisher）类的对象调用这个事件，并通知其他的对象。

**订阅器（subscriber）** 是一个接受事件并提供事件处理程序的对象。在发布器（publisher）类中的委托调用订阅器（subscriber）类中的方法（事件处理程序）。

## 声明事件

声明事件首先要声明该事件的委托类型：

```c#
public delegate void BoilerLogHandler(string status);
```

然后再声明事件本身：

```c#
// 基于上面的委托定义事件
public event BoilerLogHandler BoilerEventLog;
```

下面是完整的例子：

```c#
    // 1. 定义一个委托类型
    internal delegate void NotifyEventHandler(object sender, EventArgs e);

    // publisher
    class ProcessBusinessLogic
    {
        // 2. 声明事件
        public event NotifyEventHandler ProcessCompleted;

        // 3. 触发事件
        protected virtual void OnProcessCompleted(EventArgs e)
        {
            // ?.Invoke确保只有在有订阅者时才调用事件
            ProcessCompleted?.Invoke(this, e);
        }

        // 模拟触发事件的过程
        public void StartProcess()
        {
            Console.WriteLine("Process started");
            // 执行逻辑代码...

            // 触发事件
            OnProcessCompleted(EventArgs.Empty);
        }
    }

    // subscriber
    class EventSubscriber
    {
        public void Subscribe(ProcessBusinessLogic process)
        {
            // 4. 订阅事件
            process.ProcessCompleted += Process_ProcessCompleted;
        }
        // 事件的处理
        private void Process_ProcessCompleted(object sender, EventArgs e)
        {
            Console.WriteLine("Process Completed!");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // 事件发布者
            ProcessBusinessLogic process = new ProcessBusinessLogic();
            // 事件接收者
            EventSubscriber subscriber = new EventSubscriber();
            // 订阅事件
            subscriber.Subscribe(process);
            // 启动
            process.StartProcess();
        }
    }
```

实际应用：

```c#
    class EventTest
    {
        private int value;
        public delegate void NumMainpulationHandler();

        public event NumMainpulationHandler ChangeNum;

        protected virtual void OnNumChanged()
        {
            if (ChangeNum != null)
            {
                // 触发事件
                ChangeNum();
            }
            else
            {
                Console.WriteLine("event not fire");
            }
        }

        public EventTest()
        {
            int n = 5;
            SetValue(n);
        }
        public void SetValue(int n)
        {
            if (value != n)
            {
                value = n;
                OnNumChanged();
            }
        }

    }

    // subscriber
    public class subscribEvent
    {
        public void printf()
        {
            Console.WriteLine("event fire");
            Console.ReadKey();
        }
    }

    class MainClass
    {
        static void Main(string[] args)
        {
            EventTest e = new EventTest();
            subscribEvent v = new subscribEvent();
            e.ChangeNum += new EventTest.NumMainpulationHandler(v.printf);
            e.SetValue(7);
            e.SetValue(11);
        }
    }
```

上述代码执行流程：

1. Main函数中创建发布者EventTest的实例，构造函数中调用SetValue并触发OnNubChanged()事件，此时并未注册订阅者，控制台打印"event not fire"
2. 创建订阅者subscribEvent的实例，并在发布者的事件ChangeNum订阅新委托，委托的方法是其printf()方法
3. 此时在调用发布者的SetValue方法；并成功触发事件，打印两次"event fire"

------

此实例提供一个简单的用于热水锅炉系统故障排除的应用程序。当维修工程师检查锅炉时，锅炉的温度和压力会随着维修工程师的备注自动记录到日志文件中。

```c#
namespace BoilerEventApplication
{
    class Boiler
    {
        public int Temp { get; private set; }
        public int Pressure { get; private set; }

        public Boiler(int temp, int pressure)
        {
            Temp = temp;
            Pressure = pressure;
        }
    }

    // 事件发布器
    public class DelegateBoilerEvent
    {
        public delegate void BoilerLogHandler(string status);

        // 定义事件
        public event BoilerLogHandler BoilerEventLog;
        protected void OnBoilerEventLog(string message)
        {
            BoilerEventLog?.Invoke(message);
        }

        public void LogProgress()
        {
            string remarks = "O.K.";
            Boiler boiler = new Boiler(100, 12);
            int temp = boiler.Temp;
            int pressure = boiler.Pressure;
            if (temp > 150 || temp < 80 || pressure < 12 || pressure > 15)
            {
                remarks = "Need Maintenance";
            }
            OnBoilerEventLog($"Logging Info:\nTemperature: {temp}\nPressure: {pressure}\nMessage: {remarks}");
        }
    }
    // 该类用于写入文件
    class BoilerInfoLogger : IDisposable
    {
        private readonly StreamWriter _streamWriter;

        public BoilerInfoLogger(string filename)
        {
            _streamWriter = new StreamWriter(new FileStream(filename, FileMode.Append, FileAccess.Write));
        }

        public void Logger(string info)
        {
            _streamWriter.WriteLine(info);
        }

        public void Dispose()
        {
            _streamWriter?.Close();
        }
    }

    // 事件订阅器
    public class RecordBoilerInfo
    {
        static void Logger(string info)
        {
            Console.WriteLine(info);
        }

        static void Main(string[] args)
        {
            using (BoilerInfoLogger fileLogger = new BoilerInfoLogger("boiler.txt"))
            {
                DelegateBoilerEvent boilerEvent = new DelegateBoilerEvent();
                boilerEvent.BoilerEventLog += Logger;
                boilerEvent.BoilerEventLog += fileLogger.Logger;
                boilerEvent.LogProgress();
            }
        }
    }
}
```

# 集合

## 动态数组ArrayList

常用属性、方法查阅官方API文档

```c#
using System;
using System.Collections;

namespace CollectionApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            ArrayList al = new ArrayList();

            Console.WriteLine("Adding some numbers:");
            al.Add(45);
            al.Add(78);
            al.Add(33);
            al.Add(56);
            al.Add(12);
            al.Add(23);
            al.Add(9);
            
            Console.WriteLine("Capacity: {0} ", al.Capacity);
            Console.WriteLine("Count: {0}", al.Count);
                      
            Console.Write("Content: ");
            foreach (int i in al)
            {
                Console.Write(i + " ");
            }
            Console.WriteLine();
            Console.Write("Sorted Content: ");
            al.Sort();
            foreach (int i in al)
            {
                Console.Write(i + " ");
            }
            Console.WriteLine();
            Console.ReadKey();
        }
    }
}
```

## 哈希表Hashtable

类比map

```c#
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         Hashtable ht = new Hashtable();


         ht.Add("001", "Zara Ali");
         ht.Add("002", "Abida Rehman");
         ht.Add("003", "Joe Holzner");
         ht.Add("004", "Mausam Benazir Nur");
         ht.Add("005", "M. Amlan");
         ht.Add("006", "M. Arif");
         ht.Add("007", "Ritesh Saikia");

         if (ht.ContainsValue("Nuha Ali"))
         {
            Console.WriteLine("This student name is already in the list");
         }
         else
         {
            ht.Add("008", "Nuha Ali");
         }
         // 获取键的集合 
         ICollection key = ht.Keys;

         foreach (string k in key)
         {
            Console.WriteLine(k + ": " + ht[k]);
         }
         Console.ReadKey();
      }
   }
}
```

## 排序列表SortedList

数组和哈希表的结合，按键值排序

```c#
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         SortedList sl = new SortedList();

         sl.Add("001", "Zara Ali");
         sl.Add("002", "Abida Rehman");
         sl.Add("003", "Joe Holzner");
         sl.Add("004", "Mausam Benazir Nur");
         sl.Add("005", "M. Amlan");
         sl.Add("006", "M. Arif");
         sl.Add("007", "Ritesh Saikia");

         if (sl.ContainsValue("Nuha Ali"))
         {
            Console.WriteLine("This student name is already in the list");
         }
         else
         {
            sl.Add("008", "Nuha Ali");
         }

         // 获取键的集合 
         ICollection key = sl.Keys;

         foreach (string k in key)
         {
            Console.WriteLine(k + ": " + sl[k]);
         }
      }
   }
}
```

## 栈Stack

```c#
using System;
using System.Collections;

namespace CollectionsApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            Stack st = new Stack();

            st.Push('A');
            st.Push('M');
            st.Push('G');
            st.Push('W');
            
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
                Console.Write(c + " ");
            }
            Console.WriteLine();
            
            st.Push('V');
            st.Push('H');
            Console.WriteLine("The next poppable value in stack: {0}", 
            st.Peek());
            Console.WriteLine("Current stack: ");           
            foreach (char c in st)
            {
               Console.Write(c + " ");
            }
            Console.WriteLine();

            Console.WriteLine("Removing values ");
            st.Pop();
            st.Pop();
            st.Pop();
            
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
               Console.Write(c + " "); 
            }
        }
    }
}
```

## 队列Queue

```c#
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         Queue q = new Queue();

         q.Enqueue('A');
         q.Enqueue('M');
         q.Enqueue('G');
         q.Enqueue('W');
         
         Console.WriteLine("Current queue: ");
         foreach (char c in q)
            Console.Write(c + " ");
         Console.WriteLine();
         q.Enqueue('V');
         q.Enqueue('H');
         Console.WriteLine("Current queue: ");         
         foreach (char c in q)
            Console.Write(c + " ");
         Console.WriteLine();
         Console.WriteLine("Removing some values ");
         char ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         Console.ReadKey();
      }
   }
}
```

## 点阵列BItArray

*BitArray 类管理一个紧凑型的位值数组，它使用布尔值来表示，其中 true 表示位是开启的（1），false 表示位是关闭的（0）*

其实就是存储数字的二进制值，以false、true的形式体现

```C#
using System;
using System.Collections;

namespace CollectionsApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // 创建两个大小为 8 的点阵列
            BitArray ba1 = new BitArray(8);
            BitArray ba2 = new BitArray(8);
            byte[] a = { 60 };
            byte[] b = { 13 };
            
            // 把值 60 和 13 存储到点阵列中
            ba1 = new BitArray(a);
            ba2 = new BitArray(b);

            // ba1 的内容
            Console.WriteLine("Bit array ba1: 60");
            for (int i = 0; i < ba1.Count; i++)
            {
                Console.Write("{0, -6} ", ba1[i]);
            }
            Console.WriteLine();
            
            // ba2 的内容
            Console.WriteLine("Bit array ba2: 13");
            for (int i = 0; i < ba2.Count; i++)
            {
                Console.Write("{0, -6} ", ba2[i]);
            }
            Console.WriteLine();
           
            
            BitArray ba3 = new BitArray(8);
            ba3 = ba1.And(ba2);

            // ba3 的内容
            Console.WriteLine("Bit array ba3 after AND operation: 12");
            for (int i = 0; i < ba3.Count; i++)
            {
                Console.Write("{0, -6} ", ba3[i]);
            }
            Console.WriteLine();

            ba3 = ba1.Or(ba2);
            // ba3 的内容
            Console.WriteLine("Bit array ba3 after OR operation: 61");
            for (int i = 0; i < ba3.Count; i++)
            {
                Console.Write("{0, -6} ", ba3[i]);
            }
            Console.WriteLine();
            
            Console.ReadKey();
        }
    }
}
```

## 链表List

```c#
static void Main(string[] args)
{
        var a = new List<int>();
        a.Add(2);
        a.Add(6);
        a.Add(2);
        a.Add(10);
        Console.WriteLine($"第一个数为{a[0]}");
        a.Remove(2);//删去第一个匹配此条件的项
        a.Sort();
        foreach (var a2 in a)
        {
            Console.WriteLine(a2);
        }
        bool a3 = a.Contains(2);
        Console.WriteLine(a3);
        Console.ReadKey();
    
}
```

## 字典Dictonary

与哈希表有些区别，后续再谈

```c#
static void Main(string[] args)
      {
          var a=new Dictionary<int,int>();
          a.Add(12,14);
          a.Add(0,1);
          Console.WriteLine("删去前的Count"+a.Count);
          a.Remove(0);
          Console.WriteLine(a[12]);
          Console.WriteLine(a.Count);
         Console.WriteLine(a.ContainsKey(12));
         Console.ReadKey();
      }
```

# 泛型

特性：

- 可以创建自定义泛型接口、泛型类、泛型方法、泛型事件和泛型委托
- 可以设置泛型类使其访问特定数据
- 泛型数据类型的信息可以用反射获取

## 泛型方法

```c#
 static void Swap<T>(ref T lhs, ref T rhs)
 {
     T temp;
     temp = lhs;
     lhs = rhs;
     rhs = temp;
 }
```

## 泛型委托

```c#
delegate T NumberChanger<T>(T n);
```

```c#
using System;
using System.Collections.Generic;

delegate T NumberChanger<T>(T n);
namespace GenericDelegateAppl
{
    class TestDelegate
    {
        static int num = 10;
        public static int AddNum(int p)
        {
            num += p;
            return num;
        }

        public static int MultNum(int q)
        {
            num *= q;
            return num;
        }
        public static int getNum()
        {
            return num;
        }

        static void Main(string[] args)
        {
            // 创建委托实例
            NumberChanger<int> nc1 = new NumberChanger<int>(AddNum);
            NumberChanger<int> nc2 = new NumberChanger<int>(MultNum);
            // 使用委托对象调用方法
            nc1(25);
            Console.WriteLine("Value of Num: {0}", getNum());
            nc2(5);
            Console.WriteLine("Value of Num: {0}", getNum());
            Console.ReadKey();
        }
    }
}
```

# 匿名方法

## Lambda表达式

语法：

```c#
(parameters) => expression
// 或
(parameters) => { statement; }

// 示例：使用 Lambda 表达式定义一个委托
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(2, 3)); // 输出 5

// 示例：使用 Lambda 表达式过滤数组中的元素
int[] numbers = { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0);
foreach (var num in evenNumbers)
{
    Console.WriteLine(num); // 输出 2 4
}
```

## 匿名方法

通过使用delegate关键字创建委托实例来声明：

```C#
delegate(parameters) { statement; }
```

例如：

```c#
delegate void NumberChanger(int n);
...
NumberChanger nc = delegate(int x)
{
    Console.WriteLine("Anonymous Method: {0}", x);
};
```

# 不安全代码

当一个代码块使用`unsafe`修饰符标记时，就允许使用指针变量

```c#
static  void Main(string[] args)
{
  unsafe
  {
  	int var = 20;
	int* p = &var;
	Console.WriteLine("Data is: {0} " , var);
  	// 打印指针变量指向的数据
	Console.WriteLine("Data is: {0} " , p->ToString());
     //  获取
	Console.WriteLine("Address is: {0} " , (int)p);
  }
	Console.ReadKey();
}
```

## 使用指针访问数组元素

C#中，数组名和指向数组的指针是不同的变量类型，所以需要用 `fixed`关键字，使得像C++一样操作指针来访问数组元素

```c#
using System;
namespace UnsafeCodeApplication
{
   class TestPointer
   {
      public unsafe static void Main()
      {
         int[]  list = {10, 100, 200};
         fixed(int *ptr = list)

         /* 显示指针中数组地址 */
         for ( int i = 0; i < 3; i++)
         {
            Console.WriteLine("Address of list[{0}]={1}",i,(int)(ptr + i));
            Console.WriteLine("Value of list[{0}]={1}", i, *(ptr + i));
         }
         Console.ReadKey();
      }
   }
}
```

# 多线程

## 生命周期

- **未启动状态**：当线程实例被创建但 Start 方法未被调用时的状况。

- **就绪状态**：当线程准备好运行并等待 CPU 周期时的状况。

- 不可运行状态

  ：下面的几种情况下线程是不可运行的：

  

  - 已经调用 Sleep 方法
  - 已经调用 Wait 方法
  - 通过 I/O 操作阻塞

- **死亡状态**：当线程已完成执行或已中止时的状况。

## 主线程

第一个执行的线程就是主线程，可以通过  Thread 类的 **CurrentThread** 属性访问线程

```c#
namespace MultithreadingApplication
{
    class MainThreadProgram
    {
        static void Main(string[] args)
        {
            Thread th = Thread.CurrentThread;
            th.Name = "MainThread";
            Console.WriteLine("This is {0}", th.Name);
            Console.ReadKey();
        }
    }
}
```

常用的属性、方法先不介绍，使用时再熟悉

## 创建线程

拓展的 Thread 类调用 **Start()** 方法来开始子线程的执行

```c#
class ThreadCreationProgram
{
    public static void CallToChildThread()
    {
        Console.WriteLine("Child thread starts");
    }

    static void Main(string[] args) { 
        ThreadStart childref = new ThreadStart(CallToChildThread);
        Console.WriteLine("In Main: Creating the Child thread");
        Thread childThread = new Thread(childref);
        childThread.Start();
    }
}
```

> Thread的构造函数还可以传递Lambda表达式

## 管理线程

Join、Sleep，都是一样的；这里就略

