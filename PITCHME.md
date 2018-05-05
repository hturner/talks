---

# @color[#4286f4]@size[3em](Modelling Item Worth Based on Rankings)

@color[#ffffff](Heather Turner)

@color[#ffffff](May 15 2018)




---

## Rankings

Rankings arise in a number of settings

 - Finishing order in a set of races
 - Consumer preferences in market research
 
What is the worth of each item?

---

## Luce's Axiom

> Probability of choosing item A over item B unaffected by other items 

Suppose we have a set of $J$ items

$$S = \{i_1, i_2, \ldots, i_J\}$$

Then under Luce's axiom

`$$P(j | S) = \frac{\alpha_{j}}{\sum_{i \in S} \alpha_i}$$`

where `$\alpha_i$` represents the **worth** of item $i$.

---

## Plackett-Luce Model

Consider a ranking of $J$ items as a sequence of choices.

The *Plackett-Luce* model is then

`$$P(i_1 \succ \ldots \succ i_J) = \prod_{j=1}^J\frac{\alpha_{i_j}}{\sum_{i \in A_j} \alpha_i}$$`

where $A_j$ is the set of alternatives in choice $j$.

**PlackettLuce** can be used to fit this model.

---
@title[Neflix data]

## Example 1: Netflix Data

For the Netflix Prize, Netflix released several data sets 
comprising movie rankings.

Using `read.soc` from **PlackettLuce**, we read in a set of 
rankings for 4 movies


```r
library(PlackettLuce)
preflib <- "http://www.preflib.org/data/election/"
netflix <- read.soc(file.path(preflib,
                              "netflix/ED-00004-00000138.soc"))
head(netflix, 2)
```

```
#    n Rank 1 Rank 2 Rank 3 Rank 4
# 1 68      2      1      4      3
# 2 53      1      2      4      3
```

+++

### Convert to Rankings

**PlackettLuce** requires the rankings to give the rank per item 
vs. item per rank.

We convert the rankings using `as.rankings`, which creates a 
special data structure


```r
R <- as.rankings(netflix[,-1], input = "ordering")
colnames(R) <- attr(netflix, "item")
print(R[1:3], width = 60)
```

```
#                                                              1 
# "Beverly Hills Cop > Mean Girls > Mission: Impossible II  ..." 
#                                                              2 
# "Mean Girls > Beverly Hills Cop > Mission: Impossible II  ..." 
#                                                              3 
# "Beverly Hills Cop > Mean Girls > The Mummy Returns > Mis ..."
```

---

### Fit Plackett-Luce Model

Now `PlackettLuce` can be used to fit the model, with frequencies 
as weights


```r
mod <- PlackettLuce(R, weights = netflix$n)
coef(mod, log = FALSE)
```

```
#             Mean Girls      Beverly Hills Cop      The Mummy Returns 
#              0.2306285              0.4510655              0.1684719 
# Mission: Impossible II 
#              0.1498342
```

These coefficients are the *worth* parameters and represent the 
probability that each movie is ranked first.

---

### Inference

For inference it is better to work on the log scale. We must set one item as the reference (log-worth = 0).

We can use `qvcalc` to compute comparison intervals for all items


```r
qv <- qvcalc(mod)
plot(qv, ylab = "Worth (log)", main = NULL)
```

```r
makeLink("qvcalc", title = "Worth of movies in Netflix data",
         alt = "Plot of estimated log-worth for each movie, with 95% comparison interval. Beverly Hills Cop is significantly more popular than the other three movies, Mean Girls is significant more popular than The Mummy Returns or Mission: Impossible II, but there was no significant difference in users’ preference for these last two movies.", width = 300)
```

```
# <img src="https://raw.githubusercontent.com/hturner/test/master/qvcalc-1.svg?sanitize=true" title="Worth of movies in Netflix data" alt="Plot of estimated log-worth for each movie, with 95% comparison interval. Beverly Hills Cop is significantly more popular than the other three movies, Mean Girls is significant more popular than The Mummy Returns or Mission: Impossible II, but there was no significant difference in users’ preference for these last two movies." width="300px" />
```

---

## Ranking properties

The Netflix rankings are an example of *strict*, *complete*
rankings.

In other applications we might have
 * tied ranks
 * incomplete rankings
     - *sub-rankings*: only some items ranked each time
     - *top-n*: only the top $n$ items are ranked
     
**PlackettLuce** implements a generalized model which handles 
ties and sub-rankings.

---

## Generalized Model

A ranking is now an ordering of sets 

$$C_1 \succ C_2 \succ \ldots \succ C_J$$

and the generalized model with ties up to order $D$ is

`$$
\prod_{j = 1}^J \frac{f(C_j)}{
\sum_{k = 1}^{\min(D_j, D)} \sum_{S \in {A_j \choose k}} f(S)}
$$`

where

`$$f(S) = \delta_{|S|} \left(\prod_{i \in S} \alpha_i \right)^\frac{1}{|S|}$$`

---

## Ranking Networks

In some cases, the underlying network of wins and losses means 
the worth cannot be estimated by maximum likelihood.

@div[left-50]


```
# <img src="https://raw.githubusercontent.com/hturner/test/master/always loses-1.svg?sanitize=true" title="Network in which one item always loses" alt="Network in which one item always loses" width="300px" />
```

<img src="../talks-svg/eRum2018/always-loses-1.svg" title="plot of chunk always-loses" alt="plot of chunk always-loses" width="300px" />

@divend

@div[right-50]


```
# <img src="https://raw.githubusercontent.com/hturner/test/master/disconnect-1.svg?sanitize=true" title="Disconnected Network" alt="Network with two separate groups of items, that are only observed to win or lose against other items in theor group" width="300px" />
```

<img src="../talks-svg/eRum2018/disconnected-1.svg" title="plot of chunk disconnected" alt="plot of chunk disconnected" width="300px" />



@divend

---

## Pseudo-rankings

**PlackettLuce** connects the network by adding `npseudo` 
*pseudo-rankings* with a ghost item.

@div[left-50]

@ul
- The MLE is always estimable
- Can be viewed as a Bayesian prior
- Default `nspeudo = 0.5`
@ulend

@divend

@div[right-50]


```
# <img src="https://raw.githubusercontent.com/hturner/test/master/pseudo-rankings-1.svg?sanitize=true" title="Network with pseudo-rankings" alt="Network with pseudo-rankings, in which each item wins and loses against ghost item" width="300px" />
```



@divend

