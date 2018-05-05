---
@title[Title slide]

# @color[#4286f4](Modelling Item Worth Based on Rankings)

@color[#ffffff](Heather Turner)

@color[#ffffff](May 15 2018)



---
@title[Code slide]

```lang=r
for (i in 1:3){
    lm(y ~ x, data = mydat, x = TRUE)
}
```

---
@title[R output]

```lang=r
Analysis of Variance Table

Response: weight
          Df Sum Sq Mean Sq F value Pr(>F)
group      1 0.6882 0.68820  1.4191  0.249
Residuals 18 8.7292 0.48496               
```
