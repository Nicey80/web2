---
title: Tidy Tuesday Vacination
author: ~
date: '2020-02-27'
slug: tidy-tuesday-vacination
categories: [ggplot, maps]
tags: [ggplot, maps]
lastmod: '2020-02-27T20:41:42Z'
layout: post
type: post
highlight: no
image: "/img/MMR_Vac_Cali.png"
---

This weeks Tidy Tuesday gave an opportunity to explore some mapping and a good excuse to look at the patchwork framework to multiplotting. I chose to explore the data from California as a good exercise in creating a chloropleth map using ggplot and the sf package. The latter was extremely useful having spent a significant amnount of time trying to join the data together but by converting the sp data to an sf data frame the flatter structure made joining the data and the subsequent plotting much easier.

This key step was as simple as *st_as_sf*

{{<figure src="/img/MMR_Vac_Cali.png">}}

## Code

Plotting code as follows
```r
#plot of state
p1 <- me2 %>%
    filter(vac_pct>0, !is.na(vac_pct)) %>% 
    ggplot()+
    geom_sf(aes(fill=vac_pct))+
    geom_point(data=meas_cal, aes(lng,lat), colour='red')+
    scale_fill_continuous() +
    #ggtitle("MMR Vacination Rates in California Counties", subtitle = "Plus facilites <50% coverage") +
    theme_map()+
    labs(var_pct="Vacination \npercent")+
    theme(legend.title = element_text(size=8), legend.text = element_text(size=8))
    
p2 <- ggmap(ph_basemap)+
    geom_sf(data=me_mend, fill="darkblue", alpha=0.5, inherit.aes=FALSE)+
    geom_point(data=meas_mendo, aes(lng,lat, size=non_vac_rate))+
    theme(axis.ticks = element_blank(), axis.text = element_blank(),
          axis.title = element_blank(), legend.title = element_text(size=8),
          legend.text = element_text(size=8))+
    #ggtitle("Mendocino County", subtitle = "Non Vacination Hotspots", caption="Mendocino County Hotspots")+
    labs(size="Non Vacination \nRate", caption = "Mendocino County Hotspots")
p2

(p1 |(p2/plot_spacer())) + plot_layout(guides = 'collect', widths = c(2,1)) + plot_annotation(
    title = 'MMR Vacination Rates in California Counties',
    subtitle = 'Highlighting facilities with <50% vacination rates',
    caption = 'Pullout: Mendocino County'
)

```
Find the full code on Github here:
https://github.com/Nicey80/tidytuesday/blob/master/2020-02-25/2020-02-25.R




