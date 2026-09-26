---
title: "Creating nested types dynamically using the TypeResolve event"
date: 2006-04-28
slug: creating-nested-types-dynamically-using-the-typeresolve-event
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/creating-nested-types-dynamically-using-the-typeresolve-event
---

# Creating nested types dynamically using the TypeResolve event

Creating a Type dynamically using the TypeResolve event. I came across this issue recently when I was working on something and thought that I will share this with everyone.

The TypeResolve event is generated when the run time is unable to determine the assembly that can create the requested Type. In the example below we have nested types, where the creation of one triggers the creation of the nested type.

If you step through the code below you will note that the creation of Enclosing triggers the creation of Nested1. Creation of Nested1 triggers the creation of Nested2. Creation of Nested2 goes through fine as it has all the types required by it. Now strangely, creation of Nested1 triggers creation of Enclosing. I was not able to figure out why this was happening. The speculation from the initial thought were that it was because of some visibility checks from Nested1.

Strangely, the types get created but an exception is thrown in the first call to CreateType. I caught the exception and ignored it and looked into the assembly that is generated and the type and the constructor was indeed created. This is not the best work around, but still gets you over this hurdle if you get into it.

```csharp
using System;
using System.Reflection;
using System.Reflection.Emit;
/*
```

* class Enclosing

```csharp
* {
    * public void Enclosing()
    * {
        * Console.WriteLine("Hello");
        * }
```

* class Nested1

```csharp
* {
    * int a;
    * int b;
    * Nested2 c;
    * }
```

* class Nested2

```csharp
* {
    * int a;
    * int b;
    * }
* Nested1 a;
* Nested2 b;
* }
```

*/

```csharp
public interface IHelloWorld
{
    void HelloWorld();
}
class Class1
{
    static TypeBuilder enc, nested1, nested2;
    static AssemblyBuilder ab;
    static ModuleBuilder mb;
    static void Main(string[] args)
    {
        AppDomain.CurrentDomain.TypeResolve += new ResolveEventHandler(CurrentDomain_TypeResolve);
        AssemblyName name = new AssemblyName();
        name.Name = "test";
        ab = AppDomain.CurrentDomain.DefineDynamicAssembly(name, AssemblyBuilderAccess.RunAndSave);
        mb = ab.DefineDynamicModule("test", "test.dll");
        enc = mb.DefineType("Enclosing", TypeAttributes.Public | TypeAttributes.Class, null, new Type[] { typeof(IHelloWorld) });
        nested1 = enc.DefineNestedType("Nested1", TypeAttributes.Sealed | TypeAttributes.NestedPublic, typeof(System.ValueType));
        nested2 = enc.DefineNestedType("Nested2", TypeAttributes.Sealed | TypeAttributes.NestedPublic, typeof(System.ValueType));
        nested1.DefineField("a", typeof(int), FieldAttributes.Public);
        nested1.DefineField("b", typeof(int), FieldAttributes.Public);
        nested1.DefineField("c", nested2, FieldAttributes.Public);
        nested2.DefineField("a", typeof(int), FieldAttributes.Public);
        nested2.DefineField("b", typeof(int), FieldAttributes.Public);
        enc.DefineField("a", nested1, FieldAttributes.Public);
        enc.DefineField("b", nested2, FieldAttributes.Public);
        ConstructorBuilder myConstructorBuilder = enc.DefineConstructor(MethodAttributes.Public, CallingConventions.Standard, Type.EmptyTypes);
        ILGenerator myConstructorIL = myConstructorBuilder.GetILGenerator();
        myConstructorIL.Emit(OpCodes.Ldstr, "Enc Constructor Called");
        MethodInfo infoMethod = typeof(Console).GetMethod("WriteLine", new Type[] { typeof(string) });
        myConstructorIL.Emit(OpCodes.Call, infoMethod);
        myConstructorIL.Emit(OpCodes.Ret);
        MethodBuilder meth = enc.DefineMethod("HelloWorld", MethodAttributes.Public | MethodAttributes.Virtual, typeof(void), Type.EmptyTypes);
        ILGenerator il = meth.GetILGenerator();
        il.Emit(OpCodes.Ldstr, "Enc::HelloWorld() method called!");
        MethodInfo writeMethod = typeof(Console).GetMethod("WriteLine", new Type[] { typeof(string) });
        il.Emit(OpCodes.Call, writeMethod);
        il.Emit(OpCodes.Ret);
        Type t =null;
        try
        {
            t = enc.CreateType();
        }
        catch (Exception e) { Console.WriteLine(e.ToString()); }
        Console.WriteLine("Creating Type: {0}", t.ToString());
        IHelloWorld ins = (IHelloWorld)Activator.CreateInstance(t);
        ins.HelloWorld();
    }
    private static Assembly CurrentDomain_TypeResolve(object sender, ResolveEventArgs args)
    {
        Type newtype = null;
        if (args.Name == "Nested2")
        {
            newtype = nested2.CreateType();
            return mb.Assembly;
        }
        if (args.Name == "Nested1")
        {
            try
            {
                newtype = nested1.CreateType();
            }
            catch (Exception e) { }
            return mb.Assembly;
        }
        if (args.Name == "Enclosing")
        {
            try
            {
                newtype = enc.CreateType();
            }
            catch (Exception e) { }
            return mb.Assembly;
        }
        return Assembly.GetExecutingAssembly();
    }
}
```
