---
title: "Two versions of an assembly in an AppDomain"
date: 2007-04-11
slug: two-versions-of-an-assembly-in-an-appdomain
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/two-versions-of-an-assembly-in-an-appdomain
---

# Two versions of an assembly in an AppDomain

The CLR binder today allows you to either accidentally or intentionally load two versions of a given assembly in an AppDomain. The pain point of this design is that it fails at run time when specific operations are performed with respect to types that are references in both the assemblies which will be majority of the cases.

Let us make a small sample to illustrate the problem that you will encounter if you had two versions of an assembly in an AppDomain:

Let us call this DomSample.cs

```csharp
using System;
using System.Reflection;
class DomSample {
    public static void Main() {
        try {
            Assembly a1 = Assembly.Load("Foo, Version=2.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            Assembly a2 = Assembly.Load("Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            Type ty1 = Type.GetType("Foo, Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            Type ty2 = Type.GetType("Foo, Foo, Version=2.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            Console.WriteLine("V1 Type = {0}", ty1.Assembly.FullName.ToString());
            Console.WriteLine("V2 Type = {0}", ty2.Assembly.FullName.ToString());
            if (!Type.Equals(ty1, ty2)){
                Console.WriteLine("The types are different");
            }
            Foo t1 = (Foo)Activator.CreateInstance(ty1);
            Foo t2 = (Foo)Activator.CreateInstance(ty2);
        }
        catch (Exception e)
        {
            Console.WriteLine(e.ToString());
        }
    }
}
```

Let us call this v1/Helper.cs

```csharp
using System;
using System.Reflection;
[assembly: System.Reflection.AssemblyVersion("1.0.0.0")]
public class Helper {
    public void HelperMethod() {
        Console.WriteLine("Version = {0}", (Assembly.GetExecutingAssembly()).ToString());
    }
}
```

Let us call this v2/Helper.cs

```csharp
using System;
using System.Reflection;
[assembly: System.Reflection.AssemblyVersion("2.0.0.0")]
public class Helper {
    public void HelperMethod() {
        Console.WriteLine("Version = {0}", (Assembly.GetExecutingAssembly()).ToString());
    }
}
```

Let us compile the two versions of the helper methods and place them in the GAC using gacutil /I Helper.dll accordingly. Let us compile the DomSample.cs using one of the versions of the helper assembly and execute it.

C:\temp\AppDomain>DomMain.exe

```text
V1 Type = Helper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94
V2 Type = Helper, Version=2.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94
```

The types are different

System.InvalidCastException: Unable to cast object of type 'Helper' to type 'Helper'.

`at DomSample.Main() in c:\temp\AppDomain\DomMain.cs:line 30`

The two types that you created are different and when you try to cast these types you get an InvalidCastException.

Have any of you encountered this? What is your thought about this problem? I would be interested to hear more about this.

## Comments

-   **Anonymous**  
    April 11, 2007  
    I have encountered similar things with Assembly.LoadFrom. Some thoughts about this are here: [http://geekswithblogs.net/akraus1/archive/2006/04/04/74323.aspx](http://geekswithblogs.net/akraus1/archive/2006/04/04/74323.aspx) Yours,   Alois Kraus
    
-   **Anonymous**  
    April 11, 2007  
    Thanks Alois for your input. I read your write and agree that it indeed is a painful experience. The load contexts cause the problem of the same assembly being loaded multiple times. I have another entry which I am working on which talks about a similar problem.
    
-   **Anonymous**  
    April 11, 2007  
    I think this is a great feature. I would like to go even further though, there should also be an API to manipulate/explore load contexts. This would make it much easier to run things side-by-side in the same AppDomain (and do things like building a managed compiler).
    
-   **Anonymous**  
    April 11, 2007  
    Thanks Jeroen for your feedback. Aren't the run time type cast exceptions a pain to deal with though?
    
-   **Anonymous**  
    April 16, 2007  
    They can be. I've actually added a debugging mode to my compiler to make debugging these problems easier. When enabled, it replaces all casts with a method call that does the type check and throws a more verbose error that includes the full assembly name. It would be nice if the CLR's InvalidCastException would include ExpectedType and ActualType properties.
    
-   **Anonymous**  
    April 17, 2007  
    Can you eloborate on a scenario where you absolutely require this?
    
-   **Anonymous**  
    May 11, 2007  
    I have the same problem and I was wondering about this solution: What if I defined the interface in a C++/ATL project (.idl file) and built a COM type library. Then in my .Net class, I can mark the interface definition with [ComImport]. In that case, will the CLR (or whatever) still try to add a version number to the interface and thereby have a problem with casting it? I have several implementations of my IFoo. Some are .Net implementations defined in the local assembly (which is also in the GAC and may be side-by-side with other versions) and some are COM/ATL implementations. The cast to IFoo never seems to be a problem for the COM impelementations; only for the .Net implementations... Any thoughts? Bob
    
-   **Anonymous**  
    October 11, 2007  
    You need to create Interface IFoo and then derive class from that and in typecast Use that IFoo interface
    
-   **Anonymous**  
    October 28, 2009  
    Research the ILMerge utility.  I think it can  determine that a single class referenced in multiple assemblies is one-and-the-same class, and then pull all the distinct class definitions into a single assembly.
    
-   **Anonymous**  
    October 28, 2009  
    I forgot to mention, in ILMerge, the specific option of interest is "UnionMerge", which actually merges members of all classes with the same name into a single class of that name.
