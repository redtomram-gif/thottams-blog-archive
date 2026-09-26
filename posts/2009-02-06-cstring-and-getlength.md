---
title: "CString and GetLength"
date: 2009-02-06
slug: cstring-and-getlength
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/cstring-and-getlength
---

# CString and GetLength

I ran into this problem recently when debugging some native code and thought that it will be good to share this with everyone.

<!-- C++ -->

    CString sampleString = CString(_T("Sample\0String"), 14);
    int len = sampleString.GetLength(); // len is 14
    CString trimmedString = sampleString.Trim(); // trimmedString = "Sample"
    CString newstring = CString(sampleString); // newString = "Sample"
    len = newstring.GetLength(); // len = 14

CString GetLength returns the length that you passed to it in the constructor and not the length of the string. This can be confusing if you copy the string and loop through the length. It can also be the cause of bugs is you get the length and use CString.GetBuffer() and loop through the buffer for the length.

It looks like CString.GetLength() is the size of the internal buffer and nothing more.

MSDN documentation for CString - [https://msdn.microsoft.com/en-us/library/aa300471(VS.60).aspx](https://msdn.microsoft.com/en-us/library/aa300471\(VS.60\).aspx)

## Comments

-   **Anonymous**  
    February 06, 2009  
    PingBack from [http://www.clickandsolve.com/?p=4610](http://www.clickandsolve.com/?p=4610)
    
-   **Anonymous**  
    February 06, 2009  
    Nice post. Where is CString.Trim() in MSDN? Is it typo of CString.TrimRight()?
    
-   **Anonymous**  
    February 07, 2009  
    Interesting, I could't find it either. But it compiles and works. It is supposed to remove leading and trailing white spaces I would think.
    
-   **Anonymous**  
    February 26, 2009  
    Good one !!! Bro, do you remember me ?
    
-   **Anonymous**  
    March 05, 2009  
    Am I correct to assume that this is a heavy bug? Just tried this code in Visual Studio 2005 and it really behaved as described. I was sure that CString::GetLength() would always return the number of charactes up to the first null terminator (without including it in the count). All my code relies on that assumption...
    
-   **Anonymous**  
    March 06, 2009  
    I would agree. I did not realize this until I ran into this issue recently. The assumption that CString makes may be is that the data in the buffer is not binary and does not contain null terminators in it. But that assumption is not evident from the documentation.
    
-   **Anonymous**  
    March 11, 2009  
    Do you have any plans to officially report this bug to Microsoft? If not, do you know what would be the place to do so on my own? And also in this case, would I be allowed to copy/paste your code from above into this bug report? Thanks, Patrik
    
-   **Anonymous**  
    March 13, 2009  
    Let me try to contact the right owners and see what they have to say. In the mean time feel free to use the code above.
    
-   **Anonymous**  
    September 27, 2009  
    use strlen() I'm pretty sure GetLength can only bu used with assert, like an if statment.
    
-   **Anonymous**  
    April 28, 2010  
    Use ReleaseBuffer for memory modifications that are external to the CString Class.
    
-   **Anonymous**  
    August 18, 2010  
    The comment has been removed
    
-   **Anonymous**  
    August 18, 2010  
    Here we are, [msdn.microsoft.com/.../ms174288.aspx](http://msdn.microsoft.com/en-us/library/ms174288.aspx) "A CString object keeps character data in a CStringData object. CString accepts null-terminated C-style strings, but does not retain the null character in the stored character data. Instead, CString tracks string length. CString does provide a null terminator when it exports a C-style string. You can insert a null character amidst stored character data, but it may produce unexpected results."
