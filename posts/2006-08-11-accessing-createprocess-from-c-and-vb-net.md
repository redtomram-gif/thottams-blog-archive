---
title: "Accessing CreateProcess from C# and VB.NET"
date: 2006-08-11
slug: accessing-createprocess-from-c-and-vb-net
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/accessing-createprocess-from-c-and-vb-net
---

# Accessing CreateProcess from C# and VB.NET

I came across this issue recently. I was trying to access CreateProcess using DLLImport from managed code using C# and VB.Net. Here is my code from C#:

<!-- C# -->

    using System;
    using System.Diagnostics;
    using System.Runtime.InteropServices;
    public struct PROCESS_INFORMATION
    {
        public IntPtr hProcess;
        public IntPtr hThread;
        public uint dwProcessId;
        public uint dwThreadId;
    }
    public struct STARTUPINFO
    {
        public uint cb;
        public string lpReserved;
        public string lpDesktop;
        public string lpTitle;
        public uint dwX;
        public uint dwY;
        public uint dwXSize;
        public uint dwYSize;
        public uint dwXCountChars;
        public uint dwYCountChars;
        public uint dwFillAttribute;
        public uint dwFlags;
        public short wShowWindow;
        public short cbReserved2;
        public IntPtr lpReserved2;
        public IntPtr hStdInput;
        public IntPtr hStdOutput;
        public IntPtr hStdError;
    }
    public struct SECURITY_ATTRIBUTES
    {
        public int length;
        public IntPtr lpSecurityDescriptor;
        public bool bInheritHandle;
    }
    public class Program
    {
        public static void Main()
        {
            STARTUPINFO si = new STARTUPINFO();
            PROCESS_INFORMATION pi = new PROCESS_INFORMATION();
            CreateProcess("C:\\WINDOWS\\SYSTEM32\\Calc.exe", null, IntPtr.Zero, IntPtr.Zero, false, 0, IntPtr.Zero, null, ref si, out pi);
            Console.ReadLine();
        }
        [DllImport("kernel32.dll")]
        static extern bool CreateProcess(string lpApplicationName, string lpCommandLine, IntPtr lpProcessAttributes, IntPtr lpThreadAttributes,
            bool bInheritHandles, uint dwCreationFlags, IntPtr lpEnvironment,
            string lpCurrentDirectory, ref STARTUPINFO lpStartupInfo,out PROCESS_INFORMATION lpProcessInformation);
    }

This works like a charm. I initially had problems with this VB code. I looked at the IL code for both and figured out that I had constructed structures in C# for Process_Information, Security_Attricutes etc, but had marked them as classes in VB. This was mking the call to fail. I figured this out after looking into the IL from VB and C# to see what was different between the two. I changed the VB to struct and it works like a charm in VB too. Check the VB code below!

<!-- VB.NET -->

    Imports System.Runtime.InteropServices
    Imports System.Security.Permissions
    Imports System.Reflection
    Module Module1
    Sub Main()
    StartupNotepad()
    Console.ReadLine()
    End Sub
    Sub StartupNotepad()
    Dim retValue As Boolean
    Dim pInfo As PROCESS_INFORMATION = New PROCESS_INFORMATION()
    Dim sInfo As STARTUPINFO = New STARTUPINFO()
    retValue = CreateProcess("c:\\windows\\system32\\NotePad.exe", Nothing, IntPtr.Zero, IntPtr.Zero, False, 0, IntPtr.Zero, Nothing, sInfo, pInfo)
    End Sub

<StructLayout(LayoutKind.Sequential)> _

Public Structure PROCESS_INFORMATION

Public hProcess As IntPtr

Public hThread As IntPtr

Public dwProcessID As UInteger

Public dwThreadID As UInteger

`End Structure 'PROCESS_INFORMATION`

<StructLayout(LayoutKind.Sequential)> _

Public Structure SECURITY_ATTRIBUTES

Public nLength As Integer

Public lpSecurityDescriptor As IntPtr

Public bInheritHandle As Boolean

`End Structure 'SECURITY_ATTRIBUTES`

<StructLayout(LayoutKind.Sequential)> _

Public Structure STARTUPINFO

Public cb As UInteger

Public lpReserved As String

Public lpDesktop As String

Public lpTitle As String

Public dwX As UInteger

Public dwY As UInteger

Public dwXSize As UInteger

Public dwYSize As UInteger

Public dwXCountChars As UInteger

Public dwYCountChars As UInteger

Public dwFillAttribute As UInteger

Public dwFlags As UInteger

Public wShowWindow As Short

Public cbReserved2 As Short

Public lpReserved2 As IntPtr

Public hStdInput As IntPtr

Public hStdOutput As IntPtr

Public hStdError As IntPtr

`End Structure 'STARTINFO`

<DllImport("kernel32.dll")> _

`Function CreateProcess( _`

ByVal lpApplicationName As String, _

ByVal lpCommandLine As String, _

ByVal lpProcessAttributes As IntPtr, _

ByVal lpThreadAttributes As IntPtr, _

ByVal bInheritHandles As Boolean, _

ByVal dwCreationFlags As UInteger, _

ByVal lpEnvironment As IntPtr, _

ByVal lpCurrentDirectory As String, _

ByRef lpStartupInfo As STARTUPINFO, _

ByRef lpProcessInformation As PROCESS_INFORMATION) As Boolean

<!-- VB.NET -->

    End Function
    End Module

## Comments

-   **Anonymous**  
    August 12, 2006  
    a. Any reason why the Process class is not enough?  
    b. are you aware of [http://www.pinvoke.net/default.aspx/coredll/CreateProcess.html](http://www.pinvoke.net/default.aspx/coredll/CreateProcess.html)  
    
-   **Anonymous**  
    August 13, 2006  
    Could you explain a little bit more about why structs are compiled as classes and what are the implications of that? What about that attribute??  
      
    Thanks  
    
-   **Anonymous**  
    August 14, 2006  
    Using porocess is another alternative. I am aware of pinvoke.net. The sample for CreateProcess from there did not work for me.  
      
    It was my mistake on the wording. I had authored the struct as classes in VB and hence the problem. I changed them to structs and it worked fine.
    
-   **Anonymous**  
    August 14, 2006  
    Did you update the sample (and anything else you found to be incorrect) for the pinvoke entry?  :)
    
-   **Anonymous**  
    August 14, 2006  
    The sample represented PROCESS_INFORMATION and SECURITY_ATTRIBUTES and STARTUPINFO as classes in VB. This made the call to fail. I changed them to struct in VB and I could get notepad up and running from the VB code! The working sample is the code above.
    
-   **Anonymous**  
    August 16, 2006  
    The comment has been removed
    
-   **Anonymous**  
    August 16, 2006  
    I totally agree with you and would think that to be a better decision.  
      
    There were a few questions on the web relating to this not working and I just wanted to aaddress that. Regardig using this over existing Process class, I agree with you that it will be a better choice.  
      
    Question to users:  
    If you are using it, can you explain the scenario that force you to using this?
    
-   **Anonymous**  
    August 22, 2006  
    The comment has been removed
    
-   **Anonymous**  
    December 07, 2006  
    Here's one reason why one would prefer to use CreateProcess this way as opposed to the managed code of System.Diagnostics.Process is that the Process class quite simply does not exist in the .NET Compact Framework 1.0 So while Microst assumes that the minute hey shipped the newer version of the compact framework all older devices stopped existing, a few unlucy souls such as myself still have to develop for devices running the CF 1.0 I am very grateful to have stmbled across this article for that very reason.
    
-   **Anonymous**  
    January 30, 2007  
    Sorry to be a month late on this>  you also have to pInvoke to create a process if you are trying to run the process in a different user context (some WTS applications).
    
-   **Anonymous**  
    February 07, 2007  
    These are some good scenarios for this. Can't you use System.Diagnostics.Process to create a process after you impersonate an user? I have not tried it, but would be interesting to know.
    
-   **Anonymous**  
    May 06, 2007  
    Great article!!! Thanks for help :) Vlada, jelovac.vladimir@cont.co.yu
    
-   **Anonymous**  
    July 02, 2007  
    Another reason would be that you cannot create a process in suspended mode with the .NET Process class.
    
-   **Anonymous**  
    July 24, 2007  
    Beauty lies in utility & simplicity. Thank you!
    
-   **Anonymous**  
    September 22, 2008  
    One reason to use this instead of Process.Start is that Process.Start has a upper limit of maximum 2048 characters passed as parameter to the process. This method has a maximum of 32000-something
    
-   **Anonymous**  
    November 24, 2008  
    thanks very useful in creating a new Process group with C#, i think System.Diagnostics.Process lacks this
    
-   **Anonymous**  
    December 11, 2008  
    Thank you, that was very very useful. Tell me, how do you find the Window's handle ? (HWND) from createprocess ?
    
-   **Anonymous**  
    July 30, 2009  
    The comment has been removed
    
-   **Anonymous**  
    October 11, 2009  
    I tried this code, it works on Win xp and does not on citrix. I use createprocess to call AcroRd32.exe with switches (args) to print. AcroRd32.exe /h /t "mypdf" "myprinter" Any ideas why this does not work on citrix? How can i debug this code.? Appreciate any input. Thanks.
    
-   **Anonymous**  
    June 18, 2013  
    That worked fine but how could we spawn this process in C#.
    
-   **Anonymous**  
    June 18, 2013  
    Problem found in spawning the ftp process. Kindly help out !!
