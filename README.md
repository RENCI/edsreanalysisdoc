# Accessing the RENCI/NOAA Reanalysis Datasets, V0.3, 8 Sep 2022
## Overview
The Reanalysis project generates ADCIRC model output in netCDF format on the NOAA HSOFS grid.  The files are served via a THREDDS Data Server (see below for details).  Each year is stored as a separate set of files, and extracting timeseries at user-specified locations across multiple years is cumbersome.  To faciliate easier access, the netCDF files have been "chunked" along the time dimension and the data variables transposed (nodesXtime instead of timeXnodes).  This substantially speeds up extraction of data along the time dimension.  The reorganized files have different filenames from the default ADCIRC names.  E.g., fort.63.nc is renamed fort.63.d0.no-unlim.T.rc.nc.  The latter files are accessed in the notebook. 

RENCI has written a python utilities package that allows users to extract timeseries at (relatively) arbitrary points in the ADCIRC grid.  This package performs element searches to locate a point within the grid, extracts time series at the element vertices, and interpolates to the point.  The package hides the details of this process and provides high-level functions taht can be called directly.  This approach is detailed below.  

RENCI has also developed a demonstration Jupyter/python notebook for accessing the Reanalysis data.  The notebook allows a user to extract timeseries from the Reanalysis by specifying a range of years, a set of lon/lat points, and for several model variables. 

## Getting started
 The notebook and supporting python class and utilities is available at [git@github.com:RENCI/EDSReanalysis.git](git@github.com:RENCI/EDSReanalysis.git).

To use this notebook, the user needs to: 
1. clone the repository
2. make a python environment with the requirements as specified in the requirements.txt file
3. Install jupyter components in the new environment
4. Launch jupyter
5. Navigate to and launch the ReanalysisDataExtractorDemoInterface notebook. 
6. The notebook contains cells that take User Input, extract the data, and provide download links to the resulting csv files.  The user can also investigate the results within the notebook, such as making a simple plot.  

The core components of the notebook are in a utilities.py package that can be called directly by the user (i.e., outside of the notebook) for more detailed data extractions.  The **examples** directory contains information on how to do this.

**We also are planning to deploy the notebook via Binder for more direct and easier access to the demonstration.**

### Currently available variables
1. water level [m MSL]
2. significant wave height [m]
3. offset/correction [m]

## Project THREDDS Data Server

http://tds.renci.org/thredds/catalog/Reanalysis.html

There two sub-catalogs.  

1. **Forcing**: The Forcing directory contains **Winds** and **DynWatLevCor**.  **Winds/ERA5** contains the subsetted ERA5 wind and pressure fields in ADCIRC's OWI format covering the ADCIRC domain at .25 deg spatial and 1-hr temporal resolution.  Each file contains 13 months of ERA3 output;  the 12 months for the specific year, and the prior year's December.  This allows up to spin up each year with the prior December and run many years concurrently.  Note that these files are not in netCDF format, and hence cannot be served via OpenDap requests.  However, the fileServer request can be used to download the files directly.  Utilities such as wget can also be used to get the files based on the calatlog url to the file.  

    For example, the url for the 1979 pressure file is: 

    http://tds.renci.org/thredds/fileServer/Reanalysis/Forcing/Winds/ERA5/1979/1979.221

    The **DynWatLevCor/ERA5/hsofs** directory contains the offset surface files used to force the posterior simulations.  These files contains daily averaged error surfaces on the ADCIRC grid nodes (specifically, on the HSOFS grid).  wget can also be used to download these files if needed.

2. **ADCIRC**: The **ADCIRC/ERA5/hsofs** directory contains the model output for each simulation year, for both the *prior* and *posterior*.  The posterior is named "YYYY-post".   


File formats
--------

Access
--------
* Python - See notebook details above. 
* MATLAB - The same extraction process can be performed in MATLAB by leveraging the 
[adcirc_util](https://github.com/BrianOBlanton/adcirc_util.git) toolbox.   Clone the adcirc_util repo and add its path to the MATLAB path.  In the startup.m file, add the folloeing: 

```
global ADCIRC
ADCIRC=<PathTo_adcirc_util>;
addpath(ADCIRC)
AddAdcircPaths(ADCIRC)
```

The [nctoolbox](git@github.com:nctoolbox/nctoolbox.git) is also needed to access the netCDF files on the remote TDS. 

The file [ReanalysisMatlabDemo.m](https://github.com/RENCI/EDSReanalysis/blob/main/ReanalysisMatlabDemo.m) describes how to extract data from the Reanalysis in  MATLAB.  This file is also available in the EDSReanalysis repo. 
