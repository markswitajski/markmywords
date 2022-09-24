---
title: Billionaire Growth Re-creation with ggplot
author: ''
date: '2022-09-24'
slug: billionaire-growth-re-creation-with-ggplot
categories: []
tags: []
description: ''
code_folding: show
---

I don't get to use `ggplot` very often at my current day job and when I stumbled upon this visualization, I thought it might be fun to challenge myself to recreate it in R:

![original image that I will try to recreate in R](images/original_musk_growth_chart.png)

Staggering wealth growth aside, this image stood out to me for the double bars, slightly offset from each other, to show the year-over-year change. I've also never worked with the `geom_image` layer and thought the head shots would make for a fun learning opportunity.

Naturally, I decided to start with creating the data. The values themselves were straightforward, but the head shots are where I got a bit lazy. I decided to screenshot some head shots from Google image searches and use a background remover to get the images used in my visualization. If I cared enough, this is one area I could improve to make this graph cleaner.





```r
person <- c(rep("Elon\nMusk", 2),
            rep("Jeff\nBezos", 2),
            rep("Bill\nGates", 2),
            rep("Bernard\nArnault", 2),
            rep("Mark\nZuckerberg", 2),
            rep("Zhong\nShanshan", 2),
            rep("Warren\nBuffet", 2),
            rep("Larry\nPage", 2),
            rep("Sergey\nBrin", 2),
            rep("Larry\nEllison", 2))

year <- rep(c(2020, 2021) , 10)

value <- c(29, 188,
           117, 184,
           91, 132,
           106, 114,
           81, 100,
           0, 93,
           89, 87,
           67, 82,
           65, 79,
           60, 79)

order <- c(1:20)

image <- c(rep("images/elon.png",2),
           rep("images/bezos.png",2),
           rep("images/gates.png",2),
           rep("images/bernard.png",2),
           rep("images/zuck.png",2),
           rep("images/zhong.png",2),
           rep("images/warren.png",2),
           rep("images/page.png",2),
           rep("images/brin.png",2),
           rep("images/ellison.png",2))

df <- data.frame(person, year, value, order, image = image) 
```

With our data ready, I got to the fun part! The `annotate` layer was also new to me. The properties of this geom are not mapped to any variables in our data, but rather are passed in as vectors. This allows us to fine-tune the annotation's position in the graph but was a bit of "guess and check" work for a beginner like myself. Overall I was very pleased with how the graph turned out! It isn't pixel-perfect, but I proved to myself that it could be if necessary. 


```r
font_add_google("Roboto", "roboto")
font_add_google("Poppins", "poppins")
showtext_auto()

plot <- ggplot(df, aes(y=value, x= reorder(person, order))) + 
  geom_bar(data = df[which(df$year == 2021),], stat="identity", fill = "#E0E32A", width = 0.5) +
  geom_bar(data = df[which(df$year == 2020),], stat="identity", fill = "black", width = 0.5, position = position_nudge(x = -0.1)) +
  geom_image(data = df[which(df$year == 2021),], aes(image = image), size = 0.1, position = position_nudge(x = -0.2)) +
  geom_text(data = df[which(df$year == 2020 & df$value != 0 & df$value != 89),], 
            aes(label = value, angle = 90, vjust = -3, hjust = 1), size = 3) +
  geom_text(data = df[which(df$year == 2021),], 
            aes(label = value, angle = 90, vjust = 3.2, hjust = 1), size = 3, color = "#E0E32A") +
  geom_text(data = df[which(df$value == 89),], 
            aes(label = value, angle = 90, vjust = -3.2, hjust = 3), size = 3) +
  labs(title = 'ELON MUSK IS NOW THE WORLD\'S RICHEST PERSON',
       subtitle = "\nThe entrepreneur has rocketed up the ranking, surpassing all the world's top billionaires",
       x = "",
       y = "") +
  scale_fill_manual(values = c("black", "#E0E32A")) +
  scale_y_continuous(limits = c(0, 200), labels = scales::dollar_format(prefix = "$", suffix = "B")) +
  annotate(geom = "text", y = 175, x = 3, 
           label = "Elon Musk's net worth rose by 548%\nin a single year, buoyed by a surging Tesla stock.",
           hjust = "left", size = 3, color = "#004223", family = "poppins") +
  annotate(geom = "curve", x = 2.8, y = 175, xend = 1, yend = 175, curvature = -0.7, size = 0.25, color = "#004223") +
  theme_classic() +
  theme(
    axis.text.y = element_text(family = "poppins"),
    axis.text.x = element_text(family = "roboto"),
    plot.subtitle = element_text(family = "poppins", size = 8),
    plot.title = element_textbox_simple(size=17, colour = "#004223", face = "bold", family = "roboto"),
    plot.background = element_rect(fill = "#F7F9F5"),
    panel.background = element_rect(fill = "#F7F9F5",
                                    colour = "#F7F9F5")
  )
plot
```

<img src="{{< blogdown/postref >}}index_files/figure-html/plot-1.png" width="672" />

And when I say "guess and check" work, that was also true for the size of the head shots. Since I had to see this monstrosity, you do as well!

![nightmare fuel](images/nightmare.png)
