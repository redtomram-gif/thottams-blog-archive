---
title: "Code Access Security, LinkDemand and Effective Sandbox"
date: 2008-02-12
slug: code-access-security-linkdemand-and-effective-sandbox
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/code-access-security-linkdemand-and-effective-sandbox
---

# Code Access Security, LinkDemand and Effective Sandbox

I wrote about Deny and Assert in my previous blog post. In my repeated attempts to get the previous sample working for LinkDemand, I discovered that Deny and PermitOnly are not effective against LinkDemand. The code worked all the time and I was wondering why.

After some good research and lots of learning (from [Shawn](../shawnfa/the-simple-sandboxing-api)) I learnt that Deny and PermitOnly CANNOT create an effective sandbox environment. Shawm Farkas’s article on this is a must read. It clearly states how you can circumvent deny’s with asserts and so on.

Now with Deny and PermitOnly being a not so good mechanism for providing a secure sand box environment, we are on to finding the good sandbox environment. In my pursuit towards that I figured the following out.

An effective sandbox comes from grants associated with an AppDomain and the assemblies within the domain. The big issue with PermitOnly and Deny is that they don’t modify either grant set.

The sample below demonstrates how a LinkDemand works. It also demonstrates why a Demand does not work for the same case. Let us now look into the grant sets that are used to compare to either succeed or fail the call.

I have three assemblies A, B and C where A calls B which in turns calls C. C has two scenarios, one it has a LinkDemand for a permission and second it has a Demand for the permission. A, B and C instances are created in their own AppDomain and passed as MarshallByRef to C’s AppDomain. Shawn explains in this [article](../shawnfa/the-simple-sandboxing-api) very cleary the two grant sets that an assembly in an AppDomain gets, a full trust or a grant set depending on if it is in the GAC or one of the strong name assemblies passed to the CreateDomain call.

In the example below I create an instance of A, B and C in their own AppDomain and pass them to B and C which end up calling methods on them. Domain A does not grant EnvironmentPermission, Domain B grants EnvironmentPermission and C either Demands EnvironmentPermission or LinkDemands EnvironmentPermission.

Why does the LinkDemand work?

LinkDemands do not walk the stack and are evaluated at JIT time. You only JIT once, and hence grant sets are assigned when assemblies are loaded into an AppDomain and are not changed after that. In the sample below the linkdemand permission in C’s Method is compared with the grant set of the C’s AppDomain Technically, it’s comparing the LinkDemand on C’s Method with the grant set of C’s calling assembly in C’s AppDomain. For instance, if C’s caller were on the FullTrust list or were from the GAC, the LinkDemand would succeed even though C’s AppDomain has a partial trust grant set. Thus this succeeds.

Why does the Demand Fail?

The demand fails because the demand results in a stack walk. It walks the stack and compared the demanded permission set with the permission set of the callers in the call stack. Since A does not have this permission set in A’s AppDomain the demand fails. It is important to notice that it does have the permission set in C’s Domain for instance which is irrelevant in this case.

LinkDemand is primarily used by framework code to gain elevation to perform its tasks. In general it should not be required in common scenarios.

I want to thanks Shawn Farkas for helping me with this blog post. He has tons of valuable information on CAS in this blog which are excellent reads – [Shawn’s blog](../shawnfa/checking-for-a-valid-strong-name-signature)

**Main.cs:**

```
using System;
using System.Security;
using System.Security.Permissions;
using System.Reflection;
using System.Runtime.Remoting;

class Sample
{
    public static void Main()
    {
        AppDomainSetup ads = new AppDomainSetup();
        ads.ApplicationBase = System.Environment.CurrentDirectory;

        PermissionSet psMain = new PermissionSet(PermissionState.None);
        SecurityPermission sec = new SecurityPermission(PermissionState.Unrestricted);
        psMain.AddPermission(sec);
        AppDomain md = AppDomain.CreateDomain("A", null, ads, psMain, null);
        ObjectHandle obj = md.CreateInstance(@"A", "A");
        A a = (A)obj.Unwrap();

        PermissionSet psFirst = new PermissionSet(PermissionState.None);
        EnvironmentPermission env1 = new EnvironmentPermission(PermissionState.Unrestricted);
        psFirst.AddPermission(sec);
        psFirst.AddPermission(env1);
        AppDomain fd = AppDomain.CreateDomain("B", null, ads, psFirst, null);
        ObjectHandle obj1 = fd.CreateInstance(@"B", "B");
        B b = (B)obj1.Unwrap();

        PermissionSet psSecond = new PermissionSet(PermissionState.None);
        psSecond.AddPermission(sec);
        AppDomain sd = AppDomain.CreateDomain("C", null, ads, psSecond, null);
        ObjectHandle obj2 = sd.CreateInstance(@"C", "C");
        C c = (C)obj2.Unwrap();
        a.MethodMain(b, c);
    }
}
```

**A.cs:**

```
using System;
using System.Security;
using System.Security.Permissions;
using System.Reflection;
using System.Runtime.Remoting;

public class A : MarshalByRefObject
{
    public void Method(B b, C c)
    {
        Console.WriteLine("Inside A::Method");
        b.Method(c);
    }
}
```

**B.cs:**

```
using System;
using System.Security;
using System.Reflection;

public class B : MarshalByRefObject
{
    public void Method(C c)
    {
        Console.WriteLine("Inside B::Method");
        c.Method();
    }
}
```

**C.cs:**

```
using System;
using System.Security;
using System.Security.Permissions;

[SerializableAttribute()]
public class C
{
    [EnvironmentPermissionAttribute(SecurityAction.LinkDemand, Unrestricted = true)]
    public void Method()
    {
        Console.WriteLine("Inside C::Method");
    }
}
```

## Comments

-   **Anonymous**  
    February 12, 2008  
    PingBack from [http://msdnrss.thecoderblogs.com/2008/02/12/code-access-security-linkdemand-and-effective-sandbox/](http://msdnrss.thecoderblogs.com/2008/02/12/code-access-security-linkdemand-and-effective-sandbox/)
