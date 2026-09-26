---
title: "NTSD and SOS – Adding a break point on a method with reference to a specific thread"
date: 2007-03-02
slug: ntsd-and-sos-adding-a-break-point-on-a-method-with-reference-to-a-specific-thread
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/ntsd-and-sos-adding-a-break-point-on-a-method-with-reference-to-a-specific-thread
---

# NTSD and SOS – Adding a break point on a method with reference to a specific thread

I wanted to set a conditional break point from managed code and it was hard to do that using SOS. In the sample below, let us assume that I want to break in to the method ThreadMethod when a specific thread tries to access it. I want to thank [Varun Sekhri](https://blogs.gotdotnet.com/varunsekhri/ "Varun Sekhri"), Rajesh Gunnalan and Michael Rayhelson who helped me figure out a few things that I did not know about.

```csharp
using System;
using System.Threading;
public class sample
{
    public void ThreadMethod()
    {
        while(true)
        {
            Console.WriteLine("{0}", Thread.CurrentThread.Name);
            Thread.Sleep(600);
        }
    }
    static void Main()
    {
        sample s = new sample();
        Thread t1 = new Thread(s.ThreadMethod);
        Thread t2 = new Thread(s.ThreadMethod);
        t1.Name = "First Thread";
        t2.Name = "Second Thread";
        t1.Start();
        t2.Start();
        Console.WriteLine("Enter any string to stop execution:");
        String line = Console.ReadLine();
        t1.Abort();
        t2.Abort();
    }
}
```

**Here is how you would do it:**

Compile the sample using csc /debug thread.cs

windbg thread.exe

CommandLine: thread.exe

Symbol search path is: symsrv*symsrv.dll*c:\symbols*[https://msdl.microsoft.com/download/symbols](https://msdl.microsoft.com/download/symbols)

7c901230 cc int 3

`0:000> .reload`

Reloading current modules

....

```text
0:000> sxe ld mscorwks
0:000> g
0:000> .loadby sos mscorwks
```

Let us set a break point on Main to begin with

`0:000> !bpmd thread.exe sample.Main`

Adding pending breakpoints...

If you notice the debug spew, Main method just got JITed when we hit the break point

`0:000> g`

JITTED thread!sample.Main()

Setting breakpoint: bp 00DD0070 [sample.Main()]

Breakpoint 0 hit

thread!sample.Main():

00dd0070 57 push edi

`0:000> !Name2EE thread.exe sample.ThreadMethod`

Module: 00a22c14 (thread.exe)

Token: 0x06000001

MethodDesc: 00a22fd8

Name: sample.ThreadMethod()

Not JITTED yet. Use !bpmd -md 00a22fd8 to break on run.

**The ThreadMethod has not been JITed as yet. You cannot set an unmanaged break point on it as yet. For now, let us set a managed break point on this method.**

`0:000> !bpmd thread.exe sample.ThreadMethod`

Found 1 methods...

`MethodDesc = 00a22fd8`

Adding pending breakpoints...

`0:000> g`

When you hit the break point, if you look below thread ID 3 is trying to access the method. The method is not JITed and its unmanaged address is 00dd01c0. If you unassemble the code in this address you will see the disassembly of the method. Let us add a break point at WriteLine when thread 3 tries to access this method.

`0:000> bl`

0 e [c:\test\thread.cs @ 16] 0001 (0001) 0:**** thread!sample.Main()

1 e [c:\test\thread.cs @ 7] 0001 (0001) 0:**** thread!sample.ThreadMethod()

```text
0:000> bc 0
0:000> bc 1
0:000> !Threads
```

ThreadCount: 4

UnstartedThread: 1

BackgroundThread: 1

PendingThread: 1

DeadThread: 0

Hosted Runtime: no

PreEmptive GC Alloc Lock

ID OSID ThreadOBJ State GC Context Domain Count APT Exception

```text
   0 1 f34 00180f70 a020 Enabled 01381cac:01381fe8 0014c100 1 MTA
   2 2 e68 00152318 b220 Enabled 00000000:00000000 0014c100 0 MTA (Finalizer)
   3 3 988 00199f28 b020 Disabled 0138202c:01383fe8 0014c100 0 MTA
```

XXXX 4 6fc 0019a700 9420 Enabled 00000000:00000000 0014c100 0 Ukn

`0:000> !Name2EE thread.exe sample.ThreadMethod`

Module: 00a22c14 (thread.exe)

Token: 0x06000001

MethodDesc: 00a22fd8

Name: sample.ThreadMethod()

JITTED Code Address: 00dd01c0

`0:000> u 00dd01c0`

thread!sample.ThreadMethod() [c:\test\thread.cs @ 7]:

00dd01c0 57 push edi

00dd01c1 56 push esi

00dd01c2 53 push ebx

00dd01c3 50 push eax

00dd01c4 890c24 mov dword ptr [esp],ecx

00dd01c7 833dc82da20000 cmp dword ptr ds:[0A22DC8h],0

00dd01ce 7405 je thread!sample.ThreadMethod()+0x15 (00dd01d5)

00dd01d0 e829212c79 call mscorwks!JIT_DbgIsJustMyCode (7a0922fe)

`0:000> u`

thread!sample.ThreadMethod()+0x15 [c:\test\thread.cs @ 7]:

00dd01d5 33db xor ebx,ebx

00dd01d7 90 nop

00dd01d8 90 nop

00dd01d9 eb2f jmp thread!sample.ThreadMethod()+0x4a (00dd020a)

00dd01db 90 nop

00dd01dc 8b3548303802 mov esi,dword ptr ds:[2383048h]

00dd01e2 e8997f6078 call mscorlib_ni!System.Threading.Thread.get_CurrentThread() (793d8180)

00dd01e7 8bf8 mov edi,eax

`0:000> u`

thread!sample.ThreadMethod()+0x29 [c:\test\thread.cs @ 10]:

00dd01e9 8bcf mov ecx,edi

00dd01eb 3909 cmp dword ptr [ecx],ecx

00dd01ed e83e846078 call mscorlib_ni!System.Threading.Thread.get_Name() (793d8630)

00dd01f2 8bf8 mov edi,eax

00dd01f4 8bd7 mov edx,edi

00dd01f6 8bce mov ecx,esi

00dd01f8 e8278f5e78 call mscorlib_ni!System.Console.WriteLine(System.String, System.Object) (793b9124)

00dd01fd 90 nop

**From the unassembly above, we identify that the address of WriteLine is** 00dd01f8. Now you can set an unmanaged break point when thread 3 access this line as below.

```text
0:000> ~3 bp 00dd01f8
0:000> g
```

Breakpoint 0 hit

```text
eax=01381a00 ebx=00000001 ecx=01382014 edx=01381a00 esi=01382014 edi=01381a00
eip=00dd01f8 esp=00edf8a0 ebp=01381afc iopl=0 nv up ei pl nz na po nc
cs=001b ss=0023 ds=0023 es=0023 fs=003b gs=0000 efl=00000202
```

thread!sample.ThreadMethod()+0x38:

00dd01f8 e8278f5e78 call mscorlib_ni!System.Console.WriteLine(System.String, System.Object) (793b9124)

**If you look at the current thread it is thread 3 that is trying to access the method**

`0:000> ~.`

. 3 Id: 528.988 Suspend: 1 Teb: 7ffdc000 Unfrozen

```text
      Start: mscorwks!Thread::intermediateThreadProc (79ecafc5)
      Priority: 0 Priority class: 32 Affinity: 3
0:000> g
```

Breakpoint 0 hit

```text
eax=01381a00 ebx=00000001 ecx=01382014 edx=01381a00 esi=01382014 edi=01381a00
eip=00dd01f8 esp=00edf8a0 ebp=01381afc iopl=0 nv up ei pl nz na po nc
cs=001b ss=0023 ds=0023 es=0023 fs=003b gs=0000 efl=00000202
```

thread!sample.ThreadMethod()+0x38:

00dd01f8 e8278f5e78 call mscorlib_ni!System.Console.WriteLine(System.String, System.Object) (793b9124)

`0:000> ~.`

. 3 Id: 528.988 Suspend: 1 Teb: 7ffdc000 Unfrozen

```text
      Start: mscorwks!Thread::intermediateThreadProc (79ecafc5)
      Priority: 0 Priority class: 32 Affinity: 3
```

## Comments

-   **Anonymous**  
    September 17, 2007  
    PingBack from [http://mindtorrent.com/blog/archives/7](http://mindtorrent.com/blog/archives/7)
    
-   **Anonymous**  
    May 03, 2008  
    0:000> sxe ld mscorwks 0:000> g "Breaks when loading mscorwks" 0:000> .loadby sos mscorwks 0:000> !bpmd thread.exe sample.Main 0:000> g Although I follow the procedures above, I never managed break at the sample.Main . Do I miss something ??
    
-   **Anonymous**  
    May 29, 2008  
    Strange. I tried and it works for me. Can you provide more information? Do you see the break point with bl and so on?
    
-   **Anonymous**  
    July 14, 2008  
    Hi, Same happens with me also. following is the outpupt I have got. Microsoft (R) Windows Debugger Version 6.8.0004.0 X86 Copyright (c) Microsoft Corporation. All rights reserved. CommandLine: C:thread.exe Symbol search path is: SRV_d:symbolspublic_[http://msdl.microsoft.com/download/symbols](http://msdl.microsoft.com/download/symbols) Executable search path is: c:WINNTsystem32 ModLoad: 00400000 00408000   thread.exe (1f0.888): Break instruction exception - code 80000003 (first chance) eax=00000000 ebx=00131f04 ecx=00000009 edx=00000000 esi=7ffdf000 edi=00131f70 eip=77f9193c esp=0012f984 ebp=0012fc98 iopl=0         nv up ei pl nz na po nc cs=001b  ss=0023  ds=0023  es=0023  fs=003b  gs=0000             efl=00000202 ntdll!DbgBreakPoint: 77f9193c cc              int     3 0:000> .reload Reloading current modules .... 0:000> sxe ld mscorwks 0:000> g ModLoad: 79e70000 7a3d6000   C:WINNTMicrosoft.NETFrameworkv2.0.50727mscorwks.dll eax=00000000 ebx=00000000 ecx=00860000 edx=00000000 esi=00000000 edi=7ffde000 eip=77f88647 esp=0012f49c ebp=0012f52c iopl=0         nv up ei pl zr na pe nc cs=001b  ss=0023  ds=0023  es=0023  fs=0038  gs=0000             efl=00000246 ntdll!NtMapViewOfSection+0xb: 77f88647 c22800          ret     28h 0:000> .loadby sos mscorwks 0:000> !bpmd thread.exe sample.Main Adding pending breakpoints... 0:000> bl 0:000> g After hitting g, debugger is showing debuggee is running. What could be the reason. Thank You, Anish Devasia
    
-   **Anonymous**  
    January 16, 2009  
    I tried this again. bl does not show the break point after the !bpmd in my case too. But the 'g' breaks at the break point and if I do a bl it shows the break point then. Are you saying that it did not break at all for you?
