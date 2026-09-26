---
title: "Same name for a generic and non-generic class"
date: 2007-09-19
slug: same-name-for-a-generic-and-non-generic-class
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/same-name-for-a-generic-and-non-generic-class
---

# Same name for a generic and non-generic class

I was writing a sample to do something and I noticed that when I declare and instantiate a generic class, I had to pass the generic type in two places:

```csharp
1. Foo<Int32> fooGenericInstance;
2. FooGenericInstance = new Foo<Int32>();
```

First I was not sure why I had to pass the type is two places. After some pondering I came to the following conclusion.

```csharp
namespace Sample
{
    using System;
    class Foo<T>
    {
        public Foo() { }
        public void FooMethod()
        {
            Console.WriteLine("This is generic Foo class");
        }
    }
    public class Bar
    {
        static public void Main()
        {
            Foo<Int32> fooGenericInstance;
            fooGenericInstance = new Foo<Int32>();
            fooGenericInstance.FooMethod();
        }
    }
}
```

C# allows you have the same name for a generic and non-generic class. So you can write code such as below which has two types essentially which are Foo and Foo<T>. Now you have to make it very explicit both during instantiation and construction the class and constructor you are referring to. Thus you end up passing the type T in two places.

```csharp
namespace Sample
{
    using System;
    class Foo<T>
    {
        public Foo() { }
        public void FooMethod()
        {
            Console.WriteLine("This is generic Foo class");
        }
    }
    class Foo
    {
        public Foo() { }
        public void FooMethod()
        {
            Console.WriteLine("This is Non-generic Foo class");
        }
        static public void Main()
        {
            Foo fooNonGenericInstance;
            Foo<Int32> fooGenericInstance;
            fooNonGenericInstance = new Foo();
            fooGenericInstance = new Foo<Int32>();
            fooNonGenericInstance.FooMethod();
            fooGenericInstance.FooMethod();
        }
    }
}
```

## Comments

-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2861](http://www.travel-hilarity.com/airline_travel/?p=2861)
