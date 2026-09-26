---
title: "Managed C++ and C#"
date: 2007-07-16
slug: managed-c-and-c
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/managed-c-and-c
---

# Managed C++ and C#

I tried writing a sample to use managed C++ to interact with managed code and here is my learning. I created the following managed C++ code to wrap existing FLAT API’s in a class that can be called form C#.

Managed C++ code:

#using <mscorlib.dll>

```cpp
#include <vcclr.h>
using namespace System;
#include <stdio.h>
#include <wchar.h>
__declspec(dllexport) void NativeFlatMethod(const wchar_t *szParam)
{
    wprintf(L"%s \n", szParam);
}
```

__gc public class FlatAPIWrapper

```csharp
{
    public:
    static void NativeFlatMethodWrapper(System::String *szParam)
    {
        const wchar_t __pin *pChar = PtrToStringChars(szParam);
        NativeFlatMethod(pChar);
    }
};
```

Copy the above code to native.cpp and compile it as below

Cl /LD /CLR:oldsyntax native.cpp

You can ildasm the resultant assembly and see its contents. Now we want to write a C# executable that can consume the native-managed assembly that we compiled here.

Managed code:

```csharp
using System;
using System.Runtime.InteropServices;
class Program
{
    static void Main()
    {
        FlatAPIWrapper.NativeFlatMethodWrapper("FLAT API called through Managed C++ wrapper");
    }
}
```

Copy the above code to managed.cs and compile it as below

csc /r:native.dll managed.cs

This will produce managed.exe. Copy the native.dll.manifest to managed.exe.manifest and execute the executable.

There are multiple ways managed code can interact with native code. This is one such way. The others are through interop and writing unsafe code in C# itself. Interacting with C++ libraries using PInvoke is extremely difficult. The only good way to achieve this will be to write managed C++ or FLAT API wrappers that can be consumed in C# using PInvoke. I will write about these soon.

## Comments

-   **Anonymous**  
    April 13, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=2864](http://www.travel-hilarity.com/airline_travel/?p=2864)
    
-   **Anonymous**  
    August 21, 2008  
    I've tried the code example and it fails on my system (vista 64 bit, visual studio 2005); C:tempcsharpstuff>managed.exe Unhandled Exception: System.BadImageFormatException: Could not load file or assembly 'native, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null' or one of its dependencies. An attempt was made to load a program with an incorrect format. File name: 'native, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null'   at Program.Main() WRN: Assembly binding logging is turned OFF. To enable assembly bind failure logging, set the registry value [HKLMSoftwareMicrosoftFusion!EnableLog] (DWORD) to 1. Note: There is some performance penalty associated with assembly bind failure logging. To turn this feature off, remove the registry value [HKLMSoftwareMicrosoftFusion!EnableLog].
    
-   **Anonymous**  
    October 10, 2008  
    regarding "managed C++ code"... using Visual Studio 2005, what wizard options are necessary to create a managed C++ project ? All I've ever done is: new -> project -> Visual C++ -> win32 -> win32 project -> OK -> Next -> select "Dll" and enable "Export Symbols" -> finish
    
-   **Anonymous**  
    October 10, 2008  
    additionally, I have a .lib that I'd need to use in the managed C++ project. Is that possible ? I imagine that the .lib contains unmanaged binaries and the project would be "managed".
    
-   **Anonymous**  
    November 27, 2008  
    hi alex, im also facing the same problem when using the .lib. anyway have u solve it and how did u solve it..hope u able to help me out.. thanks and regards norazanita@yahoo.com
    
-   **Anonymous**  
    November 15, 2009  
    Hi, I am developing a WebService(Asp.Net using c#). I need to run or invoke unmanaged C++ code from webservice. how can i do this one. pls guide me regarding this issue.
    
-   **Anonymous**  
    November 15, 2009  
    Hi, I am developing a WebService(Asp.Net using c#). I need to run or invoke unmanaged C++ code from webservice. how can i do this one. pls guide me regarding this issue. my mail id is asifbasha.honey@gmail.com
    
-   **Anonymous**  
    December 14, 2010  
    hi zaza still facing the same problem or solved alrdy?
