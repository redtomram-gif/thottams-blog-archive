---
title: "How to control the version of .NET Framework your application runs against?"
date: 2007-06-11
slug: how-to-control-the-version-of-net-framework-your-application-runs-against
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/how-to-control-the-version-of-net-framework-your-application-runs-against
---

# How to control the version of .NET Framework your application runs against?

Let us look at a small sample that illustrated how this configuration entry works.

```csharp
using System;
class Sample
{
    public static void Main()
    {
        Console.WriteLine("This is a sample");
    }
}
```

Let us copy this code to RequireRuntime.cs and compile it. Let us now add a configuration file RequiredRuntime.exe.config which has the following entry in it. If you run it on a machine that has V2.0.50727 installed in it the application runs and prints the appropriate string.

<configuration>

```xml
   <startup>
      <requiredRuntime version="v2.0.50727"/>
   </startup>
```

</configuration>

Let us modify the configuration file to require a different version that does not exist. Copy the following to the same configuration file. When you execute the application you will get an error stating the following:

<configuration>

```xml
   <startup>
      <requiredRuntime version="v5.0.50727"/>
   </startup>
```

</configuration>

“To run this application, you first must install one of the following version of the .NET Framework:

V5.0.50727

Contact your application publisher for instructions about obtaining the appropriate version of the .NET Framework”

This is a real cool way to ensure that your application runs only againt the version of the .NET Framework you have tested it against. There is also another configuration entry called SupportedRuntime which comes in real handy. I will write about that in my next blog.

## Comments

-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2865](http://www.travel-hilarity.com/airline_travel/?p=2865)
    
-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2865](http://www.travel-hilarity.com/airline_travel/?p=2865)
