---
title: "NTSD and SOS basics"
date: 2007-02-12
slug: ntsd-and-sos-basics
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/ntsd-and-sos-basics
---

# NTSD and SOS basics

I wanted to take a very simple console application and use ntsd with SOS to debug it. The example demonstrated very simple operations as setting a break point, viewing the managed stack, stack variables, object instance etc.

We can go deeper into some of the in depth concepts in the next post.

Let us take a simple console application as below:

```csharp
using System;
public class sample
{
    string str;
    public void MyMethod(string arg)
    {
        str = "Member Variable";
        Console.WriteLine("Argument: {0} - {1]", arg, str);
    }
    static void Main()
    {
        sample s = new sample();
        s.MyMethod("Hello");
    }
}
```

We would like to do the following as part of our debugging.

1.  Set a break point on MyMethod
2.  Watch the variables passed to the method
3.  Watch the instance of sample and its variables

Let us compile the application with debug enabled. Type

csc /Debug App.cs

This would generate App.exe and App.pdb. Now, let us start debugging this application:

C:\Blog>ntsd App.exe

```text
0:000> .symfix
0:000> .sympath+ .
0:000> .reload
0:000> sxe –c “ “ clrn
0:000> g
0:000> .loadby sos mscorwks
0:000> !bpmd App.exe sample.MyMethod
0:000> g
0:000> !clrstack –a
```

OS Thread Id: 0x154c (0)

ESP EIP

001bf268 009700f0 sample.MyMethod(System.String)

```text
    PARAMETERS:
        this = 0x01501964
        arg = 0x01501948
```

001bf26c 009700a9 sample.Main()

```text
    LOCALS:
        <CLR reg> = 0x01501964
```

001bf490 79e8273b [GCFrame: 001bf490]

`0:000> !DumpObj 0x01501948`

Name: System.String

MethodTable: 790fc6cc

EEClass: 790fc62c

Size: 28(0x1c) bytes

(C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll)

String: Hello

Fields:

`MT Field Offset Type VT Attr Value Name`

790ff7f0 4000096 4 System.Int32 0 instance 6 m_arrayLength

790ff7f0 4000097 8 System.Int32 0 instance 5 m_stringLength

790fe2dc 4000098 c System.Char 0 instance 48 m_firstChar

790fc6cc 4000099 10 System.String 0 shared static Empty >> Domain:Value 003507e8:790d7eb4 <<

7913cb00 400009a 14 System.Char[] 0 shared static WhitespaceChars >> Domain:Value 003507e8:01501548 <<

`0:000> !DumpObj 0x01501964`

Name: sample

MethodTable: 002a301c

EEClass: 002a1200

Size: 12(0xc) bytes

(C:\blog\app.exe)

Fields:

`MT Field Offset Type VT Attr Value Name`

790fc6cc 4000001 4 System.String 0 instance 00000000 str

```text
// Type in a couple of “p” until you get to the place where the local variable is assgned the value “Member Variable”
0:000> !DumpObj 0x01501964
```

Name: sample

MethodTable: 000c301c

EEClass: 000c1200

Size: 12(0xc) bytes

(C:\blog\app.exe)

Fields:

`MT Field Offset Type VT Attr Value Name`

790fc6cc 4000001 4 System.String 0 instance 01411970 str

`0:000>  !DumpObj 01411970`

Name: System.String

MethodTable: 790fc6cc

EEClass: 790fc62c

Size: 48(0x30) bytes

(C:\Windows\assembly\GAC_32\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll)

String: Member Variable

Fields:

`MT Field Offset Type VT Attr Value Name`

790ff7f0 4000096 4 System.Int32 0 instance 16 m_arrayLength

790ff7f0 4000097 8 System.Int32 0 instance 15 m_stringLength

790fe2dc 4000098 c System.Char 0 instance 4d m_firstChar

790fc6cc 4000099 10 System.String 0 shared static Empty >> Domain:Value 001007c8:790d7eb4 <<

7913cb00 400009a 14 System.Char[] 0 shared static WhitespaceChars >> Domain:Value 001007c8:01411548 <<

## Comments

-   **Anonymous**  
    February 13, 2007  
    Unfortunately, much of this does not currently work for the public. For example, the version of SOS that ships with the 2.0 CLR is old and does not include the !bpmd command. (There are, of course, more manual ways to set a managed breakpoint.) There is no 2.0 version of SOS currently included in the debugging tools distribution.
    
-   **Anonymous**  
    February 13, 2007  
    Dan, I loaded the 2.0.50727.42 version of SOS.dll and confirmed that it includes !bpmd.  That's the version of SOS that ships with the 2.0 redist. I think that version of SOS also ships with the 2.0  SDK.  If you find a package that includes the wrong SOS, please tell us exactly where it came from so we can investigate. Some early 2.0 beta builds might have included a version of SOS that didn't have !bpmd.  And  WinDbg ships with an old build of SOS that only works with 1.1 and 1.0.  But if you're debugging 2.0 then your SOS should have version # 2.0.50727.42. HTH, Bridgette
    
-   **Anonymous**  
    February 13, 2007  
    I just checked and !bpmd is there. HOWEVER, it's not listed in the output of !help, so I always assumed it wasn't. Thanks.
    
-   **Anonymous**  
    February 15, 2007  
    I tried !help and see BPMD there as well.
    
-   **Anonymous**  
    March 01, 2007  
    Nice and simple. 2 minute kick-start to managed debugging and gives pointers in the right direction. Looking forward to future aricles on debugging of multi-threaded applications.
    
-   **Anonymous**  
    March 28, 2007  
    Where is the good old EAX register (the clr equivalent) after a .call?  I'm sure I'm looking right at it, but it doesn't appear to be on the stack.  Thanks
