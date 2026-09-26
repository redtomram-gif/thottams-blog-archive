---
title: "Using CodeDOM to generate CSharp (C#) and VB code"
date: 2006-08-15
slug: using-codedom-to-generate-csharp-c-and-vb-code
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/using-codedom-to-generate-csharp-c-and-vb-code
---

# Using CodeDOM to generate CSharp (C#) and VB code

I spent sometime to understand CodeDOM and in the process wrote a sample which will help me understand this technology better. I wanted to share the sample I wrote with everyone so that it can be helpful for people getting introduced to this powerful technology.

A good starting point for CodeDOM will be to start from [here](https://msdn.microsoft.com/library/default.asp?url=/library/en-us/cpgenref/html/cpconcodedomquickreference.asp) (Quick reference on CodeDOM). It has helpful documentation depending on what you want to get accomplished.

If you limit your syntax to the LCD of all the languages then this powerful technology can provide you with language agnostic way of generating the code tree which can later be used to generate code for the language of choice.

I have tried to capture the basics of this with a sample below. The sample demonstrates two ways of getting to code. First where you define the code as a string and compile it using a known provider. The scenarios for this are not very common, but yet it is possible.

The second is the most common scenario. You generate the code using the CodeCompileUnit and use the language provider of choice to generate the code and compile it to get the executable. This is often used in IDE which provide you with startup projects.

```
using System;
using System.CodeDom;
using System.CodeDom.Compiler;
using System.Reflection;
using Microsoft.CSharp;
using Microsoft.VisualBasic;
using System.IO;
public class CodeDomSample
{
    CodeCompileUnit GenerateCSharpCode()
    {
        CodeCompileUnit compileUnit = new CodeCompileUnit();

        // Create a NameSpace - "namespace CodeDomSampleNS"
        //
        CodeNamespace codedomsamplenamespace = new CodeNamespace("CodeDomSampleNS");
        // Create using statement - "using System;"
        //
        CodeNamespaceImport firstimport = new CodeNamespaceImport("System");
        // Add the using statement to the namespace -
        // namespace CodeDomSampleNS {
        // using System;
        //
        codedomsamplenamespace.Imports.Add(firstimport);
        // Create a type inside the namespace - public class CodeDomSample
        //
      CodeTypeDeclaration newType = new CodeTypeDeclaration("CodeDomSample");
        newType.Attributes = MemberAttributes.Public;
        // Create a Main method which will be entry point for the class
        // public static void Main
        //
        CodeEntryPointMethod mainmethod = new CodeEntryPointMethod();
        // Add an expression inside Main -
        // Console.WriteLine("Inside Main ...");
        CodeMethodInvokeExpression mainexp1 = new CodeMethodInvokeExpression(
            new CodeTypeReferenceExpression("System.Console"),
            "WriteLine", new CodePrimitiveExpression("Inside Main ..."));
        mainmethod.Statements.Add(mainexp1);
        // Add another expression inside Main
        // CodeDomSample cs = new CodeDomSample()
        //
        CodeStatement cs = new CodeVariableDeclarationStatement(typeof(CodeDomSample), "cs", new CodeObjectCreateExpression(new CodeTypeReference(typeof(CodeDomSample))));
        mainmethod.Statements.Add(cs);

        // At the end of the CodeStatements we should have constructed the following
        // public static void Main() {
        // Console.WriteLine("Inside Main ...");
        // CodeDomSample cs = new CodeDomSample();
        // }
        // Create a constructor for the CodeDomSample class
        // public CodeDomSample() { }
        //
        CodeConstructor constructor = new CodeConstructor();
        constructor.Attributes = MemberAttributes.Public;
        // Add an expression to the constructor
        // public CodeDomSample() { Comsole.WriteLine("Inside CodeDomSample Constructor ...");
        //
        CodeMethodInvokeExpression constructorexp = new CodeMethodInvokeExpression(new CodeTypeReferenceExpression("System.Console"), "WriteLine", new CodePrimitiveExpression("Inside CodeDomSample Constructor ..."));
        constructor.Statements.Add(constructorexp);
        // Add constructor and mainmethod to type
        //
        newType.Members.Add(constructor);
        newType.Members.Add(mainmethod);
        // Add the type to the namespace
        //
        codedomsamplenamespace.Types.Add(newType);
        // Add the NameSpace to the CodeCompileUnit
        //
        compileUnit.Namespaces.Add(codedomsamplenamespace);
        // Return the CompileUnit
        //
        return compileUnit;
    }
    // Generate code for a particular provider and compile it
    //
    void GenerateCode(CodeCompileUnit ccu, String codeprovider)
    {
        CompilerParameters cp = new CompilerParameters();
        String sourceFile;
        CompilerResults cr;
        switch (codeprovider)
        {
            case "CSHARP":
                // Generate Code from Compile Unit using CSharp code provider
                //
                CSharpCodeProvider csharpcodeprovider = new CSharpCodeProvider();
                if (csharpcodeprovider.FileExtension[0] == '.')
                {
                    sourceFile = "CSharpSample" + csharpcodeprovider.FileExtension;
                }
                else
                {
                    sourceFile = "CSharpSample." + csharpcodeprovider.FileExtension;
                }
                IndentedTextWriter tw1 = new IndentedTextWriter(new StreamWriter(sourceFile, false), " ");
                csharpcodeprovider.GenerateCodeFromCompileUnit(ccu, tw1, new CodeGeneratorOptions());
                tw1.Close();
                cp.GenerateExecutable = true;
                cp.OutputAssembly = "CSharpSample.exe";
                cp.GenerateInMemory = false;
                cr = csharpcodeprovider.CompileAssemblyFromDom(cp, ccu);
                break;
            case "VBASIC":
                // Generate Code from Compile Unit using VB code provider
                //
                VBCodeProvider vbcodeprovider = new VBCodeProvider();
                if (vbcodeprovider.FileExtension[0] == '.')
                {
                    sourceFile = "VBSample" + vbcodeprovider.FileExtension;
             }
                else
                {
                    sourceFile = "VBSample." + vbcodeprovider.FileExtension;
                }
                IndentedTextWriter tw2 = new IndentedTextWriter(new StreamWriter(sourceFile, false), " ");
       vbcodeprovider.GenerateCodeFromCompileUnit(ccu, tw2, new CodeGeneratorOptions());
                tw2.Close();
                cp.GenerateExecutable = true;
                cp.OutputAssembly = "VBSample.exe";
                cp.GenerateInMemory = false;
                cr = vbcodeprovider.CompileAssemblyFromDom(cp, ccu);
                break;
        }
        return;
    }
    // Another sample of hardcoding the code in a string and compiling it using the CSharpCodeProvider
    //
    public void CSharpCodeExample()
    {
        String sourcecode = "\nusing System;\npublic class Sample \n{\n static void Main()\n {\n Console.WriteLine(\"This is a test\");\n }\n}";
        CSharpCodeProvider provider = new CSharpCodeProvider();
   CompilerParameters cp = new CompilerParameters();
        cp.GenerateExecutable = true;
        cp.OutputAssembly = "Result.exe";
        cp.GenerateInMemory = false;
        CompilerResults cr = provider.CompileAssemblyFromSource(cp, sourcecode);
        if (cr.Errors.Count > 0)
        {
            Console.WriteLine("Errors building {0} into {1}", sourcecode, cr.PathToAssembly);
            foreach (CompilerError ce in cr.Errors)
            {
                Console.WriteLine(" {0}", ce.ToString());
                Console.WriteLine();
            }
        }
        else
        {
            Console.WriteLine("Source \n \n {0} \n \n \n built into {1} successfully.", sourcecode, cr.PathToAssembly);
        }
        return;
    }
    static public void Main()
    {
        CodeDomSample cds = new CodeDomSample();
        cds.CSharpCodeExample();
        CodeCompileUnit ccu = cds.GenerateCSharpCode();
        cds.GenerateCode(ccu, "CSHARP");
        cds.GenerateCode(ccu, "VBASIC");
    }
}
```

## Comments

-   **Anonymous**  
    August 15, 2006  
    I've been a fan of CodeDom for quite a while and have a set of tutorials on my weblog.  
    When Peter was in charge of it, I sent him a few suggestions, which were probably too late, since he said that the code base was closed until vnext :/  
    I'd love to help improve this part of .net, since I think it's a pretty important one.  
      
    Omer
    
-   **Anonymous**  
    August 15, 2006  
    I would love to hear your feedback on CodeDOM and your scenarios that could help us make this better.
    
-   **Anonymous**  
    January 16, 2009  
    Hi Thottam, Great article! Extremely helpful! Thanks a lot!
    
-   **Anonymous**  
    February 16, 2010  
    is possible to create tree structure of source code using CodeDOM? if yes, can you please give an example?
    
-   **Anonymous**  
    August 18, 2010  
    Nice article, Is there a way to generate CodeDOM from source code (cs file). I am designing an custom forms designer and need to be able to load a cs file and create the form.
