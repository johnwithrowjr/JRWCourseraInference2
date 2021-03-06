Statistical Inference Course Project Response 2
================================

**Principal Author**  
Dr. John R. Withrow Jr.  
Cherokee Nation Technologies  
Contractor for USDA-FS FHTET  
NRRC Building A Ste 331  
2150 Centre Avenue  
Fort Collins, CO 80526  
johnwithrow@fs.fed.us  



### Executive Summary
The dataset ToothGrowth is analyzed, in particular to assess associations between odontoblast growth in guinea pigs and Vitamin C dosage and delivery method.  A positive association between odontoblast growth and Vitamin C dosage is statistically detected.

***

### Analytical Context
#### Scientific Discussion
This document described the analysis of an existing dataset.  All analyses were done in compliance with industry-standard techniques as described in the present course in Statistical Inference and reported using state-of-the-art techniques as described in a preceeding course in Reproducible Research.

Analysis is performed on the "ToothGrowth" dataset in R, which according to available documentation, is based upon the work of Crampton (1947) who investigated the effect of Vitamin C on tooth growth in guinea pigs.  The response variable is the length of odontoblasts (cells responsible for tooth growth) in 60 guinea pigs. Each animal received one of three dose levels of vitamin C (0.5, 1, and 2 mg/day) by one of two delivery methods, (orange juice or ascorbic acid (a form of vitamin C and coded as VC).

#### Software Environment 

```r
sessionInfo()
```

```
## R version 3.2.1 (2015-06-18)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 7 x64 (build 7601) Service Pack 1
## 
## locale:
## [1] LC_COLLATE=English_United States.1252 
## [2] LC_CTYPE=English_United States.1252   
## [3] LC_MONETARY=English_United States.1252
## [4] LC_NUMERIC=C                          
## [5] LC_TIME=English_United States.1252    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## loaded via a namespace (and not attached):
##  [1] magrittr_1.5       rsconnect_0.4.1.11 formatR_1.4       
##  [4] markdown_0.7.7     htmltools_0.2.6    tools_3.2.1       
##  [7] stringi_1.1.1      rmarkdown_0.9.6    knitr_1.13        
## [10] stringr_1.0.0      digest_0.6.9       evaluate_0.9
```
Random Number Seed: 19680828

```r
set.seed(19680828)
```

***

### Analytical Process
#### Data Acquisition

For this analysis we used the "ToothGrowth" dataset available via the R datasets package:


```r
data("ToothGrowth")
```

#### Data Description

Initial investigations of the ToothGrowth dataset are as follows:


```r
str(ToothGrowth)
```

```
## 'data.frame':	60 obs. of  3 variables:
##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...
```

```r
head(ToothGrowth)
```

```
##    len supp dose
## 1  4.2   VC  0.5
## 2 11.5   VC  0.5
## 3  7.3   VC  0.5
## 4  5.8   VC  0.5
## 5  6.4   VC  0.5
## 6 10.0   VC  0.5
```

It shows a single R data frame of sixty observations of three variables.  

#### Exploratory data analysis

Tables and histograms of these three variables are as follows:


```r
table(ToothGrowth$supp)
```

```
## 
## OJ VC 
## 30 30
```

```r
par(mfrow=c(1,2))
hist(ToothGrowth$dose,xlab="Dose (mm/day)",main="Histogram of Vitamin C Dosage",col="blue")
hist(ToothGrowth$len,xlab="Length",main="Histogram of Odontoblast Growth",col="blue")
```

![plot of chunk histograms](figure/histograms-1.png)

```r
par(mfrow=c(1,1))
```

The table of supp values shows an even split between guinea pigs who received Vitamin C by way of orange juice (OJ) and ascorbic acid (VC).  The histogram of Vitamin C dosages shows an even split between three different dosage levels, which according to the documentation are in milligrams per day.  Units are not provided for odontoblast lengths, shown in the second histogram with values ranging from zero to 35.

To explore relationships between odontoblast lengths and Vitamin C dosage, three separate histograms are shown with statistics:


```r
par(mfrow=c(1,3))
hist(ToothGrowth$len[ToothGrowth$dose == 0.5],xlim=c(0,35),xlab="Length",main="Dosage of 0.5mm/day",col="blue")
hist(ToothGrowth$len[ToothGrowth$dose == 1],xlim=c(0,35),xlab="Length",main="Dosage of 1.0mm/day",col="blue")
hist(ToothGrowth$len[ToothGrowth$dose == 2],xlim=c(0,35),xlab="Length",main="Dosage of 2.0mm/day",col="blue")
```

![plot of chunk histbydose](figure/histbydose-1.png)

```r
par(mfrow=c(1,1))
z <- c(mean(ToothGrowth$len[ToothGrowth$dose == 0.5]),mean(ToothGrowth$len[ToothGrowth$dose == 1]),mean(ToothGrowth$len[ToothGrowth$dose == 2]))
```

| Mean Growth with Dose 0.5 | Mean Growth with Dose 1.0 | Mean Growth with Dose 2.0 |
|---------------------------|---------------------------|---------------------------|
| 10.605                  | 19.735                  | 26.1                  |


```r
par(mfrow=c(1,2))
hist(ToothGrowth$len[ToothGrowth$supp == 'OJ'],xlim=c(0,35),xlab="Length",main="Method of Orange Juice",col="blue")
hist(ToothGrowth$len[ToothGrowth$supp == 'VC'],xlim=c(0,35),xlab="Length",main="Method of Ascorbic Acid",col="blue")
```

![plot of chunk histbysupp](figure/histbysupp-1.png)

```r
par(mfrow=c(1,1))
z <- c(mean(ToothGrowth$len[ToothGrowth$supp == 'OJ']),mean(ToothGrowth$len[ToothGrowth$supp == 'VC']))
```

| Mean Growth with OJ | Mean Growth with VC |
|---------------------|---------------------|
| 20.6633333            | 16.9633333            |

Based upon these initial findings, it is suspected that odontoblast growth may be directly proportional to Vitamin C dosage, and that the use of orange juice as a delivery mechanism may be more effective than ascorbic acid.

#### Statistical prediction/modeling

First we investigate the relationship between odontoblast growth and delivery mechanism using a simple t test (Ott 1993):

```r
t.test(ToothGrowth$len ~ ToothGrowth$supp)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  ToothGrowth$len by ToothGrowth$supp
## t = 1.9153, df = 55.309, p-value = 0.06063
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -0.1710156  7.5710156
## sample estimates:
## mean in group OJ mean in group VC 
##         20.66333         16.96333
```

We (albeit marginally) fail to detect a significant difference in the effect of delivery mechanism on odontoblast growth (t=1.1953, p=0.0606).  

To investigate effects of dosage, three t-tests were required, each one comparing two of the three available dosage levels.  To account for possible issues of simultaneous inference, a Bonferroni adjustment (Neter et al. 1996) was made to the signficance level:

![](equations/eq1.png)

Results are as follows:


```r
means <- c(mean(ToothGrowth$len[ToothGrowth$dose == 0.5]),mean(ToothGrowth$len[ToothGrowth$dose == 1.0]),mean(ToothGrowth$len[ToothGrowth$dose == 2.0]))
fmeans <- format(means,digits=2,nsmall=2)
vars <- c(var(ToothGrowth$len[ToothGrowth$dose == 0.5]),var(ToothGrowth$len[ToothGrowth$dose == 1.0]),var(ToothGrowth$len[ToothGrowth$dose == 2.0]))
fvars <- format(vars,digits=2,nsmall=2)
ns <- c(length(ToothGrowth$len[ToothGrowth$dose ==0.5]),length(ToothGrowth$len[ToothGrowth$dose ==1.0]),length(ToothGrowth$len[ToothGrowth$dose ==2.0]))
ses <- c(sqrt(vars[1]/ns[1]),sqrt(vars[2]/ns[2]),sqrt(vars[3]/ns[3]))
fses <- format(ses,digits=2,nsmall=2)
cis <- c(means[1]+c(-1,1)*ses[1]*qt(1-0.05/3,ns[1]),means[2]+c(-1,1)*ses[2]*qt(1-0.05/3,ns[2]),means[3]+c(-1,1)*ses[3]*qt(1-0.05/3,ns[3]))
ci1 <- paste("(",format(cis[1],digits=2,nsmall=2),",",format(cis[2],digits=2,nsmall=2),")")
ci2 <- paste("(",format(cis[3],digits=2,nsmall=2),",",format(cis[4],digits=2,nsmall=2),")")
ci3 <- paste("(",format(cis[5],digits=2,nsmall=2),",",format(cis[6],digits=2,nsmall=2),")")
TG1 <- subset(ToothGrowth,dose %in% c(0.5,1))
TG2 <- subset(ToothGrowth,dose %in% c(1,2))
TG3 <- subset(ToothGrowth,dose %in% c(0.5,2))
TT1 <- t.test(TG1$len ~ TG1$dose,conf.level=1-0.05/3)
TT2 <- t.test(TG2$len ~ TG2$dose,conf.level=1-0.05/3)
TT3 <- t.test(TG3$len ~ TG3$dose,conf.level=1-0.05/3)
stat1 <- paste("t = ",format(TT1$statistic,digits=2,nsmall=2),", p = ",format(TT1$p.value,digits=2,nsmall=2))
stat2 <- paste("t = ",format(TT2$statistic,digits=2,nsmall=2),", p = ",format(TT2$p.value,digits=2,nsmall=2))
stat3 <- paste("t = ",format(TT3$statistic,digits=2,nsmall=2),", p = ",format(TT3$p.value,digits=2,nsmall=2))
```

|Dosage Level|Mean Length    |Std Error    |Conf. Int|1.0        |2.0        |
|------------|---------------|-------------|---------|-----------|-----------|
| 0.5 mm/day | 10.61 | 1.01 | ( 8.31 , 12.90 ) | t =  -6.48 , p =  1.3e-07 | t =  -11.80 , p =  4.4e-14 |
| 1.0 mm/day | 19.73 | 0.99 | ( 17.48 , 21.99 ) |           | t =  -4.90 , p =  1.9e-05 |
| 2.0 mm/day | 26.10 | 0.84 | ( 24.17 , 28.03 ) |           |           |

The results show that, even with the Bonferroni adjustment, all differences were statistically significant, supporting the conclusion that there is a statistical association between odontoblast growth and Vitamin C dosage. 

### Conclusion
We conclude that there is statistical support for the notion that odontoblast growth is positively associated with Vitamin C dosage.  The notion that odontoblast growth is associated with mode of delivery is not conclusively supported, but it should be noted that there is some indication that orange juice may be a better mechanism.  Further study on this may be warranted.

***

### References
- Crampton EW 1947. The growth of the odontoblast of the incisor teeth as a criterion of vitamin C intake of the guinea pig, The Journal of Nutrition 33(5): 491-504, http://jn.nutrition.org/content/33/5/491.full.pdf.  
- Neter J, Kutner MH, Nachtsheim CJ, Wasserman W 1996. Applied linear statistical models, fourth edition, WCB McGraw-Hill, 1408pp.  
- Ott RL 1993. An introduction to statistical methods and data analysis, Fourth Edition, Duxbury Press, 1051pp.  

***

#### End of Analysis
