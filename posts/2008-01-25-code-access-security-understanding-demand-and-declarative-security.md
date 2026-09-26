---
title: "Code Access Security - Understanding Demand and Declarative Security"
date: 2008-01-25
slug: code-access-security-understanding-demand-and-declarative-security
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/code-access-security-understanding-demand-and-declarative-security
---

# Code Access Security - Understanding Demand and Declarative Security

I wanted to talk about Code Access Security this time. Code Access Security is a big in itself. In this post I wanted to talk about Declarative Security. Declarative security uses attributes to place security information on assemblies, classes and methods.

In the sample below I have defined a method “MethodDemandingFileIOPermission” which demands “FileIOPermission” declaratively. In Main I deny FileIOPermission to begin with and try calling this method. The call to this method should fail as we have denied this permission. The first output from the code proves this.

Deny FileIOPermission

Unable to access MethodDemandingFileIOPermission after Deny

Now, I call into another method which asserts FileIOPermission and calls the same method. This time call should succeed as a Demand stops walking the stack wen it encounters an Assert. So, in this case the Deny from Main has no effect on the call and it succeeds. The output is shown as below

Assert FileIOPermission from another method

Calling method MethodDemandingFileIOPermission succeeded

Next, we revert the assert in the method where we asserted to make sure that the call indeed succeeded because of the assert. We make the same call again and as expected it fails this time. The output from the code is shown below.

RevertAssert in AssertAndCall and call again

Unable to access MethodDemandingFileIOPermission Assert reverted in AssertAndCall

We Assert one more time and make the call to ensure the call succeeds this time. The output is as below.

Assert FileIOPermission one more time from another method

Calling method MethodDemandingFileIOPermission succeeded

When the call returns to Main the Assert is reverted. The Deny takes effect and so a subsequent call to the method from Main fails. The output from the code shows the following result.

Assert permission reverted when returned to Main()

Unable to access MethodDemandingFileIOPermission Assert reverted on return

We now Assert declaratively and make the call and the call succeeds this time also. On return the Assert is reverted and the call fails as expected.

Assert FileIOPermission Declaratively

Calling method MethodDemandingFileIOPermission succeeded

Assert permission reverted when returned to Main()

Unable to access MethodDemandingFileIOPermission Assert reverted on return

We now revert the Deny from Main and make the call to make sure that it was the Deny that was failing the call. As expected the call succeeds as soon as the Deny is reverted.

RevertDeny from Main()

Calling method MethodDemandingFileIOPermission succeeded

```csharp
using System;
using System.Reflection;
using System.Security.Permissions;
using System.Security;
using System.IO;
class Sample
{
    public static void Main()
    {
        FileIOPermission fileIOPerm = new FileIOPermission(PermissionState.Unrestricted);
        fileIOPerm.Deny();

        Console.WriteLine("Deny FileIOPermission");
        try
        {
            Sample.MethodDemandingFileIOPermission();
        }
        catch(Exception e)
        {
            Console.WriteLine(" Unable to access MethodDemandingFileIOPermission after Deny");
        }
        Sample.AssertAndCall(fileIOPerm);
        Console.WriteLine("Assert permission reverted when returned to Main()");
        try
        {
            Sample.MethodDemandingFileIOPermission();
        }
        catch (Exception e)
        {
            Console.WriteLine(" Unable to access MethodDemandingFileIOPermission Assert reverted on return");
        }
        CodeAccessPermission.RevertDeny();
        Console.WriteLine("RevertDeny from Main()");
        try
        {
            Sample.MethodDemandingFileIOPermission();
        }
        catch (Exception e)
        {
            Console.WriteLine(" Unable to access MethodDemandingFileIOPermission Assert reverted on return");
        }
        Sample.AssertDeclarativelyAndCall();
    }
    static public void AssertAndCall(FileIOPermission fileIOPerm)
    {
        Console.WriteLine("Assert FileIOPermission from another method");
        fileIOPerm.Assert();
        Sample.MethodDemandingFileIOPermission();
        CodeAccessPermission.RevertAssert();
        Console.WriteLine("RevertAssert in AssertAndCall and call again");

        try
        {
            Sample.MethodDemandingFileIOPermission();
        }
        catch (Exception e)
        {
            Console.WriteLine(" Unable to access MethodDemandingFileIOPermission Assert reverted in AssertAndCall");
        }
        Console.WriteLine("Assert FileIOPermission one more time from another method");
        fileIOPerm.Assert();
        Sample.MethodDemandingFileIOPermission();
    }

    [FileIOPermissionAttribute(SecurityAction.Assert, Unrestricted = true)]
    static public void AssertDeclarativelyAndCall()
    {
        Console.WriteLine("Assert FileIOPermission Declaratively");
        Sample.MethodDemandingFileIOPermission();
    }
    [FileIOPermissionAttribute(SecurityAction.Demand, Unrestricted = true)]
    static public void MethodDemandingFileIOPermission()
    {
        Console.WriteLine(" Calling method MethodDemandingFileIOPermission succeeded");
    }
}
```

## Comments

-   **Anonymous**  
    January 24, 2008  
    PingBack from [http://msdnrss.thecoderblogs.com/2008/01/25/code-access-security-understanding-demand-and-declarative-security/](http://msdnrss.thecoderblogs.com/2008/01/25/code-access-security-understanding-demand-and-declarative-security/)
