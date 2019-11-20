---
title: "R Notebook"
output: html_notebook
---

This is an [R Markdown](http://rmarkdown.rstudio.com) Notebook. When you execute code within the notebook, the results appear beneath the code.  

Try executing this chunk by clicking the *Run* button within the chunk or by placing your cursor inside it and pressing *Cmd+Shift+Enter*. 

```{r}
library(DataComputing)
library(tidyr)
library(readr)
library(dplyr)

NMC<-readr::read_csv("NMC_5_0.csv")

head(NMC)
```
```{r}
NMC %>%
  filter(stateabb %in% c("USA", "CHN") & year > "1950") %>%
  select(stateabb, year, cinc) %>%
  ggplot(aes(x=year, y=cinc, color = stateabb)) +
  geom_point() +
  stat_smooth() +
  ylab("Year of Measure") +
  xlab("Composite Index of National Capabilities")

```

Find the average military expenditure of countries since from the beginning of the dataset. Notice that there are spikes in military expenditure during WWI (1917), US joins WWII (1941) (growth in milex grows rapidly each year after), Begining of Vietnam War (1955), Peak of cold war and local minima after fall of USSR (1993), attacks on Sept. 11 2001 (2002 budgetary information). This demonstrates that everytime the United States' entered a war, it has had a direct impact on the average military expenditure of the world. 
```{r}
NMC %>%
  select(stateabb, year, milex) %>%
  group_by(year) %>%
  summarise(avg_Expenditure = mean(milex)) %>%
  ggplot(aes(x=year, y=avg_Expenditure)) +
  geom_point() +
  xlab("Year of Observation") +
  ylab("Military Expenditure In Billion of USD") +
  geom_vline(xintercept = 1917) +
  geom_vline(xintercept = 1941) +
  geom_vline(xintercept = 1955) +
  geom_vline(xintercept = 1993) +
  geom_vline(xintercept = 2002) 
  

```
Interestingly Enough if we run the same operation, except this time removing the United States, we can begin to notice that these verticle bars which have deeply rooted signifigance in the United States, begin to loose meaning, i.e. they are starting to move from peaks in the data; thus demonstrating the signifigance of impact the US has on the world military expenditures. NOTE: the y limit droped by a factor of 10^2 thousand dollars when we removed the United States!
```{r}
NMC %>%
  select(stateabb, year, milex) %>%
  filter(stateabb != "USA") %>%
  group_by(year) %>%
  summarise(avg_Expenditure = mean(milex)) %>%
  ggplot(aes(x=year, y=avg_Expenditure)) +
  geom_point() +
  xlab("Year of Observation") +
  ylab("Military Expenditure In Billion of USD") +
  geom_vline(xintercept = 1917) +
  geom_vline(xintercept = 1941) +
  geom_vline(xintercept = 1955) +
  geom_vline(xintercept = 1993) +
  geom_vline(xintercept = 2002)

```
```{r}
determineUSMilitaryExpenditure <- function(passedYear)
{
  
result<-
NMC %>%
  select(stateabb, milex, year) %>%
  filter(stateabb=="USA") %>%
  filter(year==passedYear) %>%
  select(milex)
  
return (result)  

}
```

Since 1993, there has been a spike in military expenditure in the United States. The difference between the expenditure of these two years in thosand U.S Dollars:
```{r}
year1993MilEx <- determineUSMilitaryExpenditure(1993)
year2012MilEx <- determineUSMilitaryExpenditure(2012)
(year2012MilEx - year1993MilEx)
```

Even though the military expenditure has went up over the course of that time, there is a strong negative association between the funds and military personelle. If a larger military expenditure does not mean more military personelle, than that leads us to question what we are spending our money on.
```{r}
milper<-
NMC%>%
  select(year, milper) %>%
  group_by(year) %>%
  summarise(milper=mean(milper)) %>%
  filter(year>=1993) %>%
  select(milper)

milex<-
NMC%>%
  select(year, milex) %>%
  group_by(year) %>%
  summarise(milex=mean(milex)) %>%
  filter(year>=1993) %>%
  select(milex)


cor(milex, milper, method = c("pearson"))
```
