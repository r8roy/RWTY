# Update Documentation

The original documentation for every function can be found [here](https://cran.r-project.org/web/packages/rwty/rwty.pdf). I will only primarily note our new additions.

## Generating ESS data

Here are the main functions to be used for generating raw data (all take a list of ```rwty.chain``` objects as input):

```topological.pseudo.ess()```: computes pseudo ESS the specified number of times with different focal trees and outputs a dataframe containing the pseudo ESS values for each chain.  
```topological.autocorr()```:  outputs a dataframe containing the average distance between pairs of trees at the specified number of different sampling intervals for each chain.  
```topological.approx.ess()```: outputs a dataframe with approximate ESS for each chain.

These all work as specified in the original docs, except:
- ```treedist``` can now be any of "PD" (for path distance),  "RF" (for Robinson Foulds distance), "WRF" (for weighted RF),  "JRF" (for Jaccard-Robinson-Foulds), "KF" (for Kuhner-Felsenstein) or "KC" (for Kendall-Colijn) or a user-defined distance function. RF, WRF and KF are implemented in phangorn, whereas PD is implemented in RWTY and JRF is implemented in TreeDist (notably without arboreality as in [Böcker, Canzar, Klau 2013](https://arxiv.org/abs/1307.7824)). A user-defined distance function can be any function which takes two ```phylo``` objects (from ape) as input and outputs a number.  
- There is now a ```output.file = NA``` parameter which specifies where to write the results of the calculations (.csv format). If ```NA``` then only outputs the R dataframe. 

## ESS Plots

```makeplot.autocorr()``` generates the autocorrelation plot for the given list of chains.  
```makeplot.topology()``` generates a topology trace plot using the specified distance for each chain.  
```makeplot.pseudo.ess()``` generates  a plot showing pseudo ESS and confidence intervals for each chain.  
For each of these functions:
- ```chains``` can now be a dataframe outputted by the relevant function (```topological.pseudo.ess```, ```topological.autocorr```, ```topological.approx.ess```) or a filepath to a .csv containing the calculation data. This is so values can be computed and stored separately.  
- There is an added ```check.chains = TRUE``` parameter, which when set to ```FALSE``` will allow facet plots of MCMC runs from trees on different taxa (this may be to correct an error from the original package, meaning this should have been possible even with checks)

## Hard-coding new distances

The functions for ESS calculation described above can now accept a user-defined distance function as the ```treedist``` parameter. If desired, one can also hardcode in a distance along with a new keyword for its selection ```treedist```. [This commit](https://github.com/r8roy/RWTY/commit/28ca5c1ba07fb0df03a9594938c1028f338074dd) makes exactly the required changes for adding Kuhner-Felsenstein with keyword "KF" and should be used as a guide. In general one must:
1. Given distance metric```f```, add ```f.distance```, ```f.dist``` and ```f.dist.squared``` functions in ```topological.autocorr.R```

2.  Change if-blocks in ```topological.autocorr.R``` ```topological.pseudo.ess``` ```makeplot.autocorr.R``` to use the right version of```f``` as defined in step 1 given a desired keyword.
