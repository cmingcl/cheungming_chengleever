---
type: brief
engagement: perfect-competition
capability: marginal-analysis
date: 2026-10-02
status: committed          # committed | superseded
hypothesis: "Carrots and mesclun max out; tomatoes fill the last 14 beds"
---

# <Engagement> — engagement brief

## The problem
I am a farmer on a 1.5-acre market garden with 64 beds, and I have to decide
how many beds of tomatoes, carrots, and mesclun to plant this season. I make
this decision once: the plan is committed for the whole 36-week season and
cannot be changed in July. If I decide badly, I either leave profit on the
table or plant beds that cost more to tend than they earn, possibly losing
money on the season.

The market sets crop prices, so I am a price taker: the only thing I choose is
the number of beds of each crop. Fixed for me are the prices (revenue per
bed), the $20,000 overhead, the 36-week season, fertilizer per bed, hours per
bed, the diminishing-returns rates, and the wage rates.

Two things limit my choice: bed space and labor. Each crop has its own cap
(20 tomatoes, 20 carrots, 30 mesclun), and together the caps add up to 70,
while I only have 64 beds, so something has to give. For labor, I have my own
720 hours and can hire up to four temporary workers at $17.36/hr, 1,440 hours
each. The core difficulty is that the more beds of one crop I plant, the
harder every bed of that crop becomes to tend, so labor costs climb as I
plant more. Each crop has different earnings, labor requirements, and
diminishing-returns rates.

My goal is to maximize profit for the season.

## What I am assuming
- Prices hold for the whole season no matter how much I grow (I am too small
  to move the market).
- Revenue per bed is reliable: no crop failures, weather losses, or unsold
  produce.
- Labor follows the given formula: each added bed of a crop raises the labor
  needed for every bed of that crop by its diminishing-returns rate.
- I use my own 720 hours before hiring temporary workers.
- Temporary workers can be hired for partial hours rather than as whole
  workers.
- The $20,000 overhead is paid no matter what I plant, so it affects total
  profit but not the best mix.

Assumptions I would most want to test: whether temporary workers must be
hired whole (paying for a full 1,440 hours could change how many beds are
worth planting), and whether prices really stay flat if a crop has a bad or
very good year.

## Hypothesis
I expect the optimal mix to be 14 tomatoes, 20 carrots, and 30 mesclun
(all 64 beds), because carrots and mesclun get more expensive so slowly that
they stay profitable all the way to their caps, and tomatoes take the
remaining 14 beds. Tomatoes are the most labor-intensive crop and get more
expensive fastest (10% per bed), so they are the crop that gives up beds when
the caps add up to more than 64. The mechanism that decides it is the
64-bed land limit: tomatoes stay profitable enough to fill every bed left
over, but the bed limit stops them well before their cap of 20.

## How I would know I was wrong
My hypothesis is wrong if the model leaves any beds empty, or plants fewer
than 14 tomatoes. That would mean tomatoes reach the point where one more bed
costs more in labor than it earns (P = MC) before the land runs out, so rising
cost, not land, is what limits them. 
