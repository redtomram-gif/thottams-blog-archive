---
title: "Lang.NET Symposium 2006"
date: 2006-08-08
slug: lang-net-symposium-2006
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/lang-net-symposium-2006
---

# Lang.NET Symposium 2006

I still remember us planning for this event a few months back. It started off as a small group of people wanting to reinvent the then compiler labs into something bigger. The goal was to get all the right people in a room and have great speakers talk.

From where we started, I feel good about the conference overall. The symposium had a great mix of people from the industry and academia. We were lucky to get world class speakers present their work in this symposium.

The audiences for this event were also the right set. More than 90% of the room raised their hands for questions like: “Who has implemented a language?” - Who has worked in C?” and “Who has used VB?

The world’s best in the languages spaces were here in Redmond. To name a few we had Miguel De Icaza, Prof. John Gough, Prof. William Cook, Gilad Bracha, Prof. Shriram Krishnamurthi, Cory Ondrejka, Anders Hejlsberg, Don Syme, Gary Flake, Jim Hugunin, Jim Purbrick, etc presenting at the symposium.

The event had good publicity that there was a dedicated attendee Darryl K Taft from eWeek sitting through the symposium producing two articles. Even Mary Jo Foley stopped by during Tuesday and spent half a day at the conference. Here are some links to the articles in eWeek that were related to this symposium.

Sun digging deep for dynamic languages - [https://www.eweek.com/article2/0,1759,1997386,00.asp](https://www.eweek.com/article2/0,1759,1997386,00.asp "https://www.eweek.com/article2/0,1759,1997386,00.asp")

New Compiler enables Ruby to Run on .NET-  [https://www.eweek.com/article2/0,1759,1996960,00.asp](https://www.eweek.com/article2/0,1759,1996960,00.asp "https://www.eweek.com/article2/0,1759,1996960,00.asp")

After the conference was over, people who presented and attended the conference blogged vividly about the conference. To name a few

[https://tirania.org/blog/archive/2006/Aug-03.html](https://tirania.org/blog/archive/2006/Aug-03.html)

[https://blog.secondlife.com/category/webtech/](https://blog.secondlife.com/category/webtech/)

[https://www.dotnetlanguages.net/DNL/Default.aspx](https://www.dotnetlanguages.net/DNL/Default.aspx)

[https://wesnerm.blogs.com/net_undocumented/](https://wesnerm.blogs.com/net_undocumented/)

[https://lambda-the-ultimate.org/taxonomy/term/23](https://lambda-the-ultimate.org/taxonomy/term/23)

[https://nfocentrale.net/miser/astraendo/pn/](https://nfocentrale.net/miser/astraendo/pn/)

[https://www.artima.com/forums/flat.jsp?forum=152&thread=170518](https://www.artima.com/forums/flat.jsp?forum=152&thread=170518)

The proceedings from the conference were recorded and will be available soon. Michael Lehman will have the recordings posted and the attendees notified about it.

I want to thank Erik Meijer and Michael Lehman for all their help and support in making this happen. Without their help this would not have been possible. I also want to thank my manager Alessandro Catorcini for his support through the numerous hurdles while we were organizing for this symposium.

I had some interesting conversation with Miguel during the second day. I had been a great fan of him and getting the opportunity to talk with him was great. I also got to meet Bjorn Freeman from Eclipse with whom I had some very interesting conversations during breaks.

I had the privilege of dropping Prof. John Gough at his hotel during the symposium and enjoyed a lengthy conversation with him while we were stuck in 520 on our way back home.

I also had some enlightening conversation with Prof. Shriram Krishnamurthi during the symposium about higher education. It has always been in the corner of my mind to pursue a Ph.d in computer science and his advice around that was valuable.

I enjoyed the secondlife presentation by Jim Purbrick and organized another talk of the same for the entire CLR team on Friday. Jim obliged for the talk though he was just 6 hours away to boarding his flight. The talk went amazingly well though it was arranged in just an hour.

I had the opportunity to talk with Jim for a very long time while we were waiting for his cab. We had coffee and talked about various things. I got to learn about Linden labs quite a bit during those conversations.

**Summary of the conference by the hour and day is below.**

**Lang.NET day 1:**

The first day started off with a room packed with approximately 60 people. The symposium was kicked off with a key note talk from Anders Hejlsberg. Anders talked about [LINQ (Language Integrated Query) and C# 3.0](https://channel9.msdn.com/showpost.aspx?postid=114680 "https://channel9.msdn.com/ShowPost.aspx?PostID=114680") in detail and it was well received by the audience. He had more of hands on coding during his talk and illustrated examples on using LINQ to retrieve process information on your machine and sort them. The talk was similar to the talk at Compiler Labs 2006 with a few more code examples.

The second talk on the first day was from John Gough on [Gardens Point Ruby.NET Compiler](https://sky.fit.qut.edu.au/%7Egough/ "https://plas.fit.qut.edu.au/Ruby.NET/") titled “Dealing with Ruby on the CLR”. John emphasized that this was not a Ruby interpreter, Ruby.NET bridge, but a true Ruby compiler. He covered issues of blocks, continuations and dynamic inheritance that pose challenges to implementing Ruby on the CLR. Gough also said that this was not a toy project and he actually had a running version of one with him that did a demo offline during a break. On visiting their site they have a beta release of their compiler available.

After a short break, we resumed the sessions with some of the accepted papers for the conference. We had Christopher Diggins talk about [CAT programming language](https://www.cdiggins.com/cat/ "https://www.cdiggins.com/cat/"). There were questions about the need and the application for such a language which was not well answered. Diggins was primarily doing this a hobby and was not prepared for questions around the need for such a language.

The second accepted paper for the day was [Page XML](https://pagexml.net/ "https://www.adventurersisle.net/login/Markstest.xml") from Mark Cooper. Mark and his brother Paul cooper presented their work, which was a programming language in XML. He demonstrated examples where he had if conditions specified in XML. It was interesting and I think is a good translation on the client instead of looking at XML as data. This was primarily a research work and there was no real application scenarios described in the presentation.

The symposium had a break for lunch and we started again at 1:30 for Jim Hugunin’s talk on “Dynamically Typed Languages on the Common Language Runtime”. Jim’s presentation was extremely lively with abundant samples and demos. Jim demonstrated PowerShell in [IronPython](https://www.codeplex.com/Wiki/View.aspx?ProjectName=IronPython "https://www.codeplex.com/Wiki/View.aspx?ProjectName=IronPython") which was great. Jim also touched on generalizing concepts that all dynamic languages will need like DynamicObjects and so on. There was lots of energy in the presentation and was rated very high in the feedback that was received. Jim also

After a short break, we had another accepted paper presented by [Markus Lumpe](https://www.cs.iastate.edu/%7Elumpe/ "https://www.cs.iastate.edu/~lumpe/"). He was talking in a very low voice and it was hard to follow his presentation. Someone in the front has a good summary of the talk at [summary](https://www.dotnetlanguages.net/DNL/Default.aspx "https://www.dotnetlanguages.net/DNL/Default.aspx")

Followed by Markus talk was another paper that was from [Professor Susan Eisenbach](https://www.doc.ic.ac.uk/%7Esue/ "https://www.doc.ic.ac.uk/~sue/"). The title of the talk was “C21.1: Versioning in the TwentyFirst Century”. This talk was interesting, but left many raised eye brows. She talked about compiling with one version and running with another version where the class definition is different. She was particularly talking about assembly linking and loading, but it was confusing akin to the subject itself. I would encourage the versioning guys to send her an email and understand her problem better. She quoted that this was a real life problem with Merry Lynch when someone on the audience passed a sarcastic remark.

We had break now, but there were some registration questions on VSIP during one of the presentations and so we had some people from VSIP come and present their licensing model during the break.

After another break we had William Cook talk about “AppleScript and the effect of latency on programming Languages”. His talk is posted at his [site](https://www.cs.utexas.edu/%7Ewcook/ "https://www.cs.utexas.edu/~wcook/presentations/CookLANG.NET2006.pdf"). William Cook talked about a client talking with a server and all the factors that could make the communication efficient. He listed the main concepts like batch, cache, guessing, pre fetch etc. He talked about if the latency problem should be addressed in the languages. He also demonstrated some of his solutions from Apple Script which had resemblance with LINQ. He also talked about conditional path analysis which was interesting.

The last talk of the day was from [Mike Barnett](https://research.microsoft.com/%7Embarnett/ "https://research.microsoft.com/~mbarnett/") on “Every Language should (will) have contracts”. He talked about spec# and delved directly into code. He talked about the contents which he has in his [paper](https://research.microsoft.com/specsharp/papers/krml136.pdf "https://research.microsoft.com/specsharp/papers/krml136.pdf") on using contracts to develop break free code.

I had the opportunity to drive back John Gough to his hotel on my way home. I had a good conversation with him and he liked the conference and the arrangements.

**Lang.NET day 2:**

Day 2 started off with [Gilad Bracha](https://bracha.org/Site/Home.html "https://bracha.org/Site/Home.html") from SUN Microsystems talking about “Dynamically Typed Languages for the Java Platform”. Gilad was a very entertaining speaker. Gilad’s talk caught press attention instantly with an article on eWeek on [Sun digging deep for dynamic languages](https://www.eweek.com/article2/0,1759,1997386,00.asp "https://www.eweek.com/article2/0,1759,1997386,00.asp"). Bracha also noted that Sun currently offered support for dynamic languages like Jython, Kawa, Groovy and ECMAScript. Bracha also talked about [hotswapping](https://blogs.sun.com/roller/page/gbracha "https://blogs.sun.com/roller/page/gbracha?entry=jsr292_and_hotswapping") as a concept and it was some work on their pipe line. He also talked about invokeddynamic and invokedvirtual in his talk which was added to Sun Java.

The next talk was from [Gary Flake](https://flakenstein.net/ "https://flakenstein.net/") on “How I learned to stop worrying and love the imminent internet singularity”. It was a very interesting talk as he started off saying that this was best time in the world to be a computer researcher and many things were coming together. He also talked about the long tail concept in detail. His references to number of elephants and bacteria were amusing. He also had references to number of Britney spears in the world to other singers. He referred to the current state of the industry of how one took over the other. He also talked about innovators dilemma. He talked about how Google emerged in the search market.

This was followed by two accepted papers from [James Lapalme](https://www.iro.umontreal.ca/%7Elapalj/ "https://www.iro.umontreal.ca/~lapalj/Home.html") and Markus Lorez. They talked about “Separating modeling and simulation aspects in framework based modeling of hardware/software systems” and “Hydra – Translating source code from a unified code foundation into different target languages”.

We had Bradley Millington talk about BLinq after lunch. I missed some sessions of this talk but I have talked with BLinq guys off line to understand their work which is LINQ for the web.

The most interesting talk of that day was from [Shriram Krishnamurthi.](https://www.cs.brown.edu/%7Esk/ "https://www.cs.brown.edu/~sk/") He talked about DrScheme. He talked about stateful objects that are flowing freely in the system and the system adapts to the changes seamlessly. He demonstrated a web application where non availability of state information which lead to incorrect processing on the client. He later went on to demonstrate a live application where he ran a mouse co-ordinate program which was tracked by the state of the co-ordinates. He took that example into deeper implementation examples where he showed a paper submission web site designed on these principles. He felt very strong about the need for continuations which was contradicted by William Cook and Gilad.

[Paul Vick](https://www.panopticoncentral.net/ "https://www.panopticoncentral.net/") followed Shriram with his talk on VB. He had trouble with his demos as he updated his machine recently. He demonstrated [XML integration](../vbteam/new-vb-linq-ctp-now-live-on-msdn "https://detritus.blogs.com/lycangeek/2006/08/langnet_2006_da.html#more") into VB. He referred to Jim Hugunin’s talk on VB and IP and also gave some directions on VB 9 and more.

The last speaker of the second day was [Miguel De Icaza](https://tirania.org/ "https://tirania.org/blog/"). He talked about mono and had very verbose demonstration on the screen. His laptop running Novell Linux has some cool Lunar UI with drag effects and multiple screens flipping with good UI effects. Good amount of people asked him questions on what he was running after the talk. He demonstrated the mono version of C# IDE and compiled code and dragged and dropped UI controls and executed it. He also showed code that he took from the net to compile an implementation of mspaint on mono. It was not the ultimate mspaint, but he was trying to prove a concept. He also demonstrated the reflector working on mono. It was filled with him working on the machine, but overall it was great.

**Lang.NET day 3:**

The last day of the symposium started off with a stellar talk on [Second Life](https://secondlife.com/ "https://secondlife.com/"). It was amazing to know such a thing exists as virtual life. The scale of Second Life was huge and Jim said that their customer base was around 60000 mature audiences. The average age of their customers was in mid 30’s. He talked about virtual land and all that good stuff in his talk. It was interesting to know that characters (Avtars) move from machine to machine in his virtual world. You can attach any object to a rocket and fly him to another machine. In that case the object disappears from machine 1 and is recreated in the new machine in the same state. The machines are split in to geographical locations and you can buy space on them.

He also had a demonstration of building an avatar and making him do things. It was cool to see that you can blog about your avatar from within the game. This was the most interesting of the whole symposium.

The second talk on the last day was from [Don Syme](https://research.microsoft.com/%7Edsyme/ "https://research.microsoft.com/~dsyme/"). He talked about “Type-safe, scalable, efficient scripting on .NET and beyond”. This was similar to the recorded talk that was played in the last compiler labs. This time we had Don live presenting his work at the symposium.

We had [John Lam](https://www.iunknown.com/ "https://www.objectsharp.com/") talk about Ruby. His talk is repeated to the CLR team tomorrow. It is a great talk and I encourage everyone to go for the same.

We had the evening session wind down with a presentation on Phoenix by Andy Ayers. This was followed by an interesting presentation by Danny Thorpe on “Paradoxes in Web App Development“. He demonstrated windows live where you can browse with virtual earth and get the JavaScript for that display and paste it in another application. He referred that much of his work was on JavaScript. Some of the demos can be tried out at live [https://dev.live.com/virtualearth/sdk/](https://dev.live.com/virtualearth/sdk/ "https://dev.live.com/virtualearth/sdk/") which was very cool. You can browse to the tabs and look at the code and the SDK and so on.

We had the attendees visit the company store and have an open session where they talked with each other and people from Microsoft. There was a big discussion Second Life and how people spend time and money of it. It was interesting. The final talk was from Bruce Payette on Windows PowerShell. I had listened to this demonstration and talk and even used the shell. So I used that time writing this summary!

## Comments

-   **Anonymous**  
    August 09, 2006  
    Last week was the Lang.NET Symposium 2006 here in Redmond.&amp;nbsp; I still remember us planning for this...
    
-   **Anonymous**  
    August 10, 2006  
    The links point to the MS OWA you might want to fix that.
    
-   **Anonymous**  
    August 11, 2006  
    Thanks for pointing it out. I cut and paste it a few times from word and the links were lost somewhere in between. I have fixed all of them.
    
-   **Anonymous**  
    August 21, 2006  
    PingBack from [http://mdavey.wordpress.com/2006/08/21/langnet/](http://mdavey.wordpress.com/2006/08/21/langnet/)
    
-   **Anonymous**  
    May 31, 2009  
    PingBack from [http://portablegreenhousesite.info/story.php?id=9710](http://portablegreenhousesite.info/story.php?id=9710)
    
-   **Anonymous**  
    June 08, 2009  
    PingBack from [http://cellulitecreamsite.info/story.php?id=7632](http://cellulitecreamsite.info/story.php?id=7632)
    
-   **Anonymous**  
    June 08, 2009  
    PingBack from [http://toenailfungusite.info/story.php?id=795](http://toenailfungusite.info/story.php?id=795)
