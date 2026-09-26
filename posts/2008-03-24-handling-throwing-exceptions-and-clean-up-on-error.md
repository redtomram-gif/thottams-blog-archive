---
title: "Handling, throwing - exceptions and Clean up on error"
date: 2008-03-24
slug: handling-throwing-exceptions-and-clean-up-on-error
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/handling-throwing-exceptions-and-clean-up-on-error
---

# Handling, throwing - exceptions and Clean up on error

There are four ways to throw an exception in my opinion. They are:

1. Throw a new exception

`a. throw new ArgumentException( … );`

2. Throw the exception you caught

`a. throw e;`

3. Throw the same exception preserving the stack

`a. throw;`

4. Throw with the original exception as an internal exception

```
a. throw ArgumentException(innerException);
using System;
class Sample
{
    public void Method1()
    {
        // possible throw options
        // throw new ArgumentException(); - Origin of the exception
    }
    public void Method2()
    {
        try
        {
            Method1();
        }
        catch(ArgumentException ae)
        {
            // possible throw options
          // throw;
            // - Preserves the original stack
            // throw ae;
            // - Breaks the stack an and throws the same exception from now on
            // throw new ArgumentException(ae);
            // - throws a new exception with an inner exception
            // throw new ArgumentException();
            // - Throws a new exception
        }
        catch(Exception e)
        {
            Console.WriteLine(e.ToString());
        }
    }
    public void Method3()
    {
        bool cleanup = true;
        try
        {
            Method2();
            Method1();
            cleanup = false;
        }
        catch (Exception)
        {
            // perform Cleanup on error
        }
        finally
        {
            if (cleanup)
            {
                // perform cleanup on error
            }
        }
    }
    public static void Main()
    {
        Sample s = new Sample();
        try {
            s.Method2();
        }
        catch(ArgumentException ae)
        {
            Console.WriteLine(ae.ToString());
        }
    }
}
```

I personally would like to classify them as into two categories,

1. Origin of the exception, the place where the exception starts

a. Throw new ArgumentException(..)

2. The place where the exception is handled and either gets mapped or rethrown

a. Throw new ArgumentException()

b. Throw new ArgumentException(innerException)

```
c. Throw e;
d. Throw;
```

There are not many options during the origin of the exception and so that is very clear. The confusion comes only when an exception passes through your code and you attempt to handle it.

In this case again, all four options are available, but only a few make sense. For example

1. Throw new ArgumentException()

2. Throw ae

both don’t make much sense. Both of these have brighter counterparts in throw new ArgumentException(ae) and throw; which either captures the inner exception or preserves the stack. That leaves us with only two options in the way you handle and throw an exception:

1. If you want to map your exceptions

a. Throw new ArgumentException(innerException); (or)

2. If you want to log contextual information and pass on the same exception

```
a. Throw;
If you use the finally pattern as below, then you don’t need the throw;
```

If you use the Exception pattern as shown below then you need the throw to preserve the stack.

Incidentally, I noticed that performing cleanup on error in .NET is complicated. You have to either catch all exceptions and cleanup or use a variable that can tell you if a cleanup is required in the finally block. Both of them seem to violate the .NET patterns of catching only exceptions that you can handle and not using return values to understand errors.

If you had the cleanup in the catch block and you want to make sure it runs on any error, you have to catch Exception e which is not advised. This is another pattern that can be used to avoid that, but seems to me to rely on return value type paradigm which is also not advised either.

So what you choose seems to be up to you to choose!

[Krzysztof Cwalina](https://blogs.msdn.com/kcwalina/default.aspx) has a very interesting article on exception at [https://blogs.msdn.com/kcwalina/archive/2007/01/30/ExceptionHierarchies.aspx](https://blogs.msdn.com/kcwalina/archive/2007/01/30/ExceptionHierarchies.aspx)

## Comments

-   **Anonymous**  
    March 24, 2008  
    PingBack from [http://msdnrss.thecoderblogs.com/2008/03/24/handling-throwing-exceptions-and-clean-up-on-error-2/](http://msdnrss.thecoderblogs.com/2008/03/24/handling-throwing-exceptions-and-clean-up-on-error-2/)
    
-   **Anonymous**  
    March 25, 2008  
    You can differentiate in a finally block if you are in an exception unwind scenario or normal cleanup. Simple define this helper function and you are ready to go: static bool InException() {  return (Marshal.GetExceptionPointers() == IntPtr.Zero) ? false : true } This way you can choose what to do void func() {  try  {    Otherfunc()  }  finally  {    if( IsInException() )    {       // Do other things          }    else    {       // Normal cleanup logic    }  } } Yours,  Alois Kraus
    
-   **Anonymous**  
    March 31, 2008  
    I did not know that I could do this. Thanks for the great info. This probably eliminates the need for throw; wouldn't it?
    
-   **Anonymous**  
    April 08, 2008  
    Hi Thotham, I did investigate the solution a bit further here: [http://geekswithblogs.net/akraus1/archive/2008/04/08/121121.aspx](http://geekswithblogs.net/akraus1/archive/2008/04/08/121121.aspx) In your comment did you mean get rid of the catch handler? Yes that would be the case. The only drawback is that you cannot get your hands on the exception object in a finally clause. The most common use case for an unhandled exception is to trace it. But for this I would not want to write a catch handler. But so far I was not able to find the exception object in the finally block. Yours,  Alois Kraus
    
-   **Anonymous**  
    April 16, 2008  
    Yes. I was looking into the use of 'throw;' is it can be done this way. That is a good point that you cannot get a hand on the exception object. I am also not sure if there is any performance impact in doing this as this will be done pretty much throughout the code if adopted as a pattern for tracing or logging.
