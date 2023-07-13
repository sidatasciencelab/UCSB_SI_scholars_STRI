Impacts of agroforestry practices on the biodiversity of Panamanian
birds
================
Alex White
2023-07-13

``` r
library(tidyverse)
library(ggplot2)
library(sf)
sf_use_s2(FALSE)
library(renv)
library(taxize)
```

## A game of names…

``` r
coffee_birds <- read.csv("../data/coffee_Bird_Data.csv", stringsAsFactors = F)
head(coffee_birds)
```

    ##                  Common_Name Number_Observed distance_band Point_Name
    ## 1      Black-faced Solitaire               1       outside    PaBHF05
    ## 2            hummingbird sp.               1       outside    PaBHF05
    ## 3 Golden-browed Chlorophonia               1       outside    PaBHF05
    ## 4       White-tailed Emerald               1        within    PaBHF05
    ## 5            Mountain Thrush               5        within    PaBHF05
    ## 6      White-throated Thrush               3        within    PaBHF05
    ##   duration_hrs   sub_comments Date_Time latitude longitude
    ## 1        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821
    ## 2        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821
    ## 3        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821
    ## 4        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821
    ## 5        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821
    ## 6        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821

We downloaded the ebird taxonomy from the Cornell Lab of Ornithology
[here](https://www.birds.cornell.edu/clementschecklist/download/?__hstc=60209138.ef3edea8ce1040824b180bde7f920a82.1687545990397.1687545990397.1689258046438.2&__hssc=60209138.2.1689258046438&__hsfp=1390145134&_gl=1*1kufgl6*_ga*MTk2MzQwNTE1MC4xNjg3NDU5NDQ4*_ga_QR4NVXZ8BM*MTY4OTI1ODA0Ni4zLjEuMTY4OTI1ODA4NS4yMS4wLjA.&_ga=2.109099270.241330988.1689258046-1963405150.1687459448).
The .csv is available in the `/data` folder in the github repo.

``` r
ebird_taxonomy <- read.csv("../data/ebird_taxonomy_v2022.csv", stringsAsFactors = F)
head(ebird_taxonomy)
```

    ##   TAXON_ORDER CATEGORY SPECIES_CODE      PRIMARY_COM_NAME
    ## 1           2  species      ostric2        Common Ostrich
    ## 2           7  species      ostric3        Somali Ostrich
    ## 3           8    slash       y00934 Common/Somali Ostrich
    ## 4          10  species      grerhe1          Greater Rhea
    ## 5          16  species      lesrhe2           Lesser Rhea
    ## 6          17     issf      lesrhe4    Lesser Rhea (Puna)
    ##                             SCI_NAME           ORDER1                    FAMILY
    ## 1                   Struthio camelus Struthioniformes Struthionidae (Ostriches)
    ## 2             Struthio molybdophanes Struthioniformes Struthionidae (Ostriches)
    ## 3     Struthio camelus/molybdophanes Struthioniformes Struthionidae (Ostriches)
    ## 4                     Rhea americana       Rheiformes           Rheidae (Rheas)
    ## 5                       Rhea pennata       Rheiformes           Rheidae (Rheas)
    ## 6 Rhea pennata tarapacensis/garleppi       Rheiformes           Rheidae (Rheas)
    ##   SPECIES_GROUP REPORT_AS
    ## 1     Ostriches          
    ## 2     Ostriches          
    ## 3     Ostriches          
    ## 4         Rheas          
    ## 5         Rheas          
    ## 6         Rheas   lesrhe2

``` r
coffee_joined <- left_join(coffee_birds, ebird_taxonomy, by = join_by(Common_Name == PRIMARY_COM_NAME))
head(coffee_joined)
```

    ##                  Common_Name Number_Observed distance_band Point_Name
    ## 1      Black-faced Solitaire               1       outside    PaBHF05
    ## 2            hummingbird sp.               1       outside    PaBHF05
    ## 3 Golden-browed Chlorophonia               1       outside    PaBHF05
    ## 4       White-tailed Emerald               1        within    PaBHF05
    ## 5            Mountain Thrush               5        within    PaBHF05
    ## 6      White-throated Thrush               3        within    PaBHF05
    ##   duration_hrs   sub_comments Date_Time latitude longitude TAXON_ORDER CATEGORY
    ## 1        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821       27589  species
    ## 2        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821        5138     spuh
    ## 3        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821       31340  species
    ## 4        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821        4934  species
    ## 5        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821       27857  species
    ## 6        0.167 KA,V=2,L=0,N=0    3/9/22 8.811042 -82.39821       27889  species
    ##   SPECIES_CODE                SCI_NAME           ORDER1
    ## 1      blfsol1      Myadestes melanops    Passeriformes
    ## 2       hummin         Trochilidae sp. Caprimulgiformes
    ## 3      gobchl1 Chlorophonia callophrys    Passeriformes
    ## 4      whteme1     Microchera chionura Caprimulgiformes
    ## 5      mourob1         Turdus plebejus    Passeriformes
    ## 6      whtrob1        Turdus assimilis    Passeriformes
    ##                                          FAMILY                  SPECIES_GROUP
    ## 1                Turdidae (Thrushes and Allies)                       Thrushes
    ## 2                    Trochilidae (Hummingbirds)                   Hummingbirds
    ## 3 Fringillidae (Finches, Euphonias, and Allies) Finches, Euphonias, and Allies
    ## 4                    Trochilidae (Hummingbirds)                   Hummingbirds
    ## 5                Turdidae (Thrushes and Allies)                       Thrushes
    ## 6                Turdidae (Thrushes and Allies)                       Thrushes
    ##   REPORT_AS
    ## 1          
    ## 2          
    ## 3          
    ## 4          
    ## 5          
    ## 6

``` r
coffee_species <- coffee_joined %>% filter(CATEGORY == "species")
```

We could also use a more generalized R package `taxize` to standardize
the common names to latin species epithets (“genus_species”). We won’t
develop this method but the beginnings of this approach are shown below.

``` r
common_names <- coffee_birds %>% distinct(Common_Name)
common_names <- as.vector(common_names$Common_Name)
uid <- get_tsn(common_names[1:3], searchtype = "common", accepted = TRUE)
```

    ## ══  3 queries  ═══════════════

    ## 
    ## Retrieving data for taxon 'Black-faced Solitaire'

    ## ✔  Found:  Black-faced Solitaire

    ## 
    ## Retrieving data for taxon 'hummingbird sp.'

    ## ✖  Not Found:  hummingbird sp.

    ## 
    ## Retrieving data for taxon 'Golden-browed Chlorophonia'

    ## ✔  Found:  Golden-browed Chlorophonia
    ## ══  Results  ═════════════════
    ## 
    ## • Total: 3 
    ## • Found: 2 
    ## • Not Found: 1

## Quantifying biodiversity

The goal of this exercise is to better understand how scientists
quantify metrics of biodiversity and how those metrics are used to both
make decisions in conservation and to understand fundamental processes
of biology. Below we will work with the bird coffee dataset to quantify
various metrics of diversity for different study plots in the coffee
dataset, and to understand how values of diversity changes depending on
what species features we select to represent biological diversity.

### Taxonomic richness

Perhaps the simplest metric of biodiversity is taxonomic richness, or
the number of individual taxonomic groups represented at the study
location. Taxonomic groups can represent any taxonomic rank in the
Linnaean taxonomic system (e.g., order, family, genus, species), and
depending on the study, difference ranks are used as a unit of richness.
All of the species in our dataset are birds, and so they are all in the
same class *Aves*. The taxonomic rank in our dataset is mostly at the
level of species, so it makes good sense to quantify and compare
*species richness* across our sites.

First we need to understand how many points are surveyed in our data.

``` r
point_names <- coffee_species %>% 
  distinct(Point_Name)
point_names <- as.vector(point_names$Point_Name)
length(point_names)
```

    ## [1] 130

``` r
dim(coffee_species)
```

    ## [1] 11884    17

There are 11884 observations in 130 different locations in our dataset.
Our goal is to count the number of unique species in each location. One
*could* try to just count the unique number of observations per
location. Does this reflect the quantity we are trying to estimate?

``` r
species_richness <- coffee_species %>% count(Point_Name)
```

### For loops

Let’s try something a bit more sophisticated. Digging deeper, it appears
that these observations were collected on different days. The same
species could be recorded multiple times per location. We need to
approach the problem systematically. We can use a powerful tool called a
`for` loop.

- Loops are the fundamental structure for repetition in programming
- `for` loops perform the same action for each item in a list of things

<!-- -->

    for (item in list_of_items) {
      do_something(item)
    }

In this case we want to build a `for` loop that loops over each location
in our dataset and counts the number of unique species detected in that
location. Following the template above we build our `for` loops as
follows:

``` r
for (place in point_names){ #start the for loop
  
  sp_rich <- coffee_species %>% # grab our original dataset
    filter(Point_Name == place) %>% # filter the dataset for each point name
    distinct(SCI_NAME) # select the distinct species names
  
  sp <- length(as.vector(sp_rich$SCI_NAME)) # generate a vector of the species names and provide the length of that vector
  
  #print(sp) # print the value of species richness
}
```

There is one obvious problem with the loop we’ve created - the results
are merely printed. We need to store the results in an object (likely a
vector), and we need to modify out loop in order to do that efficiently.

### Looping with an index & storing results

R loops iterate over a series of values in a vector or other list like
object. When we use that value directly this is called looping by value.
But there is another way to loop, which is called looping by index.
Looping by index loops over a list of integer index values, typically
starting at 1. These integers are then used to access values in one or
more vectors at the position indicated by the index.

We often use `i` to stand for “index” as the variable we update with
each step through the loop.

If we modified our previous loop to use an index it would look like
this:

``` r
for (i in 1:length(point_names)){ #start the for loop
  
  sp_rich <- coffee_species %>% # grab our original dataset
    filter(Point_Name == i) %>% # filter the dataset for each point name
    distinct(SCI_NAME) # select the distinct species names
  
  sp <- length(as.vector(sp_rich$SCI_NAME)) # generate a vector of the species names and provide the length of that vector
  
  #print(sp) # print the value of species richness
}
```

Here we still need to add a method to store the results. To do this we
start by creating an empty object the same length as the results will be
before the loop starts. To store results in a vector we use the function
`vector` to create an empty vector of the right length. `mode` is the
type of data we are going to store. `length` is the length of the
vector.

``` r
sp <- vector(mode = "numeric", length = length(point_names))
```

Then add each result in the right position in the `sp` vector. For each
trip through the loop put the output into the empty vector at the `i`th
position.

``` r
for (i in 1:length(point_names)){ #start the for loop
  
  sp_rich <- coffee_species %>% # grab our original dataset
    filter(Point_Name == point_names[i]) %>% # filter the dataset for each point name
    distinct(SCI_NAME) # select the distinct species names
  
  sp[i] <- length(as.vector(sp_rich$SCI_NAME)) # generate a vector of the species names and provide the length of that vector
  
}
sp  # print the value of species richness
```

    ##   [1] 37 33 35 23 61 38 48 18 31 29 47 44 26 32 24 36 39 33 46 30 43 34 31 25 27
    ##  [26] 19 22 31 33 27 33 29 25 28 41 51 42 62 36 53 44 39 40 46 39 35 48 52 31 53
    ##  [51] 41 30 30 37 43 39 47 39 36 38 43 38 29 42 35 32 23 45 25 22 30 24 28 31 30
    ##  [76] 45 48 43 27 37 34 42 39 38 34 30 32 41 47 46 36 32 44 39 35 32 39 27 34 38
    ## [101] 28 39 21 21 32 38 29 55 44 55 48 39 33 38 53 28 45 39 40 49 49 17 26 38 27
    ## [126] 34 40 32 34  2

Now that we’ve built the loop and saved the output, we need to attach
our old

# Appendix

Exercises from
<https://datacarpentry.org/semester-biology/exercises/Loops-basic-for-loops-R/>

## Basic For Loops (Loops)

Exercises from
<https://datacarpentry.org/semester-biology/exercises/Loops-basic-for-loops-R/>

1.  The code below prints the numbers 1 through 5 one line at a time.
    Modify it to print each of these numbers multiplied by 3.

<!-- -->

    numbers <- c(1, 2, 3, 4, 5)
    for (number in numbers){
      print(number)
    }

2.  Write a for loop that loops over the following vector and prints out
    the mass in kilograms (mass_kg = 2.2 \* mass_lb)

<!-- -->

    mass_lbs <- c(2.2, 3.5, 9.6, 1.2)

3.  Complete the code below so that it prints out the name of each bird
    one line at a time.

<!-- -->

    birds = c('robin', 'woodpecker', 'blue jay', 'sparrow')
    for (i in 1:length(_________)){
      print(birds[__])
    }

4.  Complete the code below so that it stores one area for each radius.

<!-- -->

    radius <- c(1.3, 2.1, 3.5)
    areas <- vector(_____ = "numeric", length = ______)
    for (__ in 1:length(________)){
      areas[__] <- pi * radius[i] ^ 2
    }
    areas

5.  Complete the code below to calculate an area for each pair of
    lengths and widths, store the areas in a vector, and after they are
    all calculated print them out:

<!-- -->

    lengths = c(1.1, 2.2, 1.6)
    widths = c(3.5, 2.4, 2.8)
    areas <- vector(length = __________)
    for (i in _____) {
      areas[__] <- lengths[__] * widths[__]
    }
    areas
