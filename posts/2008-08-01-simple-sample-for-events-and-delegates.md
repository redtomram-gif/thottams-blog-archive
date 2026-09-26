---
title: "Simple sample for Events and Delegates"
date: 2008-08-01
slug: simple-sample-for-events-and-delegates
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/simple-sample-for-events-and-delegates
---

# Simple sample for Events and Delegates

I wanted to write a small sample to illustrate events and delegates. Here is a compact sample that illustrates it.

```csharp
using System;
public class EventSample
{
    public delegate void EventHandler();
    public event EventHandler myeh;
    public void Method()
    {
        Console.WriteLine("Inside Sample Method ... ");
    }
    public void OnChange()
    {
        myeh();
    }
    public static void Main()
    {
        EventSample es = new EventSample();
        es.myeh += new EventHandler(es.Method);
        es.OnChange();
    }
}
```

## Comments

-   **Anonymous**  
    July 31, 2008  
    PingBack from [http://blog.a-foton.ru/2008/08/simple-sample-for-events-and-delegates/](http://blog.a-foton.ru/2008/08/simple-sample-for-events-and-delegates/)
    
-   **Anonymous**  
    August 25, 2008  
    The comment has been removed
