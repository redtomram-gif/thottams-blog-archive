---
title: "Why is generic constructor not allowed?"
date: 2007-09-19
slug: why-is-generic-constructor-not-allowed
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/why-is-generic-constructor-not-allowed
---

# Why is generic constructor not allowed?

Generic argument is not allowed in the constructor. When I compile the code below I get a compilation error saying:

error CS1519: Invalid token '(' in class, struct, or interface

member declaration

The reason is simple. You can have a class Foo<T> and a class Foo as described in my previous post. Now when you says

`fooInstance = new Foo<T>();`

there is no way for the compiler to find out the right class constructor to call.

```csharp
namespace Sample
{
    using System;
    public class Foo <T>
    {
    }
    class Foo
    {
        public Foo<T>()
        {
            Console.WriteLine("This is generic Foo class {0}",typeof(T));
        }
    }
    public class Bar
    {
        static public void Main()
        {
            Foo fooGenericInstance;
            fooGenericInstance = new Foo<Int32>();
        }
    }
}
```

## Comments

-   **Anonymous**  
    September 19, 2007  
    PingBack from [http://msdnrss.thecoderblogs.com/2007/09/19/why-is-generic-constructor-not-allowed/](http://msdnrss.thecoderblogs.com/2007/09/19/why-is-generic-constructor-not-allowed/)
