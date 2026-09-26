---
title: "System.Data.DataException and System.Data.DataSet.RaiseMergeFailed exception"
date: 2009-03-24
slug: system-data-dataexception-and-system-data-dataset-raisemergefailed-exception
source: https://learn.microsoft.com/en-us/archive/blogs/thottams/system-data-dataexception-and-system-data-dataset-raisemergefailed-exception
---

# System.Data.DataException and System.Data.DataSet.RaiseMergeFailed exception

This was another interesting issue I encountered recently. The error presented itself as an event log entry from our logging as below:

Unhandled Exception: System.Data.DataException: <target>.Column2 and <source>.Column2 have conflicting properties: DataType property mismatch.

```
   at System.Data.DataSet.RaiseMergeFailed(DataTable table, String conflict, MissingSchemaAction missingSchemaAction)
   at System.Data.Merger.MergeSchema(DataTable table)
   at System.Data.Merger.MergeTableData(DataTable src)
   at System.Data.Merger.MergeDataSet(DataSet source)
   at System.Data.DataSet.Merge(DataSet dataSet, Boolean preserveChanges, MissingSchemaAction missingSchemaAction)
   at System.Data.DataSet.Merge(DataSet dataSet)
   at ConsoleApplication1.Program.TryMerge(DataSet original)
   at ConsoleApplication1.Program.DemonstrateMerge()
   at ConsoleApplication1.Program.Main(String[] args)
```

If you look deeper in to the exception it tells you the column that Merge is complaining about. You get into this problem if the data type of the columns that you are merging are different. Now, you might wonder how you could run into an issue like this.

The scenario in which I ran into this issue was interesting. If one of your stored procedures returns a nvarchar in one case and returns a null in another, you will run into this error. One the merge when C# tries to merge the nvarchar and null it does not know the type of null and fails with a DataException. Another interesting observation is that the same error does not happen when you return a variable of a defined type and assign null to it.

Hope it is useful to others who have run into a similar stack and are trying to debug it.

I have tweaked a sample from MSDN to expose the same stack.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data;
namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            Program p = new Program();
            p.DemonstrateMerge();
        }
        private void DemonstrateMerge()
        {
            DataSet dataSet = new DataSet("dataSet");
            DataTable table = new DataTable("Table1");
            DataColumn idColumn = new DataColumn("Column1", Type.GetType("System.Int32"));
            idColumn.AutoIncrement = true;
            DataColumn itemColumn = new DataColumn("Column2", Type.GetType("System.Int32"));
            itemColumn.Unique = true;
            DataColumn[] keyColumn = new DataColumn[1];
            DataRow row;
            table.Columns.Add(idColumn);
            table.Columns.Add(itemColumn);
            dataSet.Tables.Add(table);
            keyColumn[0] = idColumn;
            table.PrimaryKey = keyColumn;
            for (int i = 0; i < 3; i++)
            {
                row = table.NewRow();
                row["Column2"] = i+100;
                table.Rows.Add(row);
            }
            dataSet.AcceptChanges();
            PrintValues(dataSet, "Original values");
            dataSet.EnforceConstraints = true;
            TryMerge(dataSet);
        }
        private void TryMerge(DataSet original)
        {
            DataSet dataSet = new DataSet("dataSet");
            DataTable table = new DataTable("Table1");
            DataColumn idColumn = new DataColumn("Column1", Type.GetType("System.Int32"));
            idColumn.AutoIncrement = true;
            DataColumn itemColumn = new DataColumn("Column2", Type.GetType("System.Int64"));
            itemColumn.Unique = false;
            DataColumn[] keyColumn = new DataColumn[1];
            DataRow row;
            table.Columns.Add(idColumn);
            table.Columns.Add(itemColumn);
            dataSet.Tables.Add(table);
            keyColumn[0] = idColumn;
            table.PrimaryKey = keyColumn;
            for (int i = 0; i < 3; i++)
            {
                row = table.NewRow();
                row["Column2"] = 100;
                table.Rows.Add(row);
            }
            dataSet.AcceptChanges();
            original.Merge(dataSet);
            PrintValues(original, "Merged values");
        }
        private void PrintValues(DataSet dataSet, string label)
        {
            Console.WriteLine("\n" + label);
            foreach (DataTable table in dataSet.Tables)
            {
                Console.WriteLine("TableName: " + table.TableName);
                foreach (DataRow row in table.Rows)
                {
                    foreach (DataColumn column in table.Columns)
                    {
                        Console.Write("\t table " + row[column]);
                    }
                    Console.WriteLine();
                }
            }
        }
    }
}
```

## Comments

-   **Anonymous**  
    March 24, 2009  
    PingBack from [http://www.anith.com/?p=22312](http://www.anith.com/?p=22312)
