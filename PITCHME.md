---
<center>
# @color[#4286f4](Modelling Item Worth Based on Rankings)

@color[#ffffff](Heather Turner)

@color[#ffffff](May 15 2018)
</center>



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
##    n Rank 1 Rank 2 Rank 3 Rank 4
## 1 68      2      1      4      3
## 2 53      1      2      4      3
```

---

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

```r
##                                                              1 
## "Beverly Hills Cop > Mean Girls > Mission: Impossible II  ..." 
##                                                              2 
## "Mean Girls > Beverly Hills Cop > Mission: Impossible II  ..." 
##                                                              3 
## "Beverly Hills Cop > Mean Girls > The Mummy Returns > Mis ..."
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
##             Mean Girls      Beverly Hills Cop      The Mummy Returns 
##              0.2306285              0.4510655              0.1684719 
## Mission: Impossible II 
##              0.1498342
```

These coefficients are the *worth* parameters and represent the 
probability that each movie is ranked first.
