---
title: "Calling delegates using BeginInvoke, Invoke, DynamicInvoke and delegate"
date: 2007-11-01
slug: calling-delegates-using-begininvoke-invoke-dynamicinvoke-and-delegate
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/calling-delegates-using-begininvoke-invoke-dynamicinvoke-and-delegate
---

# Calling delegates using BeginInvoke, Invoke, DynamicInvoke and delegate

I wanted to write about delegates this month. There are different ways in which you can invoke delegates to get either a synchronous or an asynchronous behavior. But from what I noticed, the delegate model provides you very less control when you invoke it asynchronously. The caller cannot easily abort or terminate the operation.

If you want to execute a delegate asynchronously and still want to have good control from the caller then invoke it explicitly from a thread. The sample below demonstrates multiple ways you can invoke a delegate and each has its own features with it. Till I wrote this, I didn’t realize that there are 6 ways to invoke a delegate and I am sure there are a few more that I am missing here. It was also interesting that when you execute a delegate on a thread then it cannot return a value as the caller of the thread is gone when the thread returns. If you want to pass parameters to the delegate in the thread you can use the ParameterizedThread option which is very useful.

<!-- C# -->

    using System;
    using System.Threading;
    using System.Runtime.Remoting.Messaging;
    namespace Delegate
    {
        public class SampleClass
        {
            public static bool SampleMethod()
            {
                Console.WriteLine("Inside sample method ...");
                return true;
            }
            public static void SampleThreadMethod()
            {
                while (true)
                {
                    Console.WriteLine("Inside sample thread method ...");
                    Thread.Sleep(500);
                }
            }
        }
        public delegate bool SampleMethodCaller();
        public class DelegateSample
        {
            ManualResetEvent waiter;
            public void CallBackMethodForDelegate(IAsyncResult result)
            {
                SampleMethodCaller smd = (SampleMethodCaller)((AsyncResult)result).AsyncDelegate;
                bool returnValue = smd.EndInvoke(result);
                Console.WriteLine("Callback Result:- {0}", returnValue);
                waiter.Set();
            }
            public void CallDelegateAndWait()
            {
                Console.WriteLine("Sample 1: Calling delegate using BeginInvoke and waiting for it to complete");
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                IAsyncResult result = smd.BeginInvoke(null, null);
                Console.WriteLine("After invoking delegate no callback...");
                bool returnValue = false;
                returnValue = smd.EndInvoke(result);
                Console.WriteLine("Wait Result:- {0}", returnValue);
            }
            public void CallDelegateUsingCallBack()
            {
                Console.WriteLine("Sample 2: Calling delegate using BeginInvoke and waiting on call back");
                waiter = new ManualResetEvent(false);
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                IAsyncResult result = smd.BeginInvoke(CallBackMethodForDelegate, null);
                Console.WriteLine("After invoking delegate in callback...");
                waiter.WaitOne();
            }
            public void CallDelegateUsingInvoke()
            {
                Console.WriteLine("Sample 3: Calling delegate using Invoke");
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                bool result = smd.Invoke();
                Console.WriteLine("Invoke call Result:- {0}", result);
            }
            public void CallDelegate()
            {
                Console.WriteLine("Sample 4: Calling delegate straight forward");
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                bool result = smd();
                Console.WriteLine("Simple call Result:- {0}", result);
            }
            public void CallDelegateDynamicInvoke()
            {
                Console.WriteLine("Sample 5: Calling delegate using DynamicInvoke");
                SampleMethodCaller smd = new SampleMethodCaller(SampleClass.SampleMethod);
                bool result = (bool)smd.DynamicInvoke();
                Console.WriteLine("Dynamic Invoke call Result:- {0}", result);
            }
            public void CallingDelegateOnAThread()
            {
                Console.WriteLine("Sample 6: Calling delegate on a thread");
                Thread nt = new Thread(SampleClass.SampleThreadMethod);
                nt.Start();
                Thread.Sleep(2000);
                Console.ReadLine();
                nt.Abort();
            }
            public static void Main()
            {
                DelegateSample ds = new DelegateSample();
                ds.CallDelegateAndWait();
                Console.WriteLine(" -----------------");
                ds.CallDelegateUsingCallBack();
                Console.WriteLine(" -----------------");
                ds.CallDelegateUsingInvoke();
                Console.WriteLine(" -----------------");
                ds.CallDelegate();
                Console.WriteLine(" -----------------");
                ds.CallDelegateDynamicInvoke();
                Console.WriteLine(" -----------------");
                ds.CallingDelegateOnAThread();
            }
        }
    }

## Comments

-   **Anonymous**  
    November 07, 2007  
    You've been kicked (a good thing) - Trackback from DotNetKicks.com
    
-   **Anonymous**  
    March 04, 2009  
    Very good artical with good examples. Thanksss
    
-   **Anonymous**  
    April 28, 2010  
    good article, but pls give some explaination.
    
-   **Anonymous**  
    May 02, 2010  
    The comment has been removed
