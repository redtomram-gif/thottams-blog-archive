---
title: "PInvoke-Reverse PInvoke and __stdcall - __cdecl"
date: 2007-06-02
slug: pinvoke-reverse-pinvoke-and-__stdcall-__cdecl
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/pinvoke-reverse-pinvoke-and-__stdcall-__cdecl
---

# PInvoke-Reverse PInvoke and __stdcall - __cdecl

I came across this issue yesterday and thought that I will blog about the same. The example below demonstrates a simple PInvoke and Reverse PInvoke through delegates. The initial call was defined to use the __cdecl convention. As you can notice when executing the code the stack gets corrupted and the for loop ends prematurely.

Copy code to: nat.cpp

Compile: cl /LD nat.cpp

```
#include <stdio.h>
#include <string.h>
typedef void ( *callback)(wchar_t * str);
```

extern "C" __declspec(dllexport) void caller(wchar_t * input, int count, callback call)

```
{
      for(int i = 0; i < count; i++)
      {
            call(input);
      }
}
```

Copy code to: man.cs

Compile: csc man.cs

```
using System.Runtime.InteropServices;
public class foo
{
    public delegate void callback(string str);
    public static void callee(string str)
    {
        System.Console.WriteLine("Managed: " +str);
    }
    public static int Main()
    {
        caller("Hello World!", 10, new callback(foo.callee));
        return 0;
    }
    [DllImport("nat.dll",CallingConvention=CallingConvention.StdCall)]
    public static extern void caller(string str, int count, callback call);
}
```

The above code prints the “Managed: Hello World!” twice and exits. To fix this let us change the calling convention to __stdcall.

Copy code to: nat.cpp

Compile: cl /LD nat.cpp

```
#include <stdio.h>
#include <string.h>
typedef void (__stdcall *callback)(wchar_t * str);
```

extern "C" __declspec(dllexport) void __stdcall caller(wchar_t * input, int count, callback call)

```
{
      for(int i = 0; i < count; i++)
      {
            call(input);
      }
}
```

Now if you run the code it will print the message “Managed: Hello World!” 10 times. Marshalling and getting the signatures can be extremely subtle.

## Comments

-   **Anonymous**  
    June 12, 2007  
    Is there an MDA that allows you to detect this calling convention mismatch? Also you might want to read this - [http://blogs.msdn.com/mithuns/archive/2006/12/11/pay-attention-to-the-calling-convention.aspx](http://blogs.msdn.com/mithuns/archive/2006/12/11/pay-attention-to-the-calling-convention.aspx)
    
-   **Anonymous**  
    April 01, 2009  
    The comment has been removed
    
-   **Anonymous**  
    October 10, 2013  
    6 years later, this still works. :) Thanks.
    
-   **Anonymous**  
    July 10, 2014  
    The comment has been removed
