1. System Requirements

-	Python 3 or above.
-	Conda environment: pcrglobwb_python3, which includes modules such as PCRaster, netCDF4 etc.
-	Numpy 2.0.0 is required for the Random Forest component of the hybrid framework.

2. Installation guide

DynQual v1.1 can be installed following similar steps as for DynQual v1.0 (https://github.com/UU-Hydro/DYNQUAL). Within DynQual v1.1, water temperature (Tw) is simulated by solving the surface water energy balance, dissolved oxygen (DO) is simulated by solving the Streeter-Phelps equation, while TDS, BOD and FC concentrations are simulated accounting for both the dilution capacity and in-stream decay processes.

2.1. Clone or download the DynQual v1.1 repository into the current working directory:

git clone https://github.com/Graha010/DYNQUAL.git 

2.2. You will need a working Python environment, we recommend to install Miniconda, particularly for Python 3. Follow their instructions given at https://docs.conda.io/en/latest/miniconda.html. The user guide and short reference on conda can be found at https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html.

2.3. Install all modules required (e.g. PCRaster, netCDF4) for running DynQual v1.1 by creating a conda environment using the following command (Note: make sure you navigate to the DYNQUAL folder before running this line. You only need to create the environment once and it will take ~10 minutes):
 
conda env create --name pcrglobwb_python3 -f pcrglobwb_py3.yml

2.4. Activate the environment in a command prompt (Note: you may first need to run the command ‘conda init’ and restart your shell):

conda activate pcrglobwb_python3

2.5. DynQual configuration .ini file

For running DynQual v1.1, a configuration .ini file is required. In this file, you provide all the necessary information related to your run (e.g. time period, study extent, etc) and paths to your input data (e.g. climatological forcing, land cover, etc). 

Some example configuration .ini files are provided in the ‘ini’ directory, both for DynQual runs that are one-way coupled to PCR-GLOBWB2 (‘Online’) and for runs using hydrological input as a forcing (‘offline’). We provide all the required input data and .ini files for the Rhine-Meuse basin which can be downloaded to your local machine (~6 MB), facilitating a self-contained example run (https://doi.org/10.5281/zenodo.13895791). Input data for the DynQual runs at the global extent are available on the OPeNDAP server: (https://opendap.4tu.nl/thredds/catalog/data2/pcrglobwb/catalog.html) – allowing for users to run DynQual v1.1 for any land area without needing to download the input files (which exceed 250GB). 

Some adjustments must be made:
-	inputDir = : this must be set to the directory where the input data is stored.
-	outputDir = : this must be set to a directory you can access.
-	cloneMap = : The cloneMap file defines the spatial resolution and extent of your study area and must be in the pcraster format. Please make sure this is stored locally in your computer. Some examples are given in this repository https://github.com/UU-Hydro/PCR-GLOBWB_model/blob/master/clone_landmask_maps/clone_landmask_examples.zip

Other adjustments are optional (e.g.):
-	startTime = and endTime = : denote your simulation period.
-	precipitationNC = : denotes the location of your precipitation input data file, relative to inputDir.
-	outDailyTotNC = : denote which variables you to write to netcdf at daily resolution to outputDir/netcdf/.

Some DynQual specific adjustments can also be made (e.g.):
-	calculateLoads = : Set to TRUE for simulating pollutant loadings, FALSE for prescribing (pre-calculated) loadings. Note: TRUE is only avaliable for online DynQual runs.
-	loadsPerSector = : Set to TRUE for segregation of pollutant loadings per sector (i.e. for attribution), FALSE for combined loadings only. Note: TRUE is only avaliable for online DynQual runs.

When adjusting input data files in your .ini file, remember to check the units.

When adjusting which output data to report in your .ini file, remember to ensure you are only reporting simulated variables.

2.6. Running DynQual

Ensure the correct conda environment in a command prompt: conda activate pcrglobwb_python3 

Navigate to the main DynQual model directory (DYNQUAL). You can start a DynQual run using the following commands:

For online run: python DynQualModel/deterministic_runner.py <ini_configuration_file>

For offline run: python DynQualModel/deterministic_runner_offline.py <ini_configuration_file>

where <ini_configuration_file> is your configuration file of DynQual.


2.7. Running DynQual_Random Forest

To simulate the hybrid model (DynQual_Random Forest), the required variables from DynQual v1.1 are: waterTemperature, BOD_concentration, discharge and dissolved_oxygen. Download the random forest ‘RF_Final_1.joblib’ from https://doi.org/10.5281/zenodo.13329996 under ‘Random_Forest.zip’. The hybrid DynQual_Random Forest model can then be applied to simulate dissolved oxygen concentration by running Script_3a.py to Script_3e.py from the folder ‘Historic_analyes.zip’ (https://doi.org/10.5281/zenodo.13329996). Note that numpy version 2.0.0 is necessary to use the RF_Final.joblib file. The hybrid output from these scripts will be in numpy format (.np), and these files can be converted to NetCDF (.nc) using Script_4_pt1.py to Script_4_pt5.py. 

3. Demo

The files ‘Europe_DO.nc’ and ‘Nile_DO.nc’ (https://doi.org/10.5281/zenodo.13875933) provide the hybrid dissolved oxygen outputs for the land masks associated with central Europe and the river Nile in Africa, respectively. These NetCDFs can be used to demonstrate the analyses scripts. Using the scripts Script_W_Historic.py and Script_Y_Historic.py from ‘Historic_analyses.zip’ (https://doi.org/10.5281/zenodo.13329996), it is possible to estimate (i) mean daily dissolved oxygen during 1980-2019; (ii) average trends in stress days (DO < 5 mg/l) and hypoxia days (DO < 3 mg/l). Note that the input file directories need to be changed in line with ‘Europe_DO.nc’ and ‘Nile_DO.nc’. These scripts form the basis of the analysis and are also transferable for the future period (2020-2100). The expected run time of these demos is quick (approximately 10 mins) on a standard computer. 

4. Instructions for use

Instructions for the full reproduction of the quantitative aspects of the paper are provided in the full set of scripts (https://doi.org/10.5281/zenodo.13329996). These scripts contain the DynQual v1.1 model (https://github.com/Graha010/DYNQUAL, which is applied globally), and Random Forest component (RF_Final_1.joblib), and all the python scripts that were used throughout the study for past (1980-2019) and future conditions (2020-2100). The daily dissolved oxygen output from DynQual_Random Forest over the period 1980-2100 will be provided on Yoda (https://geo.data.uu.nl/research-dynqual-randomforest/) (~10 TB), and the scripts from Zenodo can be applied to determine the same outputs from this study in terms of changes in dissolved oxygen, stress days (DO < 5 mg/l), hypoxia days (DO < 3mg/l) and the associated impacts on freshwater fishes.
