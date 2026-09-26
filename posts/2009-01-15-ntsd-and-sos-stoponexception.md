---
title: "NTSD and SOS: StopOnException"
date: 2009-01-15
slug: ntsd-and-sos-stoponexception
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/ntsd-and-sos-stoponexception
---

# NTSD and SOS: StopOnException

`!StopOnException` comes in handy when you want to break when a specific
exception occurs.

The program below throws an `OutOfMemoryException` on every iteration of the
loop except one, where it throws a single `ArgumentException`. That gives you
something to practise on: plenty of noise, and one exception you actually care
about.

<!-- C# -->

    using System;
    class Program
    {
        static void Main(string[] args)
        {
            Program p = new Program();
            p.ExceptionSample();
        }
        private void ExceptionSample()
        {
            int i=0;
            while (i < 100)
            {
                if (i == 60)
                {
                    try
                    {
                        throw new ArgumentException();
                    }
                    catch (Exception)
                    {
                    }
                }
                else
                {
                    try
                    {
                        throw new OutOfMemoryException();
                    }
                    catch (Exception)
                    {
                    }
                }
                i++;
            }
        }
    }

Build it with debug information and start it under the debugger:

<!-- Console -->

    csc /debug Program.cs
    ntsd program.exe

Add the current directory to the symbol path, break when the runtime is loaded,
and let the program go:

<!-- Console -->

    .sympath+ .
    sxe ld mscorwks
    g

Now that mscorwks is loaded, load SOS, tell it to stop the first time an
`ArgumentException` is created, and continue:

<!-- Console -->

    .loadby sos mscorwks
    !StopOnException -Create System.ArgumentException 1
    g

The debugger breaks on the iteration that throws `ArgumentException`.
`!ClrStack -a` shows the managed stack along with the parameters and locals of
each frame:

<!-- Console -->

    !ClrStack -a
    OS Thread Id: 0x103c (0)
    Child-SP         RetAddr          Call Site
    000000000019ee50 000007ff00180182 Program.ExceptionSample()
        PARAMETERS:
            this = 0x0000000002603358
        LOCALS:
            0x000000000019ee78 = 0x000000000000003c
            0x000000000019ee7c = 0x0000000000000000
    000000000019eec0 000007fefa3f2672 Program.Main(System.String[])
        PARAMETERS:
            args = 0x0000000002603338
        LOCALS:
            0x000000000019eee0 = 0x0000000002603358

The first local of `ExceptionSample` is `0x3c`, which is 60 — exactly the
iteration that throws the `ArgumentException`.

`!pe` prints the details of the exception itself:

<!-- Console -->

    !pe
    Exception object: 00000000026087d0
    Exception type: System.ArgumentException
    Message: Value does not fall within the expected range.
    InnerException: <none>
    StackTrace (generated):
    <none>
    StackTraceString: <none>
    HResult: 80070057

This will stop when the exception occurs. You can print the details of the
exception using `!pe` and look into the locals using `!ClrStack -a`. This comes
in handy when you want to debug a problem and break at the point the exception
is thrown, rather than sifting through every exception the process happens to
raise.
