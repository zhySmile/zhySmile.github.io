---
title: Unity 之协程
categories: 技术分享
tags: [Unity]
---
Unity 之协程
<!-- more -->

# 1.什么是协程
协程即协同程序，是一种特殊的函数，能够中断执行当前的代码，直到中断指令结束后再接着执行之前的代码。

# 2.协程的作用
    1. 延时等待一段时间执行代码
    2. 等某个操作完成之后再执行后面的代码

# 3.协程的原理
Unity 在每一帧都会去处理 GameObject 里带有的 Coroutine Function, 直到 Coroutine Function 被执行完毕. 当一个 Coroutine 开始启动时, 它会执行到遇到 yield 为止, 遇到 yield 的时候 Coroutine 会暂停执行, 直到满足 yield 语句的条件, 会开始执行 yield 语句后面的内容, 直到遇到下一个 yield 为止 ... 如此循环直到整个函数结束。
Unity 的协程系统基于 C# 的接口 : IEnumerator, 允许为自己的集合类型编写枚举类.
迭代器模式是通过 IEnumerator 和 IEnumerable 接口及它们的泛型等价物来封装的。如果某个类型实现了 IEnumerable 接口，就意味着它可以被迭代访问。

IEnumerator 接口定义了以向前方式遍历或枚举集合元素的基本底层协议。声明方式如下：

    public interface IEnumerator
    {
        object Current { get; }
        bool MoveNext();
        void Reset();
    }

MoveNext 将当前元素向前移动到下一个位置，如果集合中没有更多的元素，那么它会返回 false。Current 返回当前位置的元素。在取出第一个元素之前，必须先调用 MoveNext——即使是空集合也支持这个操作。调用 Reset 方法的作用是将位置移动回到起点，允许再一次遍历集合。

集合并未实现枚举器，而是通过接口 IEnumerable 提供枚举器：

    public interface IEnumerable
    {
        IEnumerator GetEnumerator();
    }

通过定义一个返回枚举器的方法，IEnumerable 实现了将迭代器逻辑转交由另一类完成的灵活性。这意味着多个使用者可以同时遍历集合，而不会互相干扰。IEnumerable 可以看作是 "IEnumerator 的提供者",它是集合类需要实现的最基础接口。

IEnumerator 和 IEnumerator 用法：

    string s = "Hello";
    IEnumerator rator = s.GetEnumerator();
    while (rator.MoveNext())
    {
        char c = (char)rator.Current;
        Debug.Log(c);
    }

输出结果：
![](/img/unityCoroutine/1.png)  

我们很少采用这种直接调用枚举器的方法，因为 C# 提供了一个快捷语法：foreach 语句。下面是使用 foreach 语句重写的具有相同效果的示例：

    string s = "Hello";
    foreach (char c in s)
    {
        Debug.Log(c);
    }

返回另一个集合的枚举器就是调用内部集合的 GetEnumerator。然而，这种方法仅仅适合一些最简单的情况，那就是内部集合的元素正好是所需要的类型。更好的方法是使用 C# 的 yield return 语句编写迭代器。迭代器是 C# 语言的一个特性，它能够协助完成集合编写，与 foreach 语句协助完成集合遍历的方式是一样的。迭代器会自动处理 IEnumerable 和 IEnumerator 或者它们的泛型类的实现。

    public class MyCollectiion:IEnumerable
    {
        int[] data = { 1, 2, 3 };
        public IEnumerator GetEnumerator()
        {
            foreach (int i in data)
            {
                yield return i;
            }
        }
    }

迭代器用内置的状态机去跟踪当前个下一个元素。每次遇到新的 yield return 语句是，都会返回值。然后，当下一次迭代开始的时候，会紧接着上一个 yield return 语句之后执行。

# 4.实现 WaitForMilliSeconds

以下为实现 WaitForMilliSeconds：

    public class WaitForMilliSeconds: IEnumerator
    {
        double MilliSeconds;

        public WaitForMilliSeconds(double milliSeconds)
        {
            MilliSeconds = milliSeconds;
            _start = DateTime.Now.TimeOfDay.TotalMilliseconds;
        }

        public object Current
        {
            get
            {
                return null;
            }
        }

        public bool MoveNext()
        {
            return DateTime.Now.TimeOfDay.TotalMilliseconds < _start + MilliSeconds;
        }

        public void Reset()
        {
        }

        private double _start;
    }

调用示例：

    public class CoroutineTest : MonoBehaviour
    {
        private void Start()
        {
            StartCoroutine(TestWaitForMilliSecondsCoroutine(1000));
        }
        private IEnumerator TestWaitForMilliSecondsCoroutine(double milliseconds)
        {
            yield return new WaitForMilliSeconds(milliseconds);
        }
    }

# 5.协程是否可以使用 try catch 捕获异常

测试代码1：

    private IEnumerator TestCoroutineTryCatch(double milliseconds)
    {
        try
        {
            yield return WaitForMilliSeconds(milliseconds);
        }
        catch (Exception e)
        {
            Debug.Log(e);
        }
    }

此段代码编译不能通过，“无法在包含 catch 子句的 Try 块体中生成值”。

测试代码2：
    
    private IEnumerator TestWaitForMilliSecondsCoroutine(double milliseconds)
    {
        try
        {
            List<int> tList = new List<int>();
            Debug.Log(tList[1]);
            yield return WaitForMilliSeconds(milliseconds);
        }
        finally
        {
            Debug.Log(e);
        }
    }

yield return 可以包含在 try-finally 语句块中。

结论：
yield return 语句不能出现在带 catch 子句的 try 语句块中，也不能出现在 catch 或 finally 语句块中。出现这些限制的原因是编译器必须将迭代器转换为带有 MoveNext、Current 和 Dispose 成员的普通类，而且转换异常语句块可能会大大增加代码复杂性。
但是，可以在只带 finally 语句块的 try 块中使用 yield 语句。
当枚举器到达队列末尾或者处理完毕时，finally 语句块就可以执行了。如果提前使用 break 中断循环，那么 foreach 语句会显式地结束枚举器，这是一种正确使用枚举器的方法。

# 6.本文参考来源 
《果壳中的 C#》