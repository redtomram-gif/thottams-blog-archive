---
title: "Managed Debugging Assistants (MDA's)"
date: 2006-10-20
slug: managed-debugging-assistants-mdas
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/managed-debugging-assistants-mdas
---

# Managed Debugging Assistants (MDA's)

When I read through the responses for the questionnaire below I saw that many users were not aware of MDA’s and that drove the topic for this post. I had written a small sample for a simple MDA which I thought that I will share with everyone.

MDA’s can help you diagnose problems that you might not know about normally. These are like advanced diagnostic messages that tell you what is going wrong in your application. There are many ways to turn MDA’s on and off. Let us look in to them here.

Consider the following source code (sample.cs):

```
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
public class Program {
    public static void Main() {
        Beep(5,5);
    }
    [DllImport("kernel32.dll", SetLastError=true)]
    [return: MarshalAs(UnmanagedType.Bool)]
    internal static extern bool Beep(long frequency, long duration);
}
```

Let us compile this code and execute it :- csc sample.cs /debug

This runs without any errors. If you enable MDA’s then it will let you know that the marshalling for Beep is wrong as it takes two DROWD and hence the parameter should be “int” instead of “long”. How do you catch this error is the question? MDA’s come in handy for this. Here is how you would do it with MDA’s.

**Step-1: Turning on MDA’s**

There is a global registry key which is used to turn on MDA’s.

Windows Registry Editor Version 5.00

`[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework]`

"MDA"="1"

**Step – 2: Selecting individual MDA’s as required:**

Now you can select individual MDA’s that you want in three different ways as mentioned below

1. Through an exe.mda.config file

2. Through environment variable

3. Through Exceptions in Visual Studio

**1. Enabling MDA’s through sample.exe.mda.config file**

Let us produce sample.exe.mda.config file as below:

<mdaConfig>

```
  <assistants>
    <pInvokeStackImbalance enable="true"/>
  </assistants>
```

</mdaConfig>

This enables the pInvokeStackImbalance MDA which will fire off when it finds irregularities in marshalling.

**2. Enabling MDA’s through environment variables**

Set the environment variable `COMPLUS_MDA as mentioned below.`

`COMPLUS_MDA=` pInvokeStackImbalance

**3. Enabling MDA’s from Visual Studio**

You can enable MDA’s from Visual Studio by going to Debug-Exceptions-Managing Debugging Assistants and selecting the MDA’s that are of interest to you.

Let us enable the pInvokeStackImbalance MDA using the sample.exe.mda.config option and run the above executable again. This time you get an error message as follows:

Managed Debugging Assistant 'PInvokeStackImbalance' has detected a problem in sample.exe.

Additional Information: A call to PInvoke function 'sample!Program::Beep' has unbalanced the stack. This is likely because the managed PInvoke signature does not match the unmanaged target signature. Check that the calling convention and parameters of the PInvoke signature match the target unmanaged signature

This is pretty good information that you can use to correct the signature of the pInvoke from long, long to int,int and re run the code. This time the MDA does not fire up. Your marshalling should be correct then! There is good information about MDA’s at the following locations and I recommend everyone to read them.

The msdn article at [MDA](https://msdn.microsoft.com/msdnmag/issues/06/05/BugBash/default.aspx "https://msdn.microsoft.com/msdnmag/issues/06/05/BugBash/default.aspx") walks through the same example that I have here with excellent details. I strongly urge reading through that. I read it and it is extremely useful.

Other references are:

[https://blogs.msdn.com/jmstall/archive/2005/11/10/introducing-mdas.aspx](../jmstall/managed-debug-assistants-mdas-are-cool)

[https://msdn2.microsoft.com/en-us/library/d21c150d.aspx](https://msdn2.microsoft.com/en-us/library/d21c150d.aspx)

## Comments

-   **Anonymous**  
    October 31, 2006  
    That is great stuff! For those of us doing WinForms and/or Interop, MDAs look like "just what the doctor ordered". Thanks for making this kind of information more widely known.
    
-   **Anonymous**  
    November 02, 2006  
    I am glad this was useful. It will be nice to know your experience after your tunred on MDA's in your application and if it was helpful in any form.
    
-   **Anonymous**  
    December 01, 2006  
    It provides very useful information. Thanks for doing this.
    
-   **Anonymous**  
    December 05, 2006  
    Thanks Tao. I am glad this was useful.
    
-   **Anonymous**  
    March 28, 2007  
    hello people is good day!!! [http://autonewsportal.info](http://autonewsportal.info) [http://autdotcar.info](http://autdotcar.info)
    
-   **Anonymous**  
    May 12, 2007  
    The comment has been removed
    
-   **Anonymous**  
    June 11, 2007  
    Try using the following signature for GetVolumeInformation Imports System.Runtime.InteropServices Public Declare Function GetVolumeInformation _    Lib "kernel32" Alias "GetVolumeInformationA" ( _    <MarshalAsAttribute(UnmanagedType.LPStr)> ByVal lpRootPathName As String, _    ByVal lpVolumeNameBuffer As System.IntPtr, _    ByVal nVolumeNameSize As UInteger, _    ByRef lpVolumeSerialNumber As UInteger, _    ByRef lpMaximumComponentLength As UInteger, _    ByRef lpFileSystemFlags As UInteger, _    ByVal lpFileSystemNameBuffer As System.IntPtr, _    ByVal nFileSystemNameSize As UInteger _    ) As <MarshalAsAttribute(UnmanagedType.Bool)> Boolean
    
-   **Anonymous**  
    June 12, 2007  
    Actually even better is to use StringBuilder for the 2nd and 7th parameters:  Private Declare Function GetVolumeInformation _    Lib "kernel32" Alias "GetVolumeInformationA" ( _    <MarshalAsAttribute(UnmanagedType.LPStr)> ByVal lpRootPathName As String, _    ByVal lpVolumeNameBuffer As StringBuilder, _    ByVal nVolumeNameSize As UInteger, _    ByRef lpVolumeSerialNumber As UInteger, _    ByRef lpMaximumComponentLength As UInteger, _    ByRef lpFileSystemFlags As UInteger, _    ByVal lpFileSystemNameBuffer As StringBuilder, _    ByVal nFileSystemNameSize As UInteger _    ) As <MarshalAsAttribute(UnmanagedType.Bool)> Boolean
    
-   **Anonymous**  
    August 12, 2008  
    <a href= [http://index7.riesxd.com](http://index7.riesxd.com) >intetin</a> <a href= [http://index3.riesxd.com](http://index3.riesxd.com) >t mobil cell phones plans</a> <a href= [http://index4.riesxd.com](http://index4.riesxd.com) >mfc42.dll islinkedto missing export kernel32.dll</a> <a href= [http://index2.riesxd.com](http://index2.riesxd.com) >deastisdesigns</a> <a href= [http://index8.riesxd.com](http://index8.riesxd.com) >triamcindlone cream</a>
    
-   **Anonymous**  
    February 07, 2012  
    It is really helpful. I was trying how to turn off pInvokeStackImbalance but could not get the answer. Thanks
