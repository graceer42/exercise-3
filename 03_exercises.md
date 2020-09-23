---
title: 'Weekly Exercises #3'
author: "Grace Reardon"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for graphing and data cleaning
```

```
## -- Attaching packages ------------------ tidyverse 1.3.0 --
```

```
## v ggplot2 3.3.2     v purrr   0.3.4
## v tibble  3.0.3     v dplyr   1.0.2
## v tidyr   1.1.2     v stringr 1.4.0
## v readr   1.3.1     v forcats 0.5.0
```

```
## -- Conflicts --------------------- tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
```

```r
library(ggthemes)      # for even more plotting themes
library(geofacet)      # for special faceting with US map layout
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())       # My favorite ggplot() theme :)
```


```r
#Lisa's garden data
garden_harvest <- read_sheet("https://docs.google.com/spreadsheets/d/1DekSazCzKqPS2jnGhKue7tLxRU3GVL1oxi-4bEM5IWw/edit?usp=sharing") %>% 
  mutate(date = ymd(date))
```

```
## Reading from "2020_harvest"
```

```
## Range "Sheet1"
```

```r
# Seeds/plants (and other garden supply) costs
supply_costs <- read_sheet("https://docs.google.com/spreadsheets/d/1dPVHwZgR9BxpigbHLnA0U99TtVHHQtUzNB9UR0wvb7o/edit?usp=sharing",
  col_types = "ccccnn")
```

```
## Reading from "2020_seeds"
## Range "Sheet1"
```

```r
# Planting dates and locations
plant_date_loc <- read_sheet("https://docs.google.com/spreadsheets/d/11YH0NtXQTncQbUse5wOsTtLSKAiNogjUA21jnX5Pnl4/edit?usp=sharing",
  col_types = "cccnDlc")%>% 
  mutate(date = ymd(date))
```

```
## Reading from "seeds_planted"
## Range "Sheet1"
```

```
## Warning in .Primitive("as.double")(x, ...): NAs introduced by coercion
```

```r
# Tidy Tuesday data
kids <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-15/kids.csv')
```

```
## Parsed with column specification:
## cols(
##   state = col_character(),
##   variable = col_character(),
##   year = col_double(),
##   raw = col_double(),
##   inf_adj = col_double(),
##   inf_adj_perchild = col_double()
## )
```

## Setting up on GitHub!

Before starting your assignment, you need to get yourself set up on GitHub and make sure GitHub is connected to R Studio. To do that, you should read the instruction (through the "Cloning a repo" section) and watch the video [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md). Then, do the following (if you get stuck on a step, don't worry, I will help! You can always get started on the homework and we can figure out the GitHub piece later):

* Create a repository on GitHub, giving it a nice name so you know it is for the 3rd weekly exercise assignment (follow the instructions in the document/video).  
* Copy the repo name so you can clone it to your computer. In R Studio, go to file --> New project --> Version control --> Git and follow the instructions from the document/video.  
* Download the code from this document and save it in the repository folder/project on your computer.  
* In R Studio, you should then see the .Rmd file in the upper right corner in the Git tab (along with the .Rproj file and probably .gitignore).  
* Check all the boxes of the files in the Git tab and choose commit.  
* In the commit window, write a commit message, something like "Initial upload" would be appropriate, and commit the files.  
* Either click the green up arrow in the commit window or close the commit window and click the green up arrow in the Git tab to push your changes to GitHub.  
* Refresh your GitHub page (online) and make sure the new documents have been pushed out.  
* Back in R Studio, knit the .Rmd file. When you do that, you should have two (as long as you didn't make any changes to the .Rmd file, in which case you might have three) files show up in the Git tab - an .html file and an .md file. The .md file is something we haven't seen before and is here because I included `keep_md: TRUE` in the YAML heading. The .md file is a markdown (NOT R Markdown) file that is an interim step to creating the html file. They are displayed fairly nicely in GitHub, so we want to keep it and look at it there. Click the boxes next to these two files, commit changes (remember to include a commit message), and push them (green up arrow).  
* As you work through your homework, save and commit often, push changes occasionally (maybe after you feel finished with an exercise?), and go check to see what the .md file looks like on GitHub.  
* If you have issues, let me know! This is new to many of you and may not be intuitive at first. But, I promise, you'll get the hang of it! 



## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.


## Warm-up exercises with garden data

These exercises will reiterate what you learned in the "Expanding the data wrangling toolkit" tutorial. If you haven't gone through the tutorial yet, you should do that first.

  1. Summarize the `garden_harvest` data to find the total harvest weight in pounds for each vegetable and day of week. Display the results so that the vegetables are rows but the days of the week are columns.


```r
garden_harvest %>%
  mutate(weight_lbs = weight*0.00220462)  %>%
  mutate(day = weekdays(date)) %>%
  group_by(vegetable,day) %>%
  summarise(veg_sum = sum(weight_lbs)) %>%
  pivot_wider(id_cols = vegetable, 
              names_from = day,
              values_from = veg_sum)
```

```
## `summarise()` regrouping output by 'vegetable' (override with `.groups` argument)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Saturday"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Friday"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["Monday"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Thursday"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["Tuesday"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["Sunday"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["Wednesday"],"name":[8],"type":["dbl"],"align":["right"]}],"data":[{"1":"asparagus","2":"0.04409240","3":"NA","4":"NA","5":"NA","6":"NA","7":"NA","8":"NA"},{"1":"basil","2":"0.41005932","3":"0.46737944","4":"0.0661386","5":"0.02645544","6":"0.11023100","7":"NA","8":"NA"},{"1":"beans","2":"4.70906832","3":"1.52559704","4":"6.5080382","5":"3.39291018","6":"4.38719380","7":"1.52780166","8":"4.08295624"},{"1":"beets","2":"0.37919464","3":"0.02425082","4":"0.6724091","5":"11.89172028","6":"0.15873264","7":"0.32187452","8":"0.18298346"},{"1":"broccoli","2":"NA","3":"NA","4":"0.8201186","5":"NA","6":"NA","7":"NA","8":"0.70768302"},{"1":"carrots","2":"2.33028334","3":"2.13848140","4":"0.8708249","5":"2.67420406","6":"0.35273920","7":"NA","8":"NA"},{"1":"chives","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA","7":"NA","8":"0.01763696"},{"1":"cilantro","2":"0.03747854","3":"0.07275246","4":"NA","5":"NA","6":"0.00440924","7":"NA","8":"NA"},{"1":"corn","2":"1.31615814","3":"3.44802568","4":"0.7583893","5":"NA","6":"0.72752460","7":"1.45725382","8":"5.30211110"},{"1":"cucumbers","2":"9.64080326","3":"7.42956940","4":"4.7752069","5":"3.30693000","6":"10.04645334","7":"3.10410496","8":"5.30652034"},{"1":"edamame","2":"4.68922674","3":"NA","4":"NA","5":"NA","6":"1.40213832","7":"NA","8":"NA"},{"1":"hot peppers","2":"NA","3":"NA","4":"1.2588380","5":"NA","6":"0.14109568","7":"NA","8":"0.06834322"},{"1":"jalapeño","2":"0.93916812","3":"1.29411194","4":"0.4343101","5":"0.22487124","6":"0.54895038","7":"NA","8":"0.09479866"},{"1":"kale","2":"1.49032312","3":"NA","4":"1.7659006","5":"NA","6":"0.28219136","7":"0.38139926","8":"0.61729360"},{"1":"kohlrabi","2":"NA","3":"NA","4":"NA","5":"0.42108242","6":"NA","7":"NA","8":"NA"},{"1":"lettuce","2":"1.10671924","3":"1.80117454","4":"2.4581513","5":"2.45153744","6":"0.91712192","7":"1.15963012","8":"1.14860702"},{"1":"onions","2":"1.34040896","3":"0.07275246","4":"0.5092672","5":"0.60186126","6":"0.70768302","7":"0.26014516","8":"NA"},{"1":"peas","2":"2.85277828","3":"0.93696350","4":"4.6341112","5":"3.39731942","6":"2.06793356","7":"2.05691046","8":"1.08026380"},{"1":"peppers","2":"1.38229674","3":"0.14991416","4":"0.2535313","5":"0.70988764","6":"1.44402610","7":"NA","8":"0.94798660"},{"1":"potatoes","2":"2.15611836","3":"NA","4":"0.9700328","5":"1.66669272","6":"NA","7":"NA","8":"4.57017726"},{"1":"pumpkins","2":"92.68883866","3":"NA","4":"NA","5":"NA","6":"31.85675900","7":"NA","8":"NA"},{"1":"radish","2":"0.23148510","3":"0.19400656","4":"0.1962112","5":"0.14770954","6":"0.09479866","7":"0.08157094","8":"NA"},{"1":"raspberries","2":"0.53351804","3":"0.57099658","4":"0.1300726","5":"0.28880522","6":"0.33510224","7":"NA","8":"NA"},{"1":"spinach","2":"0.26014516","3":"0.19621118","4":"0.1477095","5":"0.23368972","6":"0.49603950","7":"0.48722102","8":"0.21384814"},{"1":"squash","2":"56.22221924","3":"NA","4":"NA","5":"NA","6":"18.46810174","7":"NA","8":"NA"},{"1":"strawberries","2":"0.16975574","3":"0.48722102","4":"0.4784025","5":"0.08818480","6":"NA","7":"0.08157094","8":"NA"},{"1":"Swiss chard","2":"NA","3":"0.56438272","4":"1.0736499","5":"2.23107544","6":"0.07054784","7":"0.73634308","8":"0.71429688"},{"1":"tomatoes","2":"25.94396816","3":"58.67596130","4":"9.7091465","5":"34.51773534","6":"48.75076206","7":"63.68485794","8":"31.55913530"},{"1":"zucchini","2":"3.41495638","3":"18.72163304","4":"12.1959578","5":"5.74964896","6":"16.46851140","7":"12.23564100","8":"2.04147812"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  2. Summarize the `garden_harvest` data to find the total harvest in pound for each vegetable variety and then try adding the `plot` variable from the `plant_date_loc` table. This will not turn out perfectly. What is the problem? How might you fix it?


```r
#garden_harvest %>%
 # mutate(weight_lbs = weight*0.00220462)  %>%
 # group_by(vegetable)%>%
 # summarise(sum_veg = sum(weight_lbs))%>%
 # left_join(plant_date_loc,
            # by = "plot")
```

  3. I would like to understand how much money I "saved" by gardening, for each vegetable type. Describe how I could use the `garden_harvest` and `supply_cost` datasets, along with data from somewhere like [this](https://products.wholefoodsmarket.com/search?sort=relevance&store=10542) to answer this question. You can answer this in words, referencing various join functions. You don't need R code but could provide some if it's helpful.
  
You first have to group garden_harvest by vegetable and variety. That would allow you to join the two data sets because there would only be one row for each variety in each set. You could use left_join. You would also have to add data to supply_costs that had the grocery store cost by weight, but after you did that you could add two variables, one that calculated the cost for each pound of what you harvested (y = supply cost * weight which would then let you have a variable that calculated the difference between the harvest cost and grocery cost (money saved  = grocery cost - y). 

  4. Subset the data to tomatoes. Reorder the tomato varieties from smallest to largest first harvest date. Create a barplot of total harvest in pounds for each variety, in the new order.


```r
garden_harvest %>%
  filter(vegetable == "tomatoes") %>%
  mutate(weight_lbs = weight*0.00220462)  %>%
  group_by(variety) %>%
  summarize(min_date = min(date), sum_weight = sum(weight_lbs)) %>%
  mutate(variety2 = fct_reorder(variety,min_date))  %>%
  arrange(min_date)%>%
  ggplot(aes(y = variety2,
           x = sum_weight)) +
  geom_bar(stat = 'identity') 
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

![](03_exercises_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

  5. In the `garden_harvest` data, create two new variables: one that makes the varieties lowercase and another that finds the length of the variety name. Arrange the data by vegetable and length of variety name (smallest to largest), with one row for each vegetable variety. HINT: use `str_to_lower()`, `str_length()`, and `distinct()`.
  

```r
garden_harvest %>%
  mutate(variety = str_to_lower(variety)) %>%
  mutate(var_length = str_length(variety)) %>%
  distinct(vegetable,var_length, variety) %>%
  arrange(var_length)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["var_length"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"hot peppers","2":"thai","3":"4"},{"1":"tomatoes","2":"grape","3":"5"},{"1":"jalapeño","2":"giant","3":"5"},{"1":"peppers","2":"green","3":"5"},{"1":"pumpkins","2":"saved","3":"5"},{"1":"lettuce","2":"reseed","3":"6"},{"1":"beets","2":"leaves","3":"6"},{"1":"lettuce","2":"tatsoi","3":"6"},{"1":"carrots","2":"dragon","3":"6"},{"1":"carrots","2":"bolero","3":"6"},{"1":"potatoes","2":"purple","3":"6"},{"1":"potatoes","2":"yellow","3":"6"},{"1":"carrots","2":"greens","3":"6"},{"1":"potatoes","2":"russet","3":"6"},{"1":"hot peppers","2":"variety","3":"7"},{"1":"peppers","2":"variety","3":"7"},{"1":"broccoli","2":"yod fah","3":"7"},{"1":"edamame","2":"edamame","3":"7"},{"1":"spinach","2":"catalina","3":"8"},{"1":"cilantro","2":"cilantro","3":"8"},{"1":"cucumbers","2":"pickling","3":"8"},{"1":"tomatoes","2":"big beef","3":"8"},{"1":"tomatoes","2":"jet star","3":"8"},{"1":"squash","2":"delicata","3":"8"},{"1":"squash","2":"red kuri","3":"8"},{"1":"chives","2":"perrenial","3":"9"},{"1":"strawberries","2":"perrenial","3":"9"},{"1":"asparagus","2":"asparagus","3":"9"},{"1":"Swiss chard","2":"neon glow","3":"9"},{"1":"raspberries","2":"perrenial","3":"9"},{"1":"zucchini","2":"romanesco","3":"9"},{"1":"tomatoes","2":"bonny best","3":"10"},{"1":"carrots","2":"king midas","3":"10"},{"1":"tomatoes","2":"better boy","3":"10"},{"1":"tomatoes","2":"old german","3":"10"},{"1":"tomatoes","2":"brandywine","3":"10"},{"1":"tomatoes","2":"black krim","3":"10"},{"1":"tomatoes","2":"volunteers","3":"10"},{"1":"tomatoes","2":"amish paste","3":"11"},{"1":"beets","2":"sweet merlin","3":"12"},{"1":"squash","2":"blue (saved)","3":"12"},{"1":"basil","2":"isle of naxos","3":"13"},{"1":"onions","2":"delicious duo","3":"13"},{"1":"corn","2":"dorinny sweet","3":"13"},{"1":"corn","2":"golden bantam","3":"13"},{"1":"lettuce","2":"mustard greens","3":"14"},{"1":"beets","2":"gourmet golden","3":"14"},{"1":"lettuce","2":"lettuce mixture","3":"15"},{"1":"tomatoes","2":"cherokee purple","3":"15"},{"1":"tomatoes","2":"mortgage lifter","3":"15"},{"1":"radish","2":"garden party mix","3":"16"},{"1":"kale","2":"heirloom lacinto","3":"16"},{"1":"peas","2":"magnolia blossom","3":"16"},{"1":"peas","2":"super sugar snap","3":"16"},{"1":"beans","2":"bush bush slender","3":"17"},{"1":"broccoli","2":"main crop bravado","3":"17"},{"1":"kohlrabi","2":"crispy colors duo","3":"17"},{"1":"squash","2":"waltham butternut","3":"17"},{"1":"pumpkins","2":"new england sugar","3":"17"},{"1":"beans","2":"chinese red noodle","3":"18"},{"1":"beans","2":"classic slenderette","3":"19"},{"1":"onions","2":"long keeping rainbow","3":"20"},{"1":"lettuce","2":"farmer's market blend","3":"21"},{"1":"pumpkins","2":"cinderella's carraige","3":"21"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  6. In the `garden_harvest` data, find all distinct vegetable varieties that have "er" or "ar" in their name. HINT: `str_detect()` with an "or" statement (use the | for "or") and `distinct()`.


```r
garden_harvest %>%
  mutate(variety_r = str_detect(variety, "er|ar"))%>%
  distinct(variety, variety_r)%>%
  filter(variety_r == TRUE)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["variety"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety_r"],"name":[2],"type":["lgl"],"align":["right"]}],"data":[{"1":"Garden Party Mix","2":"TRUE"},{"1":"Farmer's Market Blend","2":"TRUE"},{"1":"Super Sugar Snap","2":"TRUE"},{"1":"perrenial","2":"TRUE"},{"1":"asparagus","2":"TRUE"},{"1":"mustard greens","2":"TRUE"},{"1":"Bush Bush Slender","2":"TRUE"},{"1":"Sweet Merlin","2":"TRUE"},{"1":"variety","2":"TRUE"},{"1":"Cherokee Purple","2":"TRUE"},{"1":"Better Boy","2":"TRUE"},{"1":"Mortgage Lifter","2":"TRUE"},{"1":"Old German","2":"TRUE"},{"1":"Jet Star","2":"TRUE"},{"1":"Bolero","2":"TRUE"},{"1":"volunteers","2":"TRUE"},{"1":"Classic Slenderette","2":"TRUE"},{"1":"Cinderella's Carraige","2":"TRUE"},{"1":"Waltham Butternut","2":"TRUE"},{"1":"New England Sugar","2":"TRUE"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


## Bicycle-Use Patterns

In this activity, you'll examine some factors that may influence the use of bicycles in a bike-renting program.  The data come from Washington, DC and cover the last quarter of 2014.

<center>

![A typical Capital Bikeshare station. This one is at Florida and California, next to Pleasant Pops.](https://www.macalester.edu/~dshuman1/data/112/bike_station.jpg){300px}


![One of the vans used to redistribute bicycles to different stations.](https://www.macalester.edu/~dshuman1/data/112/bike_van.jpg){300px}

</center>

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usualy, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`.


```r
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

```
## Parsed with column specification:
## cols(
##   name = col_character(),
##   lat = col_double(),
##   long = col_double(),
##   nbBikes = col_double(),
##   nbEmptyDocks = col_double()
## )
```

**NOTE:** The `Trips` data table is a random subset of 10,000 trips from the full quarterly data. Start with this small data table to develop your analysis commands. **When you have this working well, you should access the full data set of more than 600,000 events by removing `-Small` from the name of the `data_site`.**

### Temporal patterns

It's natural to expect that bikes are rented more at some times of day, some days of the week, some months of the year than others. The variable `sdate` gives the time (including the date) that the rental started. Make the following plots and interpret them:

  7. A density plot, which is a smoothed out histogram, of the events versus `sdate`. Use `geom_density()`.
  

```r
Trips %>%
  ggplot(aes(x = sdate)) +
  geom_density()
```

![](03_exercises_files/figure-html/unnamed-chunk-7-1.png)<!-- -->
  
  8. A density plot of the events versus time of day.  You can use `mutate()` with `lubridate`'s  `hour()` and `minute()` functions to extract the hour of the day and minute within the hour from `sdate`. Hint: A minute is 1/60 of an hour, so create a variable where 3:30 is 3.5 and 3:45 is 3.75.
  

```r
Trips %>%
  mutate(hour = hour(sdate), min = minute(sdate), 
         stime = hour + min/60) %>%
  ggplot(aes(x = stime)) +
  geom_density()
```

![](03_exercises_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
  
  9. A bar graph of the events versus day of the week. Put day on the y-axis.
  

```r
Trips %>%
  mutate(day = weekdays(sdate)) %>%
  ggplot(aes(y = day)) +
  geom_bar()
```

![](03_exercises_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
  
  10. Facet your graph from exercise 8. by day of the week. Is there a pattern?
  

```r
Trips %>%
  mutate(hour = hour(sdate), 
         min = minute(sdate), 
         stime = hour + min/60, 
         day = weekdays(sdate)) %>%
  ggplot(aes(x = stime)) +
  geom_density() +
  facet_wrap(vars(day))
```

![](03_exercises_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
  Saturdays and Sundays have more people checking out bikes during the day
  
The variable `client` describes whether the renter is a regular user (level `Registered`) or has not joined the bike-rental organization (`Causal`). The next set of exercises investigate whether these two different categories of users show different rental behavior and how `client` interacts with the patterns you found in the previous exercises. Repeat the graphic from Exercise \@ref(exr:exr-temp) (d) with the following changes:

  11. Change the graph from exercise 10 to set the `fill` aesthetic for `geom_density()` to the `client` variable. You should also set `alpha = .5` for transparency and `color=NA` to suppress the outline of the density function.
  

```r
Trips %>%
  mutate(hour = hour(sdate), 
         min = minute(sdate), 
         stime = hour + min/60, 
         day = weekdays(sdate)) %>%
  ggplot(aes(x = stime, fill = client, alpha = .5)) +
  geom_density(color = NA) +
  facet_wrap(vars(day))
```

![](03_exercises_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

  12. Change the previous graph by adding the argument `position = position_stack()` to `geom_density()`. In your opinion, is this better or worse in terms of telling a story? What are the advantages/disadvantages of each?
  

```r
Trips %>%
  mutate(hour = hour(sdate), 
         min = minute(sdate), 
         stime = hour + min/60, 
         day = weekdays(sdate)) %>%
  ggplot(aes(x = stime, fill = client, alpha = .5)) +
  geom_density(color = NA, position = position_stack()) +
  facet_wrap(vars(day))
```

![](03_exercises_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
  
  13. Add a new variable to the dataset called `weekend` which will be "weekend" if the day is Saturday or Sunday and  "weekday" otherwise (HINT: use the `ifelse()` function and the `wday()` function from `lubridate`). Then, update the graph from the previous problem by faceting on the new `weekend` variable. 
  

```r
Trips %>%
mutate(hour = hour(sdate), 
         min = minute(sdate), 
         stime = hour + min/60,
       weekday = wday(sdate, label = TRUE),
       weekend = ifelse(weekday == c("Sat","Sun"), "Weekend", "Weekday")
       ) %>%
  ggplot(aes(x = stime, fill = client, alpha = .5)) +
  geom_density(color = NA) +
  facet_wrap(vars(weekend))
```

```
## Warning: Problem with `mutate()` input `weekend`.
## i longer object length is not a multiple of shorter object length
## i Input `weekend` is `ifelse(weekday == c("Sat", "Sun"), "Weekend", "Weekday")`.
```

```
## Warning in `==.default`(weekday, c("Sat", "Sun")): longer object length is not a
## multiple of shorter object length
```

```
## Warning: Problem with `mutate()` input `weekend`.
## i longer object length is not a multiple of shorter object length
## i Input `weekend` is `ifelse(weekday == c("Sat", "Sun"), "Weekend", "Weekday")`.
```

```
## Warning in is.na(e1) | is.na(e2): longer object length is not a multiple of
## shorter object length
```

![](03_exercises_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
  
  14. Change the graph from the previous problem to facet on `client` and fill with `weekday`. What information does this graph tell you that the previous didn't? Is one graph better than the other?
  

```r
Trips %>%
mutate(hour = hour(sdate), 
         min = minute(sdate), 
         stime = hour + min/60,
       weekday = wday(sdate, label = TRUE),
       weekend = ifelse(weekday == c("Sat","Sun"), "Weekend", "Weekday")
       ) %>%
  ggplot(aes(x = stime, fill = weekday, alpha = .5)) +
  geom_density(color = NA) +
  facet_wrap(vars(client))
```

```
## Warning: Problem with `mutate()` input `weekend`.
## i longer object length is not a multiple of shorter object length
## i Input `weekend` is `ifelse(weekday == c("Sat", "Sun"), "Weekend", "Weekday")`.
```

```
## Warning in `==.default`(weekday, c("Sat", "Sun")): longer object length is not a
## multiple of shorter object length
```

```
## Warning: Problem with `mutate()` input `weekend`.
## i longer object length is not a multiple of shorter object length
## i Input `weekend` is `ifelse(weekday == c("Sat", "Sun"), "Weekend", "Weekday")`.
```

```
## Warning in is.na(e1) | is.na(e2): longer object length is not a multiple of
## shorter object length
```

![](03_exercises_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
  
### Spatial patterns

  15. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. We will improve this plot next week when we learn about maps!
  

```r
Stations %>% 
  left_join(Trips, 
         by= c("name" = "sstation")) %>%
  group_by(name,lat,long) %>%
  summarize(count = n()) %>%
  ggplot(aes(x = lat, y = long, color = count)) +
  geom_point()
```

```
## `summarise()` regrouping output by 'name', 'lat' (override with `.groups` argument)
```

![](03_exercises_files/figure-html/unnamed-chunk-15-1.png)<!-- -->
  
  16. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? (Again, we'll improve this next week when we learn about maps).
  

```r
Stations %>% 
  left_join(Trips, 
         by= c("name" = "sstation")) %>%
  group_by(name,lat,long) %>%
  summarize(percent_casual = mean(client == "Casual")) %>%
  ggplot(aes(x = lat, y = long, size = percent_casual)) +
  geom_point()
```

```
## `summarise()` regrouping output by 'name', 'lat' (override with `.groups` argument)
```

```
## Warning: Removed 12 rows containing missing values (geom_point).
```

![](03_exercises_files/figure-html/unnamed-chunk-16-1.png)<!-- -->
  
### Spatiotemporal patterns

  17. Make a table with the ten station-date combinations (e.g., 14th & V St., 2014-10-14) with the highest number of departures, sorted from most departures to fewest. Save this to a new dataset and print out the dataset. Hint: `as_date(sdate)` converts `sdate` from date-time format to date format. 
  

```r
top_stations <- Stations %>% 
  left_join(Trips, 
         by= c("name" = "sstation")) %>%
  mutate(sdate = as_date(sdate)) %>% 
  group_by(sdate,name) %>%
  summarize(count = n()) %>%
  arrange(-count) %>%
  head(n = 10) 
```

```
## `summarise()` regrouping output by 'sdate' (override with `.groups` argument)
```
  
  18. Use a join operation to make a table with only those trips whose departures match those top ten station-date combinations from the previous part.
  

  
  19. Build on the code from the previous problem (ie. copy that code below and then %>% into the next step.) and group the trips by client type and day of the week (use the name, not the number). Find the proportion of trips by day within each client type (ie. the proportions for all 7 days within each client type add up to 1). Display your results so day of week is a column and there is a column for each client type. Interpret your results.

**DID YOU REMEMBER TO GO BACK AND CHANGE THIS SET OF EXERCISES TO THE LARGER DATASET? IF NOT, DO THAT NOW.**

## GitHub link

  20. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 03_exercises.Rmd, provide a link to the 03_exercises.md file, which is the one that will be most readable on GitHub.

## Challenge problem! 

This problem uses the data from the Tidy Tuesday competition this week, `kids`. If you need to refresh your memory on the data, read about it [here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-15/readme.md). 

  21. In this exercise, you are going to try to replicate the graph below, created by Georgios Karamanis. I'm sure you can find the exact code on GitHub somewhere, but **DON'T DO THAT!** You will only be graded for putting an effort into this problem. So, give it a try and see how far you can get without doing too much googling. HINT: use `facet_geo()`. The graphic won't load below since it came from a location on my computer. So, you'll have to reference the original html on the moodle page to see it.
  

**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
