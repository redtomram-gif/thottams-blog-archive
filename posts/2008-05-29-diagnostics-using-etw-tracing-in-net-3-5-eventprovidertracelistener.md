---
title: "Diagnostics: Using ETW tracing in .NET 3.5 (EventProviderTraceListener)"
date: 2008-05-29
slug: diagnostics-using-etw-tracing-in-net-3-5-eventprovidertracelistener
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/diagnostics-using-etw-tracing-in-net-3-5-eventprovidertracelistener
---

# Diagnostics: Using ETW tracing in .NET 3.5 (EventProviderTraceListener)

.NET exposes an elegant diagnostics model that can be used by applications. It is a bit confusing to start with. There are a few listeners that exist in the Diagnostics namespace. Some of them are

1. TextWriterTraceListener

2. DefaultTraceListener

3. EventLogTraceListener

4. EventProviderTraceListener

5. EventSchemaTraceListener

6. XMLWriterTraceListener

7. ConsoleTraceListener

The EventSchemaTraceListener, EventLogTraceListener and EventProviderTraceListener were too confusing as they all read the same at a glance. Here is a sample I wrote using this listener to get some clarity around it.

ETW.cs:

<!-- C# -->

    using System;
    using System.Diagnostics;

    /// <summary>
    /// EventProviderTraeListener:
    ///   https://msdn.microsoft.com/en-us/library/system.diagnostics.eventing.eventprovidertracelistener.aspx
    /// Trace Switch:
    ///   https://msdn.microsoft.com/en-us/library/aa983740.aspx
    /// </summary>
    class ETWSample
    {
        static void Main(string[] args)
        {
            TraceSource myTraceSource = new TraceSource("TraceSourceApp");
            myTraceSource.TraceEvent(TraceEventType.Error, 1, "Tracing Error Message.");
            myTraceSource.TraceEvent(TraceEventType.Warning, 2, "Tracing Warning Message.");
            myTraceSource.TraceEvent(TraceEventType.Information, 3, "Tracing Information.");
            myTraceSource.TraceEvent(TraceEventType.Verbose, 4, "Tracing Verbose Message.");
            myTraceSource.TraceEvent(TraceEventType.Critical, 5, "Tracing Critical Message.");
            myTraceSource.Close();
            return;
        }
    }

I also wrote the following configuration file such that the appropriate listeners are added to the source.

ETW.exe.config


<!-- XML -->

    <configuration>
      <system.diagnostics>
        <sources>
          <source name="TraceSourceApp" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch">
            <listeners>
              <add name="ConsoleListener"/>
              <add name="ETWListener"/>
              <remove name="Default"/>
            </listeners>
          </source>
        </sources>

        <switches>
          <add name="SourceSwitch" value="Verbose" />
        </switches>

        <sharedListeners>
          <add name="ConsoleListener" type="System.Diagnostics.ConsoleTraceListener"/>
          <add name="ETWListener" type="System.Diagnostics.Eventing.EventProviderTraceListener, System.Core, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"
                   initializeData="{BA2DC22C-CCCE-4D17-AFC9-9811DD739970}" />
        </sharedListeners>
      </system.diagnostics>
    </configuration>


I compiled the cs file as csc ETW.cs /d:TRACE and got etw.exe. On running etw.exe I see the trace messages in the screen. This is because of the fact that consolelistener is added as a listener.

Now in order to enable ETW tracing I ran logman to start a logging session.

logman start mysession -p {BA2DC22C-CCCE-4D17-AFC9-9811DD739970} -o etwtrace.etl –ets

This creates an .etl file which is filled with data when I stop the session. It looks like it has parameters that you can use to tweak the behavior, but help around here is very poor and hence unfortunately requires lots of trial and error. Run perfmon and look into Data Collector Sets\Event Trace Sessions. The properties for mysession has a few options that you can experiment with.

To stop the session use

logman stop mysession –ets

If you notice, the provider guid given in the logman session and the listener configuration are the same.

To get information on what has been traced in the etl file run the following command

tracerpt etwtrace.etl

This will give you two files summary.txt and dumpfile.xml, which have readable information.

I was wondering if the etl files were viewable through event viewer. I tried to load the etl file from eventvwr using open saved log and it failed to load it. But it has another option to save events as an evtx file in the eventvwr actions menu. Kamesh Jayaram and [Krishna Kumar](https://blogs.msdn.com/krishkp) enlightened me with this.

I saved the etl file events as an evtx file and tried opening the evtx file in event viewer. I was able to see the trace messages in event viewer.

I added the EventSchemaTraceListener and generated a XML log file. I tried loading that file in eventvwr and it failed to load. I am yet to figure that out! If someone knows anything about it I will be interested to know.

I also learn that if you set the schema of the etl upfront with eventvwr you can avoid the step of extracting events from the etl file and load the etl directly. Lack of quality help makes that hard to do as well!

Another important thing to note is that the changes to the configuration files are not dynamic and hence requires the process to be restarted.

Summary:

Logging should be simple such it in itself doesn't become a problem. This is a good step in the right direction, but needs more polishing. I feel that the multiple hoops involved in getting this right makes it complex. Lack of proper help worsens it. Similar reading names for the filters is exasperating. I would like to hear your views and perspectives on this if you have used the ETW tracing libraries.

## Comments

-   **Anonymous**  
    May 28, 2008  
    PingBack from [http://www.travel-hilarity.com/airline_travel/?p=4830](http://www.travel-hilarity.com/airline_travel/?p=4830)
    
-   **Anonymous**  
    May 29, 2008  
    Thanks Thottam for sharing this. I've been experimenting with the new ETW classes as well some time back and was very disappointed with the documentation. But ETW seems to be very powerful and I'm happy that there is a first step to make it available to the .NET world. Please keep us updated with the experiences you make! Cheers, Volker
    
-   **Anonymous**  
    May 29, 2008  
    You saved a lot my time on this topic. Your comment on the state of help on the topic is apt.
    
-   **Anonymous**  
    May 29, 2008  
    ETW is indeed very powerful. I saw some performance numbers for ETW and they were very impressive. This is a good first step in the right direction, but needs some simplicity to be sprinkled on it.
    
-   **Anonymous**  
    February 16, 2009  
    Thanks for the pointers... BTW, your links to MSDN in the post are actually pointing to some OWA site.
    
-   **Anonymous**  
    June 13, 2009  
    Have you tried xperf? xperf has a viewer that analyze ETL files.
    
-   **Anonymous**  
    April 05, 2010  
    Can we use ETW tracing to capture events from the application running on some other machine in network.
