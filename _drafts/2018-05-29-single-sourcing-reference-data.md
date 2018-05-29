---
published: false
---
## Single Sourcing Static Data Using T4

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

The easiest way to generate appropriate code is to provide enough information to generate a C# model

### Extracting Data and Dictionary Generation

### Further Learning

### Acknowledgement



