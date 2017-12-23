---
layout: post
title:  "Visualizing flight delays using Hadoop and R"
date:   2015-11-17 19:02:34 +0800
---

In this post I talk about my flight delay analysis using flight [on-time performance data](http://web.archive.org/web/20170113153149/http://transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) published by Bureau of Transportation Statistics.

The flight dataset used contains flight data from 1987 to 2015. While this dataset isn't very large, it still requires some careful data processing. For this purpose, I decided to use Apache Hadoop for data cleaning and processing. The cleaned data is then used for visualizations using R.

If you'd like to dig deep the [source code](https://github.com/manthan787/flight-delays) and detailed [report](https://github.com/manthan787/flight-delays/blob/master/report.pdf) is available.

## Data Processing

As mentioned, Apache hadoop is used to process the flight dataset. Although the dataset contains many interesting fields, we're only interested in getting the delays at top 5 airports and top 5 airlines for different months and different years.

As expected, the dataset contains quite a few invalid records with corrupt values. The mapreduce job written for filtering out such invalid records roughly functions as follows:

**Mapper**: The map phase of the mapreduce job is responsible for

* Validating each record by performing sanity checks
* Emitting valid records as key-value pairs

**Reducer**: In the reduce phase all the flights having the same output key (same airline, airport, year and month) are combined by aggregating the delay and flight count in DelayWritable. Since, there could be many flights with same key in one file, we apply the same reducer as combiner which significantly reduced the amount of data shuffled for the reduce phase.
