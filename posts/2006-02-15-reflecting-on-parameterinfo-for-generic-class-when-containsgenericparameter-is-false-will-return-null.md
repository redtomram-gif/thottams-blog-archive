---
title: "Reflecting on ParameterInfo for Generic class when ContainsGenericParameter is FALSE will return null"
date: 2006-02-15
slug: reflecting-on-parameterinfo-for-generic-class-when-containsgenericparameter-is-false-will-return-null
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/reflecting-on-parameterinfo-for-generic-class-when-containsgenericparameter-is-false-will-return-null
---

# Reflecting on ParameterInfo for Generic class when ContainsGenericParameter is FALSE will return null

<!-- C# -->

    using System;
    using System.Reflection;
    public class MyClass<T>
    {
        public delegate T MyDelegate();
        public MyClass(MyDelegate del){}
    }
    public class Program
    {
        public static void Main()
        {
            Type type = typeof(MyClass<>);
            if(type == null)
            {
                Console.WriteLine("Type is: NULL");
            }
            else
            {
                Console.WriteLine("Type is: " + type.ToString());
            }
            ConstructorInfo constructor = type.GetConstructors()[0];
            if(constructor == null)
            {
                Console.WriteLine("ConstructorInfo is: Null");
            }
            else
            {
                Console.WriteLine("ConstrutorInfo is: "+constructor.ToString());
            }
            ParameterInfo parameter = constructor.GetParameters()[0];
            if(parameter == null)
            Console.WriteLine("ParameterInfo is: Null");
            else
            Console.WriteLine("Parameterinfo is: "+parameter.ToString());
            if(parameter.ParameterType.ContainsGenericParameters == false)
            {
                Console.WriteLine("FullName of Parameter type will be null:"+parameter.ParameterType.FullName);
            }
            else
            {
                Console.WriteLine("FullName of Parameter type:"+parameter.ParameterType.FullName);
            }
        }
    }

The above code demonstrates that Reflecting on a Generic parameter when its ContainsGenericParameter value is false will return a null value in FullName. When the type of the paramter is well defined the FullName will return the appropriate value.

## Comments

-   **Anonymous**  
    February 19, 2006  
    Welcome to the world of blogging :-)  
      
    
-   **Anonymous**  
    September 18, 2006  
    How does one create a Type with a FullName which is null?  
      
    From your definition, I would assume that the FullName would be null for the following:  
      
    Type t = typeof(List<>);  
    but in reality  
    t.FullName == System.Collections.Generic.List`1  
      
    I need to represent a type with FullName==null so that I can call Type.GetMethod() passing a Type[] and be able to return a method such as  
    ExampleMethod(List<> l)
-   **Anonymous**  
    October 18, 2006  
    I understand what you are saying. I don't think there is a way to change the full name of a type. I am not sure why the parameter type when the generics part is ill defined returns a numm for the full name while the direct reference to the type does indeed return a fullname. Let me follow this up and get back.
