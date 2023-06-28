---
title: "EDA of Argentina's Amateur Radio Callsigns Dataset"
date: 2023-06-25T17:00:00-03:00
draft: true
plotly: true
toc: true
tags: ["amateur radio", "python", "data analysis", "data visualization"]
summary: "EDA on a dataset of amateur radio licenses of Argentina"
---

## Introduction

Amateur Radio, also referred as ham radio, is a hobby and service that brings people, electronics and communication together. People use ham radio to talk across town, around the world, or even into space, all without the Internet or cell phones. It's fun, social, educational, and can be a lifeline during times of need. Hams set up stations in a club, at home, in their vehicles and in the field[^1]. Some even climb to "activate" (transmit and make contacts from) mountain summits[^2].

Hams use radios(*transceptors*) and antennas to comunicate via radiofrecuency. It's a technical and diverse hobby, which offers different flavours for different people, with some focusing on DX (long distance communications), others on contests, Morse code (known as CW by hams), kit building or just chatting. 

> *If you want to know more about ham radio, you can check the [Appendix](#appendix).*

Most countries have a communications agency which is in charge of managing the frequencies for different uses. In Argentina, that agency is [ENACOM](https://www.enacom.gob.ar/) and it published every 3-4 months a list of all active licenses in the country. Being an amateur radio operator[^3] and a data analyst myself, this dataset begged me to be dissected. 

The north star of this effort could be summarized as follows:

> *What is the distribution of amateur radio licenses across Argentina? How do the licenses split in the different amateur radio categories? Can I discover some of the variables that explain these differences?*

## Loading relevant data

First of all, I downloaded the xlsx file that contains the dataset from ENACOM's site. While ENACOM offers an API, it didn't offer any additional advantage to downloading the file, as the listed resource points to the same URL for downloading the file. 

Additionaly, I created a CSV file with relevant characteristics of the different provinces of Argentina[^4]. This CSV contains the following fields:

+ **province(index)**, province's name.
+ **population**, in number of people (obatined from [provisional 2022 census results][CENSUS])
+ **area**, in $km^{2}$. [*(Source)*][WIKIPEDIA]
+ **salaries**, which refers to the monthly mean individual salary for each province, for february 2023. [*(Source)*][SALARIES]

Finally, I loaded a GeoJSON of Argentina's provinces [(*Source*)](https://www.kaggle.com/datasets/pablomgomez21/geojson-file-provincias-argentinas). This would be useful latter on for making maps.

## Looking and preparing the data

I began by looking at the dataset, using `df.head()`:

```text
|    | Titular de la Licencia              | Señal Distintiva   | Categoría   | Expiración          | Provincia              | Localidad                       |
|---:|:------------------------------------|:-------------------|:------------|:--------------------|:-----------------------|:--------------------------------|
|  0 | PROTECCION CIVIL (MIN. SEG. NACION) | LU0CD              | SUPERIOR    | 2025-05-21 00:00:00 | Ciudad de Buenos Aires | CIUDAD AUTONOMA DE BUENOS AIRES |
|  1 | ARTURO JORGE PEYRU                  | LU1AAA             | NOVICIO     | 2024-05-07 00:00:00 | Ciudad de Buenos Aires | CIUDAD AUTONOMA DE BUENOS AIRES |
|  2 | CHRISTIAN LUIS DIAZ                 | LU1AAB             | NOVICIO     | 2024-04-26 00:00:00 | Ciudad de Buenos Aires | CIUDAD AUTONOMA DE BUENOS AIRES |
|  3 | WENCESLAO BERNARDINO MOREL          | LU1AAC             | GENERAL     | 2024-05-16 00:00:00 | Ciudad de Buenos Aires | CIUDAD AUTONOMA DE BUENOS AIRES |
|  4 | JUAN ANTONIO BILOTA                 | LU1AAD             | NOVICIO     | 2024-08-14 00:00:00 | Ciudad de Buenos Aires | CIUDAD AUTONOMA DE BUENOS AIRES |

```

The dataset contains the name of the licence holder, the callsing, the category, the expiration date, and the province and city of the holder's station.

Afterwards, I performed some data manipulation operations:

1. Renamed columns to more convenient and english[^5] names.
2. Removed leading and trailing spaces from all columns of the `object` type.
3. Added same stlye to strings in `category` and `city` (Capitalize first letter of every word).
4. Renamed all entires of `province` column belonging to three provinces with `df['province].replace()`. This was done to get a uniform naming for all provinces across resources.
5. Changed `df['expiration_date]` to DateTime.

```python
# Renaming the columns for ease of use
df = df.rename(columns={
    'Titular de la Licencia': 'license_owner',
    'Señal Distintiva': 'callsign',
    'Categoría': 'category',
    'Expiración': 'expiration_date',
    'Provincia': 'province',
    'Localidad': 'city'
})

# Remove leading or trailing spaces for all object columns
for column in df.columns:
    if df[column].dtype == object:
        df[column] = df[column].str.strip() 

# Apply same format to category and city column
df['category'] = df['category'].str.title()
df['city'] = df['city'].str.title()

# Rename province names to adhere to a standard
df['province'] = df['province'].replace({
    'Provincia de Buenos Aires': 'Buenos Aires',
    'Ciudad de Buenos Aires': 'Ciudad Autónoma de Buenos Aires',
    'Tierra del Fuego A. e I.A.S.': 'Tierra del Fuego, Antártida e Islas del Atlántico Sur'
})
```

Next, I checked for NaN values: The dataset contains 3 entries with NaN on *province* and 6 *city* columns. While I can't guess the city of the license holder, I can fill the NaN values of the *province* column by looking at the callsign. This is due to the fact that the suffix[^6] contains information about the holder's province: e.g. if a license begins in "*LU8Y*" the "*Y*" tells me that the holder is in Neuquén province. For that purpose, I wrote a small function that looks at the first two digits of the suffix and returns a province name, and apply it to NaN values.

> *Ever wonder the meaning behind those callsigns that hams put on their trucks, homes, email addresses and evolve into surname status? Check out the [Appendix](#appendix) to find out*. 

The last step of the data manipulation stage consisted in creating a GeoDataFrame, in which the provinces characteristics were bundled with the province's polygons from the GeoJSON file. I grouped licences by province (total and differentiated by category) and added that informatio to the GeoDataFrame. Finally, I created additional fields that will come handy: "*pop_density*", "*latitude*"[^7], "*licences_per_perc*" (total licenses as percentage of propulation).

## Analyzing the data and yes! Visualizations!

Having the proper data inplace, I started looking at the numbers, or more correctly, to the graphs.

First I plotted the total number of licenses per province, as seen below:

{{< plotly json="/post_files/ham_eda/json/ham_graph1.json" height="700px" >}}

[Text explaining the huge disproportion between Buenos Aires and the rest of the country.]

{{< plotly json="/post_files/ham_eda/json/ham_graph2.json" height="700px" >}}

[Text reinforcing the idea explored in the previous graph]

[Text introducing the idea of licenses as percentage of population]

{{< plotly json="/post_files/ham_eda/json/ham_graph4.json" height="700px" >}}

[Text reinforcing the idea but through a map]

{{< plotly json="/post_files/ham_eda/json/ham_graph5.json" height="700px" >}}

[Make the point that we can already start to observe trends]

[Lets see if area explains]

{{< plotly json="/post_files/ham_eda/json/ham_graph7.json" height="700px" >}}

[Lets see if population density explains]

{{< plotly json="/post_files/ham_eda/json/ham_graph100.json" height="700px" >}}

[Lets see if latitude explains]

***EERROR: GRAPH NOT IN DIRECTORY***

[Lets see if salaries explains]

{{< plotly json="/post_files/ham_eda/json/ham_graph100.json" height="700px" >}}

[Explore differences in categories]

{{< plotly json="/post_files/ham_eda/json/ham_graph8.json" height="700px" >}}


## Conclusions & Future Work
Some text


{{< github "ajossorioarana/arg_ham_callsigns" >}}

## Appendix

{{< youtube id="wt5wZhC5crI" title="HAM Official Documentary (2022)" >}}

[^1]: Paraphrased from [ARRL](http://www.arrl.org/what-is-ham-radio) (*American Radio Relay League*).
[^2]: This activity is called [SOTA](https://www.sota.org.uk/) (*Summits on the air*)
[^3]: Although I have passed the requirements to obtain a license and a callsign, that process is delayed. In the meantime, I regularly meet with other hams on our local radio club [*Radio club de los Andes (LU8YE)*](https://www.qrz.com/db/LU8YE).
[^4]: While this data (alongside with uniformed names and codes for provinces) can be obtained from Argentina's [Open Data](www.datos.gob.ar), for ease of use were just manually assembled. The number of provinces and the small number of fields collected justify this process.
[^5]: I do not consider "$English > Español$". In fact, I love my mother tongue. But pragmatism dictates this change, so as to match the article's language.
[^6]: In almost every other country, the prefix and the number are the ones that deliver geographical information. Not in Argentina, though.
[^7]: Latitude field corresponds to the latitude of the centroid of province polygon.

[CENSUS]: https://www.indec.gob.ar/indec/web/Nivel4-Tema-2-41-165
[WIKIPEDIA]: https://en.wikipedia.org/wiki/Provinces_of_Argentina
[SALARIES]: https://datos.gob.ar/dataset/produccion-salarios-promedio-mediano-por-provincia-sector-actividad/archivo/produccion_8ed16580-cc11-48ba-8ba3-81ab3c95b6da