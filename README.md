# UCSB-SI Scholars Summer Trip to STRI in Panama

This repository contains instructional materials for the UCSB-SI Scholars trip to Panama for summer 2023. We expect these materials to be entirely executable in either R Studio or jupyter notebooks using the [Smithsonian Data and Compute Hub (SDCH)](https://smithsonian.2i2c.cloud/). SDCH provides access to both course data and the necessary compute environment - students are required to select the necessary server option when logging in to SDCH. The exact server option will be specified at in any .Rmd or .ipynb contained in this repository. 

--------

**Instructors**:
* Mario Castellanos - Executive Director, Office of Education Partnerships, UCSB (mario.castellanos@ucsb.edu)
* Ruth Bennett - Research Wildlife Biologist, Migratory Bird Center, Smithsonian National Zoo and Conservation Biology Institute (bennettr@si.edu)
* Jesus Maldonado - Research Geneticist, Center for Conservation Genomics, Smithsonian National Zoo and Conservation Biology Institute (maldonadoj@si.edu)
* Scott Sillett - Research Wildlife Biologist, Migratory Bird Center, Smithsonian National Zoo and Conservation Biology Institute (silletts@si.edu)
* Cristina Soto-Balderas P20 Coordinator, Office of Education Partnerships,(cristina.soto.balderas@ucsb.edu)
* Mark Torchin - Research Ecologist, Smithsonian Tropical Research Institute (torchinm@si.edu)
* Alex White - Biodiversity Research Data Scientist, Smithsonian Data Science Lab, Smithsonian Office of Research Computing, OCIO (whiteae@si.edu)

**Peer Mentors:**
* Dalila Lara (dalilalara@ucbs.edu)
* Zach Wilson (zacharycwillson@gmail.com)
* Richie Montes Lemus (richard.montes.lemus@gmail.com)

--------

## Course Framework

The UCSB-SI Scholars STRI trip combines field excursions, informal biological observation/data collection, site visits to STRI field research stations, and data science/quantitative ecology workshops to expose students to the array of science opportunities available at STRI and similar locations in the conservation and biodiversity science universe. In 2023 we intend to take advantage of the fact that students will arrive in Panama with a robust introduction to R via their ERES summer workshop experience (which for most students took place in 2022). Data Science workshops will therefore focus on more advanced computational skills and lean more towards instruction pertaining to specific scientific concepts rather than a pure coding skills bootcamp. We expect to combine field data collection, acquisition of global datasets via the web, using R as a GIS to analyze geographic data and produce map based visualizations, and leveraging the tropics to better understand effective conservation strategies and the basic ecological and evolutionary mechanisms underpinning patterns of global biodiversity. 

--------

The conceptual backdrop of these workshops will lead students through computational and field exercises addressing a sequence of 3 scientific ideas/concepts/questions:

1. Why the "tropics"? What makes the tropics unique from a conservation, ecology, and evolution perspective? What unites the Panamanian tropics with other tropical regions around the world. [keywords: Tropical Biology, Conservation Biology, Biogeography, Latitudinal Diversity Gradient]
2. How can we quantify the differences between ecological communities based on the species they contain? How are sun coffee, shade coffee, and forest bird communities different from one another? Do ecological or evolutionary differences between bird species lead to different conclusions about conservation practices for tropical agriculture. [keywords: Agroforestry, Quantitative Ecology, Functional Diversity, Phylogenentic Diversity]
3. What are the gross ecological differences between Barro Colorado Island and Santa Cruz Island? How can we quantify those differences using plant or bird community datasets? Can we visualize the differences between those communities based on the geographic distributions of the species they contain? How might climatic differences between the two islands (e.g., precipitation and precipitation seasonality) help us understand the ecological differences. Historical differences?

Instructors and students will investigate these questions together first in a short series of introductory talks, later by using the Merlin phone app to collect field census of birds in Gamboa and Pipeline road, and finally by taking advantage of public and Migratory Bird Center datasets to develop quantitative ecology skills and better understand metrics of biodiversity. In the latter half of the trip, students will have the option to use eBird data or as yet to be define marine community data to develop a group-based final project compare BCI and Santa Cruz Island using the skills they develop in the first week. 

------------
### Leveraging published datasets to examine the impacts of coffee cultivation practices on birds

One of the first objectives for our students will be to combine field data collected by Dr. Bennett and her colleagues with published global data sets of avian biodiversity. Students will learn first hand how field data can be combined with published work to examine specific questions (in the case tropical agro-forestry practices and their impacts) that were originally outside the purview of the published work. Students will gain skills in identifying and downloading published datasets from online repositories, combining those data with data collected from the field, and using this combination to assess the impacts of coffee cultivation practices on the biodiversity of birds in coffee plantations. Students will have censuses of bird species in forest, sun coffee, and shade coffee field sites. These species lists will be used to measure and compare biodiversity at the field sites using the following datasets: 

#### Functional Diversity of Birds
1. The first dataset represents trait data for global bird species (both morphological measurements and functional traits). This work combines measurements of museum specimens and field caught individuals of all bird species of the world with known information about their ecological traits:

&emsp; &emsp; [Link to Tobias et al. 2022](https://onlinelibrary.wiley.com/doi/full/10.1111/ele.13898)  

&emsp; &emsp; [Link to data from Tobias et al. 2022](https://figshare.com/s/b990722d72a26b5bfead)

```
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
```

#### Phylogenetic Diversity of Birds
2. The second dataset represents all known evolutionary relationships between the birds of the world. While a number of phylogenetic trees have been published, we will make use of a tree that was recently assembled to make use of the most modern interpretation of 5 previously published trees. Methods to assemble these data into a consensus tree are found [here](http://blog.phytools.org/2016/04/consensus-methods-and-computing-average.html)

&emsp; &emsp; [Link to Lum et al. 2022](https://royalsocietypublishing.org/doi/10.1098/rspb.2022.0088)  

&emsp; &emsp; [Link to data from Lum et al. 2022](https://doi.org/10.6084/m9.figshare.c.5923562.v1)  

```
Lum, Deon, Frank E. Rheindt, and Ryan A. Chisholm. "Tracking scientific discovery of avian phylogenetic diversity over 250 years." Proceedings of the Royal Society B 289.1973 (2022): 20220088. https://doi.org/10.1098/rspb.2022.0088

@article{lum2022tracking,
  title={Tracking scientific discovery of avian phylogenetic diversity over 250 years},
  author={Lum, Deon and Rheindt, Frank E and Chisholm, Ryan A},
  journal={Proceedings of the Royal Society B},
  volume={289},
  number={1973},
  pages={20220088},
  year={2022},
  publisher={The Royal Society}
}
```
#### Geographic Distributions of Birds
3. The third dataset we intend to leverage is a geodatabase of the geographic distributions of all bird species of the world. Each species range is mapped as a polygon and can be used to generate global gridded presence/absence data for each bird species censused in a given local community. Mapping the broad geographic distributions of species that are co-located in a specific field site gives an understanding of the geographic differences between communities based on how the species are ranged elsewhere. Species lists which entirely non-overlapping may generate very similar lists, thus indicated local partitioning of habitat by distinct communities. Such map-based visualizations are called "assemblage dispersion fields" (White et al. 2019).     

&emsp; &emsp; [Link to White et al. 2019](https://www.nature.com/articles/s41467-019-10253-6)   
&emsp; &emsp; [Link to BirdLife International geodatabase request](http://datazone.birdlife.org/species/requestdis)  




