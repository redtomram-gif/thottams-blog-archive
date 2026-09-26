---
title: "Asynchronous delegates and callback"
date: 2009-04-11
slug: asynchronous-delegates-and-callback
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/asynchronous-delegates-and-callback
---

# Asynchronous delegates and callback

I was debugging some issue related to asynchronous delegates and wanted to share that information with everyone.

When you do a BeginInvoke the delegate is invoked on a thread from the thread pool and on completion the callback is also called on the same thread pool thread. Now, any exceptions that is thrown on this thread cannot be caught by the main thread and so will crash the process. If you want to handle the exception, then you have to catch the expected exception in the callback and store it to some state and rethrow the exception from the main thread so that the callers above you can handle it gracefully.

In the sample below if you remove the try catch block in the callback or replace the exception.Add(e) with a throw, then the exception will crash the process. If you swallow the exception then you will be hiding failures and continuing which could result in other failures.

```
using System;
using System.Threading;
using System.Runtime.Remoting.Messaging;
using System.Collections.Generic;
namespace Delegate
{
    public class SampleClass
    {
        public static bool SampleMethod()
        {
            Console.WriteLine("Inside sample method ...");
            throw new ArgumentException();
        }
    }
    public delegate bool SampleMethodCaller();
    public class DelegateSample
    {
        List<Exception> exceptions;
        ManualResetEvent waiter;
        public DelegateSample()
        {
            exceptions = new List<Exception>();
        }
        public void CallBackMethodForDelegate(IAsyncResult result)
        {
            SampleMethodCaller smd = (SampleMethodCaller)((AsyncResult)result).AsyncDelegate;
            try
            {
                bool returnValue = smd.EndInvoke(result);
            }
      catch (ArgumentException e)
            {
            exceptions.Add(e);
            }
            finally
            {
                waiter.Set();
            }
        }
        public void CallDelegateUsingCallBack()
        {
            try
            {
                waiter = new ManualResetEvent(false);
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                IAsyncResult result = smd.BeginInvoke(CallBackMethodForDelegate, null);
     waiter.WaitOne();
                if (exceptions.Count != 0)
                {
                    throw exceptions[0];
                }
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Catch exceptions here ...");
            }
        }
        public static void Main()
        {
            DelegateSample ds = new DelegateSample();
            ds.CallDelegateUsingCallBack();
            Console.WriteLine(" -----------------");
        }
    }
}
```

## Comments

-   **Anonymous**  
    April 11, 2009  
    PingBack from [http://www.anith.com/?p=28308](http://www.anith.com/?p=28308)
    
-   **Anonymous**  
    April 13, 2009  
    Hey buddy thanks for sharing :-)
    
-   **Anonymous**  
    May 06, 2011  
    What is the point of a BeginInvoke if you wait for that thread to end after that? It would be great if it was a way to do the same with out having to wait for that event, but I don't think there is, except if you have a timer in the main thread checking from time to time if the are any errors in the Exceptions array.
    
-   **Anonymous**  
    June 10, 2011  
    Any comments on Keeper's comments ?
