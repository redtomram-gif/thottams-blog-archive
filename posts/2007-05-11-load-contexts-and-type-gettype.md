---
title: "Load contexts and Type.GetType"
date: 2007-05-11
slug: load-contexts-and-type-gettype
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/load-contexts-and-type-gettype
---

# Load contexts and Type.GetType

`Type.GetType(AN,T) actually translates to Assembly.Load(AN).GetType(T). This actually can be the cause of many confusions. Often programmers end up in a situation where they have explicitly loaded the assembly, but Type.GetType is not able to find the type from the assembly they just loaded.`

Let us look into the sample below. Let us do a Type.GetType for Foo and provide the path to the location of Foo in the config file as mentioned below.

Copy the source below to TypeSample.cs and the config to TypeSample.exe.config

```
using System;
using System.Reflection;
class DomSample {
    public static void Main(){
        try {
            Type ty1 = Type.GetType("Foo, Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            if (ty1 != null) {
          Console.WriteLine("V1 Type = {0}", ty1.Assembly.FullName.ToString());
            }
            else {
                Console.WriteLine("Unable to find type ...");
            }
        }
        catch (Exception e) {
            Console.WriteLine(e.ToString());
        }
    }
}
```

<configuration>

```
   <runtime>
      <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
         <probing privatePath="v1" />
      </assemblyBinding>
   </runtime>
```

</configuration>

Make sure that you create a path v1 under APPBASE and create a Foo.cs which is compiled and signed to Foo.dll. Copy the following source to Foo.cs and comple it to Foo.dll and sign it with the key (see my article on signinig)

```
using System;
using System.Reflection;
[assembly: System.Reflection.AssemblyVersion("1.0.0.0")]
public class Foo {
    public void Bar() {
        Console.WriteLine("Version = {0}", (Assembly.GetExecutingAssembly()).ToString());
    }
}
```

If you run TypeSample.exe you will get the following output

V1 Type = Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94

If you run the sample under ntsd you will notice that Foo.dll is loaded from directory v1 in the default AppDomain

Opened log file 'ntsd.log'

`0:000> !dumpdomain`

--------------------------------------

System Domain: 7a390b60

LowFrequencyHeap: 7a390b84

HighFrequencyHeap: 7a390be0

StubHeap: 7a390c3c

Stage: OPEN

Name: None

--------------------------------------

Shared Domain: 7a391138

LowFrequencyHeap: 7a39115c

HighFrequencyHeap: 7a3911b8

StubHeap: 7a391214

Stage: OPEN

Name: None

Assembly: 001e8828

--------------------------------------

Domain 1: 001b0890

LowFrequencyHeap: 001b08b4

HighFrequencyHeap: 001b0910

StubHeap: 001b096c

Stage: OPEN

SecurityDescriptor: 001b1a98

Name: DomSample.exe

Assembly: 001e8828 [C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll]

ClassLoader: 001e88c0

SecurityDescriptor: 001e8738

`Module Name`

790c2000 C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll

Assembly: 001f95e0 [c:\blog\May2007\DomSample.exe]

ClassLoader: 001f9678

SecurityDescriptor: 001f94e0

`Module Name`

00712c24 c:\blog\May2007\DomSample.exe

Assembly: 00208128 [c:\blog\May2007\v1\Foo.dll]

ClassLoader: 00208398

SecurityDescriptor: 00208298

`Module Name`

007130d8 c:\blog\May2007\v1\Foo.dll

Now let us modify the code above to use LoadFrom and then try the Type.GetType.

Copy the code below to TypeSampleLoadFrom.cs and compile it to TypeSampleLoadFrom.exe. Let us not provide a config file this time such Type.GetType cannot find the assembly Foo when it tries to load it.

```
using System;
using System.Reflection;
class DomSample {
    public static void Main() {
        try {
            Assembly.LoadFrom("c:\\Blog\\May2007\\v2\\Foo.dll");
            Type ty1 = Type.GetType("Foo, Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94");
            if(ty1 != null) {
                Console.WriteLine("V1 Type = {0}",ty1.Assembly.FullName.ToString());
            }
            else {
                Console.WriteLine("Unable to find type ...");
            }
        }
        catch(Exception e) {
            Console.WriteLine(e.ToString());
        }
    }
}
```

The above program yields the result "Unable to find type ...". If you watch the program under ntsd you will notice that Foo.dll is loaded from the LoadFrom, but the Type.GetType was not able to use the already loaded Foo.dll or find Foo.dll to load it to retreive the type information

`0:001> !dumpdomain`

--------------------------------------

System Domain: 7a390b60

LowFrequencyHeap: 7a390b84

HighFrequencyHeap: 7a390be0

StubHeap: 7a390c3c

Stage: OPEN

Name: None

--------------------------------------

Shared Domain: 7a391138

LowFrequencyHeap: 7a39115c

HighFrequencyHeap: 7a3911b8

StubHeap: 7a391214

Stage: OPEN

Name: None

Assembly: 002e86e8

--------------------------------------

Domain 1: 002b08c8

LowFrequencyHeap: 002b08ec

HighFrequencyHeap: 002b0948

StubHeap: 002b09a4

Stage: OPEN

Name: DomSample1.exe

Assembly: 002e86e8 [C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll]

ClassLoader: 002e8780

SecurityDescriptor: 002f4ef8

`Module Name`

790c2000 C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll

Assembly: 002fa790 [c:\blog\May2007\DomSample1.exe]

ClassLoader: 002fa828

SecurityDescriptor: 002fa690

`Module Name`

00102c24 c:\blog\May2007\DomSample1.exe

Assembly: 00302bc0 [c:\Blog\May2007\v2\Foo.dll]

ClassLoader: 003072b0

SecurityDescriptor: 00306b88

`Module Name`

00103120 c:\Blog\May2007\v2\Foo.dll

Let us modify the above sample and add a config TypeSampleLoadFrom.exe.config as follows

<configuration>

```
   <runtime>
      <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
         <probing privatePath="v1" />
      </assemblyBinding>
   </runtime>
```

</configuration>

You will get the output V1 Type = Foo, Version=1.0.0.0, Culture=neutral, PublicKeyToken=1faea1974f697f94.

If you run the program under the debugger you will find that there are two Foo.dll loaded in the default AppDomain one loaded from the LoadFrom context and the other load by Type.GetType in the other context. I had specifically placed the same Foo.dll in different locations to make this happen. The Foo.dll loaded by Type.GetType was loaded from v1 directory and the Foo.dll loaded from the LoadFrom context was loaded from v2 directory. If you had both point to the same location, you will however see only one copy of the dll.

`0:000> !dumpdomain`

--------------------------------------

System Domain: 7a390b60

LowFrequencyHeap: 7a390b84

HighFrequencyHeap: 7a390be0

StubHeap: 7a390c3c

Stage: OPEN

Name: None

--------------------------------------

Shared Domain: 7a391138

LowFrequencyHeap: 7a39115c

HighFrequencyHeap: 7a3911b8

StubHeap: 7a391214

Stage: OPEN

Name: None

Assembly: 003386f8

--------------------------------------

Domain 1: 003008a0

LowFrequencyHeap: 003008c4

HighFrequencyHeap: 00300920

StubHeap: 0030097c

Stage: OPEN

SecurityDescriptor: 00301aa8

Name: DomSample1.exe

Assembly: 003386f8 [C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll]

ClassLoader: 00338790

SecurityDescriptor: 00344ed0

`Module Name`

790c2000 C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll

Assembly: 0034a7c8 [c:\blog\May2007\DomSample1.exe]

ClassLoader: 0034a860

SecurityDescriptor: 0034a6c8

`Module Name`

002d2c24 c:\blog\May2007\DomSample1.exe

Assembly: 00365b00 [c:\Blog\May2007\v2\Foo.dll]

ClassLoader: 00365998

SecurityDescriptor: 00358038

`Module Name`

002d3120 c:\Blog\May2007\v2\Foo.dll

Assembly: 00366bd0 [c:\blog\May2007\v1\Foo.dll]

ClassLoader: 00366a58

SecurityDescriptor: 00365c48

`Module Name`

002d3548 c:\blog\May2007\v1\Foo.dll

## Comments

-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2868](http://www.travel-hilarity.com/airline_travel/?p=2868)
    
-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2868](http://www.travel-hilarity.com/airline_travel/?p=2868)
