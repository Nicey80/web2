---
title: Tidy Tuesday NFL data
author: ~
date: '2020-02-04'
slug: tidy-tuesday-nfl-data
categories: [dataviz, r, nfl]
tags: [tidyverse, dataviz]
lastmod: '2020-02-10T21:03:01Z'
layout: post
type: post
highlight: no
image: "/img/Superbowl.png"
---

**Did the best team win the Superbowl?**

Armed with the Tidy Tuesday data from NFL seasons back to 2000 I set out to answer the question above. With a rudimentary metric assessing the team strength based on margin of victory and strength of schedule. I created a quick data viz showing that less than third of the time the team ranked top by this metric has won the superbowl

### The viz

Using the ggimage package to bring a bit of Lombardi glitz to the data viz and a little bit of data wrangling to rank and join the superbowl winners with the top ranked team data gave us this output

{{<figure src="/img/Superbowl.png">}}

*The code to produce the plot is at the bottom of this post*


### Interesting Notes

Perhaps unsurprisingly the Patriots 18-1 season ranks as the strongest team in the past 20 years and the upset win by the Giants the largest difference between Superbowl winner and stringest team in this analysis.

Seattle's Legion of Boom Superbowl winners rank as the highest ranked Superbowl winner form teh past 20 years.

### Future Improvements

There were plenty more ways I could have analysed this data set. Maybe looked at average margin of victory over time to understand if the gap between top and bottom of the NFL is getting wider. From a purely coding perspective the opportunity to look more at ggrepel or other labelling is for the near future given to reduce the overlap in the labeling. Definitely a dataset to return to!

### The code

```r
## NFL Attendance

library(tidyverse)
library(ggimage)
library(ggrepel)

attendance <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-04/attendance.csv')
standings <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-04/standings.csv')
games <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-04/games.csv')


s1 <- standings %>% #superbowl winners
    group_by(year, team_name, sb_winner) %>% 
    summarise(srs=mean(simple_rating)) %>% 
    as_tibble() %>% 
    filter(sb_winner=='Won Superbowl')

s2 <- standings %>% #top ranked team
    group_by(year, team_name, sb_winner) %>% 
    summarise(srs=mean(simple_rating)) %>% 
    as_tibble() %>% 
    group_by(year) %>% 
    filter(srs==max(srs),sb_winner!='Won Superbowl' ) #filter within group
    
sb <- s2 %>% 
    full_join(s1, by="year") %>% 
    #replace_na(list(srs.x=0)) %>% 
    mutate(top_score=if_else(srs.y>=srs.x,TRUE ,FALSE)) %>% 
    replace_na(list(top_score=TRUE)) 

image <- 'lom.jpg'

sb %>% 
    ggplot(aes(x=year))+
    #geom_point(aes(y=srs.y), size=3)+
    geom_image(aes(y=srs.y), image=image)+ #put the lomardi trophy at the rank of the Superbowl winner
    geom_label(aes(y=srs.y-1, label=team_name.y, fill=top_score), size=2.5)+ # label the winner
    #geom_text(aes(y=srs.y-1, label=team_name.y, fill=top_score), angle=90)+
    geom_point(aes(y=srs.x), size=2, colour='red')+ # add a red dot for the highest ranked team
    #geom_label(aes(y=srs.x+1, label=team_name.x))+
    ggtitle("Did the best team win the Superbowl?", 
            subtitle="Best team by Simple rating system (MoV+SoS)")+
    ylab("SRS Rating")+
    xlab("Year")+
    theme_classic()+
    scale_fill_manual(values=c("#999999", "#E69F00"), 
                        name="Winner as\nhighest ranked",
                        breaks=c(TRUE,FALSE),
                        labels=c("Highest Ranked", "Lower Ranked"))

```
