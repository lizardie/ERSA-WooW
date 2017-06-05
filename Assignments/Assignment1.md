---
title:  Shiny Paper on Mobile Phones with JO
author: Marina Toger [^fn2], John Östh [^fn3]
date:   2017-05-06
---
[^fn2]: Tel Aviv University
[^fn3]: Uppsala Universitet

# Abstract

*Here I try to document what we did with John[^fn1] for **NECTAR2017** in presentation "An exploration of Digital traces of urban flows: cell phone data census of mobility".*
[^fn1]: So we do not forget

## Presentation 

### Intro
lots of interest in OD matrices (in fact we are funded for that)

but data often are either:
* too rarely sampled (e.g., surveys, census) 
* imprecise - phone call records, too sparse events 
* precise but computationally intensive 
* precise but unavailable

so can one use cell phone data to get a valid, large-scale assessment?
* yes (Calabrese et al, 2015)

![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.32.57.png "Phone with Calabrese's review part")

### Cell phone based location estimation methods
Network based: e.g. Cell-Id positioning (easier to implement for us) or triangulation (computationally intensive)
Handset based location services: e.g. GPS (people opt out for privacy, heavy battery usage)
Hybrid: e.g. network- assisted A-GPS (precise but same problems as with GPS)
Wi-Fi based (need access to routers)
    (Steenbruggen et al., 2013; Calabrese et al., 2015)
![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.39.01.png "location estimation precisions by type")

### Cell network based location estimation methods

cell ID : 
![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.40.45.png "cell ID: position inaccurate, cheaper computationally, bad with sparse events")

cell ID + distance : 
![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.40.50.png "cell ID + distance")

cell ID + sector [or] cell ID + sector + distance : 
![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.40.55.png "cell ID + sector, aka “pizzas” or cell ID + sector + distance , aka “bananas”: more precise, more computation intensive")

###So what do we do?
we compare 2 datasets: **census data** (PLACE) with **cell phone data** (MIND) to assess convergent validity

#### PLACE database (Statistics Sweden SCB)
Anonymised longitudinal registers
	all individuals 16 years of age and older that were registered in Sweden as of December 31 for each year, here we use employed individuals only in November)
Data: 
	education, income, demography, employment 
Location: 
	geography on workplaces and housing (coordinates at 100m level)
Time: 
	annual register 1990-2014, here we use 2014
N: 
	Almost 10 mln unique  individuals in 2014 (>11.5 mln 1990-2014)

#### MIND database (at Uppsala University)
Individual anonymised phone records
	all mobile phone users in Sweden from a major Swedish network operator
Data: 
	Network-driven CDR: calls, texts, data usage, mms, silent handovers 
Location: 
	~100 m accuracy in urban areas, recorded as long as the phone is switched on. Quality improves with mobility, decreases with stationary state and in rural locations
Time: 
	divided to 5 min time steps, continuously, here we use data from a regular Tuesday in 2016
N: 
	Approximately 1.2 mln users
	

### MIND - estimation of location

we use tower cell ID positions, and cell ID data location estimation is imprecise for sparse event records, CDR: phone call, sms, mms and internet usage (González et al. 2008)
BUT we have dense temporal records, because we have silent handover events ( 320 million rows for a Tuesday in November 2016)

![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 18.49.48.png "Hard handover, source: Becvar & Zelenka 2006")



### MIND - estimation of the most likely location
![alt text](/Volumes/1TB/GitHub/ERSA-WooW/ERSA-WooW/Marinka/Screen Shot 2017-06-05 at 20.26.17.png "Our area has a high density of antennas - estimated accuracy of ~100m")

AND the cool thing is that unlike some other methods, this is fairly fast, so one can exploit big data sets. We may still be wrong occasionally, but in the aggregate, most approximations are pretty good.

### Phone data for comparison

To create comparable input sets, we select commute hours for the phone data so it is theoretically close to the once-a-year survey: we choose a ‘boring’ month, a ’boring’ day and the most ‘boring’ hours - Tuesdays of November 2016
Phones are assigned diurnal fixed **home** values based on the values of the mast where the longest time was spent between 00:00-07:00
Phones are assigned **work** place based on location between 09:00-17:00
**Mobility** 09:00-15:00 to contain all those that go to work late

### Method
1. Process data – calculate locations 
2. Calculate flows aggregated to 1 km²
3. Fill OD matrix for both datasets
4. Calculate accessibility $$Aj=\frac{F_{ij}}{\sum{d^{-\beta}}}$$ (Hansen 1959)
5. Compare accessibility values



$$Aj=F_{ij}/∑d^{-\beta}$$, where:
$$F_{ij}$$ are the registered (PLACE) or revealed (MIND) flow of people from i to j,
$$-\beta=-0.000115$$ for both datasets, estimated with Half-Life Model: $$-\beta=- ln(0.5)/m$$ , where $$m=6010$$ median distance in meters in Stockholm (Östh, Lyhagen & Reggiani, 2016)
d = distance from i to j in meters as crow flies







useful code snippets:

for flow analysis we generate unique ids from x y coordinates:
```R
install.packages('RStata')
```

```Stata
use "OD_FLOWS_PHONES_AGG.dta" , clear
gen oid = string(kmyo) + "000" + string(kmxo)
gen did = string(kmyd) + "000" + string(kmxd)
```






LaTeX: Without really going into the nitty gritty of this language, let's write our quintessential `hello world' program. 

% hello.tex - A very simple LaTeX example!
\documentclass{article} 
\begin{document} 
		Hello World! 
\end{document} 

You know, I just discovered that: 
\begin{equation}
	E = mc^2 
\end{equation}


And what about figures, diagrams and graphs?

Fortunately, figures, diagrams and graphs are much easier to handle in LaTeX than tables. And, as long as you want to draw something in a schematic way, you can even make your own diagrams and graphs using a couple of additional packages!

Figures

Because the output of LaTeX is already in pdf format it can easily incorporate figures from many format, such as the printer languages .pdf and .ps, but as well .jpeg and others. For example, this command: 


\begin{figure}[h!]
	\center 
	\includegraphics[width=0.3\textwidth] 
	{ligatures_latex} \caption{Correct 
	use of ligatures in \LaTeX (Source: 
	\href{http://nitens.org/ taraborelli/latex}
	{Taborelli})} 
\end{figure}


produces Figure 1:

The command h! states that the figure should very explicitly (the ! command) be placed about here (the h command).

When captions are incuded they are correctly numbered and size and position of the figure (even across columns) can easily be adjusted. 



From here ... into the abyss