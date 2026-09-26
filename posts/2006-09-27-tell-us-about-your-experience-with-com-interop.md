---
title: "Tell us about your experience with COM interop"
date: 2006-09-27
slug: tell-us-about-your-experience-with-com-interop
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/tell-us-about-your-experience-with-com-interop
---

# Tell us about your experience with COM interop

COM interoperability is a very powerful feature in the CLR that bridges the gap between native and managed code. It lets you reuse your existing COM and non-COM native DLL in the managed environment easily. It also lets native code call back to managed code in a seamless way.

It provides a wealth of features which I wanted to talk about here in some detail.

1.  tlbimp
2.  tlbexp
3.  tlbref
4.  regasm
5.  RCW and Life Time Management
6.  CCW
7.  Marshalling and Custom Marshalling
8.  pInvoke
9.  Eventing
10.  System.Runtime.Interopservices namespace
11.  MDA and debugging
12.  Threading/Apartment/Context
13.  Availability of documentation with code samples

There are inherent differences in the way native code, COM code and managed code work. This adds to lots of confusion in the ways they interact and work together. This in my opinion has been a significant pain point for users of this technology. I also feel that this area is highly complex when it comes to identifying and solving a scenario, task or problem that you have to solve. As an example, I have been asked the question of when should users use custom marshalling many times now. We want to make the user/developer experience with this feature better and are looking for some input on the same. Here are some questions that I have that will help me understand the problems that our customers face with this technology

Questions:

1.  **Knowledge/Skill of user**
    1.  Is your usage of COM primarily as a component consumer (e.g. via RAD-style usage in an IDE) or component developer (e.g. via ATL)?
    2.  Have you developed a COM/Unmanaged component in a commercial software package?
    3.  Have you developed a commercial software package on the Visual Basic platform (specifically VB6/VB.NET)?
    4.  Do you view your usage of COM Interop as transitional/migratory, or is it a permanent element of your architecture going forward? How skilled are your developers with unmanaged, COM and managed code?
    5.  Has the COM Interop functionality from the CLR played a role in your application compatibility / backwards compatibility story? Do you find your adoption of .NET is biased by legacy needs?
2.  **Customer Scenario and choice of technology**
    1.  What is your end user customer scenario? (Ex: Five components A, B, C, D, E. A is a legacy COM component. B is our UI/Business application in C# which needs functionality from A. etc)
    2.  What is the scenario inside that which drives you to use COM interop as a solution? (Ex: Reuse of existing functionality/library that is available in Com from managed code. Exposing managed API’s for COM to customers etc)
    3.  How did you decide that COM interop was the best solution for the problem?
    4.  How difficult was the decision and what were the alternatives that you considered? (Ex: complete native implementation, re-implementing in managed, managed C++ bridge, COM interop etc)
    5.  What features of COM interop do you use and for what scenarios and reasons? If disparate state them separately with explanation. (Ex: tlbimp, RCW, CCW, Eventing, pInvoke, Marshalling etc)
    6.  What is the diversity of your environment in terms of architecture (32 and 64 bit) and languages (C++, COM, VB, C# etc)?
3.  **Developer experience during development**
    1.  Do you use the COM interop technologies from Visual Studio or from command prompt? (Ex: run tlbimp from command or import through Visual Studio)
    2.  What parts of the COM interop technology from the list above does development use to solve the above scenario?
    3.  What is the heaviest of the above that makes the meat of the product? (Ex: Marshalling, Eventing etc)
    4.  What are the most complicated problems that you have encountered during development and in which areas?
    5.  How do you investigate problems in this area? (Ex: Use MDA, Debug using windbg etc)
4.  **Servicing experience**
    1.  Have you encountered significant end user problems because of failures in your COM interop later? If yes, which part of COM interop did they come from? (List top 4 areas)
    2.  What were the problems in most of the cases? Was it with the platform or your implementation?
    3.  How did you identify the problem?
    4.  How did you solve the problem?
5.  **Debugging experience**
    1.  How do you debug problems in COM interop?
    2.  Do you use windbg, SOS, public symbols etc?
    3.  Do you use MDA’s for your debugging? ([https://msdn2.microsoft.com/en-us/library/d21c150d.aspx](https://msdn2.microsoft.com/en-us/library/d21c150d.aspx))
    4.  Do you look at you native and managed code in oleview and ildasm, reflector to see what is happening under the hood?
    5.  What is your average time spent in a problem in this area? How many problem have you seen in this area in a given product cycle (months)?
6.  **Help and documentation available**
    1.  What specific material do you refer for this area and how useful is it?
    2.  Do you know about pInvoke.net? Do you use it?
    3.  How would you rate MSDN documentation for this area? What is lacking if any?
    4.  Do you find enough samples available to get you on the ramp with the various technologies here?
    5.  How do you rate our help and documentation on MDA’s?
7.  **Other – Any other things that you want us to know**

I want to be clear that I am not looking for adding new features to this area, but looking for making the experience with the current features easier and better. Your input here will be valuable in driving this technology in the right direction.

## Comments

-   **Anonymous**  
    September 28, 2006  
    The comment has been removed
    
-   **Anonymous**  
    September 28, 2006  
    Why no mention of direct C++ interop using C++/CLI?
    
-   **Anonymous**  
    September 28, 2006  
    I think spending resources making COM interop easier is not the way to go.  
      
    Make it unnecessary by means of managed wrappers.  There are still too many pieces  
    of Win32 with no wrappers.  
      
    I was hoping for more of this with Vista, but it looks like most of that got chopped.  
      
    A set of questions for another topic would be:  
    a) Windows Platform interrop,  
    b) 3rd party dll's, ocx's etc,    
    c) legacy in house code.  
      
    If A, then find out the best subsystems to wrap next..    
      
      
      
    
-   **Anonymous**  
    September 29, 2006  
    The comment has been removed
    
-   **Anonymous**  
    September 29, 2006  
    The comment has been removed
    
-   **Anonymous**  
    September 29, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 02, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 03, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 10, 2006  
    Here is a major issue I have encountered with COM Interop - its a MSDN newsgroup posting I made yesterday : A class in assembly A implements a COM interface as follows : [ComImport, Guid("CEF04FDF-FE72-11d2-87A5-00C04F6837CF"), InterfaceType(ComInterfaceType.InterfaceIsIUnknown)] internal interface IMyInterface { [PreserveSig] int MyMethod(); } class MyClass : IMyInterface { int MyMethod() { ... } } This interface is exposed to the unmanaged world via COM Interop. Now, assembly B also defines IMyInterface and gets this interface from the unmanaged world as follows : IntPtr unk = GetMyInterface(); // Gets IMyInterface from unmanaged world object obj = Marshal.GetObjectFromIUnkown(unk); // its really the class MyClass from assembly A above IMyInterface myIntf = obj as IMyInterface; // this cast does not work and returns null becuase of separate declarations of IMyInterface in the two assemblies ! How to get this scenario to work? One would think that .Net would recognize that both the interfaces are the same ( based on the ComImport and Guid attributes) and allow the cast, but it doesnt allow the cast. It is not possible for me to use a shared assembly and put the declaration of IMyInterface in that assembly. So how can I solve this problem? Is not always possible to have the interface in the shared assembly, because one .Net assembly may expose an interface (eg COM IDataObject) to the unmanaged world while another .Net assembly may consume the interface. Though the underlying interface is the same, .Net will not allow the casting. (In the case of IDataObject, this situation will not arise if you use the Microsoft defined type IOleDataObject - however this will not be possible for other interfaces). Thus .Net does not support the basic tenet of COM which says that COM is a "binary standard". It should not matter what the underlying ".Net type" of the interface is as long as the binary contract of the interface is the same. I think this is a serious flaw in COM support of .Net. I would love to hear some more views on this. Regards Bob
    
-   **Anonymous**  
    October 11, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 11, 2006  
    I have worked daily for the last couple of years with COM interop and the underlying framework and toolset. One short thing I WISH were added to the interop (I know you weren't looking to add, but thought I would mention): Disposable RCW's that did a hard-release on the underlying COM object. This would of made our .NET experience much more natural and intuitive and our COM experience predictable (not to mention saving weeks of work). =) Thanks for gathering feedback!
    
-   **Anonymous**  
    October 12, 2006  
    COM interop works great (providing you have adam nathan's book!) The hard one is p/invoke with non-trivial function signatures. Try wrapping (for example) AcceptSecurityContext. The new MDAs help but its still a lot of trial and error. I still end up write clr/c++ code to wrap the win32 calls in many cases. Solutions a) all win32 wrapped b) a wizard in vs c) a file of declarations (VB had this ages ago )
    
-   **Anonymous**  
    October 16, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 17, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 18, 2006  
    Bob:   I understand your scenario as follows. You defined a COM interface in unmanaged code and want to use that in managed code. In that process you redeclared that interface and tried casting the object that you got from unmanaged code to the managed interface that you had declared locally and this failed. Is this correct?   Wouldn't tlbimp solve the probem for you that it will expose the COm interface in the managed code as well?
    
-   **Anonymous**  
    October 18, 2006  
    Jude:    Microsoft Office has interop assemblies that it ships. Microsoft.Office.Interop.Word is available already for you to use. Please let me know if this helps.
    
-   **Anonymous**  
    October 18, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 18, 2006  
    pm:    I agree to your point above. I ran into the same problems when I attempted to solve the CreateProcess in my other blog. I don't think wrapping all the Win32 API is a feasible, scalable and maintainable solution. A wizard will be a good idea which we have in our mind as well. Thanks for your feedback.
    
-   **Anonymous**  
    October 18, 2006  
    Vivek:    Was your problems with RCW around anything in specific, say life time management for example.    Can you provide information on the marshalling problems you faced?    Thanks for your valuable feedback.
    
-   **Anonymous**  
    October 18, 2006  
    Alois:    I have seen similar requests when it comes to arrays and size which are implied in COM and that they would like to have that exposed in the managed signature. Your ask is different but shares the similar concept.    Can you eloborate on your problem with nested interfaces? I am not able to understand it clearly.    Thanks for your feedback
    
-   **Anonymous**  
    October 19, 2006  
    Hi Thottam, I was thinking of way to import COM interfaces in a way where I can decide if I want to use the imported functions with default error handling (Marshaller throws an ComException if it does encounter an unkown HResult) or and here comes the extension an extended interface which does allow me to the the original HRESULT without any exception at all. This can be beneficial if you have to call some CoM functions quite often in a try-manner. The rationale behind my idea is very much the same that did lead to the int.TryParse method. A possible way to achieve this would be to create from the base interface a second interface where every function returns the original HRESULT code and passes the return value as out parameter. namespace ComImportLibrary {    // [Flags(TYPEFLAGS.SpecialNonThrowing]    // instruct the Marshaller to treat type casts to this interface in a special way    public interface ITryBaseCalc    {        HResult TryAdd(int a, int b, out int result);    }    [ComImport, Guid("845FB958-4279-11D2-BF23-00805FBE84A6")]    public interface IBaseCalc : ITryBaseCalc    {        // interface functions        int Add(int a, int b);    }    // [Flags(TYPEFLAGS.SpecialNonThrowing]    public interface ITryExtendedCalc    {        HResult TryDiv(int a, int b, out int result);    }    [ComImport, Guid("845FB958-4279-11D2-BF23-00805FBE84A6")]    public interface IExtendedCalc : IBaseCalc, ITryExtendedCalc    {        int Div(int a, int b);    } } Yours, Alois Kraus
    
-   **Anonymous**  
    October 22, 2006  
    The comment has been removed
    
-   **Anonymous**  
    October 25, 2006  
    Alois Kraus:    Thanks for the suggestion. It sounds like a good idea.
    
-   **Anonymous**  
    October 26, 2006  
    Hi Thottam, what does happen next? Is this a feature that will seriously considered to be implemented in the next version or is it on the long list of minor improvements which will very likely be skipped for the next release? Yours,  Alois Kraus
    
-   **Anonymous**  
    October 27, 2006  
    We are currently planning on the features for the next release. There are a few essential work that we have to do. Apart from that I personally feel that it will be very useful to make the existing experience with this feature better. From the comments here and the thougts that our team has had from working in this area, I will be presenting a plan to our management and take it from there. I am optimistic at this point. I am sorry that I am unable to give details on releases now.
    
-   **Anonymous**  
    June 14, 2009  
    PingBack from [http://cutebirdbaths.info/story.php?id=3542](http://cutebirdbaths.info/story.php?id=3542)
    
-   **Anonymous**  
    June 16, 2009  
    PingBack from [http://topalternativedating.info/story.php?id=12903](http://topalternativedating.info/story.php?id=12903)
