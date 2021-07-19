---
title: "TSI Calculator"
date: 2020-06-30T10:10:07-03:00
draft: false
tags: ["limnology" , "tsi"]
---

So the other day I was talking to my wife, letting out my frustrations about work and interests. She listened attentively how I ramble about my curiosity for programming and how it could be used (and it's used) to solve or analyze hydraulic, hydrological and limnological problems. She pondered, watching how my ideas where starting to drown in an endless sea of actionless, and after taking a breath she said:

{{< blockquote author="My wife" >}}
  "Stop rambling and do SOMETHING!"
{{< /blockquote >}}

And so, after the call to action urged by my wife, I began to work on some small projects I've considered over the previous months.

The first one is a **TSI calculator**.

### TSI - What the heck is that?
[TSI](https://w.wiki/VgB) stands for *Trophic State Index*. It's a measure of the eutrophication of the water body. Here is a short video that illustrates this concept:

{{< youtube mLbDbmmV6Qc >}}

I chose to limit the project to Carlson's TSI and the modifications made by Aizaki (1981). These indexes take into account the **Secchi depth** (**SD**) measurement, the **total phosphorus** (**TP**) and **chlorophyll-a** (**Chl-a**) measurement.

To be fair, I don't think there is a need for this. The potential users of this script are limnologist, ecologist or aquatic biologist who need to asses the trophic state, but they alredy develop and use their simple but reliable Excel sheets to do the job.