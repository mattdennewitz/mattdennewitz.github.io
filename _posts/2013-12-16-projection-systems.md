---
title: How I'd Improve Projection Systems
layout: post
summary: Two suggestions for projection system authors.
date:   2013-12-16 12:30:00
---

I'm a big fan of baseball projections. They're a welcome sight around the
in the doldrums between seasons, and they're fun to debate.

In prototyping a unified dashboard for mixing and remixing projections (more on that later),
I've spent ample time with most major distributions.
There are two points, I feel, that would make working with this data
less troublesome.

1. **Include a license.**

   Including a license is an essential step for anyone releasing data into the wild.
   Without a license, it's terminally unclear if I can build around your data;
   knowing what rights and attribution you retain in your
   distribution would be *great*.
   At the very least, include note about how I may use your data.
   And, explicitly stating &#8220;no license&#8221; tells me more than not
   having a license ever could.

   Please consider a Creative Commons license, like 
   <a href="http://creativecommons.org/licenses/by/4.0" target="_blank">CC BY</a> or <a href="http://creativecommons.org/licenses/by-nd/4.0" target="_blank">CC BY-ND</a>.

2. **Strive to include at least one globally-available player ID.**

   Not all player names are unique. If you're projecting major and minor leaguers,
   you're going to have name duplication.

   The Chadwick Bureau
   <a target="_blank" href="http://chadwick-bureau.com/the-register/">has done the hard work</a>
   to build a player Rosetta Stone incorporating Retrosheet, MLBAM, FanGraphs, B.Pro, NPB, and more.
   It's not perfect, but it should prove helpful in filling gaps. It has for me.

   I realize this potentially introduces more complexity than authors
   can afford or can afford to care about. Failing that, please include as many peripherals
   (team, age, bats, throws) as possible. Matching using only these data points is not ideal,
   but their availability goes a long way toward disambiguation.
   These peripherals are also what could be used in the case where
   a player does not have a database from any major source.

#### Bonus request

**Split player names into first and last names.**

First and last names are always useful, especially when disambiguating without an ID.
Please consider splitting names if possible.
