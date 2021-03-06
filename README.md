# Thermal_Ghost_sim
**Bryce M. Henson**   
Development of unit tests for calculating correlation functions and then to be used for numerical proof of principle for thermal ghost imaging.


Thermal ghost imaging exploits the correlations that exist between thermaly distributed particles(photons or atoms) in order to image an object with a single pixel detector. The setup involves splitting the input thermal atoms into two arms, one with a spatialy resolved detector and the other with the object and a single pixel detector behind it. Pervious work in our group has used more higly correlated source of metastable helium in order to perform "traditional" ghost imaging ([R.Khaimov,B. Henson, et. Al "Ghost imaging with atoms" Nature](https://www.nature.com/articles/nature20154),[Arxiv preprint](https://arxiv.org/abs/1607.02240)), the hope is that the dramaticaly higher flux of thermal atom sources may compensate for the decreased correlation amplitude.


## Simulation Results
| ![A comparison runtime for different hist algorithms](/results/10k_shots_10k_counts_1e-3_corr_len.png "Fig1") | 
|:--:| 
 **Figure 1**- Simulation of thermal ghost imaging with 10k shots (5k with mask,5k for normalization), 10k counts per shot and correlation width of 1e-3 (normalized to thermal width) **(with superthermal correlations)**. Top row is the raw coincidence density with the mask, lower row is divided by the same without the mask. |


## Goals
The goals of this project are
* Form a unit test for correlation functions that we use
  * colinear
  * back to back
  * would be nice if we could have a function that produces a well known g2 amplitude for us to compare with.
* Come up with an algorithm for constructing fake data with arbitrary correlations
  * Thermal correlations
  * Arbitrary function
  * nth order
* Demonstrate the many ways that correlation functions may be calculated and compare their scaling
  * Radial point by point
  * x,y,z point by point
  * x,y,z bin and fft
  * see if the [fast binning algorithms](https://github.com/brycehenson/fast_search_based_histogram) i have previously developed can be helpfull 
* Test if thermal ghost imaging is practicaly realizable in our system.
  * QE
  * FLux
  * Resolution

## To DO
- benchamrk other ndhitograming codes https://au.mathworks.com/matlabcentral/fileexchange/3957-ndhistc  
- build a correlation calculator library
- build a wrapper that deals with the chunking & normalization
  
  
## Methods
* Radial correlation
  * to give uniform error need to space the bins as 1/r^2
* cartesian
  * largre number of bins needed
  * can smooth before dividing
  * can integerate radialy to give the equivelent to Radial correlation

## Complexity
My thoughts on how the various methods scale. n counts in each of m shots, p bins for the correlation, in dimensionality d
* point by point
  * computing cartesian differences to all other points **O( m·d·((1/2)·n^2-n) )**
  * histogram differences **O( m·d·((1/2)·n^2-n)·(1 +log(p) +1  ) )** (differences,bin search,increment bin)
  * calcuate full normalization **O( ((1/2)·(m·n)^2-(m·n))·3·(1+log(p)) )**, in reality you only need a small fraction of the full counts to normalize with
* FFT
  * Bin **O( m·d·n·log(p) )**
  * FFT padded (may be a way to remove this factor of two) **O( m·( d·n·log(p)+ (2p)^d log((2p)^d) ) )**
  * sum up shots **O( m·( d·n·log(p)+ (2p)^d log((2p)^d) + p ) )**

  
  
  
  
## Install
``` 
git clone --recurse-submodules -j8  https://github.com/brycehenson/thermal_ghost_sim.git
```
then to update 
```
git submodule update --init --recursive 
git submodule foreach --recursive git pull origin master
```
  
  

## Contributions  
This project would not have been possible without the many open source tools that it is based on. In no particular order: 
* ***James Conder*** [gaussfilt](https://au.mathworks.com/matlabcentral/fileexchange/43182-gaussfilt-t-z-sigma)
* ***Ander Biguri*** [Perceptually uniform colormaps](https://au.mathworks.com/matlabcentral/fileexchange/51986-perceptually-uniform-colormaps)
* ***Benjamin Kraus*** [nanconv](https://au.mathworks.com/matlabcentral/fileexchange/41961-nanconv)
* ***Daniel Eaton***    [sfigure](https://au.mathworks.com/matlabcentral/fileexchange/8919-smart-silent-figure)
* ***Denis Gilbert***    [M-file Header Template](https://au.mathworks.com/matlabcentral/fileexchange/4908-m-file-header-template)
