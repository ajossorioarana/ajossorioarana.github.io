---
title: "Analizing Trophic State of 130 Argentinian Lakes"
date: 2023-06-06T20:00:00-03:00
draft: true
plotly: true
toc: true
tags: ["limnology", "eutrophication", "freshwater", "python", "data analysis"]
description: "EDA on small dataset of 103 Argentinian lakes and analysis of influence of different factors in trophic state"
---


## Introduction

During my MSc. in Ecohydrology studies at UNLP, one of the first courses given to us was **freshwater ecology**. Beign a civil engineer, I was used to analyzing different variables, specially through the use of graphs. Nevertheless, the sheer amount of variables that must be taken into account when dealing with an ecological issue, and the intricate interactions between the variables was at the same time both mind-blowing and intelectually stimulating. This was strenghen by my position at [**EcoAqua**][ECOAQUA], which created a two-way questioning between the need for a practical application of the concepts I was beign presented at class and the theoretical validity of my approaches at work.

It was during that course that I first encountered the paper *"Relationships between air temperature, depth, nutrients and chlorophyll in 103 Argentinian lakes[^1]"*, by Dr. Rolando Quirós. Published in 1988, the paper presents a small dataset of morphometric, climatic and edaphic variables for 103 Argentine lakes and reservoirs. The measurements were collected during mid-summer, through three consecutives summers (1984, 1985 and 1986).

Argentina is a country with a diverse geography, ranging from the cool and windy Patagonian plateu to the hot and humid subtropical "Selva Misionera". This array of biomes is reflected in the different waterbodies that populate the country. Lagoons on the Pampa plains tend to form on eolic depresions, while lakes in the Patagonian Andes usually have a glacial origin. As such, ecohydrological and hydrmorphic differences between region are expected.

Aside from the distinction between geographic regions, the waterbodies can be classified as natural or man-made. In the dataset, these are label as *lake* and *reservoir*. This differentiation is relevant on account of the ecohydrological differences in water and nutrient regimes between lakes and reservoirs, due to the origin of the latter with the construction of a dam thorugh a river.

### Eutrophication & TSI

Eutrophication can be defined as *"A process of pollution that occurs when a lake or stream becomes over-rich in plant nutrient; as a consequence it becomes overgrown in algae and other aquatic plants. The plants die and decompose. In decomposing the plants rob the water of oxygen and the lake, river or stream becomes lifeless. Nitrate fertilizers which drain from the fields, nutrients from animal wastes and human sewage are the primary causes of eutrophication[^2]"*. 

Eutrophication is a pressing issue on many waterbodies throughout the world, and dealing with it requires a interdisciplinary effort, with insight from a wide range of people, from ecohydrologists and limnologists to state agencies and local stakeholders. 

Beign an important problem for societies, the necessity of asses the trophic state of a waterbody was evident. In his paper "A trophic state index for lakes"[^3], published in 1977[^4], Carlson presented his now famous **Trophic State Index (TSI)**. These indexes are powerfull tools, as it helps characterize and summarize the trophic state of a waterbody, make comparisons and they serve as a bridge to communicate the lake trophic realities to the public in a less technical manner. 

Carlson's TSI werw constructed in a way that when the TSI goes up by 10 represent a doubling of algal biomass. As algal biomass is difficult to measure, the TSI indexes are calculated based on Secchi disk transparency, total phosphorus and chlorophyll. The formulae for the three TSI are shown below:

$$ 
TSI_{SD} = 60 - 14.41 {\ln}(SD) 

TSI_{TP} = 4.15 + 14.42 {\ln}(TP)

TSI_{CHL-a} = 30.6 + 9.81 {\ln}(CHL-a) 
$$

One extremely important note is that these TSI values ***should not*** be averaged, they must be **interpreted**. The different TSI values all point to the algal biomass, using other related variables as proxy. Given a particular waterbody, an important gap between different TSI indexes is hinting towards a particualr situation of the lake's limnology. 

### Guiding question

With this introduction out of the way, let's delve into the main question to ask the data:

> ***What is the tophic state of argentine lakes and reservoirs across the country, and which variables explain these differences***.


## Understanding the dataset
As a first step we will take a look at the dataset composition and the prnicipal characteristics of the different variables that make up the dataset.

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

The first aspect that pops up is abscence of null values. This is expected, given the origin of the dataset, but is not a situation that commonly appears when dealing with other sources of data.

We can see that the dataset has 11 fields, two of them of `object` type while the other 9 present numerical values. We can check some of the values by using  `data.head()`, `data.tail()` or `data.sample()`. 

Calling `data.sample(5)`:

| NAME      | TYPE   | REGION           |   AREA |   ZMEAN |   ALT |   LAT |   TEMP |   SDT |   TP |   TON |   CHL-a |
|----------|:-------|:-----------------|-------:|--------:|------:|------:|-------:|------:|-----:|------:|--------:|
Verde     | lake   | Patagonian Andes |    1.4 |    18.3 |   520 | 42.72 |    5   | 11    |    4 |    23 |    0.68 |
La Chilca | lake   | Pampa Plain      |   10   |     1.5 |    55 | 35.78 |   15.5 |  1.05 |   81 |   220 |   13.5  |
De Lobos  | lake   | Pampa Plain      |    7.5 |     1.2 |    20 | 35.27 |   16   |  0.25 |  285 |   359 |  166.2  |
Quichaura | lake   | Patagonian Andes |    2.5 |     3.4 |   900 | 43.55 |    7.5 |  5    |   38 |    83 |    1.1  |
Pico 4    | lake   | Patagonian Andes |    5.3 |     6.8 |   550 | 44.27 |    5   | 12    |    9 |    24 |    1.81 |

With this in mind, we can review what each of the columns represents:

+ **NAME (index)**: Name of the waterbody.
+ **TYPE**: Type of the waterbody.
+ **REGION**: Geographical region where the waterbody is located.
+ **AREA**: Area, in $km^2$.
+ **ZMEAN**: Mean depth, in $m$.
+ **ALT**: Elevation, in $m$.
+ **LAT**: Latitude, $°S$.
+ **TEMP**: Annual mean air temperature, in $°C$.
+ **SDT**: Secchi disk , measure of waterbody visibility, in $m$.
+ **TP**: Total phosphorus, in $mg.m^{-3}$.
+ **TON**: Total organic nitrogen, in $\mu.M$
+ **CHL-a**: Total chlorophyll-a, in $mg.m^{-3}$.

The two categorical variables are **TYPE** and **REGION**. We will explore which are the unique values presented in each of those columns:

```python
print(f"Unique values for 'TYPE': {data['TYPE'].unique().tolist()}")
print(f"Unique values for 'REGION': {data['REGION'].unique().tolist()}")
```
```
Unique values for 'TYPE': ['lake', 'reservoir']
Unique values for 'REGION': ['ANW', 'Pampas Mountains', 'Cuyo', 'Pampa Plain', 'Patagonian Plateau', 'Patagonian Andes']
```

We have already mentioned why discriminating between lakes and reservoir makes sense when analyzing trophic state. Regarding the field **REGION**, the dataset is divided into different geographical regions, which can be seen [here][GEOGRAPHICAL]. Some clarifications: "ANW" refers to "Argentine Northwest" and Patagonia was divided into Patagonian Andes and Patagonia Plateau, due to the morphometric differences between lakes in those areas[^5].

To check how many entries we have for each value, we draw upon `value_counts()`:

```python
data['TYPE'].value_counts()
```
```
TYPE
lake         73
reservoir    30
Name: count, dtype: int64
```
```python
data['REGION'].value_counts()
```
```
REGION
Patagonian Andes      39
Pampa Plain           26
ANW                   11
Pampas Mountains      10
Patagonian Plateau     9
Cuyo                   8
Name: count, dtype: int64
```

Let's now take a look at the descriptive statistics of the numerical fields by the use of `data.describe()`:

|           |     AREA |    ZMEAN |      ALT |       LAT |      TEMP |       SDT |       TP |     TON |    CHL-a |
|:----------|---------:|---------:|---------:|----------:|----------:|----------:|---------:|--------:|---------:|
| **count** |  103     | 103      |  103     | 103       | 103       | 103       |  103     | 103     | 103      |
| **mean**  |   73.37  |  25.6107 |  583.777 |  37.2027  |  11.866   |   4.32272 |  221.854 | 100.369 |  25.7726 |
| **std**   |  234.396 |  36.2721 |  481.896 |   5.60313 |   5.23958 |   4.94059 |  823.341 | 125.403 |  54.056  |
| **min**   |    0.09  |   0.7    |    2     |  24.12    |   3       |   0.03    |    1     |   6     |   0.16   |
| **25%**   |    4.35  |   3.1    |  159.5   |  34.375   |   6       |   0.675   |    9     |  21     |   0.83   |
| **50%**   |   12     |   8.1    |  550     |  37.88    |  14       |   2       |   30     |  45     |   6.7    |
| **75%**   |   44.3   |  33.15   |  844.5   |  42.31    |  16       |   7.25    |  125.5   | 126     |  23.75   |
| **max**   | 1984     | 166      | 3250     |  45.9     |  20.4     |  19       | 7912     | 762     | 405.3    |

There is a lot to consider in this table, but we will focus on some insights:
+ The difference between mean and median (50th percentile) is notable in a number of variables, which indicates the presence of outliers in those fields.
+ Total phosphorus total, organic nitrogen and chlorophyll-a show a great difference between the 75% percentile and the maximum value. This situation is not present in the case of Secchi disk transparency.
+ The standard deviation for area, altitude and total phosphorus is huge.

## Defining new fields

Having taken a look at the dataset, we will create some additional fields, that should prove useful to our analysis.

```python
data['VOL'] = data['AREA'] * data['ZMEAN']
data['DEPTH'] = np.where((data['ZMEAN'] > 5), 'deep', 'shallow')
data['TSI-SD'] = 60 - 14.41*np.log(data['SDT'])
data['TSI-TP'] = 4.15 + 14.42*np.log(data['TP'])
data['TSI-CHL-a'] = 30.6 + 9.81*np.log(data['CHL-a'])
```

+ A **volume (VOL)** field is added, as the multiplication of the waterbody's area and mean depth.
+ **DEPTH** is a categorical value, used to divide the waterbodies between shallow and deep ones. The cut was made at the mean depth of 5 meters and, while it's arbitrary[^6], represents the mean depth at which the limnological dynamics of the waterbody change[^7]. 
+ Columns for the three calculations of Carlson's TSI are added to the DataFrame.

## Univariate analysis



## Correlation


![Correlation Matrix](/post_files/arg_lakes/corr_matrix.png)

Relationship between TON and Chlorophyll-a...

{{< plotly json="/post_files/arg_lakes/ton_chl_link.json" height="400px" >}}

Relationship between mean depth and TSI-Chl-a...

{{< plotly json="/post_files/arg_lakes/tsi_chl_depth.json" height="400px" >}}

## Distribution of TSI

The following graph shows ...

{{< plotly json="/post_files/arg_lakes/violin_tsi_SD.json" height="400px" >}}

We can see that...

{{< plotly json="/post_files/arg_lakes/violin_tsi_TP.json" height="400px" >}}

We can see that...

## TSI Comparison
We can see that...

{{< plotly json="/post_files/arg_lakes/three_tsi_regions.json" height="800px" >}}

Trophic State Indexes can be compared...

{{< plotly json="/post_files/arg_lakes/tsi_comp.json" height="400px" >}}

## Conclusions & Future Work
Some conclusion.

{{< github "ajossorioarana/EDA_argentine_lakes" >}}


[ECOAQUA]: https://www.ecoaqua.com.ar
[QUIROS]: https://www.agro.uba.ar/users/quiros/
[COMPLEX]: https://en.wikipedia.org/wiki/Complex_system
[GEOGRAPHICAL]: https://en.wikipedia.org/wiki/Regions_of_Argentina
[SCHEFFER]: https://link.springer.com/book/10.1007/978-1-4020-3154-0#:~:text=Ecology%20of%20Shallow%20Lakes%20brings,many%20shallow%20lakes%20and%20ponds.

[^1]: This article, alongside many other from Dr. Quirós can be freely obtained at his UBA [website][QUIROS].
[^2]: As defined by the European Environment Agency [here](https://www.eea.europa.eu/archived/archived-content-water-topic/wise-help-centre/glossary-definitions/eutrophication).
[^3]: The modesty of the title strikes a notable contrast to current scientific publications. It reminds of Brian Moss words: *"Most of the scientific literature is written as if no one wants to communicate anything, anyway. The writing is pompous, self-serving, full of unnecessary jargon and distinctly off-putting. Quite a lot of it is deceptive in that something is written as if it is an entirely new revelation when this is far from the case."* (Ecology of freshwaters: a view for the twenty-first century / Brian Moss. — 4th ed., p.1)
[^4]: The original paper can be read for free [here](https://aslopubs.onlinelibrary.wiley.com/doi/epdf/10.4319/lo.1977.22.2.0361). 
[^5]: Lakes in the Andes tend to have a glacial origin, compared to the mostly eolic formation of lakes in the Patagonian plateau.
[^6]: Marten Scheffer roughly considers the thresholds at 3 meters; Some other references talk about 5 up to 10 meters.
[^7]: One excelente reference for the specifics of limnological processes in shallow lakes can be found in the book [*"Ecology of Shallow Lakes"*][SCHEFFER] by Marten Scheffer.