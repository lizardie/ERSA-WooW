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





Managing workflow 

It is difficult to define what `good workflow' is, but it certainly has to satisfy each of the following elements to some degree:
Almost directly taken from this http://blog.revolutionanalytics.com/2010/10/a-workflow-for-r.html; another wonderful source is Healy2013. 

1 Transparency: A good workflow organizes the elements of the project logically and clearly, to make it easy for an observer (including yourself) to understand how the pieces come together. 
2 Maintainability: A good workflow makes it easy to modify and adapt the project. Standardized script names and good commenting practices are key here. 
3 Modularity: A good workflow encapsulates discrete tasks into separate components (e.g. scripts), so that it's always clear where modifications need to be made (and only made in one place), and components are re-usable for other projects. 
4 Portability: A good workflow makes it easy to move the project to another system, or hand it over to another person to work on, in such a way that it can still easily be run elsewhere. (By using relative (not absolute) pathnames is one example.) 
5 Reproducibility: A good workflow makes it easy for you, or others, to reproduce your results. 
6 Efficiency: A good workflow saves you time, by making it easier to work on the project, and by automating as much of the process as possible. 

LaTeX: instead of 'What You See Is What You Get' tools 

Word and Powerpoint are usually described as 'What You See Is What You Get' (WYSIWYG) tools. But what you get is sometimes below par. That's why some advocate the use of 'What You Mean Is What You Get' (WYMIWYG) tools. And these tools usually apply to markup languages. The most famous of these languages is HTML, which is just a bunch of code describing how text should look like on screen. 

Latex is a markup language as well, that just consists of code describing how a text should look like (on paper, screen or beamer). Instead of a word processing program, LaTeX is a typesetting program that determines for itself how words, sentences, paragraphs, etcetera, are placed on paper, slide or pdf file. Actually, LaTeX is a program (or compiler) that is fed by an input text file. Novice users find this most difficult; everything should be more or less coded. However, this allows for great flexibility, transparency, portability, and even efficiency. The same code can namely be used for papers, presentations and posters alike.

Where LaTeX truly shines is in the scientific domain.

But not only given the nice examples on this http://www.tug.org/texshowcase/ website. 

If one wants to consistently typeset text, figures, tables, references and especially equations, nothing outperforms LaTeX.

LaTeX itself is just a bunch of macros around TeX, a typesetting program devised by the notorious computer scientist Donald Knuth. LaTeX was created by Leslie Lamport in 1984 and TeX was created in 1978. Yes, it is that old. 

And in the end, that is what you want to do: write a nice shiny paper. 

I got it, and now what?

There are numerous tutorials on the web, but I recommend to first take a look at the http://latex-project.org/ latex project website, which comes with a whole bunch of tutorials (and yes, they are all free). 

Without really going into the nitty gritty of this language, let's write our quintessential `hello world' program. 

% hello.tex - A very simple LaTeX example!
\documentclass{article} 
\begin{document} 
		Hello World! 
\end{document} 

with the following output (but then as a separate document):

Hello World!

Okay, this perhaps doesn't look like much, but it actually contains quite some interesting elements. First, lines starting with a % sign are used as comments. Second, you first have to specify what kind of text you are typesetting (in this case an article). And finally, every element needs a begin and an end (just as we all do). In this case, the whole document needs to be specified. Things start to heat up a bit, when you add formulas such as when we add the following lines to our simple example 

You know, I just discovered that: 
\begin{equation}
	E = mc^2 
\end{equation}

with the following output: 
 
You know, I just discovered that: 

E = mc2

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

Always use a citation manager

The first thing I do recommend to all students (and researchers) is to use a citation manager. Even when using applications such as Microsoft Word (the horror ...). This is one tool that immediately delivers in terms of efficiency. Even if initially setting up a database with references might be time-consuming it still pays off in later stages. The point is that you only once have to type in a reference within a database. Later on you can always use this reference within the text and the reference or bibliography list should be automatically generated. And there are even reference managers that can import folders with articles in pdf format and automatically generate a reference database. 

Within the LaTeX environment, the application BibTeX is usually used. There are separate editors for this system, but the easiest way is using a more generic reference manager (the free Mendeley application comes to mind) that can write files to .bib files (the BibTeX format) or incorporate references directly within Microsoft Word. In this way, your remain as flexible as possible. 

From here ... into the abyss

Right. so we know now how to write a shiny paper with tables, references and perhaps even customated diagrams. Would that be it? No, it is just the start of a completely automated, customated, transparent and (hopefully) more efficient workflow. For that you need a set of tools that are complementary to each other. And fortunately, most of them are. However, choose your tools carefully. All of them are costly in terms of learning curves and there is only so much time available to you. Having said that, there are a couple of choices you might want to make eventually, of which the following three are probably most important. 

First of all, the choice of editor. Of course, TexShop or TeXNicCenter are nice editors, but are mostly suited for LaTeX. In the end, you might want to use a more versatile editor. One editor capable of editing R scripts, stata do files, html code, etcetera. All with proper highlighting and a consistent set of shortcuts. I am not saying which editor you should use. Too many have found their demise at the http://en.wikipedia.org/wiki/Editor_war editor war. There are many good (and free) ones out there. And even the ones you have to pay for are well worth the money. Pick up and stick to it for some time. For OsX there is now a free version of Textmate, which is quite good. And cross platforms, sublime is another editor well worth looking into. If you really want to go nerdy, opt for Emacs of Vi(m). Longstanding, very capable (and fast) editors. However, be aware of a steep learning http://www.terminally-incoherent.com/blog/wp-content/uploads/2006/08/curves.jpg curve. 

Second, as already hinted upon, the combination of LaTeX with statistical programs such as Stata and R is a killer app. If one has to choose, I would advise you to invest in R. Stata is more an econometric package, while R is much more statistical. And the userbase of the latter is much larger than the former, ensuring that with R most problems dealing with statistics (or even GIS) have already been dealt with. Moreover, there is a very nice editor, Rstudio, able to integrate them both. Heck, you can even weave LaTeX and R code together (called Sweave or Knitr) with mindblowing results. This comes rather close to what Donald Knuth coined literate programming: the simultaneous generation of the output of both text as analysis.

Third, and finally, think very carefully about backup programs. You know, you never need it until you really need it. The first advise is to always use an external disk with a backup application (such as Time Machine on OsX). Secondly, having some of your applications (especially your code, and thus text, files) somewhere in the cloud has additional benefits. Apart from a backup advantage, it also facilitates working together on a project. The best known of these applications is Dropbox, although cooperation using this is still a bit cumbersome. Another, more nerdy, example is using Git, which is a full blown distributed revision control and source code management system, created by Linus Torvards (that guy from Linux). It takes some time to get use to, but in the end it grows on you and becomes not only easier to use, but makes you in the end even more efficient. 

As a last example about complementarity, the before mentioned Rstudio application does not only edit LaTeX and R, but is able as well to set up a Git repository system. In the end, it comes all together. 

Some final thoughts

This might all seem a bit daunting. The best advise for novices would be: start one step at a time (``A journey of a thousand miles begins with a single step'', Lao-tzu). In this case this simply means that you start with a template file (such as that from the Elvesier article class), incorporate or copy in some text, divide it by section and subsection headers and see whether it works. 

Granted, the learning curves of most of these tools are rather steep (except for that of using citation managers---you really should use them). However, it does pay-off in the end. And most of these tools have the fortunate feature to be very complementary to each other. A good example is making slides. There is a package out there called beamer, which allows you to create slides from your text. This basically means, that all your codes used for your paper can be used again for your slides (within a LaTeX context, so equations won't fly all over the slide as sometimes happens with more conventionals presentation programs). If you know one application really well, it becomes easier to understand others or even use the same commands. And there are quite a few applications or languages out there that work with text files and are very handy for a researcher---HTML, Markdown, Latex, scripting languages from Stata and R and even languages as Ruby and Python come to mind. The latter, for example, is the native language of ArcGis scripts. 

Obviously, this paper is way too short to do justice to them. However, starting with LaTeX would be a good introduction to a more customated and automated workflow.