---
title: "Generic list and powershell"
date: 2009-10-12
slug: generic-list-and-powershell
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/generic-list-and-powershell
---

# Generic list and powershell

I met with an accident recently riding my motorcycle and hence was unable to blog for some time. Here is my post after a brief break.

I wanted to create a generic list of my class in power shell. It was an interesting experience. Here is what I found.

When I tried to create a generic list of say type int it was straight forward ... well sort of ... the syntax was awkward, but still worked.

```
$list = New-Object "System.Collections.Generic.List``1[System.Int32]"
$list.Add(3)
$list
```

I wrote a sample assembly as below:

```
using System;
public class Sample
{
    public Sample() {}
    public void SampleMethod() { Console.WriteLine("Hello");}

}
```

Compiled this to sample.dll as csc /target:library sample.cs

`[Reflection.Assembly]::LoadFrom("c:\Sample\Sample.dll")`

Make sure if the assembly is loaded

`[System.AppDomain]::CurrentDomain.GetAssemblies()`

Now add the class Sample to the list. It gave me the error below.

`$list = New-Object "System.Collections.Generic.List``1[Sample]"`

New-Object : Cannot find type [System.Collections.Generic.List`1[Test]]: make sure the assembly containing this type is loaded.

At line:1 char:19 + $list = New-Object <<<< "System.Collections.Generic.List``1[Test]"

I am not sure why this is not working as the syntax is very similar to the one I had above for Int32. After some search and experimenting I figured out another way to get the generic list working.

```
$si = new-object Sample
$st = [Type] $si.GetType()
$base = [System.Collections.Generic.List``1]
$qt = $base.MakeGenericType(@($st))
$so = [Activator]::CreateInstance($qt)
$so.Add($si)
```

write-host $so

Sample

`$so[0].SampleMethod()`

Hello

## Comments

-   **Anonymous**  
    October 14, 2009  
    Man, hope you're OK. I am also riding a bike and other people simply don't see us on the road. Nice post, will bookmark it for future reference.
    
-   **Anonymous**  
    April 10, 2013  
    Very good post, simple and effective !!
