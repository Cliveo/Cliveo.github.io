---
layout: post
title:  "Power Platform - Aggregate data using FetchXml"
date:   2025-01-12 14:48:01 +1300
categories: Azure
---

I've been finding that many developers I meet are not familiar with all of the powerful functionality available when querying Dataverse. One of the features is the ability to `aggregate` data when fetching it using `FetchXml`.

This can be particularly useful in situations when you want to reduce the logic needed inside your automation & offload the heavy lifting to tools you already have available. 

Power automate & logic apps both have pricing models which encourage you to reduce the number of actions you make. I often see people query Dataverse, only to iterate over the dataset to calculate something you could just ask the API for up front.

# Documentation

The documentation for the aggregation functions is quite good. 

It can be found [here](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/fetchxml/aggregate-data){:target="_blank"}

# Available functions

| Function     | Return Value                                          |
|--------------|-------------------------------------------------------|
| avg          | The average value of the column values with data.    |
| count        | The number of rows.                                  |
| countcolumn  | The number of rows with data in that column.         |
| max          | The maximum value of the rows in that column.        |
| min          | The minimum value of the rows in that column.        |
| sum          | The total value of the column values with data.      |

# Example data

I'm using the Microsoft example, this is the data I've created in my environment:

![here](/assets/fetch-aggregate/1.png)

# Example use

In my example I'm going to try and get the `sum` of account employees from my environment. The benefit of doing this using `FetchXml` is it results in only one action to do the calculation. 

Had I done the `List` action and then an `Apply to each` this could result in thousands of requests to Dataverse, risking throttling & the need to purchase more requests.

![here](/assets/fetch-aggregate/2.png)

# Conclusion

The ability to aggregate data using `FetchXml` is a lesser known but very powerful feature of Dataverse, It makes me wonder, how many other features are available in the Organization Service or WebAPI that are also under utilized?