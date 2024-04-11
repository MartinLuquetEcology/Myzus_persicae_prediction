# Myzus_persicae_prediction
Predicting the seasonal flight activity of Myzus persicae in sugar beet. Data and code associated with https://doi.org/10.1002/ps.7653

This repository can be found on Recherche Data Gouv, the French platform for Open Science. You can find it here (V2) : https://doi.org/10.57745/XPBKLV.

Author: Martin LUQUET

Contact information : martin.luquet.pro@gmail.com, martin.luquet@inrae.fr (prefer the first one)

The repository includes all of the R scripts and data files required to reproduce the analyses and figures presented in the paper "Predicting the seasonal flight activity of Myzus persicae, the main aphid vector of Virus Yellows in sugar beet" from Luquet et al. 2023 in Pest Management Science.
Weather data is missing, as we are not allowed to share them, but the structure of these data is explained (see below).

## Quick overview of the main repository

```  
 Myzus_persicae_flight_prediction/
   Bash_scripts/
   Data/
     France_map/ 
     weather_data/
   Figures/
     Supplementary_figures/
   Outputs/
   Processed_data/
   R/
     Figures/
     Functions/
     Modelling/
        Parallelized/
     Output_analysis/
   Supplementary_material/
```   

'Myzus_persicae_flight_prediction' includes the following elements, whose content is further detailed in next sections.

-   The present 'README.txt' file details the content of the R scripts and files of this repository.
-   The 'LICENSE_GNUGPLv3.txt' file specifies the license applied on the code contained in the repository. The license applied is GNU General Public License v3.0 (GNU%20General%20Public%20License%20v3.0).
-   The 'PackageVersions.txt' file specifies the versions of all used R packages.
-   The 'Myzus_persicae_flight_prediction.Rproj' is an R project file. When launched in R, it allows to readily reproduce analyses.
-   The folder 'Bash_scripts' includes the Bash Scripts sent to the HPC cluster hosted by GenOuest (https://www.genouest.org/)
-   The folder 'Data' includes the data presented in the paper. This includes aphid flight data coming from the Agraphid suction trap network, as well as GIS data to produce France maps. This also includes metadata and applicable licenses.
	- Note that this does NOT include the meteorological data coming from the SAFRAN system that we used in the analysis. We are not allowed to share this data hosted by Météo-France. See next section ('Data & metadata files') and 'Data/weather_data/README_weather_data.txt'.
-   The folder 'Figures' includes the figures from the paper
-   The folder 'Outputs' is empty, but is meant to store all outputs generated from the analysis. It is empty here as it represents several Go but if the analysis is reproduced, outputs will automatically be stored here.
-   The folder 'Processed_data' includes processed data generated throughout the analysis.
-   The folder 'R' includes the R scripts needed to reproduce the analysis.
-   The folder 'Supplementary_material' includes two RMarkDown files used to produce Appendix S3 and S4.

The following analyses were performed with R version 4.2.1 (2022-06-23 ucrt) -- "Funny-Looking Kid"
R Core Team (2022). 
R: A language and environment for statistical computing. 
R Foundation for Statistical Computing, Vienna, Austria. 
URL https://www.R-project.org/.
The versions of the packages used are specified in the txt file 'PackageVersions.txt'.

## Data & metadata files

The 'Data' folder includes:

- The 'France_map' folder, including
	- 'french_depts.csv' is a file indicating whether French regions are included in the sugar beet area of production (used to generate Fig. 1).
	- all other files are GIS files corresponding to the map of France, used to generate Figure 5.
		- These come frop OpenStreetMap (https://www.openstreetmap.org/#map=6/46.449/2.210)
			- © les contributeurs d'OpenStreetMap sous licence ODbL
		- These were downloaded from https://www.data.gouv.fr/fr/datasets/contours-des-regions-francaises-sur-openstreetmap/
		- The licence is ODbL 1.0 and is detailed in 'LICENCE.txt' (see also http://osm.org/copyright)
		- File description (in French) is in 'regions-descriptif.txt'

- The 'weather_data' folder containing a unique .txt file, 'README_weather_data.txt'.
	- Initially, it contained two .RDS files, 'cumulated_sums.RDS' and 'cumulated_sums_all.RDS', contaning the weather data used in the analysis. These contained R lists storing cumulative temperature and mean precipitation over various periods.
	- This data is confidential (Météo-France) and cannot be shared.
	- The precise content of these .RDS files, as well as the list structure needed to reproduce this analysis is explained in 'README_weather_data.txt'.

- Three .csv files containing data from the Agraphid suction trap network.
	- 'trap_summary.csv' is a small dataset describing some characteristics of the Agraphid traps.
	- 'myzus_params.csv' and 'myzus_params_north.csv' contain the aphid flight data as well as predictors.
		- 'myzus_params.csv': all Agraphid traps
		- 'myzus_params.csv': the 10 traps belongig to the French area of sugar beet production	
		- Note that these are NOT the raw Agraphid data, which we are not allowed to share. The data was already processed to generate aphid flight features for each trap-year.
	- The 'ETALAB_2.0.txt' file specifies the license applied on the data and metadata contained in the repository. The license applied is Etalab Open License 2.0 ([etalab-2.0)](https://spdx.org/licenses/etalab-2.0.html)).

- The 'agraphid_metadata.txt' file describes these three datasets.

## Analysis

To reproduce the analysis described in the paper, here is the procedure:

- First, we advise to open the 'Myzus_persicae_flight_prediction.Rproj' R project in the main folder. That way, the analysis can be reproduced by directly lauching the scripts, without bothering about working directories.

### Data modelling

- model_training.R in the 'R/Modelling' folder allows generating all the models described in the paper. This represents several hundreds of models and can take several days. To go faster, the exact same analysis can be run
using parallelization (see next line). If the script is launched, generated outputs will automatically be stored in the 'Outputs' folder as tables containing model performances. We kept the 'Outputs' folder empty here as outputs represent several Go.

- All 4 .R scripts in the 'R/Modelling/Parallelized' can be used to generate the same outputs, faster. It uses mclapply() from the 'parallel' package but only works on UNIX.

- The scripts can be sent to an HPC cluster to be made way faster. We used the bash scripts located in 'Bash_scripts' to run them on GenOuest (https://www.genouest.org/).

### Gathering and analysing outputs

- Once the outputs are obtained and stored in 'Outputs', they can be gathered to pick the best models for each response, prediction date and model structure (M1, M2, M3) as described in the paper. This is done by running 'R/Output_analysis/saving_best_models'.
	- These are then stored in 'Processed_data/selected_models.RDS'.

- From there, different analyses are carried out in the scripts contained in the 'R/Output_analysis' folder.
	- 'performance_tables.R' summarises performance from all models.
	- 'predictor_parameters.R' investigates the variation in estimates across models and prediction dates.
	- 'performance_spatial_variation.R' investigates the spatial variation in model performances, inside and outside the French area of sugar beet production.

## Reproducing Figures and Supplementary Files

- All Figures presented in the paper can be generated with the scripts in 'R/Figures'. Figure 1 was post-processed on GIMP v. 2.10 using 'Figures/Figure_1_GIMP_20230531.xcf'.

- Similarly, supplementary files 1 and 2 can be reproduced with the R Markdown Files in 'Supplementary_material'.

Note that all analyses use custom functions located in 'R/Functions'.

# Licenses
The data and metadata stored in the folder 'Myzus_persicae_flight_prediction' (derived from Luquet et al. 2023) are licensed under 
	- the Etalab Open License 2.0 ([etalab-2.0)](https://spdx.org/licenses/etalab-2.0.html)) for the Agraphid data
	- the ODbL 1.0 for the OpenStreetMap data (http://osm.org/copyright)
All the R codes used to format the data, perform the data analyses and visualization are licensed under the [GNU General Public License v3.0](GNU%20General%20Public%20License%20v3.0).
