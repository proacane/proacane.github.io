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