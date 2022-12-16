---
title: Tidy Tuesday Income Data and plotting themes
author: ~
date: '2021-02-22'
slug: tt-income-data-and-plotting-themes
categories: [dataviz]
tags: [tidyverse, dataviz]
lastmod: '2021-02-22T22:46:39Z'
layout: post
type: post
highlight: no
image: "/img/income_equality.png"
---

A while since I've completed a tidy tuesday challenge here so to get back in the swing of things I've chosen a fairly simple plot of income equality by race and focused on altering the default these to try to create a more striking visual effect for the data.

{{<figure src="/img/Spotify.png">}}

## Code

The code is pretty straight forward to create the chart. The key effect I was going for here was to alter the theme to make a more striking visual.

```r
tuesdata <- tidytuesdayR::tt_load('2021-02-09')
tuesdata

tt <- tuesdata$income_distribution
tt

library(tidyverse)
library(scales)
library(ggrepel)

str(tt)
summary(tt)
unique(tt$race)

tt %>% ggplot(aes(x=year, y=income_median)) + geom_line(aes(group=race, colour=race))


tt2 <- tt %>% filter(year==2019) %>% group_by(year,race) %>% 
    summarise(med=mean(income_median)) %>% as_tibble()

tt %>% ggplot(aes(x=year, y=income_median)) + 
    geom_line(data=tt %>% filter(race!="All Races"),aes(group=race, colour=race))+
    geom_line(data=tt %>% filter(race=="All Races"),colour="yellow",size=1, linetype=3)+
    geom_label_repel(data=tt2,aes(x=2030, y=med,label=race), nudge_x = 0)+
    ggtitle(label="Racial Income Inequaltiy in USA", subtitle = "Median income since the financial crash of 2008 has grown much slower in the Black population")+
    theme(legend.position = 'none', 
          axis.title = element_blank(), 
          text = element_text(family = "Tahoma", colour = "green"), 
          axis.text = element_text(colour = "green"), 
          plot.background = element_rect(fill="black"),
          panel.background=element_rect(fill="black"))+
    scale_y_continuous(labels=label_dollar(scale = 0.001, suffix = "k"))

```

### Altering the theme

Key changes to the default theme
1. Removed the legend and replaced with offset labels using the geom_label_repel geom
2. Axis titles removed as the scales are fairly self explanatory axis title=element_blank()
3. Text colour and font changed with text and axis text commands
4. Plot and panel backgrounds changed to black to try to make the sries more visible
