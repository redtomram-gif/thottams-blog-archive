---
title: "Debugging load problems using fusion log"
date: 2007-06-02
slug: debugging-load-problems-using-fusion-log
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/debugging-load-problems-using-fusion-log
---

# Debugging load problems using fusion log

Fusion log comes in very handy when you want to understand or debug binding behaviors. Let us take the sample code below. Copy the code to DomSample.cs and compile it. Let us assume for now that the code tries to load some arbitrary assembly “Foo” which does not exist. If you run this code you will see the following exception.

```csharp
using System;
using System.Reflection;
class DomSample
{
    public static void Main()
    {
        try
        {
            Assembly.Load("Foo");
        }
        catch(Exception e)
        {
            Console.WriteLine(e.ToString());
        }
    }
}
```

System.IO.FileNotFoundException: Could not load file or assembly 'Foo' or one of its dependencies. The system cannot find the file specified.

File name: 'Foo'

Let us set the registry key to enable fusion logging. HKLM/Software/Microsoft/Fusion/EnableLog DWORD 1. The [MSDN page](https://msdn2.microsoft.com/en-us/library/e74a18c4\(vs.71\).aspx) describes how to enable fusion log.

Now if you run the same executable you will see a more descriptive failure. The failure will be as follows:

--- A detailed error log follows.

=== Pre-bind state information ===

LOG: User = NTDEV\thottams

LOG: DisplayName = Foo

(Partial)

LOG: Appbase = file:///C:/blog/june/

LOG: Initial PrivatePath = NULL

Calling assembly : DomSample, Version=0.0.0.0, Culture=neutral, PublicKeyToken=n

ull.

===

LOG: This bind starts in default load context.

LOG: No application configuration file found.

LOG: Using machine configuration file from C:\Windows\Microsoft.NET\Framework\v2

.0.50727\config\machine.config.

LOG: Policy not being applied to reference at this time (private, custom, partia

l, or location-based assembly bind).

LOG: Attempting download of new URL file:///C:/blog/june/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/Foo/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/Foo.EXE.

LOG: Attempting download of new URL file:///C:/blog/june/Foo/Foo.EXE.

If you notice it clearly tells you the various paths the binder searched to find the file. Let us add some oribing paths to this executables configuration and see what happens. Let us create the file DomSample.exe.config and add the following to it:

<configuration>

```xml
   <runtime>
      <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
         <probing privatePath="BAR" />
      </assemblyBinding>
   </runtime>
```

</configuration>

If you run the executable now you will find that the binder searches in a few more locations to find the assembly Foo this time. The results from the log look as below:

=== Pre-bind state information ===

LOG: User = NTDEV\thottams

LOG: DisplayName = Foo

(Partial)

LOG: Appbase = file:///C:/blog/june/

LOG: Initial PrivatePath = NULL

Calling assembly : DomSample, Version=0.0.0.0, Culture=neutral, PublicKeyToken=n

ull.

===

LOG: This bind starts in default load context.

LOG: Private path hint found in configuration file: BAR.

LOG: Using application configuration file: C:\blog\june\DomSample.exe.config

LOG: Using machine configuration file from C:\Windows\Microsoft.NET\Framework\v2

.0.50727\config\machine.config.

LOG: Policy not being applied to reference at this time (private, custom, partia

l, or location-based assembly bind).

LOG: Attempting download of new URL file:///C:/blog/june/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/Foo/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/BAR/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/BAR/Foo/Foo.DLL.

LOG: Attempting download of new URL file:///C:/blog/june/Foo.EXE.

LOG: Attempting download of new URL file:///C:/blog/june/Foo/Foo.EXE.

LOG: Attempting download of new URL file:///C:/blog/june/BAR/Foo.EXE.

LOG: Attempting download of new URL file:///C:/blog/june/BAR/Foo/Foo.EXE.

This information can be extremely useful when you are investigating binder issues.

## Comments

-   **Anonymous**  
    June 02, 2007  
    Ce post de Thottam R. Sriram me fait penser que je devais me faire depuis longtemps un petit post pense-bête
    
-   **Anonymous**  
    October 24, 2007  
    The comment has been removed
    
-   **Anonymous**  
    March 18, 2008  
    Reference HowtheRuntimeLocatesAssemblies [http://msdn2.microsoft.com/en-us/library/yx7xezcf.a](http://msdn2.microsoft.com/en-us/library/yx7xezcf.a)...
    
-   **Anonymous**  
    December 26, 2009  
    When an assembly fails to load the exception's message says "blah blah, go to HKLMSoftwareMicrosoftFusion!EnableLog" while in fact the exclamation mark is a typo, it shuold have been a backslash - HKLMSoftwareMicrosoftFusionEnableLog.
    
-   **Anonymous**  
    June 30, 2010  
    2bagel: ! means that the EnableLog is a DWORD value instead of a key.
