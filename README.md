### Informal Response 1

Code:
``` python
library(haven)
library(tidyverse)
library(tidymodels)
library(glmnet)
library(vip)
library(heatmaply)

households <- read_dta("KH_2014_DHS_03102021_2047_160420/KHHR73DT/KHHR73FL.DTA")
View(households)

hhid <- households$hhid
unit <- households$hv004
weights <- households$hv005 / 1000000
location <- as_factor(households$shprov)
size <- households$hv009
sex <- households[ ,267:288]
age <- households[ ,289:310]
edu <- households[ ,311:332]

wealth <- households$hv270

hhs <- cbind.data.frame(hhid,unit,weights,location,size,sex,age,edu,wealth)

gender <- hhs %>%
  pivot_longer(cols = starts_with(match = "hv104"),
               names_to = "pid",
               values_to = "gender",
               values_drop_na = TRUE)

age <- hhs %>%
  pivot_longer(cols = starts_with(match = "hv105"),
               names_to = "pid",
               values_to = "age",
               values_drop_na = TRUE)

edu <- hhs %>%
  pivot_longer(cols = starts_with(match = "hv106"),
               names_to = "pid",
               values_to = "edu",
               values_drop_na = TRUE)

gender <- select(gender, -starts_with(match = "hv"))
age <- select(age,-starts_with(match = "hv"))
edu <- select(edu, -starts_with(match = "hv"))

gender$id <- paste(gender$hhid, substr(gender$pid, 7,8), sep = '')
age$id <- paste(age$hhid, substr(age$pid, 7,8), sep = '')
edu$id <- paste(edu$hhid, substr(edu$pid, 7,8), sep = '')

pns <- inner_join(gender, age, by = "id") %>% inner_join(., edu, by = "id")

pns <- pns %>% # add weights later
  select(size, gender, age, edu, wealth)

pns$size <- as.factor(pns$size)
pns$gender <- as.factor(pns$gender)
pns$age <- as.factor(pns$age)
pns$edu <- as.factor(pns$edu)
pns$wealth <- as.factor(pns$wealth)

pns_prep <- slice_sample(pns, n = 1000, replace = FALSE)


plot <- heatmaply(
  scale(pns_prep),
  xlab = "Features",
  ylab = "Combinations",
  main = "Scaled data",
  cexRow = .25)
ggsave("scale.png", width = 25, height = 25)

plot <- heatmaply(
  normalize(pns_prep),
  xlab = "Features",
  ylab = "education",
  main = "Normalize data",
  cexRow = .25)
ggsave("normal.png", width = 25, height = 25)

plot <- heatmaply(
  percentize(pns_prep),
  xlab = "Features",
  ylab = "education",
  main = "Percentize data",
  cexRow = .25)
ggsave("percent.png", width = 25, height = 25)
```
[scale.png](https://github.com/rj-bartlett/Informal-Response-Module-2/issues/1#issue-858411370)
[normal.png](https://github.com/rj-bartlett/Informal-Response-Module-2/issues/2#issue-858411658)
[percent.png](https://github.com/rj-bartlett/Informal-Response-Module-2/issues/3#issue-858411825)
