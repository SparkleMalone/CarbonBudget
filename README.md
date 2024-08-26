
## Objectives:
In this workshop you will:

1. Use select() to choose variables from a data frame.
2. Use filter() to choose data based on values.
3. Use reframe to work with subsets of data.
4. Visualize data with ggplot2.
5. Learn to write basic functions.

# The Carbon Budget

Carbon exists primarily in three forms: as carbon dioxide (CO2) in air and water, as organic carbon (found in proteins, fats, carbohydrates, and nucleic acids) in living and dead organisms, and as carbonate ions (CO₃²⁻) in water, rocks, shells, and bones. The cycling of carbon occurs through both biological and geochemical processes. Biological processes transfer carbon between organisms and the environment, while geochemical processes transfer carbon between sedimentary rocks, the atmosphere, oceans, and living organisms. Biological processes operate on relatively short timescales, ranging from years to hundreds of years, whereas geochemical processes span millions of years. To understand how these processes are interconnected in the carbon cycle, it is helpful to think in terms of pools (sources and sinks) and fluxes. Pools are reservoirs of carbon, with sources acting as carbon emitters and sinks as carbon absorbers. Fluxes represent the movement of carbon between these sources and sinks. Importantly, a single pool can function as both a source and a sink. For instance, the atmosphere serves as a source of carbon dioxide for photosynthesis, but it is also a sink for carbon released through respiration, combustion, and decay. Given that carbon dioxide is a greenhouse gas, there is concern that continued increases in atmospheric carbon could lead to global climate change. In this exercise you will explore the global carbon budget and become familiar with unit changes. This budget exercise is based on Friedlingstein et al., (2023).

Load the required packages:

```{r}
library(tidyverse)
library(ggplot2)
```

# Import your data and name the file Carbon_Budget:
```{r}
Carbon_Budget <- read.csv("data/Carbon Budget.csv")
```
Explore what is in the dataset and how it is organized:
```{r}
summary(Carbon_Budget)
names(Carbon_Budget)
```

Explore the Major Reservoirs of Carbon Pools. To do this, filter the dataframe to include only Type == Pool.
```{r}
Carbon_Budget %>% filter(Type == 'Pool' )
```

What are the unique Major_Reserviors?
```{r}
Carbon_Budget %>% filter(Type == 'Pool' ) %>% select( Major_Reservior) %>% unique()
```
Summarize Pools by major reservoirs:
```{r}
Carbon_Budget %>% filter(Type == 'Pool' ) %>% reframe(.by= Major_Reservior,
                                                      Carbon_Gt = sum(Carbon_Gt ))

Carbon_Budget %>% filter(Type == 'Pool' ) %>% reframe(.by= Major_Reservior,
                                                      Carbon_Gt = sum(Carbon_Gt )) %>% ggplot() + geom_bar(aes(x=Major_Reservior, y=Carbon_Gt),stat="identity")
                                                      
```

What are the unique sub_Reservoirs for terrestrial?
```{r}
Carbon_Budget %>% filter(Type == 'Pool', 
                         Major_Reservior == 'Terrestrial') %>% select( Sub_Reservior)
```

How much Carbon is in the Atmosphere?
```{r}
atmosphere.pool <- Carbon_Budget %>% filter(Type == 'Pool', Major_Reservior == 'Atmosphere' ) %>% select(Carbon_Gt) %>% sum
```

How much Carbon is in the Ocean?
```{r}
ocean.pool <- Carbon_Budget %>% filter(Type == 'Pool', Major_Reservior == 'Ocean' ) %>% select(Carbon_Gt) %>% sum
```
How much Carbon is on land? 
```{r}
terrestrial.pool <- Carbon_Budget %>% filter(Type == 'Pool', Major_Reservior == 'Terrestrial',  Sub_Reservior != 'Sedimentary Rocks'  ) %>% select(Carbon_Gt) %>% sum
```

Explore how much carbon moves into or out of major and sub reservoirs:

Land: 
```{r}
terrestrial.net <-Carbon_Budget %>% filter(Type == 'Flux', Major_Reservior == 'Terrestrial' ) %>% select(Carbon_Gt) %>% sum()
```

Oceans:
```{r}
ocean.net <-Carbon_Budget %>% filter(Type == 'Flux', Major_Reservior == 'Ocean' ) %>% select(Carbon_Gt) %>% sum()
```

Atmosphere:

```{r}
atmosphere.net <-Carbon_Budget %>% filter(Type == 'Flux') %>% select(Carbon_Gt) %>% sum()
```

### Figure 2 shows that the atmospheric flux is 5.2. What is responsible for the difference?

What percentage of the total carbon in the land, ocean, and atmosphere (excluding the sedimentary rocks) is in the atmosphere?
```{r}
atmosphere.pool/ (atmosphere.pool + terrestrial.pool+ ocean.pool) *100
```
# Functions:

A key feature of R is functions. Functions are “self contained” modules of code that accomplish a specific task. Functions usually take in some sort of data structure (value, vector, dataframe etc.), process it, and return a result.

The general usage for a function is the name of the function followed by parentheses:

The input(s) are called arguments, which can include:the physical object (any data structure) on which the function carries out a task specifications that alter the way the function operates (e.g. options). Not all functions take arguments, for example: getwd(). However, most functions can take several arguments. 

One of the great strengths of R is the user’s ability to add functions. Sometimes there is a small task (or series of tasks) you need done and you find yourself having to repeat it multiple times. In these types of situations it can be helpful to create your own custom function. The structure of a function is given below:

```{r}
  name_of_function <- function(argument1, argument2) {
    statements or code that does something
    return(something)
  }
```
Lets write a function to convert ppm to Gt C using Table 1 in Friedlingstein et al., (2023).
```{r}
ppm_to_Gt_C <- function( ppm){
  Gt_C = ppm*2.124
  return(Gt_C)
}
```
### Challenge: Create a generic function that can convert from ppm, Pg C, Gt C, or Mt C to Gt_C. Use Table 1 in Friedlingstein et al., (2023).