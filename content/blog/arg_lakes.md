---
title: "Analizing Trophic State of 130 Argentine Lakes"
date: 2023-06-06T20:00:00-03:00
draft: true
plotly: true
tags: ["limnology", "eutrophication", "freshwater", "python", "data analysis"]
---

# Introduction
Some introduction.

# Setting Up
Set up.

Running `data.info()` yields the following information:

```text
<class 'pandas.core.frame.DataFrame'>
Index: 103 entries, Rodeo to Ameghino
Data columns (total 11 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   TYPE    103 non-null    object 
 1   REGION  103 non-null    object 
 2   AREA    103 non-null    float64
 3   ZMEAN   103 non-null    float64
 4   ALT     103 non-null    int64  
 5   LAT     103 non-null    float64
 6   TEMP    103 non-null    float64
 7   SDT     103 non-null    float64
 8   TP      103 non-null    int64  
 9   TON     103 non-null    int64  
 10  CHL-a   103 non-null    float64
dtypes: float64(6), int64(3), object(2)
memory usage: 9.7+ KB
```


Correlation...

![Correlation Matrix](/files/arg_lakes_corr_matrix.png)

Relationship between TON and Chlorophyll-a...

{{< plotly json="/plotly/arg_lakes_ton_chl_link.json" height="400px" >}}

Relationship between mean depth and TSI-Chl-a...

{{< plotly json="/plotly/arg_lakes_tsi_chl_depth.json" height="400px" >}}

# Distribution of TSI

The following graph shows ...

{{< plotly json="/plotly/arg_lakes_violin_tsi_SD.json" height="400px" >}}

We can see that...

{{< plotly json="/plotly/arg_lakes_violin_tsi_TP.json" height="400px" >}}

We can see that...

# TSI Comparison
Trophic State Indexes can be compared...

{{< plotly json="/plotly/arg_lakes_tsi_comp.json" height="400px" >}}

We can see that...

# Conclusion
Some conclusion.