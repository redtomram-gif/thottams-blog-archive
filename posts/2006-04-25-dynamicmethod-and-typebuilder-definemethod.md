---
title: "DynamicMethod and TypeBuilder.DefineMethod"
date: 2006-04-25
slug: dynamicmethod-and-typebuilder-definemethod
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/dynamicmethod-and-typebuilder-definemethod
---

# DynamicMethod and TypeBuilder.DefineMethod

There are two ways you can create a method dynamically.

1.  DynamicMethod class
2.  TypeBuilder.DefineMethod

You don’t need a type or assembly to generate a method using DynamicMethod. A method generated using DynamicMethod can be assocaited with any type of module. When defining methods using DynamicMethods, the method and parameters need not be named as the metadata is not saved.

The sample below shows how dynamic methods can be created using DynamicMethods. The example also demonstrated how you can invoke a generic method from within the DynamicMethod.

<!-- C# -->

    using System;
    using System.Reflection;
    using System.Reflection.Emit;
    public class D {
        static public void Method<T>(T val) {
            Console.WriteLine("In method ..." + val);
        }
    }
    class Demo {
        static void Main() {
            Type[] t = { typeof(string) };
            DynamicMethod dm = new DynamicMethod("DynamicMethod", typeof(void), t, typeof(object), false);
            DynamicILInfo il = dm.GetDynamicILInfo();
            SignatureHelper sigHelper = SignatureHelper.GetLocalVarSigHelper();
            il.SetLocalSignature(sigHelper.GetSignature());
            byte[] code = { 0x00, 0x72, 0x01, 0x00, 0x00, 0x70, 0x28, 0x04, 0x00, 0x00, 0x0a, 0x00, 0x2a };
            int token0 = il.GetTokenFor("{I am the parameter for Generic Method<T>(T val)}");
            int token1 = il.GetTokenFor(typeof(D).GetMethod("Method").MakeGenericMethod(typeof(String)).MethodHandle);
            PutInteger4(token0, 0x0002, code);
            PutInteger4(token1, 0x0007, code);
            il.SetCode(code, 8);
            Object[] param = { "Calling DynamicMethod ..." };
            dm.Invoke(null, param);
        }
        static void PutInteger4(int value, int startPos, byte[] array) {
            array[startPos++] = (byte)value;
            array[startPos++] = (byte)(value >> 8);
            array[startPos++] = (byte)(value >> 16);
            array[startPos++] = (byte)(value >> 24);
        }
    }

The second method of creating a method dynamically is by using TypeBuilder.DefineMethod. In this the method is associated with a type. The sample below demonstrates how to create a method using the define method and call it.

<!-- C# -->

    using System;
    using System.Collections.Generic;
    using System.Text;
    using System.Reflection.Emit;
    using System.Reflection;
    public interface IHelloWorld {
        void HelloWorld();
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
            ILGenerator il = meth.GetILGenerator();
            il.Emit(OpCodes.Ldstr, "Hello World!");
            MethodInfo infoMethod = typeof(Console).GetMethod("WriteLine", new Type[] {typeof(string)});
            il.Emit(OpCodes.Call, infoMethod);
            il.Emit(OpCodes.Ret);
            MethodInfo myHelloMethodInfo = typeof(IHelloWorld).GetMethod("HelloWorld");
            ntb.DefineMethodOverride(meth, myHelloMethodInfo);
            Type myType = tb.CreateType();
            Type myNestedClassType = ntb.CreateType();
            IHelloWorld ihw = (IHelloWorld)Activator.CreateInstance(myNestedClassType);
            ihw.HelloWorld();
            ab.Save("Hello.dll");
            return;
        }
    }
