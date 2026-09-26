---
title: "Overriding a property using new and reflection"
date: 2006-03-16
slug: overriding-a-property-using-new-and-reflection
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/overriding-a-property-using-new-and-reflection
---

# Overriding a property using new and reflection

When you override a property using new with a new type there is discrepancy in behavior from C# and reflection. C# is not aware of the property in the base class and it has been overridden by the new property. But reflection is well aware of both the properties depending on what you are asking for. If you want the derived class property only you need to specify the BindingFlags.DeclaredOnly flag or else it will walk the entire hierarchy.

```csharp
using System;
using System.Reflection;
class Base {
    public string MyName {
        set {}
        get{ return "Hello";}
```

}a

```csharp
}
class Derived : Base {
    public new int MyName {
        set {}
    }
}
class Test {
    public static void Main() {
        Base b = new Base();
        Derived d = new Derived();
        // Trying to get all properties works
        //
        PropertyInfo[] p = (PropertyInfo [])typeof(Derived).GetProperties();
        foreach(PropertyInfo p1 in p) {
            Console.WriteLine(p1.ToString());
        }
        // Trying to get the property from the derived class alone works
        //
        PropertyInfo p2 = typeof(Derived).GetProperty("MyName", BindingFlags.DeclaredOnly|BindingFlags.Instance | BindingFlags.Public);
        if(p2 != null) {
            Console.WriteLine(p2.ToString());
        }
        // This call is ambiguous and throws an exception (System.Reflection.AmbiguousMatchException)
        //
        typeof(Derived).GetProperty("MyName",BindingFlags.Instance | BindingFlags.Public);
        // This will not compile as the get property is not defined in the derived class
        //
        // Console.WriteLine("Value of MyName in derived class: ", d.MyName);
    }
}
```

## Comments

-   **Anonymous**  
    April 27, 2006  
    I mentioned a while back that I change from being a PM for System.Reflection and System.Reflection.Emit..e.t.c...
    
-   **Anonymous**  
    August 11, 2006  
    Thank so much for posting this ... it really helped me overcome this exact problem.
    
-   **Anonymous**  
    August 11, 2006  
    Thanks for the note. It feels good to find that my posting helped someone and encourages me to post more like this.
    
-   **Anonymous**  
    April 23, 2007  
    Is there a way to get the inherited properties as well while using BindingFlags.DeclaredOnly? Suppose that the base class had the property A, which the derived class wishes to get (e.g. typeof(Derived).GetProperty("A", ...)). i.e. is there a way to return all inherited properties, while at the same time, if there is are multiple properties with the same name, return only those declared at the calling-type's level? Thanks a lot
    
-   **Anonymous**  
    April 25, 2007  
    Thottam blogged about how C# and Reflection differ in how they bind to hidden properties: [http://blogs.msdn.com/thottams/archive/2006/03/17/553376.aspx](http://blogs.msdn.com/thottams/archive/2006/03/17/553376.aspx)
    
-   **Anonymous**  
    April 25, 2007  
    I discussed binding to hidden properties using Reflection here: [http://blogs.msdn.com/weitao/archive/2007/04/26/binding-to-hidden-properties-using-reflection.aspx](http://blogs.msdn.com/weitao/archive/2007/04/26/binding-to-hidden-properties-using-reflection.aspx)
    
-   **Anonymous**  
    January 21, 2009  
    PingBack from [http://www.keyongtech.com/432604-how-to-override-a-property](http://www.keyongtech.com/432604-how-to-override-a-property)
