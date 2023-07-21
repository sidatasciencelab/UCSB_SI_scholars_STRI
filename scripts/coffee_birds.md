Impacts of agroforestry practices on the biodiversity of Panamanian
birds
================
Advanced Data Science Field Course at STRI, UCSB-Smithsonian Scholars
2023-07-21

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
coffee_species <- coffee_joined %>% filter(CATEGORY == "species") %>% filter(distance_band == "within") %>% filter(!Point_Name == "1-651 Callejón Seco, Los Naranjos, Provincia de Chiriquí, PA (8.818, -82.481)")
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

    ## [1] 129

``` r
dim(coffee_species)
```

    ## [1] 5086   17

There are 5086 observations in 129 different locations in our dataset.
Our goal is to count the number of unique species in each location. One
*could* try to just count the unique number of observations per
location. Does this reflect the quantity we are trying to estimate?

``` r
species_richness <- coffee_species %>% count(Point_Name)
#species_richness
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

    ##   [1] 28 22 26  7 45 24 27 11 15 17 24 19 15 23 14 24 26 20 28 10 19 16 17  9 15
    ##  [26]  3 14 11  4  6 25 16 20 20 26 21 27 43 11 38 24 16 32 20 20 12 28 31 14 21
    ##  [51] 20 19 11 18 19 15 26 19 17 19 30 25 19 19 20 20 11 31 19 14 23 14 18 23 13
    ##  [76] 25 28 20 20 23 19 31 26 24 21 23 17 29 29 26 26 21 35 24 21 18 26 19 29 26
    ## [101] 19 29 15 12 15 31  8 26 22 36 27 28 20 25 39 16 26 24 27 29 38 14 23 27 20
    ## [126] 24 24 27 21

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
    ## 1 28     PaBHF05
    ## 2 22     PaBHF08
    ## 3 26     PaBHF06
    ## 4  7    PaBLSC03
    ## 5 45    PaBLSh03
    ## 6 24    PaBLSh02

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
    ## 1                  91.29              3.90 11
    ## 2                  88.38              2.72 26
    ## 3                  28.32             31.94 29
    ## 4                   8.22             16.89 17
    ## 5                  14.54             21.06 23
    ## 6                   0.16              0.00 21

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

    [Link to data from Tobias et
al. 2022](https://figshare.com/s/b990722d72a26b5bfead) - these data are
already in the `/data` directory in the course repo.

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
- How will you generate the numerical space to measure distance?
- How will you measure occupancy and distance within that numerical
  space?

Ecologists have been investigating these choices and how they influence
our understanding of a biological system for decades. Magneville et
al. (2022) provide R based methods for computing across many of these
choices is their new package `mFD`.

![](../images/mFD_ecography.png)

While the paper is comprehensive, many R packages are now supplied with
a useful [website](https://cmlmagneville.github.io/mFD/). The best way
to deploy a new approach like this is to use the website as a “recipe
book” so to speak. Let’s navigate to the website and compute some
functional diversity metrics for our bird surveys.
![](../images/mFD.png)

First, we (most likely) need to install the package. Use the
`install.packages()` function.

    # Install stable version of < mFD > from CRAN ----
    install.packages("mFD")

``` r
library(mFD)
```

From the package website:

\*To compute functional diversity indices, users need:

- a data frame summarizing species traits (species in rows, traits in
  columns). The mFD package works with all kind of traits: quantitative,
  ordinal, nominal, circular, and fuzzy-coded.

- a matrix summarizing species gathering into assemblages (assemblages
  in rows, species in columns). All assemblages must at least contain
  one species.

- a data frame summarizing traits category (first column with traits
  name, second column with traits type, third column with fuzzy name of
  fuzzy traits - if no fuzzy traits: NA).

For a complete understanding of the functional workflow and the package
possibilities, please refer to the [mFD General
Workflow](https://cmlmagneville.github.io/mFD/articles/mFD_general_workflow.html)\*.

Exercise: How do we transform our data from single observations of
species in each point (our current `coffee_species` data) into the a
matrix of points (or assemblages) in rows and the count of each species
in the column? For simplicity, let’s just count the species as present
or absent using `[0,1]`. Try building a `for` loop, but first start with
a matrix of points and species. Fill in the matrix programatically using
a loop.

``` r
species_names <- coffee_species %>%
    distinct(SCI_NAME)

coffee_points <- matrix(data = NA, 
                        nrow = length(point_names), 
                        ncol = length(species_names$SCI_NAME))

colnames(coffee_points) <- species_names$SCI_NAME
rownames(coffee_points) <- point_names
```

We can grab our loop that we used before and modify it for our new
objective.

``` r
for (i in 1:length(point_names)){ #start the for loop
  
  sp_rich <- coffee_species %>% # grab our original dataset
    filter(Point_Name == point_names[i]) %>% # filter the dataset for each point name
    distinct(SCI_NAME) # select the distinct species names
  
  pres_ab <- colnames(coffee_points) %in% sp_rich$SCI_NAME # generate a vector of the species names and provide the length of that vector
  
  coffee_points[i,] <- as.numeric(pres_ab)
}
```

OK! Now we are moving closer to leveraging the `mFD` package. The final
piece is a dataframe telling the package what types of traits we are
using. There are many traits to choose from in the AVONET dataset, and
therefore many potential ways to view functional richness. Let’s choose
which functional traits we are going to use for this analysis.

``` r
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

``` r
trait_name <- colnames(avonet)[c(10:17,19,20,26,27,29:31)]
```

Now that we have our traits we need to follow the `mFD` recipe for
building our trait type dataframe: ![](../images/mFD_instructions.png)
Our data are quantitative, nominal, and in the case of Habitat.density,
this seems like an ordinal variable. We can build a `bird_trait_type`
dataframe using these two vectors.

``` r
trait_type <- c(rep("Q",10),"N","Q",rep("N",3))
bird_trait_type <- data.frame(trait_name, trait_type)
```

Finally, we need to filter AVONET to only include the species we need
for our analysis. We already have a vector `colnames(coffee_points)` so
let’s filter AVONET using that vector.

``` r
avonet_coffee <- avonet %>% filter(Species2 %in% colnames(coffee_points))
length(colnames(coffee_points))
```

    ## [1] 231

``` r
dim(avonet_coffee)
```

    ## [1] 229  31

It seems that we’ve lost 2 species from our coffee dataset - which
species are missing?

``` r
colnames(coffee_points)[!colnames(coffee_points) %in% avonet_coffee$Species2]
```

    ## [1] "Mionectes galbinus"  "Daptrius chimachima"

This is a very common problem when working with these types of data. The
taxonomy of all organisms is under constant revision and species names
change all the time. Out two missing species are `"Mionectes galbinus"`
and `"Daptrius chimachima"`. A quick search reveals other possible
species names that may be used in the AVONET dataset
`"Mionectes olivaceus"` and `"Milvago chimachima"`. Let’s modify our
original dataset to reflect these changes.

``` r
colnames(coffee_points)[colnames(coffee_points) == "Mionectes galbinus"] <- "Mionectes olivaceus"
colnames(coffee_points)[colnames(coffee_points) == "Daptrius chimachima"] <- "Milvago chimachima"
```

Now re-filter AVONET using the full complement of species names. One
snag seems to be that the datasframe requires all strings to be factors,
so we rebuild our dataframe using that argument.

``` r
avonet_coffee <- avonet %>% filter(Species2 %in% colnames(coffee_points))
avonet_coffee <- as.data.frame(unclass(avonet_coffee),
                               row.names = avonet_coffee$Species2, 
                               stringsAsFactors = TRUE)
avonet_coffee <- avonet_coffee %>% select(all_of(trait_name))
```

Finally we can use mFD! As we noted earlier, there are many things to
consider when computing functional richness. From the `mFD` general
workflow
[vignette](https://cmlmagneville.github.io/mFD/articles/mFD_general_workflow.html#computing-distances-between-species-based-on-functional-traits),
we need to compute the distances between species in our functional trait
space. From this space, we will compute functional richness of our
points.

![](../images/distances.png)

``` r
sp_dist_birds <- mFD::funct.dist(
  sp_tr         = avonet_coffee,
  tr_cat        = bird_trait_type,
  metric        = "gower",
  scale_euclid  = "scale_center",
  ordinal_var   = "classic",
  weight_type   = "equal",
  stop_if_NA    = TRUE)
```

``` r
fspaces_quality_birds <- mFD::quality.fspaces(
  sp_dist             = sp_dist_birds,
  maxdim_pcoa         = 10,
  deviation_weighting = "absolute",
  fdist_scaling       = FALSE,
  fdendro             = "average")
```

    ## Registered S3 method overwritten by 'dendextend':
    ##   method     from 
    ##   rev.hclust vegan

``` r
sp_faxes_coord_birds <- fspaces_quality_birds$"details_fspaces"$"sp_pc_coord"
```

``` r
alpha_fd_indices_birds <- mFD::alpha.fd.multidim(
  sp_faxes_coord   = sp_faxes_coord_birds[ , c("PC1", "PC2")],
  asb_sp_w         = coffee_points,
  ind_vect         = c("fdis", "fmpd", "fnnd", "feve", "fric", "fdiv", "fori", 
                       "fspe", "fide"),
  scaling          = TRUE,
  check_input      = TRUE,
  details_returned = TRUE)
```

``` r
fd_ind_values_birds <- alpha_fd_indices_birds$"functional_diversity_indices"
fd_ind_values_birds$point_name <- rownames(fd_ind_values_birds)
```

``` r
point_metadata <- left_join(point_metadata, fd_ind_values_birds, by = join_by(Name_Point == point_name))
glimpse(point_metadata)
```

    ## Rows: 997
    ## Columns: 27
    ## $ Name_Point              <chr> "PaBHF07", "PaBHF06", "PaBHSC01", "PaBHSC02", …
    ## $ Latitude                <dbl> 8.805410, 8.807901, 8.824319, 8.822304, 8.8200…
    ## $ Longitud                <dbl> -82.38800, -82.39425, -82.45733, -82.45616, -8…
    ## $ Habitat                 <chr> "Forest", "Forest", "Sun", "Sun", "Sun", "Sun"…
    ## $ Elevation               <int> 2013, 1750, 1574, 1519, 1497, 1458, 1732, 1609…
    ## $ Slope                   <dbl> 100.0, 97.5, 91.5, 100.5, 88.0, 88.5, 106.0, 9…
    ## $ Distance.to.Edge        <chr> "40", "40", "50", "40", "50", "50", "100", "60…
    ## $ Number_tree_species     <int> 8, 9, 4, 2, 7, 0, 10, 9, 5, 3, 5, 3, 3, 3, 2, …
    ## $ Tree_basal_area         <int> 50, 25, 2, 4, 3, 2, 51, 75, 3, 2, 2, 15, 2, 6,…
    ## $ Number_tree_individuals <int> 28, 10, 6, 0, 8, 0, 15, 9, 3, 14, 11, 4, 10, 4…
    ## $ Height_tallest_tree     <dbl> 18.0, 23.5, 14.0, 18.0, 14.0, 0.0, 23.0, 29.5,…
    ## $ Average_Canopy_Height   <dbl> 10.437500, 15.750000, 2.600000, 0.000000, 1.00…
    ## $ SE_canopy_height        <dbl> 2.871504, 1.322876, 4.241331, 0.000000, 3.0000…
    ## $ Average_canopy_density  <dbl> 91.29, 88.38, 28.32, 8.22, 14.54, 0.16, 83.49,…
    ## $ SE_canopy_density       <dbl> 3.90, 2.72, 31.94, 16.89, 21.06, 0.00, 5.16, 3…
    ## $ sp                      <dbl> 11, 26, 29, 17, 23, 21, 22, 28, 19, 29, 18, 13…
    ## $ sp_richn                <dbl> 11, 26, 29, 17, 23, 21, 22, 28, 19, 29, 18, 13…
    ## $ fdis                    <dbl> 0.3207548, 0.2738122, 0.3151912, 0.2776704, 0.…
    ## $ fmpd                    <dbl> 0.2924386, 0.2275397, 0.2775328, 0.2519361, 0.…
    ## $ fnnd                    <dbl> 0.23253838, 0.12218594, 0.14669359, 0.18901822…
    ## $ feve                    <dbl> 0.578803, 0.539716, 0.687035, 0.759605, 0.6989…
    ## $ fric                    <dbl> 0.25538461, 0.24180153, 0.30443761, 0.18904734…
    ## $ fdiv                    <dbl> 0.7443045, 0.7759855, 0.6923737, 0.7109895, 0.…
    ## $ fori                    <dbl> 0.02777623, 0.03697858, 0.05386145, 0.05040421…
    ## $ fspe                    <dbl> 0.2177709, 0.1933580, 0.2212460, 0.1853558, 0.…
    ## $ fide_PC1                <dbl> -0.004322511, -0.008187404, 0.044747323, 0.027…
    ## $ fide_PC2                <dbl> 0.021460062, 0.025793707, 0.020780864, 0.02219…

### Phylogenetic richness

We have now described our data in two ecologically relevant ways. The
first is by species richness, and the second an estimate of how those
species represent different ecological units in the community. From a
conservation perspective, the species themselves are really
representations of ecological function but also evolutionary
distinction. Species names don’t neccesarily tell us the evolutionary
uniqueness of the community, but we can use methods from phylogenetics
to assess phylogenetic diversity using evolutionary or phylogenetic
trees.

While a number of phylogenetic trees have been published, we will make
use of a tree that was recently assembled to make use of the most modern
interpretation of 5 previously published trees. Methods to assemble
these data into a consensus tree are found
[here](http://blog.phytools.org/2016/04/consensus-methods-and-computing-average.html)

    [Link to Lum et
al. 2022](https://royalsocietypublishing.org/doi/10.1098/rspb.2022.0088)

    [Link to data from Lum et
al. 2022](https://doi.org/10.6084/m9.figshare.c.5923562.v1)

As a quick aside, these data come from a paper that sought to summarize
the accumulation of phylogenetic diversity over time since Linnaean
taxonomy (e.g., genus_species) was introduced 1758. 50% of known PD for
birds was already described by ~1828.

![](../images/lum.png)

Measuring phylogenetic diversity by summing unique branches in each
phylogentic tree.

``` r
#install.packages("phytools")
library(phytools)
```

    ## Loading required package: ape

    ## 
    ## Attaching package: 'ape'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     where

    ## Loading required package: maps

    ## 
    ## Attaching package: 'maps'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     map

The first problem we have is that the names in our phylogenetic tree are
different than the names in our dataset. Each uses a different global
bird taxonomy. This is such a common problem that I have never once
encountered an analysis where the names matched arcoss global datasets.
Luckily, many lists provide a comparison between their names and the
names used by others. We will match the species names in our dataset
with those in the phylogeny by using a dataset of these comparisons
found
[here](https://www.worldbirdnames.org/new/ioc-lists/master-list-2/).

``` r
ioc_taxonomy <- read.csv("../data/IOC_13.2_vs_other_lists.csv", stringsAsFactors = F)

ioc_taxonomy <- ioc_taxonomy %>% filter(Rank == "species")

ebird_to_HM_taxonomy <- ioc_taxonomy[,c(6,9)]

ebird_to_HM_taxonomy[ebird_to_HM_taxonomy[,1] == "",1] <- NA
ebird_to_HM_taxonomy[ebird_to_HM_taxonomy[,2] == "",2] <- NA
ebird_to_HM_taxonomy <- na.omit(ebird_to_HM_taxonomy)

colnames(ebird_to_HM_taxonomy) <- c("ebird", "hm")

ebird_to_HM_taxonomy$ebird <- sub(" ","_",ebird_to_HM_taxonomy$ebird)
ebird_to_HM_taxonomy$hm <- sub(" ","_",ebird_to_HM_taxonomy$hm)

species <- sub(" ","_",species_names$SCI_NAME)
```

``` r
files <- list.files(path="../data/final_merged_trees", pattern="*.tre", full.names=TRUE, recursive=FALSE)
phy_birds <- list()
pd <- c()
for (i in 1:length(files)){
  phy_bird_tree <- read.tree(files[[i]])
  
  phy_bird_tree$tip.label <- plyr::mapvalues(phy_bird_tree$tip.label,
                                             from = ebird_to_HM_taxonomy$hm, 
                                             to = ebird_to_HM_taxonomy$ebird,
                                             warn_missing = F)
  
  phy_bird_tree = bind.tip(phy_bird_tree, "temp_edge", edge.length = 0, position = 0)
  
  removals <- phy_bird_tree$tip.label[!phy_bird_tree$tip.label %in% species]
  
  phy_birds[[i]] = drop.tip(phy_bird_tree, removals)

}

class(phy_birds) <- "multiPhylo"
```

``` r
#avg_phy_birds <- averageTree(phy_birds, start=NULL, method="quadratic.path.difference")

pd_mat <- matrix(nrow = length(phy_birds), 
                 ncol = length(point_names))

for (i in 1:length(point_names)){ #start the for loop
  
  tips <- coffee_species %>% # grab our original dataset
    filter(Point_Name == point_names[i]) %>% # filter the dataset for each point name
    distinct(SCI_NAME)
  
  tips <- sub(" ","_",tips$SCI_NAME)
  
  branch_length <- c()             
  for (z in 1:length(phy_birds)){
    
      removals <- phy_birds[[i]]$tip.label[!phy_birds[[i]]$tip.label %in% tips]
      
      loca_phy = drop.tip(phy_birds[[i]], removals)
      
      branch_length[z] <- sum(loca_phy$edge.length)
  }
  pd_mat[,i] <- branch_length
  
  #print(i)
}
```

``` r
mean_pd <- apply(pd_mat, 2, mean)
```

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
