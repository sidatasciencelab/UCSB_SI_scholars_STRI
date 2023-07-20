Impacts of agroforestry practices on the biodiversity of Panamanian
birds
================
Advanced Data Science Field Course at STRI, UCSB-Smithsonian Scholars
2023-07-20

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
glimpse(coffee_birds)
```

    ## Rows: 12,638
    ## Columns: 9
    ## $ Common_Name     <chr> "Black-faced Solitaire", "hummingbird sp.", "Golden-br…
    ## $ Number_Observed <int> 1, 1, 1, 1, 5, 3, 1, 1, 3, 3, 3, 3, 1, 1, 1, 4, 1, 1, …
    ## $ distance_band   <chr> "outside", "outside", "outside", "within", "within", "…
    ## $ Point_Name      <chr> "PaBHF05", "PaBHF05", "PaBHF05", "PaBHF05", "PaBHF05",…
    ## $ duration_hrs    <dbl> 0.167, 0.167, 0.167, 0.167, 0.167, 0.167, 0.167, 0.167…
    ## $ sub_comments    <chr> "KA,V=2,L=0,N=0", "KA,V=2,L=0,N=0", "KA,V=2,L=0,N=0", …
    ## $ Date_Time       <chr> "3/9/22", "3/9/22", "3/9/22", "3/9/22", "3/9/22", "3/9…
    ## $ latitude        <dbl> 8.811042, 8.811042, 8.811042, 8.811042, 8.811042, 8.81…
    ## $ longitude       <dbl> -82.39821, -82.39821, -82.39821, -82.39821, -82.39821,…

We downloaded the ebird taxonomy from the Cornell Lab of Ornithology
[here](https://www.birds.cornell.edu/clementschecklist/download/?__hstc=60209138.ef3edea8ce1040824b180bde7f920a82.1687545990397.1687545990397.1689258046438.2&__hssc=60209138.2.1689258046438&__hsfp=1390145134&_gl=1*1kufgl6*_ga*MTk2MzQwNTE1MC4xNjg3NDU5NDQ4*_ga_QR4NVXZ8BM*MTY4OTI1ODA0Ni4zLjEuMTY4OTI1ODA4NS4yMS4wLjA.&_ga=2.109099270.241330988.1689258046-1963405150.1687459448).
The .csv is available in the `/data` folder in the github repo.

``` r
ebird_taxonomy <- read.csv("../data/ebird_taxonomy_v2022.csv", stringsAsFactors = F)
glimpse(ebird_taxonomy)
```

    ## Rows: 16,860
    ## Columns: 9
    ## $ TAXON_ORDER      <int> 2, 7, 8, 10, 16, 17, 20, 22, 23, 24, 29, 30, 33, 38, …
    ## $ CATEGORY         <chr> "species", "species", "slash", "species", "species", …
    ## $ SPECIES_CODE     <chr> "ostric2", "ostric3", "y00934", "grerhe1", "lesrhe2",…
    ## $ PRIMARY_COM_NAME <chr> "Common Ostrich", "Somali Ostrich", "Common/Somali Os…
    ## $ SCI_NAME         <chr> "Struthio camelus", "Struthio molybdophanes", "Struth…
    ## $ ORDER1           <chr> "Struthioniformes", "Struthioniformes", "Struthionifo…
    ## $ FAMILY           <chr> "Struthionidae (Ostriches)", "Struthionidae (Ostriche…
    ## $ SPECIES_GROUP    <chr> "Ostriches", "Ostriches", "Ostriches", "Rheas", "Rhea…
    ## $ REPORT_AS        <chr> "", "", "", "", "", "lesrhe2", "lesrhe2", "", "", "hi…

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

Now that we’ve built the loop and saved the output, we need to combine
these data with our original dataset to pair the calculated metric `sp`
with the `point_names`. But because there are a number of other
variables that have also been measured for each point and we expect to
add more moving forward, let’s combine the `sp` vector with a larger
metadata table.

``` r
point_metadata <- read.csv("../data/point_metadata.csv", stringsAsFactors = F)
head(point_metadata)
```

    ##   Name_Point Latitude  Longitud Habitat Elevation Slope Distance.to.Edge
    ## 1    PaBHF07 8.805410 -82.38800  Forest      2013 100.0               40
    ## 2    PaBHF06 8.807901 -82.39425  Forest      1750  97.5               40
    ## 3   PaBHSC01 8.824319 -82.45733     Sun      1574  91.5               50
    ## 4   PaBHSC02 8.822304 -82.45616     Sun      1519 100.5               40
    ## 5   PaBHSC03 8.820029 -82.45737     Sun      1497  88.0               50
    ## 6   PaBHSC04 8.817880 -82.45737     Sun      1458  88.5               50
    ##   Number_tree_species Tree_basal_area Number_tree_individuals
    ## 1                   8              50                      28
    ## 2                   9              25                      10
    ## 3                   4               2                       6
    ## 4                   2               4                       0
    ## 5                   7               3                       8
    ## 6                   0               2                       0
    ##   Height_tallest_tree Average_Canopy_Height SE_canopy_height
    ## 1                18.0               10.4375         2.871504
    ## 2                23.5               15.7500         1.322876
    ## 3                14.0                2.6000         4.241331
    ## 4                18.0                0.0000         0.000000
    ## 5                14.0                1.0000         3.000000
    ## 6                 0.0                0.0000         0.000000
    ##   Average_canopy_density SE_canopy_density
    ## 1                  91.29              3.90
    ## 2                  88.38              2.72
    ## 3                  28.32             31.94
    ## 4                   8.22             16.89
    ## 5                  14.54             21.06
    ## 6                   0.16              0.00

We know our `sp` vector is ordered according to the `point_names`
vector. Let’s bind those together in a dataframe and join that with the
`point_metadata` table above. First generate the dataframe:

``` r
sp_richness <- data.frame(sp, point_names)
head(sp_richness)
```

    ##   sp point_names
    ## 1 37     PaBHF05
    ## 2 33     PaBHF08
    ## 3 35     PaBHF06
    ## 4 23    PaBLSC03
    ## 5 61    PaBLSh03
    ## 6 38    PaBLSh02

Now we can use `left_join()` with the `point_metadata` table to
aggregate our metadata in a single table:

``` r
point_metadata <- left_join(point_metadata, sp_richness, by = join_by(Name_Point == point_names))
head(point_metadata)
```

    ##   Name_Point Latitude  Longitud Habitat Elevation Slope Distance.to.Edge
    ## 1    PaBHF07 8.805410 -82.38800  Forest      2013 100.0               40
    ## 2    PaBHF06 8.807901 -82.39425  Forest      1750  97.5               40
    ## 3   PaBHSC01 8.824319 -82.45733     Sun      1574  91.5               50
    ## 4   PaBHSC02 8.822304 -82.45616     Sun      1519 100.5               40
    ## 5   PaBHSC03 8.820029 -82.45737     Sun      1497  88.0               50
    ## 6   PaBHSC04 8.817880 -82.45737     Sun      1458  88.5               50
    ##   Number_tree_species Tree_basal_area Number_tree_individuals
    ## 1                   8              50                      28
    ## 2                   9              25                      10
    ## 3                   4               2                       6
    ## 4                   2               4                       0
    ## 5                   7               3                       8
    ## 6                   0               2                       0
    ##   Height_tallest_tree Average_Canopy_Height SE_canopy_height
    ## 1                18.0               10.4375         2.871504
    ## 2                23.5               15.7500         1.322876
    ## 3                14.0                2.6000         4.241331
    ## 4                18.0                0.0000         0.000000
    ## 5                14.0                1.0000         3.000000
    ## 6                 0.0                0.0000         0.000000
    ##   Average_canopy_density SE_canopy_density sp
    ## 1                  91.29              3.90 18
    ## 2                  88.38              2.72 35
    ## 3                  28.32             31.94 41
    ## 4                   8.22             16.89 32
    ## 5                  14.54             21.06 30
    ## 6                   0.16              0.00 34

Voila! Now we can easily use the `point_metadata` table to analyze and
visualize the relationships between the various metrics we are
interested in! How does the habitat type (sun vs. shade) relate to the
number of species at the point? How does canopy cover play a role? How
do we quantify those effects? While it seemed trivial at first to count
the species in each point, we have now developed a programmatic approach
that we can modify to generate other metrics of diversity that go beyond
mere species counts.

### Functional richness

Counting species is one way to measure the diversity of an ecosystem,
but species may or may not be the appropriate unit of measurement for a
particular research question or conservation decision. For example,
biologists may consider how species interact with their environments and
instead look to count how different types of interactions are
represented by the species that they observe. In this framework, 2
species may share the exact same traits that govern their interactions
with the environment, and so while 2 species may be counted from a
taxonomic richness point of view only 1 *functional group* would be
counted from a *functional richness* perspective.

One of the most important functional traits for bird species is the size
and shape of their bill. The bill is the main anatomical feature that
birds use to interact with their environment and bill traits combined
with the overall size of a species can tell biologists a lot about what
a bird does in an ecosystem.

<figure>
<img src="../images/bird-bills.png"
alt="Morphological diversity of bird bills. Top row (left to right): Aethopyga gouldiae, Napothera malacoptila, Harpactes erythrocephalus, Cissa chinensis, Fratercula corniculata. Second row (left to right): Passer cinnamomeus, Aquila nipalensis, Anastomus oscitans, Serilophus lunatus, Phoenicopterus roseus. Third row (left to right): Recurvirostra avosetta, Corvus corax, Spilornis cheela, Chalcophaps indica, Thalassarche cauta. Fourth row (left to right): Ara ararauna, Phyllergates cucullatus, Paradoxornis flavirostris, Stachyris humei, Batrachostomus moniliger. Bottom left: Platalea leucorodia. Fifth row (left to right): Pomatorhinus ferruginosus, Pyrrhoplectes epauletta, Nestor notabilis. Sixth row (left to right): Pomatorhinus superciliaris, Diomedea epomophora, Upupa epops, Gallus gallus. Bottom right: Pelecanus philippensis" />
<figcaption aria-hidden="true"><strong>Morphological diversity of bird
bills.</strong> Top row (left to right): <em>Aethopyga gouldiae,
Napothera malacoptila, Harpactes erythrocephalus, Cissa chinensis,
Fratercula corniculata</em>. Second row (left to right): <em>Passer
cinnamomeus, Aquila nipalensis, Anastomus oscitans, Serilophus lunatus,
Phoenicopterus roseus.</em> Third row (left to right): <em>Recurvirostra
avosetta, Corvus corax, Spilornis cheela, Chalcophaps indica,
Thalassarche cauta.</em> Fourth row (left to right): <em>Ara ararauna,
Phyllergates cucullatus, Paradoxornis flavirostris, Stachyris humei,
Batrachostomus moniliger.</em> Bottom left: <em>Platalea
leucorodia</em>. Fifth row (left to right): <em>Pomatorhinus
ferruginosus, Pyrrhoplectes epauletta, Nestor notabilis.</em> Sixth row
(left to right): <em>Pomatorhinus superciliaris, Diomedea epomophora,
Upupa epops, Gallus gallus</em>. Bottom right: <em>Pelecanus
philippensis</em></figcaption>
</figure>

#### Merging traits with surveys

For most of the history of modern science, the only way to measure a
birds bill was to have the bird in hand. This was achieved either by
capturing a live bird (often using a mist net) or by collecting a bird
as a specimen to be kept in a natural history collection. The Division
of Birds at Smithsonian National Museum of Natural History contains
~640,000 individual bird specimens, representing the 3rd largest bird
collection in the world.

![Smithsonian National Museum of Natural History collections in the
Division of Birds.](../images/birds_museum.png) But thanks to the
efforts of biologists working with both these collections and with wild
caught individuals in remote locations, trait data are now available in
a publicly accessible dataset comprising most of the worlds bird
species. These data were meticulously compiled using caliper
measurements of traits from multiple specimens per species, and so can
provide us an estimate of the functional traits of any bird species we
encounter. Similar datasets are being generated for mammals, some plant
groups, fossil species; the list goes on. These data provide us with the
foundation for quantitative ecological studies of the relationship
between traits and the environment. We can also ask how those traits
evolved by combining trait data with DNA sequence data. These datasets
are the modern basis for investigating fundamental questions in ecology
and evolution.

The bird species dataset is named AVONET; it is described in the
following paper:

    Tobias, Joseph A., et al. "AVONET: morphological, ecological and geographical data for all birds." Ecology Letters 25.3 (2022): 581-597. https://doi.org/10.1111/ele.13898

    @article{tobias2022avonet,
      title={AVONET: morphological, ecological and geographical data for all birds},
      author={Tobias, Joseph A and Sheard, Catherine and Pigot, Alex L and Devenish, Adam JM and Yang, Jingyi and Sayol, Ferran and Neate-Clegg, Montague HC and Alioravainen, Nico and Weeks, Thomas L and Barber, Robert A and others},
      journal={Ecology Letters},
      volume={25},
      number={3},
      pages={581--597},
      year={2022},
      publisher={Wiley Online Library}
    }

    [Link to Tobias et
al. 2022](https://onlinelibrary.wiley.com/doi/full/10.1111/ele.13898)

To get a full picture of the functional diversity contained in our
Panamanian coffee dataset, let’s extract trait data from AVONET for the
species observed in the coffee plantations. First read in the data.

``` r
avonet <- read.csv("../data/AVONET_ebird.csv", stringsAsFactors = F)
glimpse(avonet)
```

    ## Rows: 10,661
    ## Columns: 31
    ## $ Species2           <chr> "Accipiter albogularis", "Accipiter badius", "Accip…
    ## $ Family2            <chr> "Accipitridae", "Accipitridae", "Accipitridae", "Ac…
    ## $ Order2             <chr> "Accipitriformes", "Accipitriformes", "Accipitrifor…
    ## $ Avibase.ID2        <chr> "AVIBASE-BBB59880", "AVIBASE-1A0ECB6E", "AVIBASE-AD…
    ## $ Total.individuals  <int> 5, 10, 11, 4, 8, 1, 6, 5, 7, 5, 4, 5, 11, 10, 8, 4,…
    ## $ Female             <int> 2, 4, 4, 4, 4, 0, 2, 2, 1, 2, 3, 2, 8, 2, 4, 3, 0, …
    ## $ Male               <int> 0, 6, 5, 0, 4, 1, 2, 3, 5, 3, 1, 3, 3, 8, 4, 1, 4, …
    ## $ Unknown            <int> 3, 0, 2, 0, 0, 0, 2, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, …
    ## $ Complete.measures  <dbl> 4, 8, 8, 3, 4, 1, 4, 4, 6, 4, 4, 4, 9, 9, 4, 3, 4, …
    ## $ Beak.Length_Culmen <dbl> 27.7, 20.6, 25.0, 22.5, 21.1, 20.0, 20.5, 19.2, 20.…
    ## $ Beak.Length_Nares  <dbl> 17.8, 12.1, 13.7, 14.0, 12.1, 11.9, 11.5, 10.6, 11.…
    ## $ Beak.Width         <dbl> 10.6, 8.8, 8.6, 8.9, 8.7, 6.6, 8.3, 7.7, 8.6, 8.6, …
    ## $ Beak.Depth         <dbl> 14.7, 11.6, 12.7, 11.9, 11.1, 12.0, 10.9, 9.6, 11.0…
    ## $ Tarsus.Length      <dbl> 62.0, 43.0, 58.1, 61.2, 46.4, 48.7, 52.6, 60.3, 43.…
    ## $ Wing.Length        <dbl> 235.2, 186.7, 229.6, 202.2, 217.6, 166.0, 163.5, 21…
    ## $ Kipps.Distance     <dbl> 81.8, 62.5, 56.6, 64.1, 87.8, 42.9, 38.9, 81.3, 49.…
    ## $ Secondary1         <dbl> 159.5, 127.4, 174.8, 138.1, 129.9, 123.1, 123.1, 13…
    ## $ Hand.Wing.Index    <dbl> 33.9, 32.9, 24.6, 31.7, 40.2, 25.8, 24.0, 37.8, 30.…
    ## $ Tail.Length        <dbl> 169.0, 140.6, 186.3, 140.8, 153.5, 127.0, 135.4, 15…
    ## $ Mass               <dbl> 248.8, 131.2, 287.5, 142.0, 186.5, 122.0, 157.5, 16…
    ## $ Mass.Source        <chr> "Dunning", "Dunning", "Dunning", "Dunning", "Dunnin…
    ## $ Mass.Refs.Other    <chr> NA, NA, NA, NA, NA, "Dunning (2021)", NA, NA, "BoW"…
    ## $ Inference          <chr> "NO", "NO", "NO", "NO", "NO", "NO", "NO", "NO", "NO…
    ## $ Traits.inferred    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
    ## $ Reference.species  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
    ## $ Habitat            <chr> "Forest", "Shrubland", "Woodland", "Forest", "Fores…
    ## $ Habitat.Density    <int> 1, 2, 2, 1, 1, 1, 1, 1, 1, 2, 1, 1, 1, 1, 1, 1, 1, …
    ## $ Migration          <int> 2, 3, 2, 2, 3, 1, 2, 2, 2, 3, 1, 1, 1, 1, 2, 1, 3, …
    ## $ Trophic.Level      <chr> "Carnivore", "Carnivore", "Carnivore", "Carnivore",…
    ## $ Trophic.Niche      <chr> "Vertivore", "Vertivore", "Vertivore", "Vertivore",…
    ## $ Primary.Lifestyle  <chr> "Insessorial", "Insessorial", "Generalist", "Insess…

There are numerous ways to quantify functional diversity from trait
measurements. Some methods incorporate only quantitative variables and
some methods incorporate categorical data like `Habitat` and
`Trophic.niche` by converting those variables to binomial characters
`[0,1]`. Many of these methods are rooted in a distance-based approach,
meaning that a (often euclidean) multidimensional space is computed
containing all species in the dataset, and diversity is measured as the
overall space occupied by those species in the multidimensional space.
This means that computing *functional richness* usually requires a few
steps.

- What traits are included in the trait space?
- What is the method for generating the numerical space in which you
  will

<!-- -->

    for (i in 1:length(point_names)){ #start the for loop
      
      sp_rich <- coffee_species %>% # grab our original dataset
        filter(Point_Name == point_names[i]) %>% # filter the dataset for each point name
        distinct(SCI_NAME) # select the distinct species names
      
      sp[i] <- sp_rich$SCI_NAME) # generate a vector of the species names and provide the length of that vector
      
    }
    sp

    [Link to data from Tobias et
al. 2022](https://figshare.com/s/b990722d72a26b5bfead)

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
