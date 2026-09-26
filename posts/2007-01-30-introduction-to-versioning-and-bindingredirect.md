---
title: "Introduction to Versioning and BindingRedirect"
date: 2007-01-30
slug: introduction-to-versioning-and-bindingredirect
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/introduction-to-versioning-and-bindingredirect
---

# Introduction to Versioning and BindingRedirect

I want to share with you this sample that demonstrates how to version an assembly. An

AssemblyIdentity consists of five parts:

1.  AssemblyName
2.  Public key
3.  Version
4.  Culture
5.  Processor Architecture

How do you specify all of these in an assembly? It is real simple. Let us walk over step by step to understand this with a simple example.

**Let us create an assembly and version it as 1.0.0.0.**

Let us create a directory C:\versionsample\bin\v1\helper.cs as below

<!-- C# -->

    using System;
    using System.Reflection;
    [assembly: System.Reflection.AssemblyVersion("1.0.0.0")]
    public class Foo
    {
        public void Bar()
        {
            Console.WriteLine("Inside HelperMethod: Version = {0}", (Assembly.GetExecutingAssembly()).ToString());
        }
    }

Now let us sign it with a key pair. In order to do that run sn –k sgkey.snk and generate key pair.

You can compile the assembly as below

csc /target:library /out:helper.dll /keyfile:sgkey.snk helper.cs

If you ildasm helper.dll you will see the version number to be 1.0.0.0 and the public key as specified in the snk file.

**Let us create a 2.0.0.0 version of the assembly we has before:**

Let us create a directory C:\versionsample\bin\v2\helper.cs as below

<!-- C# -->

    using System;
    using System.Reflection;
    [assembly: System.Reflection.AssemblyVersion("2.0.0.0")]
    public class Helper
    {
        public void HelperMethod()
        {
            Console.WriteLine("Inside HelperMethod: Version = {0}", (Assembly.GetExecutingAssembly()).ToString());
        }
    }

You can compile this with the same key file to a library

csc /target:library /out:helper.dll /keyfile.sgkey.snk helper.cs

Let us write the application as below and compile it with helper.dll from the v1 directory as below:

<!-- C# -->

    using System;
    public class sample
    {
        static void Main(string[] args)
        {
            Foo f = new Foo();
            f.Bar();
        }
    }

csc /reference:bin\v1\helper.dll Application.cs

Let us create a config file for the application as below:

Application.exe.config:


<!-- XML -->

    <configuration>
       <runtime>
          <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
             <dependentAssembly>
                <assemblyIdentity name="Helper"
                                  publicKeyToken="bcabfaff346163aa"
                             culture="neutral" />
                <codeBase version="1.0.0.0"
                          href="c:\versionsample\bin\v1\helper.dll"/>
                <codeBase version="2.0.0.0"
                          href="c:\versionsample\bin\v2\helper.dll"/>
             </dependentAssembly>
          </assemblyBinding>
       </runtime>
    </configuration>


If you run the application, you will get the following output:

Inside HelperMethod: Version = helper, Version=1.0.0.0, Culture=neutral, PublicK

`eyToken=bcabfaff346163aa`

In order to bind the application to version 2.0.0.0 of the helper assembly, let us add a bindingRedirect which moves version 1.0.0.0 to 2.0.0.0.


<!-- XML -->

    <configuration>
       <runtime>
          <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
             <dependentAssembly>
                <assemblyIdentity name="Helper"
                                  publicKeyToken="bcabfaff346163aa"
                             culture="neutral" />
                <bindingRedirect oldVersion="1.0.0.0" newVersion="2.0.0.0"/>
                <codeBase version="1.0.0.0"
                          href="c:\versionsample\bin\v1\helper.dll"/>
                <codeBase version="2.0.0.0"
                        href="c:\versionsample\bin\v2\helper.dll"/>
             </dependentAssembly>
          </assemblyBinding>
       </runtime>
    </configuration>


Now if you run the application you will see the following output:

Inside HelperMethod: Version = helper, Version=2.0.0.0, Culture=neutral, PublicK

`eyToken=bcabfaff346163aa`

## Comments

-   **Anonymous**  
    February 06, 2007  
    we are successfully created strong name for the dll and we placed inside of GAC. After that we are not able to access the dll in other projects.could u help me.
    
-   **Anonymous**  
    February 07, 2007  
    Can you provide me with some more information? Do you see this issue during development or deployment? What error are you seeing? If you can provide some more details I can try and help.
    
-   **Anonymous**  
    March 19, 2008  
    The comment has been removed
    
-   **Anonymous**  
    July 13, 2008  
    Hi, I am actually trying for assembly redirection for moss workflows.I copied the new dll into the GAC and redirected as suggested in the web.config file but i am not able to debug the new dll so its not redirecting to the new dll. Can you help me in whats that i am doin wrong Thanks in advance
    
-   **Anonymous**  
    July 13, 2008  
    Hi this is how i established the assembly redirection for upgrading moss workflows but i am not able to see that the new dll is being used. <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1"> <dependentAssembly> <assemblyIdentity name="SDS.Workflow.LandAcquisition" publicKeyToken="15aceea04566a2c2" culture="neutral" /> <bindingRedirect oldVersion="1.0.0.1" newVersion="2.0.0.1"/> <codeBase version="1.0.0.1" href="C:SDS.Workflow.LandAcquisition.dll"/> <codeBase version="2.0.0.1" href="D:SDS.Workflow.LandAcquisition.dll"/> </dependentAssembly> </assemblyBinding> Help me as its urgent Thanks in advance
    
-   **Anonymous**  
    September 25, 2008  
    in this example both Dll 's are stored in different location. What if it is stored in GAC
    
-   **Anonymous**  
    January 03, 2011  
    Actually i have an assembly with a strong name, i installed it in the GAC and was uneble to redirect it as it did above. please any idea ?
    
-   **Anonymous**  
    January 24, 2011  
    The comment has been removed
    
-   **Anonymous**  
    September 08, 2011  
    It's not working man give different solution ....
    
-   **Anonymous**  
    October 13, 2015  
    As far as I understand, if DLL is stored in GAC, you just need to provide bindingredirect and not codebase. <bindingRedirect oldVersion="1.0.0.1" newVersion="2.0.0.1"/>
