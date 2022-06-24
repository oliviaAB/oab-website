---
title: "Web scrapping for potato pedigree"
subtitle: ""
excerpt: "A little project to reconstruct potato pedigree from the Potato Pedigree Database"
date: 2020-06-10
author: "Olivia Angelin-Bonnet"
draft: true
images:
series:
tags:
categories:
layout: single-sidebar
---




During the course of a project for work, I’ve had to plot the pedigree of several potatoes varieties. These can be found in the [Potato Pedigree Database](https://www.plantbreeding.wur.nl/PotatoPedigree/index.html) set up by [Wageningen university](https://www.wur.nl/). This database allows you to search for the parents or progeny of a specific variety, and can display pedigrees as a tree. There is an option to change the depth of the tree, i.e. the number of parental generations that are displayed.

For this project however, I wanted to extract and combine the pedigree from several varieties. In this blog post, I’ll walk through how I used web scrapping to extract and reconstruct pedigrees from this database. I’ll take the example of the [Red Rascal](https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php?id=15504) variety, as it offers a number of interesting challenges.

## Extracting the pedigree information from the website into R

Unfortunately, the database does not offer any option to export the pedigree information into a table format. So I had to turn to web scrapping techniques to extract information from the website directly. There are a lot of great tutorials about web scrapping in R, so I won’t go into details; instead I’ll focus on the approach I've tried.

### Results page layout

We'll work from the [pedigree of Red Rascal](https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php?id=15504). Using Google Chrome, you can view the source code for the page via right `click > View page source`. This is the essence of web scrapping: extracting information from the source code of a web page. The part that is interesting for this project is the pedigree map:

<details>
  <summary><em>Show the source code of the Red Rascal pedigree webpage</em></summary>

```{.html .foldable}

<!DOCTYPE html>
<html lang="nl">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Potato Pedigree Database - RED RASCAL (1995) - Pedigree image map</title>
    <meta property="og:locale" content="nl_NL" />
    <meta property="og:type" content="website" />
    <meta property="og:title" content="Potato Pedigree Database - RED RASCAL (1995) - Pedigree image map" />
    <meta property="og:description" content="Potato Pedigree Database, RED RASCAL (1995) - Pedigree image map" />
    <link rel="stylesheet" media="all" href="/PotatoPedigree/layout/style.css" />
  </head>
  <body>
<div class="page">

  <header>
    <div>
        <a href="https://www.wur.nl/" title="Wageningen University &amp; Research"><img class="logo" src="/PotatoPedigree/layout/images/wur.png" alt="WUR logo"></a>  
    </div>
    <nav>
      <ul>
        <li><a href="/PotatoPedigree/">Home</a></li>
      </ul>
    </nav>
  </header>
  
  <h1>
    <img src="/PotatoPedigree/layout/images/piepertje3.gif" alt="potato" width="53" height="40" border="0" align="right">
    Potato Pedigree Database
  </h1>
  <hr class="line2" color="#003366">                

  
<font size="3" face="verdana"><b>pedigree image for 'RED RASCAL'</b> &nbsp;&nbsp;  (year: 1995)  [depth=5] </font>
<br><br>
<font size="2" face="verdana">&nbsp;change image tree depth: </font>
<select name="depth" size="1" style="font-family: Arial; font-size: 8pt;" class="box" onchange="if(this.options[this.selectedIndex].value != 0) { self.location.href='pedigree_imagemap.php?id=15504&amp;showjaar=0&amp;depth='+this.options[this.selectedIndex].value }">
  <option value="2">2</option>
  <option value="3">3</option>
  <option value="4">4</option>
  <option value="5" selected="">5</option>
  <option value="6">6</option>
  <option value="7">7</option>
  <option value="8">8</option>
  
</select>
&nbsp;&nbsp;&nbsp;
<font size="2" face="verdana">Show year of release (when known): </font>
<select name="showyear" size="1" style="font-family: Arial; font-size: 8pt;" class="box" onchange="if(this.options[this.selectedIndex].value != 2) { self.location.href='pedigree_imagemap.php?id=15504&amp;depth=5&amp;showjaar='+this.options[this.selectedIndex].value }">
  <option value="0" selected="">No</option>
  <option value="1"> Yes</option>
</select>
<ul>
  <li>
    <font size="2" face="verdana" color="blue">Colored names </font>
    <font size="2" face="verdana">indicate that there are more possibilities (several cultivars with the same name), 
    <br>only the branch of the first cultivar that was found is shown</font>
  </li>
  <li>
    <font size="2" face="verdana"><b>Clicking on a name</b> will open a popup window with new search results for that cultivar and a link to <i>europotato.org</i></font>
  </li>
</ul>

<map name="pedigreemap">
   <area title="RED RASCAL" shape="rect" coords="0,315,40,325" href="lookup.php?name=RED+RASCAL" alt="Array (1995)" target="popup">      
   <area title="TEKAU" shape="rect" coords="100,155,140,165" href="lookup.php?name=TEKAU" alt="Array (1982)" target="popup">      
   <area title="DESIREE" shape="rect" coords="100,475,140,485" href="lookup.php?name=DESIREE" alt="Array (1962)" target="popup">      
   <area title="1584C(10)" shape="rect" coords="200,75,240,85" href="lookup.php?name=1584C%2810%29" alt="" target="popup">      
   <area title="302.01" shape="rect" coords="200,235,240,245" href="lookup.php?name=302.01" alt="" target="popup">      
   <area title="URGENTA" shape="rect" coords="200,395,240,405" href="lookup.php?name=URGENTA" alt="Array (1951)" target="popup">      
   <area title="DEPESCHE" shape="rect" coords="200,555,240,565" href="lookup.php?name=DEPESCHE" alt="Array (1942)" target="popup">      
   <area title="MAUD MEG" shape="rect" coords="300,35,340,45" href="lookup.php?name=MAUD+MEG" alt="Array (&lt;1921)" target="popup">      
   <area title="1104C(2)" shape="rect" coords="300,115,340,125" href="lookup.php?name=1104C%282%29" alt="" target="popup">      
   <area title="M 109-3" shape="rect" coords="300,195,340,205" href="lookup.php?name=M+109-3" alt="" target="popup">      
   <area title="119-227" shape="rect" coords="300,275,340,285" href="lookup.php?name=119-227" alt="" target="popup">      
   <area title="FURORE" shape="rect" coords="300,355,340,365" href="lookup.php?name=FURORE" alt="Array (1930)" target="popup">      
   <area title="KATAHDIN" shape="rect" coords="300,435,340,445" href="lookup.php?name=KATAHDIN" alt="Array (1932)" target="popup">      
   <area title="DUKE OF YORK" shape="rect" coords="300,515,340,525" href="lookup.php?name=DUKE+OF+YORK" alt="Array (1891)" target="popup">      
   <area title="IMPOSANT" shape="rect" coords="300,595,340,605" href="lookup.php?name=IMPOSANT" alt="" target="popup">      
   <area title="unknown" shape="rect" coords="400,15,440,25" href="lookup.php?name=unknown" alt="" target="popup">      
   <area title="885(4)" shape="rect" coords="400,95,440,105" href="lookup.php?name=885%284%29" alt="" target="popup">      
   <area title="DR. McINTOSH" shape="rect" coords="400,135,440,145" href="lookup.php?name=DR.+McINTOSH" alt="Array (1944)" target="popup">      
   <area title="11-76" shape="rect" coords="400,255,440,265" href="lookup.php?name=11-76" alt="" target="popup">      
   <area title="11-79" shape="rect" coords="400,295,440,305" href="lookup.php?name=11-79" alt="" target="popup">      
   <area title="RODE STAR" shape="rect" coords="400,335,440,345" href="lookup.php?name=RODE+STAR" alt="Array (1908)" target="popup">      
   <area title="ALPHA" shape="rect" coords="400,375,440,385" href="lookup.php?name=ALPHA" alt="Array (1874)" target="popup">      
   <area title="USDA 40568" shape="rect" coords="400,415,440,425" href="lookup.php?name=USDA+40568" alt="" target="popup">      
   <area title="USDA 24642" shape="rect" coords="400,455,440,465" href="lookup.php?name=USDA+24642" alt="" target="popup">      
   <area title="EARLY PRIMROSE" shape="rect" coords="400,495,440,505" href="lookup.php?name=EARLY+PRIMROSE" alt="" target="popup">      
   <area title="KING KIDNEY" shape="rect" coords="400,535,440,545" href="lookup.php?name=KING+KIDNEY" alt="" target="popup">      
   <area title="INDUSTRIE" shape="rect" coords="400,575,440,585" href="lookup.php?name=INDUSTRIE" alt="Array (1900)" target="popup">      
   <area title="PEPO" shape="rect" coords="400,615,440,625" href="lookup.php?name=PEPO" alt="" target="popup">      
   <area title="735" shape="rect" coords="500,85,540,95" href="lookup.php?name=735" alt="" target="popup">      
   <area title="GLADSTONE" shape="rect" coords="500,105,540,115" href="lookup.php?name=GLADSTONE" alt="" target="popup">      
   <area title="HERALD" shape="rect" coords="500,125,540,135" href="lookup.php?name=HERALD" alt="" target="popup">      
   <area title="phu" shape="rect" coords="500,145,540,155" href="lookup.php?name=phu" alt="" target="popup">      
   <area title="USDA 41956" shape="rect" coords="500,245,540,255" href="lookup.php?name=USDA+41956" alt="" target="popup">      
   <area title="2-402" shape="rect" coords="500,265,540,275" href="lookup.php?name=2-402" alt="" target="popup">      
   <area title="USDA 41956" shape="rect" coords="500,285,540,295" href="lookup.php?name=USDA+41956" alt="" target="popup">      
   <area title="2-402" shape="rect" coords="500,305,540,315" href="lookup.php?name=2-402" alt="" target="popup">      
   <area title="PROFESSOR WOHLTMANN" shape="rect" coords="500,325,540,335" href="lookup.php?name=PROFESSOR+WOHLTMANN" alt="" target="popup">      
   <area title="ERICA" shape="rect" coords="500,345,540,355" href="lookup.php?name=ERICA" alt="" target="popup">      
   <area title="EARLY ROSE" shape="rect" coords="500,365,540,375" href="lookup.php?name=EARLY+ROSE" alt="" target="popup">      
   <area title="SEBEC" shape="rect" coords="500,385,540,395" href="lookup.php?name=SEBEC" alt="" target="popup">      
   <area title="BUSOLA" shape="rect" coords="500,405,540,415" href="lookup.php?name=BUSOLA" alt="" target="popup">      
   <area title="RURAL NEW YORKER NO. 2" shape="rect" coords="500,425,540,435" href="lookup.php?name=RURAL+NEW+YORKER+NO.+2" alt="" target="popup">      
   <area title="WHITE ROSE" shape="rect" coords="500,445,540,455" href="lookup.php?name=WHITE+ROSE" alt="" target="popup">      
   <area title="SUTTON&#039;S FLOURBALL" shape="rect" coords="500,465,540,475" href="lookup.php?name=SUTTON%27S+FLOURBALL" alt="" target="popup">      
   <area title="ZWICKAUER FRUHE" shape="rect" coords="500,565,540,575" href="lookup.php?name=ZWICKAUER+FRUHE" alt="" target="popup">      
   <area title="SIMSON" shape="rect" coords="500,585,540,595" href="lookup.php?name=SIMSON" alt="" target="popup">      
   <area title="TASSO" shape="rect" coords="500,605,540,615" href="lookup.php?name=TASSO" alt="" target="popup">      
   <area title="63/85" shape="rect" coords="500,625,540,635" href="lookup.php?name=63%2F85" alt="" target="popup">      
</map>

<img src="pedigree_image.php?id=15504&amp;depth=5&amp;showjaar=0" usemap="#pedigreemap" border="0">

<br><br>

<font size="1" face="verdana">note: tree images are dimensioned to accomodate full info at the deepest level (the more levels, the taller the picture), 
<br>
if no info is available at a deep level you may want to reduce the tree depth to obtain a more concise overview
<br><br></font> 


<hr class="line2" color="#003366"></div>
<footer class="text">
  <h2>Plant Breeding</h2>            
</footer>
<footer class="bottom">
  <a href="https://www.wur.nl/" title="Wageningen University &amp; Research">
    <img src="/PotatoPedigree/layout/images/wur_white.png" alt="Wageningen University and Research - To explore the potential of nature to improve the quality of life" class="left logo" title="Wageningen University and Research - To explore the potential of nature to improve the quality of life">
  </a>
</footer>  <!-- Google Analytics -->
  
  <script>
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
    ga('create', 'UA-18743767-2', 'auto');
    ga('send', 'pageview');  
  </script>
  
  <!-- End Google Analytics -->
  </body>
</html>
```
</details>

Right from the start, there is a challenge: by looking at the HTML code, you’ll notice that the pedigree plot is rendered by displaying a static source image, then overlaying on top some text boxes that allow the use to click on the name of a variety on the image to get more information. This is unfortunate, because it means that the web page does not contain information about the actual relationships between the different varieties. However, we can still extract the name of the varieties displayed on the pedigree, as well as their position on the pedigree tree, and we can use this to reconstruct the tree.

### Extracting page source in R

We’ll use the packages `rvest` and `xml2` to access the source code of the web page directly in R. This is done via the `read_html()` function from `rvest.` Note: I’m also loading the `tidyverse` metapackage, as I’ll use several of its packages to process the information.


```r
library(rvest)
library(xml2)
library(tidyverse)

page_url <- "https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php?id=15504"
raw_html <- read_html(page_url)
```

Next, we want to grab from this content the part of the HTML code with the `map` tag. This is done with the `html_nodes()` function; the `html_children()` function then extracts all area elements of the map, which in our case are the blocks of texts corresponding to the varieties in the pedigree.


```r
graph_html <- raw_html %>% 
    html_nodes("map") %>% 
    html_children()
```

The resulting graph_html object is a… We can convert this information into a dataframe using the `xml_attrs()` function from the `xml2` package:


```r
graph_df <- reduce(xml_attrs(graph_html), bind_rows)
```

The resulting dataframe has one row per area element, and each column represents an attribute of the area element.


```r
head(graph_df) %>% 
  knitr::kable()
```



|title      |shape |coords          |href                          |alt          |target |
|:----------|:-----|:---------------|:-----------------------------|:------------|:------|
|RED RASCAL |rect  |0,315,40,325    |lookup.php?name=RED+RASCAL    |Array (1995) |popup  |
|TEKAU      |rect  |100,155,140,165 |lookup.php?name=TEKAU         |Array (1982) |popup  |
|DESIREE    |rect  |100,475,140,485 |lookup.php?name=DESIREE       |Array (1962) |popup  |
|1584C(10)  |rect  |200,75,240,85   |lookup.php?name=1584C%2810%29 |             |popup  |
|302.01     |rect  |200,235,240,245 |lookup.php?name=302.01        |             |popup  |
|URGENTA    |rect  |200,395,240,405 |lookup.php?name=URGENTA       |Array (1951) |popup  |


### Tidying-up - a bit of data wrangling

We'll do a bit of tidying up, to grab the information that will help us reconstruct the relationships between the different varieties in the pedigree image. The position of each text box is stored in the `coord` attribute, which gives the x- an y-positions of the top left and bottom right corners of the text box, separated by commas, as follows: `x_tl`, `y_tl`, `x_br`, `y_br` (tl: top-left corner, br: bottom-right corner). We can extract these values and compute the position of the center of each text box:


```r
graph_df <- reduce(xml_attrs(graph_html), bind_rows) %>%
	select(title, coords) %>% 
  separate(coords, c("x1", "y1", "x2", "y2"), ",", convert = TRUE) %>%
  mutate(x = (x1 + x2) / 2,
         y = (y1 + y2) / 2)
```


|title      |  x1|  y1|  x2|  y2|   x|   y|
|:----------|---:|---:|---:|---:|---:|---:|
|RED RASCAL |   0| 315|  40| 325|  20| 320|
|TEKAU      | 100| 155| 140| 165| 120| 160|
|DESIREE    | 100| 475| 140| 485| 120| 480|
|1584C(10)  | 200|  75| 240|  85| 220|  80|
|302.01     | 200| 235| 240| 245| 220| 240|
|URGENTA    | 200| 395| 240| 405| 220| 400|

Now, the y coordinate of each text box is interesting, because it informs us about the generation (i.e. level within the pedigree) in which each variety appears. I’ve used a factor trick to transform the y position into an integer representing the pedigree generation. Note that when computed this way, the generation will be 0 for the variety of interest, here Red Rascal:


```r
graph_df <- reduce(xml_attrs(graph_html), bind_rows) %>%
	select(title, coords) %>% 
  separate(coords, c("x1", "y1", "x2", "y2"), ",", convert = TRUE) %>%
  mutate(x = (x1 + x2) / 2,
         y = (y1 + y2) / 2,
  generation = factor(x),
  generation = as.numeric(generation) - 1)
```


|title      |  x1|  y1|  x2|  y2|   x|   y| generation|
|:----------|---:|---:|---:|---:|---:|---:|----------:|
|RED RASCAL |   0| 315|  40| 325|  20| 320|          0|
|TEKAU      | 100| 155| 140| 165| 120| 160|          1|
|DESIREE    | 100| 475| 140| 485| 120| 480|          1|
|1584C(10)  | 200|  75| 240|  85| 220|  80|          2|
|302.01     | 200| 235| 240| 245| 220| 240|          2|
|URGENTA    | 200| 395| 240| 405| 220| 400|          2|

Finally, I’ll rename the title attribute to name (just because), and I’ll get rid of the "unknown" varieties. I’m not sure why sometimes there is an "unknown" displayed (like for Maud Meg’s parents), while sometimes the pedigree is simply left blank (like for M 109-3). The final code is:


```r
graph_df <- reduce(xml_attrs(graph_html), bind_rows) %>%
	select(title, coords) %>% 
  separate(coords, c("x1", "y1", "x2", "y2"), ",", convert = TRUE) %>%
  mutate(x = (x1 + x2) / 2,
         y = (y1 + y2) / 2,
  generation = factor(x),
  generation = as.numeric(generation) - 1) %>% 
  select(name = title, generation, x, y) %>% 
  filter(name != "unknown")
```


|name       | generation|   x|   y|
|:----------|----------:|---:|---:|
|RED RASCAL |          0|  20| 320|
|TEKAU      |          1| 120| 160|
|DESIREE    |          1| 120| 480|
|1584C(10)  |          2| 220|  80|
|302.01     |          2| 220| 240|
|URGENTA    |          2| 220| 400|


### Automating the process - a side note on URLs

We’re ready to make this first step of the process into a function. The obvious argument of this function is of course the URL of the web page. However, some poking around reveals that the URL of a specific pedigree follows a pattern. The default search URL always looks like this:

```
https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php
?id=XXXXX
```

where the ID depends on the variety you are querying. Then, if you want to change the depth of the pedigree, the url becomes:

```
https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php
?id=XXXXX&showjaar=0&depth=Y
```

Apparently the depth can be any integer between 2 and 8 included. (The argument `showjaar=0` controls whether the year of creation of the varieties should be displayed  or not in the image, it does not affects the HTML content of the text boxes). 

So rather than supplying the URL to the function, we can instead pass the ID of the pedigree search and the depth of the pedigree that we want to get. We get the following function:


```r
get_pedigree_graph <- function(image_id, depth = 5){
  page_url <- paste0(
    "https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php?id=", 
    image_id,
    "&showjaar=0&depth=",
    depth)
  raw_html <- read_html(page_url)
  
  ## Get the HTML code for the interactive plot above the static image
  graph_html <- raw_html %>% 
    html_nodes("map") %>% 
    html_children()
  
  graph_df <- reduce(xml_attrs(graph_html), bind_rows) %>% 
    select(title, coords) %>% 
    separate(coords, c("x1", "y1", "x2", "y2"), ",", convert = TRUE) %>% 
    ## Compute center of the nodes (each representing a variety)
    ## The x position determines the generation or level of the variety
    mutate(x = (x1 + x2) / 2,
           y = (y1 + y2) / 2,
           generation = factor(x),
           generation = as.numeric(generation) - 1) %>% 
    select(name = title, generation, x, y) %>% 
    filter(name != "unknown")
  
  return(graph_df)
}
```

Let's test that quickly for Red Rascal with a depth of 6 (you can compare to the pedigree shown [here](https://www.plantbreeding.wur.nl/PotatoPedigree/pedigree_imagemap.php?id=15504&showjaar=0&depth=6)):


```r
get_pedigree_graph(15504, 6) %>% 
  tail() %>% 
  knitr::kable()
```



|name             | generation|   x|    y|
|:----------------|----------:|---:|----:|
|ALABASTER        |          6| 620|  830|
|JACKSON seedling |          6| 620|  890|
|0742             |          6| 620| 1130|
|083              |          6| 620| 1150|
|ODIN             |          6| 620| 1170|
|PAULSEN D 205/81 |          6| 620| 1190|


## Reconstructing the pedigree graph

Now comes the more complicated part. We'll use the coordinates of the varieties in the pedigree image to reconstruct the parental relationships between them. We'll use the data-frame constructed above.

