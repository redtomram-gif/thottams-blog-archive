---
title: "DBNull and Nullable types"
date: 2008-06-30
slug: dbnull-and-nullable-types
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/dbnull-and-nullable-types
---

# DBNull and Nullable types

When we have C# code interacting with the data base you run into this problem. Let me try and explain it. The database has nullable columns and C# has nullable types. For example, let us take a table Employee which has Id (int), name (string) and age (int). Let us assume that age will be null if no values are provided.

When you query the database for employee, the dataset or datareader will have DBNull for age. Now, this DBNull is not the same as C# null. This is the beginning of the problem. In order to make sure your code works properly, it has to expect that the reader can return a DBNull. You have to do the following in your code:

```
int? age = null;
if (!DBNull.Value.Equals(reader["Age"]))
{
    age = (int)reader["Age"];
}
```

This code converts the DBNull to a C# null. The lack of common null types between the two makes code have two sets of defaults for data, one in the DB and the other in C#. Which can get confusing.

If you don't use nullable types due to performance, you have to invent your own null value:

```
int age = -1;
if (!DBNull.Value.Equals(reader["Age"]))
{
    age = (int)reader["Age"];
}
```

This conversion seems ugly and it is all over the code. Is there any other elegant solution to this?

## Comments

-   **Anonymous**  
    June 30, 2008  
    You could solve this with an extension method. The extension method would look something like this: public static TValue GetNullableValue<TValue>(this DbDataReader reader, string name) {   object value = reader[name];   if (DBNull.Value.Equals(value))   {       return default(TValue);   }   return (TValue)value; } And your data access code will be: int? age = customerReader.GetNullableValue<int?>("Age");
    
-   **Anonymous**  
    June 30, 2008  
    I've encountered the same ugly convention as well, and was thinking along the lines of an extension method similiar to the previous poster. Although he missed the part about not being a nullable type (easy enough to tweak his method to be similiar to SQLs IsNull function.) When it is a nullable type, I've done this in the past: int? age = dr["Age"] as int?;
    
-   **Anonymous**  
    June 30, 2008  
    This problem (dbnull) has been around since well before .net and Microsoft has never done anything to make it any better, nullable types don't really bring us any closer to a way to map between a database null and a data type. Maybe the entity framework will finally fix this issue.
    
-   **Anonymous**  
    June 30, 2008  
    I also noticed this issue when I was using VB2005 to retrieve the data from SQL2005. I created a class named DBNullable(Of T) to deal with it. But I think M$ need to do something to fix this problem because it's confusing and fussy when developing a data-based system.
    
-   **Anonymous**  
    June 30, 2008  
    I love Trygve's idea, but it doesn't apply to DataSets (yes, some of us still need to use DataSets). ...and farrio, re: 'M$', are you like 4 years old or something?
    
-   **Anonymous**  
    July 01, 2008  
    There's no reason why this shouldn't work for DataSets. You just need another extension method. You can even take the table name as a parameter in this method to resolve which table of the dataset you want to get data from.
    
-   **Anonymous**  
    July 01, 2008  
    I like the extension method idea as it will work for both nullable and non-nullable types. int? age = dr["Age"] as int?; also works. It works because .NET is unable to cast it and assigns a NULL instead, which ends up being what we desire in this case.
    
-   **Anonymous**  
    July 01, 2008  
    I think this depends on whether you need to keep -1 as the default value or what you want to keep the null value. One possibility is to use the as operator for a safe type cast: int age = reader["Age"] as int? ?? -1; int? age = reader["Age"] as int?; Unfortunately, this has negative consequences that can be difficult to catch. If you mistakenly cast a smallint to byte, for example, the as operator will silently swallow InvalidCastException. I think a more reasonable solution with some measure of safety is to use a nullable cast either with or without the null coalescing operator: int age = (int?) reader["Age"] ?? -1; int? age = (int?) reader["Age"]; The code is easy to read and it has the advantage that InvaidCastException is thrown early in case you mistakenly cast to the wrong type, which I've found is easy to do with large tables.
    
-   **Anonymous**  
    August 24, 2008  
    Thanks - the as operator with ?? <defaultvalue> works perfectly when using nullable types for the database tier.
    
-   **Anonymous**  
    August 24, 2008  
    I've been working like mad to get my 3-tier construction to work. As I'm not all that great in C# yet,
    
-   **Anonymous**  
    September 17, 2008  
    Check out the SafeDataReader class from CSLA.NET. I had customized and used to solve the repeated dbnull check.
    
-   **Anonymous**  
    October 01, 2008  
    The comment has been removed
    
-   **Anonymous**  
    June 14, 2011  
    The comment has been removed
