---
title: Tidy Tuesday Spotify data
author: ~
date: '2020-01-25'
slug: tt-spotify
categories: [dataviz]
tags: [tidyverse, dataviz]
lastmod: '2020-01-25T21:22:27Z'
layout: post
type: post
highlight: no
image: "/img/Spotify.png"
---

This weeks Tidy Tuesday challenge centred around Spotify data, over 32,000 songs from multiple genres from the 60s to today. Given this was my first Tidy Tuesday for a while I decided to go for something relatively quick and simple to try to get back into the habit and look at the popularity of different types of music over time.

{{<figure src="/img/Spotify.png">}}

## Code

Having read in the data the next step was to make a quick modification to allow me to work with dates as opposed to character values. Then the plotting fun...

```r
library(tidyverse)
library(lubridate)

spotify_songs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-21/spotify_songs.csv')

#create date feature by conversion from character type
spotify_songs <- spotify_songs %>% 
     mutate(track_date=as_date(track_album_release_date),
         rel_year=year(track_date))


spotify_songs %>% 
    ggplot(aes(x=rel_year, y=track_popularity, group=playlist_genre, colour=playlist_genre))+
    geom_point(alpha=0.2)+
    geom_smooth(colour='black')+ # default smoother to give a sence of relative trend
    facet_wrap(playlist_genre~.)+
    theme_bw()+
    theme(legend.position = 'none', #hide legend as adds no value above facet headers
          axis.title = element_blank())+ # axis titles add no further value
    ggtitle('Track popularity (0-100) by genre over time', 
            subtitle = "Popularity of all music genres has risen since 2010 except Rock")

```

### Falling out of love with Rock

Having charted the data the big at a glance take away is that since 2010 all types of music have grown in popularity by this spotify metric with the exception of Rock music. It just doesn't seem to have the highly rated tracks of the other music genres.

Here's the finished submission...
{{<tweet 1220449477888221184>}}
