---
title: "Passing Generic List and inheritance ..."
date: 2006-07-11
slug: passing-generic-list-and-inheritance
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/passing-generic-list-and-inheritance
---

# Passing Generic List and inheritance ...

I came across this interesting scenario when I was looking into some issues. In a normal method you can pass the derived class instance when a base class instance is expected. In the example below passing dc1 to Method1 shows that you can indeed pass a derived class when a BaseClass was expected. But when you generate a generic List with a derived class and try passing it to function which was expecting a generic list with a base class it does not work. Strange, but I got the error mentioned below.

To work around it I made the list as a list of base class and limited to access of the derived class by checking on the type. It helps but is not as elegant as it should have been.

<!-- C# -->

    using System;
    using System.Collections.Generic;
    using System.Collections;
    class BaseClass
    {
        public int field1;
        public BaseClass()
        {
            field1 = 30;
        }
    }
    class DerivedClass : BaseClass
    {
        int field2;
        DerivedClass()
        {
            field1 = 10;
            field2 = 20;
        }
        void Method1(BaseClass a)
        {
            Console.WriteLine("In method1 .." + field2);
        }
        void Method2(List<BaseClass> a)
        {
            for (int count = 0; count < a.Count; count++)
            {
                Console.WriteLine("In Method2 ... " + a[count].field1);
                // To be safe you check the type and so on ...
                //
                if (a[count].GetType() == typeof(DerivedClass))
                {
                    Console.WriteLine("In method2 ... " + ((DerivedClass)a[count]).field2);
                }
            }
        }
        static void Main()
        {
            BaseClass bc1 = new BaseClass();
            DerivedClass dc1 = new DerivedClass();
            DerivedClass dc2 = new DerivedClass();
            dc2.Method1(dc1);
            // This will give you compilation error:
            //
            // List<DerivedClass> bcl = new List<DerivedClass>();
            // This will work
            //
            List<BaseClass> bcl = new List<BaseClass>();
            bcl.Add(dc1);
            bcl.Add(bc1);
            dc2.Method2(bcl);
        }
    }

## Comments

-   **Anonymous**  
    July 11, 2006  
    Hi Thottam,  
      
    I ran into the same situation when I used generics extensively for the first time. And I learned that this is expected behavior.  
    The reason is that Generics are invariant. An good explanation on that can be found at the generics faq at [http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dndotnet/html/Fundamentals.asp](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dndotnet/html/Fundamentals.asp)  
    Cheers,  
    Dirk
    
-   **Anonymous**  
    July 11, 2006  
    I did not know about this until I read the article you had mentioned. Thanks for the reference and the comments. I will try to write about that next time from what I understand!
    
-   **Anonymous**  
    July 11, 2006  
    .NET actually does support covariance, but C# doesn't expose it. It wouldn't work for List<T> anyway, since that accepts T in input argument positions - covariance can only work with output arguments. I discussed it on my own blog entry (linked).
    
-   **Anonymous**  
    July 13, 2006  
    Thanks Barrkel for the reference. It was an excellent read as well.
    
-   **Anonymous**  
    August 23, 2007  
    You can get it to work if you make Method2 a generic method like this: void Method2<T>(List<T> a) where T : BaseClass { ... } This will work then: List<DerivedClass> bc1 = new List<DerivedClass>; dc2.Method2(bc1);
    
-   **Anonymous**  
    July 06, 2009  
    Thanks Nic, just what I was looking for
