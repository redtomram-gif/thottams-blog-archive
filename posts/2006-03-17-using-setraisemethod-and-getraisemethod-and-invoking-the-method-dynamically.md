---
title: "Using SetRaiseMethod and GetRaiseMethod and invoking the method dynamically"
date: 2006-03-17
slug: using-setraisemethod-and-getraisemethod-and-invoking-the-method-dynamically
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/using-setraisemethod-and-getraisemethod-and-invoking-the-method-dynamically
---

# Using SetRaiseMethod and GetRaiseMethod and invoking the method dynamically

The GetRaiseMethod returns null for all events declared with the event keyword as explained in the MSDN help. The documentation apparently is not as verbose as it should have been and we will work on that. The documentation around this is extremely confusing and I will personally take it as a work item to make it better. If you have any feedback on this please let me know and I will be more than glad to help you with this.

The GetRaiseMethod will return the method that was set through SetRaiseMethod if you generate a method dynamically. I have a sample below that actually demonstrates this. I have invoked the method just to demonstrate that the MethodInfo that we got was indeed right.

MSDN remarks for GetRaiseMethod:  
This method returns a null reference (Nothing in Visual Basic) for events declared with the C# event keyword or the Visual Basic Event keyword. This is because the C# and Visual Basic compilers do not generate such a method.

```csharp
using System;
using System.Threading;
using System.Reflection;
using System.Reflection.Emit;
public class MyApplication
{
    public delegate void MyEvent(Object temp);
    public static void Main()
    {
        // create the type
        //
        Type helloWorldClassType = CreateCallee();
        // GetRaiseMethod
        //
        EventInfo ei = helloWorldClassType.GetEvent("Click", BindingFlags.Public | BindingFlags.Instance);
        MethodInfo rm = ei.GetRaiseMethod();
        Console.WriteLine("Raise Method for event {0} is {1}", ei.Name, rm.Name);
        Console.WriteLine(helloWorldClassType.GetType().ToString());
        // Invoke the dynamic method
        //
        MethodInfo dm = helloWorldClassType.GetMethod("OnClick");
        Object o = Activator.CreateInstance(helloWorldClassType);
        // Both of them call the emitted method
        //
        dm.Invoke(o, new Object[1]);
        rm.Invoke(o, new Object[1]);
    }
    private static Type CreateCallee()
    {
        AppDomain myDomain = Thread.GetDomain();
        AssemblyName assemblyName = new AssemblyName();
        assemblyName.Name = "EmittedAssembly";
        AssemblyBuilder myAssembly = myDomain.DefineDynamicAssembly(assemblyName, AssemblyBuilderAccess.Run);
        ModuleBuilder myModule = myAssembly.DefineDynamicModule("EmittedModule");
        TypeBuilder helloWorldClass = myModule.DefineType("HelloWorld", TypeAttributes.Public);
        MethodBuilder myMethod1 = helloWorldClass.DefineMethod("OnClick", MethodAttributes.Public, typeof(void), new Type[1] { typeof(Object) });
        ILGenerator methodIL1 = myMethod1.GetILGenerator();
        methodIL1.EmitWriteLine("This is the emitted method");
        methodIL1.Emit(OpCodes.Ret);
        EventBuilder myEvent1 = helloWorldClass.DefineEvent("Click", EventAttributes.None, typeof(MyEvent));
        myEvent1.SetRaiseMethod(myMethod1);
        Type helloWorldClassType = helloWorldClass.CreateType();
        return helloWorldClassType;
    }
}
```

## Comments

-   **Anonymous**  
    March 24, 2006  
      
    What are the possible work-arounds for this?  I am trying to build a generic EventQueue class and don't want to create specialized versions that handle different delegate signatures.
-   **Anonymous**  
    March 31, 2006  
    Unfortunately there is no good work around for this. The only other way is to create delegates and invoke them which I agree is costly and painful.
-   **Anonymous**  
    May 22, 2006  
    I've searched the internet for this problem, but this blog seemed like the best result. That's why I'm asking this question here.  
    I'm trying to make an event (using DefineEvent) similar to the events C# generates.  
    It seems like DefineEvent creates the add_EventName and remove_EventName methods automatically, but when I create the type (TypeBuilder.CreateType()) there is an exception thrown saying:  
    Method 'add_EventName' ... does not have an implementation.  
    There are no methods like GetAddMethod in the EventBuilder class. Is there any way to implement these methods?
-   **Anonymous**  
    June 07, 2006  
    Can you give the code sample of what you are doing so that I can take a look? I tried in my sample above by just defining the event without declaring a body for it and did not get any compilation errors.
-   **Anonymous**  
    June 08, 2006  
    Sorry I have bothered you with my question. I used an interface to call my dynamically created event, so I didn't have to use reflection. I didn't realize I also needed to use DefineMethodOverride to hook the add_EventName to the add_EventName of the interface.  
    Again, sorry to bother you.
-   **Anonymous**  
    January 17, 2008  
    The comment has been removed
