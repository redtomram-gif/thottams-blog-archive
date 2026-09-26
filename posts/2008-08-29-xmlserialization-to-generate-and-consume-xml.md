---
title: "XmlSerialization to generate and consume XML"
date: 2008-08-29
slug: xmlserialization-to-generate-and-consume-xml
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/xmlserialization-to-generate-and-consume-xml
---

# XmlSerialization to generate and consume XML

I want to talk about Xml Serialization this month. One of the work I was involved in recently required generation of XML and a I came across this wonderful technology which helps you generate and consume XML seamlessly.

If you are generating XML in a clumsy way using printf containing the XML tags, I would strongly suggest you to look into this technology. Incidetaly this plays well with Linq too. The XML after it is loaded into the object can be parsed with Linq queries. I will write a sample for that soon.

<!-- C# -->

    using System;
    using System.Collections.Generic;
    using System.Xml;
    using System.Xml.Serialization;
    namespace Sample
    {
        public class Company
        {
            List<Book> books;
            [XmlElement(ElementName = "Book")]
            public List<Book> Books
            {
                get
                {
                    if (this.books == null)
                    {
                        this.books = new List<Book>();
                    }
                    return books;
                }
                set { }
            }
        }
        public class Book
        {
            [XmlAttribute(AttributeName = "Title")]
            public string Name { get; set; }
            [XmlAttribute(AttributeName = "Author")]
            public string Author { get; set; }
            [XmlAttribute(AttributeName = "Year")]
            public string Year { get; set; }
        }
        public class Demo
        {
            static void Main(string[] args)
            {
                Company c = new Company
                {
                    Books =
                    {
                        new Book
                        {
                            Name = "First Book",
                            Author = "First Author",
                            Year = "First Year",
                        },
                        new Book
                        {
                            Name = "Second Book",
                            Author = "Second Author",
                            Year = "Second Year",
                        }
                    }
                };
                XmlSerializer xmlSerializer = new XmlSerializer(typeof(Company));
                xmlSerializer.Serialize(Console.Out, c);
            }
        }
    }

I find that there are too many ways to load and parse XML such as XDocument, XPath, XMLSerialize, XDocument and so on. I feel it to be hard to make the right choice of technology for the scenario on hand sometimes. Yet again, documentation leaves you stranded in this space!

## Comments

-   **Anonymous**  
    August 29, 2008  
    PingBack from [http://hoursfunnywallpaper.cn/?p=3803](http://hoursfunnywallpaper.cn/?p=3803)
    
-   **Anonymous**  
    August 31, 2008  
    > If you are generating XML in a clumsy way using printf containing the XML tags, I would strongly suggest you to look into this technology. Isn't XmlSerializer being deprecated in favor of DataContractSerializer? And then we have LINQ to XSD on the horizon...
    
-   **Anonymous**  
    September 05, 2008  
    XmlSerializer is 7 years old and has been great for producing an XML of desired shape. DataContractSerializer is 2 years old and is best suited for scenarios where you can start with a type and not care about the XML format. It is more performant than XMLSerializer. LINQ to XSD (L2X) is yet to be released. It is a tech to provide a type facade on XML doc. L2X keeps the xml doc (DOM) in memory and fetches property values from XML. XmlSerializer constructs object from XML stream and does not use XML after deserialization.
    
-   **Anonymous**  
    November 22, 2008  
    Awesome blog !A lot of technical stuff . Hope it becomes a blog of note.
