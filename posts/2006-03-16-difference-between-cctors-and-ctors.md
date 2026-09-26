---
title: "Difference between .cctors and ctors"
date: 2006-03-16
slug: difference-between-cctors-and-ctors
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/difference-between-cctors-and-ctors
---

# Difference between .cctors and ctors

A static constructor is called when a direct access to any static member of the class is made. The static constructor cannot be used to create an instance of the class. The following example demonstrates this clearly. When you reflect for constructors on a class and use the static BindingFlags then you will get the static constructor as well. An invocation on this will fail with System.MemberAccessException as this method cannot be invoked. If you do not want to get the .cctor as part of your result then remove the static from the BindingFlags.

A simple print with Name will tell you if this is a ctor or a cctor.

The example below demonstrates this scenario clearly.

```csharp
using System;
using System.Reflection;
class Test
{
    // This is the cctor for the class
    static Test() { }
    // These two are the constructors for the class
    public Test(int i) { }
    public Test(string s) { }
}
class Demo
{
    static void Main()
    {
        // This will give a compilation error as there is no
        // constructor for Test that does not take any parameters
        //
        //Test t = new Test();
        Type[] paramtypes = new Type[0];
        // Call to get a constructor that does not take any parameters using the BindingFlags.Static will return the .cctor which
        // is the static constructor, but this cannot be invoked
        //
        ConstructorInfo ctor = typeof(Test).GetConstructor(
            BindingFlags.Static | BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic, null,
            CallingConventions.Standard, paramtypes, null);
        // The below snippet of code will return 3 constructors
        //
        ConstructorInfo[] c = typeof(Test).GetConstructors(BindingFlags.Static | BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic);
        // Reflection returns the static constructor for the type as well which cannot be invoked explicitly
        // In the example below you will see a list of constructors including the cctor which is the only one that does not take any parameters
        // This cannot be invoked.
        foreach (ConstructorInfo c1 in c)
        {
            Console.WriteLine(c1.ToString());
        }
        // The below snippet of code will return 2 constructors
        //
        ctor = typeof(Test).GetConstructor(
            BindingFlags.Static | BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic, null,
            CallingConventions.Standard, paramtypes, null);
        c = typeof(Test).GetConstructors(BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic);
        // Reflection returns the static constructor for the type as well which cannot be invoked explicitly
        // In the example below you will see a list of constructors including the cctor which is the only one that does not take any parameters
        // This cannot be invoked.
        foreach (ConstructorInfo c1 in c)
        {
            Console.WriteLine(c1.ToString());
        }
        // This constructor cannot be invoked as this is .cctor for the type. You will get a System.MemberAccessException
        //
        ctor.Invoke(Type.EmptyTypes);
    }
}
```

## Comments

-   **Anonymous**  
    March 16, 2006  
    I guess when you mention about .cctor you could also talk about beforefieldinit. Just a suggestion.
-   **Anonymous**  
    March 24, 2006  
    That is a very good suggestion. I was thinking of that more as an internal concept and left it out. Would you be able to add some information from your perspective here on this? If not, I will add this information when I get to this soon.
-   **Anonymous**  
    March 24, 2006  
    Here is apot from Brada on .beforefieldinit  
      
    [http://blogs.msdn.com/brada/archive/2004/04/17/115300.aspx](http://blogs.msdn.com/brada/archive/2004/04/17/115300.aspx)
-   **Anonymous**  
    April 27, 2006  
    I mentioned a while back that I change from being a PM for System.Reflection and System.Reflection.Emit..e.t.c...
