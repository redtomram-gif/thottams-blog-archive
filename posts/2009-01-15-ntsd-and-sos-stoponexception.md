---
title: "NTSD and SOS: StopOnException"
date: 2009-01-15
slug: ntsd-and-sos-stoponexception
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/ntsd-and-sos-stoponexception
---

# NTSD and SOS: StopOnException

```text
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

!StopOnException comes in handy when you want if you want to break when a specific exception occurs.
```

csc /debug Program.cs

ntsd program.exe

`.sympath+ .`

sxe ld mscorwks

g

```text
.loadby sos msorwks
!StopOnException -Create System.ArgumentException 1
```

g

`!ClrStack -a`

OS Thread Id: 0x103c (0)

Child-SP RetAddr Call Site

000000000019ee50 000007ff00180182 Program.ExceptionSample()

```text
    PARAMETERS:
        this = 0x0000000002603358
    LOCALS:
        0x000000000019ee78 = 0x000000000000003c
        0x000000000019ee7c = 0x0000000000000000
```

000000000019eec0 000007fefa3f2672 Program.Main(System.String[])

```text
    PARAMETERS:
        args = 0x0000000002603338
    LOCALS:
        0x000000000019eee0 = 0x0000000002603358
!pe
```

Exception object: 00000000026087d0

Exception type: System.ArgumentException

Message: Value does not fall within the expected range.

InnerException: <none>

`StackTrace (generated):`

<none>

StackTraceString: <none>

HResult: 80070057

This will stop when the exception occurs. You can print the details of the exception using pe and look into locals using the clrstack -a. This comes in handy if you want to debug some problem and want to break when the exception occurs.
