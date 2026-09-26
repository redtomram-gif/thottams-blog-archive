---
title: "XMLSerializer, XDocument and LINQ"
date: 2008-10-01
slug: xmlserializer-xdocument-and-linq
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/xmlserializer-xdocument-and-linq
---

# XMLSerializer, XDocument and LINQ

I took the sample from my previous post and wanted to use LINQ to query the XML. You can pretty much use the power of SQL on the document object to richly query for things you are looking for.

```
using System;
using System.Linq;
using System.Xml.Linq;
using System.Collections.Generic;
using System.Xml;
using System.Xml.Serialization;
using System.IO;
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
                    },
                    new Book
                    {
                        Name = "Third Book",
                        Author = "Third Author",
                        Year = "Third Year",
                    },
                    new Book
                    {
                        Name = "Fourth Book",
                        Author = "Fourth Author",
                        Year = "Fourth Year",
                    },
                    new Book
                    {
                        Name = "Fifth Book",
                        Author = "Fifth Author",
                        Year = "Fifth Year",
                    }
                }
            };
            XmlSerializer xmlSerializer = new XmlSerializer(typeof(Company));
            FileStream fs = new FileStream("test.xml", FileMode.Create);
            xmlSerializer.Serialize(fs, c);
            fs.Close();
            XDocument doc = XDocument.Load("test.xml");

            var bks = from books in doc.Elements("Company").Elements("Book") where (books.Attribute("Title").Value.Equals("Fourth Book")) select books;
            foreach (var book in bks)
            {
                Console.WriteLine(book);
            }
        }
    }
}
```

## Comments

-   **Anonymous**  
    September 30, 2008  
    PingBack from [http://www.easycoded.com/xmlserializer-xdocument-and-linq/](http://www.easycoded.com/xmlserializer-xdocument-and-linq/)
    
-   **Anonymous**  
    September 30, 2008  
    Better yet, serialize straight into XDocument: var doc = new XDocument(); var (var writer = doc.CreateNavigator().AppendChild()) {  xmlSerializer.Serialize(writer, c); }
    
-   **Anonymous**  
    October 07, 2008  
    It is cool to query all objects with unified well known SQL language, but I feel a bit concerned about this dynamic SQL style rather than stored procedure.
    
-   **Anonymous**  
    October 08, 2008  
    From the investigation I have done there could be type bloating if not used carefully. Also, the object model is generated from the data model and changes when the data model changes if not crafted carefully. Having a normalized object model could lead to confusion in the application layer and could tie the implementation to LINQ only subsystems. Sure, careful design is required when modelling these systems.
    
-   **Anonymous**  
    October 21, 2008  
    Another real world sample illustrating the power of LINQ: [http://blogs.msdn.com/bali_msft/archive/2008/10/22/building-objects-from-xml-of-ebay-api-with-linq.aspx](http://blogs.msdn.com/bali_msft/archive/2008/10/22/building-objects-from-xml-of-ebay-api-with-linq.aspx)
