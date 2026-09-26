---
title: "Unhandled Exception handler (UnhandledExceptionEventHandler) to collect more information about unexpected exceptions"
date: 2008-04-30
slug: unhandled-exception-handler-unhandledexceptioneventhandler-to-collect-more-information-about-unexpected-exceptions
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/unhandled-exception-handler-unhandledexceptioneventhandler-to-collect-more-information-about-unexpected-exceptions
---

# Unhandled Exception handler (UnhandledExceptionEventHandler) to collect more information about unexpected exceptions

To continue on the exception post from last week I did a bit more research and learnt some more. I thought that I will share that with everyone. We had some interesting conversations in our team about exception handling and the following thought came up.

.NET gives you an option to write an unhandled exception handler which can be used to catch exceptions that you did not expect in your code and hence did not handle in your catch block. These are errors in the code as you did not expect them and should fail the application. This interestingly works even if you “throw;” or “throw new Exception(..)” from within the catch block.

It will be a good pattern to use this to log information about all unexpected exceptions in the application without introducing a “catch(Exception e)” in the code.

The sample below demonstrates the code for the exception handler. If you uncomment the throw blocks one then the handler will be invoked when the exception is thrown. Does anyone use this pattern and have benefited from it? Are there other ways to do this? Love to hear your thoughts!

<!-- C# -->

    using System;
    public class Sample
    {
        public static void Example()
        {
            AppDomain currentDomain = AppDomain.CurrentDomain;
            currentDomain.UnhandledException += new UnhandledExceptionEventHandler(MyHandler);
            try
            {
                throw new Exception("First Handled Exception");
            }
            catch (Exception e)
            {
                Console.WriteLine("Catch clause caught : " + e.Message);
            }
            try
            {
                throw new Exception("Second Handled Exception");
            }
            catch (Exception e)
            {
                Console.WriteLine("Catch clause caught : " + e.Message);
                // throw;
                // throw new Exception("Re-throw second exception", e);
            }
            // throw new Exception("Un-Handled Exception");
        }
        static void MyHandler(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception) args.ExceptionObject;
            Console.WriteLine("Exception Message: {0}", e.Message);
        }
        public static void Main()
        {
            Example();
        }
    }

## Comments

-   **Anonymous**  
    April 30, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=4237](http://www.travel-hilarity.com/airline_travel/?p=4237)
    
-   **Anonymous**  
    April 30, 2008  
    The biggest problem with this is that the exceptions are no longer considered "Unhandled" by Visual Studio when running in the debugger.   As such, it won't break until after your unhandled handler runs which then loses the original call stack. In our applications, we use an #if !DEBUG to enable the unhandled handler, giving us the best of both worlds.
    
-   **Anonymous**  
    May 02, 2008  
    Didn't know that this causes visual studio to think the exception to be handled. I have to try this, but looks like the DEBUG is a good work around!
    
-   **Anonymous**  
    May 02, 2008  
    Thottam, Meet Larry: [http://blogs.msdn.com/larryosterman/archive/2008/05/01/resilience-is-not-necessarily-a-good-thing.aspx](http://blogs.msdn.com/larryosterman/archive/2008/05/01/resilience-is-not-necessarily-a-good-thing.aspx) He blogged about Eric's post: [http://blogs.msdn.com/eric_brechner/archive/2008/05/01/crash-dummies-resilience.aspx](http://blogs.msdn.com/eric_brechner/archive/2008/05/01/crash-dummies-resilience.aspx) Interesting that so many 'softies are blogging about handling unexpected exceptions (or not).
    
-   **Anonymous**  
    May 15, 2008  
    Thanks Drew for the link. So close and yet I have not read his blog before. Interesting articles about exceptions and others too!
    
-   **Anonymous**  
    July 01, 2008  
    I'm not a programmer; I got an error message titled "Microsoft .NET Framework and starting "an unhandled exception..." and I can't remove it from my display. Is it possible to fix it, delete it, etc. without knowing how to program? I tried the "restore" but it didn't.
    
-   **Anonymous**  
    July 02, 2008  
    I don't understand your scenario completely, but let me try. Right click on your task bar and select Task Manager. Select the Applications tab and you should be able to end this error message dialog from there if it does not have any close button on it which I doubt.
    
-   **Anonymous**  
    July 31, 2008  
    Umm, this is a joke, right? And you work at Microsoft? Your blog post comes like 6 years too late and illustrates nothing. Actually, it will do more harm than it helps other people. What you published may work in the special case presented, but it isn't anyway near what is considered to be best practice.
    
-   **Anonymous**  
    December 23, 2009  
    MikeP, I am trying to implement a handler for unexpected exceptions. Can you please post some pointers (links, msdn etc) as to what the best practice is. Thanks in advance
