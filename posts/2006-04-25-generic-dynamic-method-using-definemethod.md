---
title: "Generic dynamic method using DefineMethod"
date: 2006-04-25
slug: generic-dynamic-method-using-definemethod
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/generic-dynamic-method-using-definemethod
---

# Generic dynamic method using DefineMethod

This example illustrates how you can define a generic dynamic method and call it. In the sample below we define a method void HelloWorld<T>(T val) in type Hello and call it.

The body of the HelloWorld Method accesses the parameter passed to the method and prints it by calling WriteLine. I have created a module so that you can see the IL that is generated from this code.

<!-- C# -->

    using System;
    using System.Collections.Generic;
    using System.Text;
    using System.Reflection.Emit;
    using System.Reflection;
    public interface IHelloWorld {
        void HelloWorld<T>(T val);
    }
    public class HelloClass {
        static void Main(string[] args) {
            AppDomain cd = System.Threading.Thread.GetDomain();
            AssemblyName an = new AssemblyName();
            an.Name = "HelloClass";
            AssemblyBuilder ab = cd.DefineDynamicAssembly(an, AssemblyBuilderAccess.RunAndSave);
            ModuleBuilder mb = ab.DefineDynamicModule("HelloModule", "HelloModule.dll", true);
            TypeBuilder tb = mb.DefineType("Hello", TypeAttributes.Class | TypeAttributes.Public);
            TypeBuilder ntb = tb.DefineNestedType("NestedHello",TypeAttributes.NestedPublic, typeof(HelloClass), new Type[] {typeof(IHelloWorld)});
            ntb.AddInterfaceImplementation(typeof(IHelloWorld));
            MethodBuilder meth = ntb.DefineMethod("HelloWorld", MethodAttributes.Public | MethodAttributes.Virtual, typeof(void), Type.EmptyTypes);
            string[] typeParamNames = {"T"};
            GenericTypeParameterBuilder[] typeParameters = meth.DefineGenericParameters(typeParamNames);
            Type[] parms = { typeParameters[0] };
            meth.SetParameters(parms);
            ILGenerator il = meth.GetILGenerator();
            MethodInfo infoMethod = typeof(Console).GetMethod("WriteLine", new Type[] { typeof(string) });
            if (infoMethod != null)
            {
                il.Emit(OpCodes.Ldarg_1);
                il.Emit(OpCodes.Box, typeParameters[0]);
                il.Emit(OpCodes.Call, infoMethod);
            }
            il.Emit(OpCodes.Ret);
            MethodInfo myHelloMethodInfo = typeof(IHelloWorld).GetMethod("HelloWorld");
            ntb.DefineMethodOverride(meth, myHelloMethodInfo);
            Type myType = tb.CreateType();
            Type myNestedClassType = ntb.CreateType();
            IHelloWorld ihw = (IHelloWorld)Activator.CreateInstance(myNestedClassType);
            ihw.HelloWorld("Calling generic dynamic method");
            ab.Save("Hello.dll");
            return;
        }
    }

## Comments
