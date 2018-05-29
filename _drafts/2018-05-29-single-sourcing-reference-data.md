---
published: false
---
## Single Sourcing Static Data Using T4 Templates

Consider a .NET application which needs to convert between [ISO-3166-1](https://en.wikipedia.org/wiki/ISO_3166-1) numeric and alpha-3 codes in a variety of places and has an underlying SQL database. This data is almost certainly going to be represented in the database somehow - lets say this is represented in the table below: 

| Numeric Code  | Alpha-3 Code  | English Short Name  |
| ------------- | ------------- | ------------------  |
| 004	        | AFG           | Afghanistan         |
| 008           | ALB           | Albania             |
| 036           | AUS           | Australia           |
| ...           | ...           | ...                 |

How should we convert between numeric codes and alpha-3 codes in our application efficiently? We _may_ have this data, or a subset of this data reflected in code; this is usually done in an ad-hoc manner and is often functionally harmless no matter how odoriferous the code.
```
var countries = new Dictionary<int, string> countryNumericCodeToAlpha3 =
{
	{4, "AFG"},
    {8, "ALB"},
    {36, "AUS"},
    ...
}

```

Other alternatives include:
- Fetching the mapping table each time a conversion needs to take place (not performant)
- Caching the mapping table either on startup, or the first time a conversion needs to take place (performant, but we now we have extra state to reason about)

### Can We Do Better?

My favored solution is to use a code-generation tool prior to the compilation of application code to extract data from the database and produce a static set of data we can refer to in-code. This means the database stays as the single point of truth, but we also get data we can refer to in-code in an efficient manner without having to fuss over fetching from the database at runtime or caching.

### Defining The Data Model

The easiest way to generate appropriate code is to provide enough information to generate a C# model: **TableDefinitions.xml**

```
<DBTable Name="Country" KeyColumn="NumericCode", KeyType="string", ConnectionString="INSERT CONN STRING HERE">
	<DBColumn Name="Alpha3Code" Type="string"/>
    <DBColumn Name="EnglishShortName" Type="string"/>
</DBTable>
```

In code we then serialize that into the following model:
**DBTableDefinition.cs**

```
public class DBColumn
{
	public string ColumnName { get; set; }
    public string ColumnType { get; set; }
}

public class DBTableDefinition
{
	public string TableName { get; set; }
    public string KeyColumn { get; set; }
    public string KeyType { get; set; }
    public List<DBColumn> Columns { get; set; }
}
```

With these models constructed, we can refer to them via a T4 template to generate a C# model:
**ReferenceData.tt**
```
INSERT TEMPLATE CODE HERE
```

Right clicking on the template and selecting 'Run Custom Tool' generates the following Model:
```
public class Country
{
    public string Alpha3Code { get; set; }
    public string EnglishShortName { get; set; }
}
```

### Extracting Data and Dictionary Generation

Now we have a model, we need to construct a dictionary to map between the desired key column content and the contents of the other columns. To do this, first we must write code which will generate the text for inserting a row into a `Dictionary<keyType, tableName>` where both `keyType` and `tableName` are specified in ReferenceData.xml.

```
ROW CODE HERE
```

Now that we can define a rows we need to insert into a dictionary, all we need to do is wrap that in a static class which can house the output dictionary.

```
TEMPLATE CODE HERE
```

Running the T4 template tool again via visual studio yields the following code:
```
```

Excellent. Now the database is the single source of truth, but we have an easy way to reflect that in our application code that doesn't involve managing DB connections or caches. This approach can be used for more than just dictionaries - you can generate lists, enums, and any other static data you might want, you'll just have to bend the xml definitions and code generation components appopriately!


### Further Learning
- I highly recommend [Dustin Davis' fantastic Pluralsight course on T4 templates](https://www.pluralsight.com/courses/t4-templates)
- [Microsoft's documentation on T4 templates](https://msdn.microsoft.com/en-us/library/bb126445.aspx)


### Acknowledgements
