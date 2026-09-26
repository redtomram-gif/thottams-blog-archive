---
title: "Type forwarding using TypeForwardedTo attribute in Runtime.CompilerServices"
date: 2006-11-16
slug: type-forwarding-using-typeforwardedto-attribute-in-runtime-compilerservices
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/type-forwarding-using-typeforwardedto-attribute-in-runtime-compilerservices
---

# Type forwarding using TypeForwardedTo attribute in Runtime.CompilerServices

We used this feature to solve a problem internally and I was tempted to blog about this cool feature. Type forwarding allows you to move a type from one assembly to another without having to recompile the application that uses the original assembly.

Here is how this works:

Writing the original library (original.cs)

```csharp
using System;
public class ClassToBeForwardedLater
{
    public void SomeMethod()
    {
        Console.WriteLine("Inside ClassToBeForwardedLater in Original.dll");
    }
}
```

Let us compile this to original.dll as below

csc /t:library original.cs

Building the application using the original assembly (application.exe)

```csharp
using System;
class Application
{
    public static void Main()
    {
        ClassToBeForwardedLater ctbf = new ClassToBeForwardedLater();
        ctbf.SomeMethod();
    }
}
```

Let us compile the executable as below

csc /r:original.dll application.cs

Let us run application.exe. You will get the following output

Inside ClassToBeForwardedLater in Original.dll

Re-implementing the original assembly in a new assembly (newlibrary.cs)

```csharp
using System;
public class ClassToBeForwardedLater
{
    public void SomeMethod()
    {
        Console.WriteLine("Inside ClassToBeForwarded in newlibrary");
    }
}
```

Let us compile the newlibrary.dll as below

csc /t:library newlibrary.cs

Re-publish the original assembly, but this time with a type forwarded

```csharp
using System;
using System.Runtime.CompilerServices;
[assembly:TypeForwardedTo(typeof(ClassToBeForwardedLater))]
```

Let us compile the original library as below

csc /t:library /r:newlibrary.dll original.cs

Let us run application.exe again and see what we get.

Inside ClassToBeForwardedLater in newlibrary

The method in the newlibrary is invoked though the application was built with a reference to original.dll. If you do ildasm on application.exe and view the metadata section you will see a reference to original.dll.

## Comments

-   **Anonymous**  
    November 29, 2006  
    Good simple example. Nice work. Muthu
    
-   **Anonymous**  
    December 01, 2006  
    Thanks Muthu for the feedback. Makes me feel good when I read comments like this.
    
-   **Anonymous**  
    December 07, 2006  
    I read the COM Interop article on MSDN mag and thought I'd look up your blog.  I learnt new things from your blog.  I'll keep checking frequently.  
    
-   **Anonymous**  
    December 13, 2006  
    Thanks Krishnan. Did you find the article useful? I am planning on writing another article on COM interop, do you have any thoughts for this?
    
-   **Anonymous**  
    March 26, 2007  
    May be it's just me, but i don't see any practical use of this attribute. I mean if you can modify original dll then you might as well change somemethod() instead of forwarding to to other class. You mentioned that you used it to solve some problem internally, may be you can tell us what was that problem.
    
-   **Anonymous**  
    April 25, 2007  
    The comment has been removed
    
-   **Anonymous**  
    November 01, 2007  
    Very clear example, Thanks, but I have a doubt! We specify [assembly:TypeForwardedTo(typeof(ClassToBeForwardedLater))] in the original assembly but where does this assembly point to the new one for that method. In other words, how does the main application know that func1 method, which was in the original 'aaa' assembly is now in new 'bbb' and not in a new 'ccc' assembly. Reagards,
    
-   **Anonymous**  
    December 10, 2007  
    Thanks for a nice example. It is very informative, but for making it also good for the beginers, include the type of project that they have to deal with. I was just going through some questions of MCTS and this was one of the questions. Good going !!!
    
-   **Anonymous**  
    January 01, 2008  
    hey jay according to my understanding the runtime knows in which assembly the Type has been forwarded to   because we have included the reference in the compile statement csc /t:library /r:newlibrary.dll original.cs Another thing you can do is add the using newlibrary.cs in the  orignal.cs file If you have more then one assembly having the same Type eg. ClassToBeForwardedLater. What you can do is give the complete name of the type like this using AssemblyB.cs [assembly:TyprFrowardedTo(typeof(AssemblyB.ClassToBeForwardedLater))] so that the compiler knows which assembly we are talking about . this is my first time writing a blog , I request all you out there to please comment and let me know if i am right or wrong my email add is alimalik03a@yahoo.com
    
-   **Anonymous**  
    January 08, 2008  
    Ali is right. It gets the reference from the reference during compilation. Gopinath, I didn't understand your question. What do you mean by the type of project? Are you referring to the project in Visual Studio?
    
-   **Anonymous**  
    February 02, 2008  
    Thanks for such a nice explanation Mr. Sriram. And also thanks for all the contributors to the comments specially Ali and Rob to explain further details regarding the subject. Regards, Ali.Net
    
-   **Anonymous**  
    February 02, 2008  
    The comment has been removed
