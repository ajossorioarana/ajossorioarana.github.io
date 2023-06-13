---
title: "Analizing Trophic State of 130 Argentinian Lakes"
date: 2023-06-06T20:00:00-03:00
draft: true
plotly: true
tags: ["limnology", "eutrophication", "freshwater", "python", "data analysis"]
description: "EDA on small dataset of 103 Argentinian lakes and analysis of influence of different factors in trophic state"
---

# 1. Introduction

During my MSc. in Ecohydrology studies at UNLP, one of the first courses given to us was **freshwater ecology**. Beign a civil engineer, I was used to analyzing different variables, specially through the use of graphs. Nevertheless, the sheer amount of variables that must be taken into account when dealing with an ecological issue, and the intricate interactions between the variables was at the same time both mind-blowing and intelectually stimulating. This was strenghen by my position at [**EcoAqua**][ECOAQUA], which created a two-way questioning between the need for a practical application of the concepts I was beign presented at class and the theoretical validity of my approaches at work.

It was during that course that I first encountered the paper *"Relationships between air temperature, depth, nutrients and chlorophyll in 103 Argentinian lakes[^1]"*, by Dr. Rolando Quirós. Published in 1988, the paper presents a small dataset of morphometric, climatic and edaphic variables for 103 Argentine lakes and reservoirs. The measurements were collected during mid-summer, through three consecutives summers (1984, 1985 and 1986).

### Argentina's Geographical Regions

Argentina is a country with a diverse geography, ranging from the cool and windy Patagonian plateu to the hot and humid subtropical "Selva Misionera".  

### Argentine Lakes and Reserviors



### Eutrophication

What is eutrophication?


### Main Question

Main question to be tackled is:

> ***What is the tophic state of argentine lakes and reservoirs across the country, and which variables explain the differences***.



# 2. Setting Up
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

![Correlation Matrix](/post_files/arg_lakes/corr_matrix.png)

Relationship between TON and Chlorophyll-a...

{{< plotly json="/post_files/arg_lakes/ton_chl_link.json" height="400px" >}}

Relationship between mean depth and TSI-Chl-a...

{{< plotly json="/post_files/arg_lakes/tsi_chl_depth.json" height="400px" >}}

# Distribution of TSI

The following graph shows ...

{{< plotly json="/post_files/arg_lakes/violin_tsi_SD.json" height="400px" >}}

We can see that...

{{< plotly json="/post_files/arg_lakes/violin_tsi_TP.json" height="400px" >}}

We can see that...

# TSI Comparison
We can see that...

{{< plotly json="/post_files/arg_lakes/three_tsi_regions.json" height="800px" >}}

Trophic State Indexes can be compared...

{{< plotly json="/post_files/arg_lakes/tsi_comp.json" height="400px" >}}

# 4. Conclusions & Future Work
Some conclusion.

{{< github "ajossorioarana/EDA_argentine_lakes" >}}


[ECOAQUA]: https://www.ecoaqua.com.ar
[QUIROS]: https://www.agro.uba.ar/users/quiros/

[^1]: This article, alongside many other from Dr. Quirós can be freely obtained at his UBA [website][QUIROS]