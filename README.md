Wettest MSAs by Population
=====================
Analysis that sorts all metropolitan statistical areas (MSAs) in the United States by wettest population during May of 2015.  The population wetness of an MSA is calculated as the number of people in the MSA times the amount of rain received.  For the purposes of this exercise, assume that all people remain inside between the hours of 12 AM and 7 AM local time and so rainfall during these hours does not count.

## Requirements

```
python 3.5 
[jupyter notebook](http://jupyter.org/install.html)
pandas >= 0.20.1
matplotlib >= 2.0.2
geopandas >= 0.2.1
```

## Quick Start
1. Make sure to download the data. Should only have to do this once.

`./data.sh`

2. A docker image is provided that satisfies the above requirements.

`docker pull bpburns/geopandas-notebook`
(May take a few minutes)

`./run.sh`

Point your web browser to your docker host's ip at port 8888.  Load the notebook "Wettest MSA.ipynb"

## Run Without Docker

Ensure the requirements and launch the jupyter notebook environment from this directory and open the ```Wettest MSA.ipynb``` notebook.

```
jupyter notebook \
  --NotebookApp.token='' \
  --NotebookApp.iopub_data_rate_limit=10000000000
```

## Run Without Jupyter

Ensure the required modules are installed.

`python wettest_msa.py`


## Data Sources
1. <b>201505precip.txt</b> -- Hourly precipitation totals for weather stations (WBANs) in the month of May 2017  
2. <b>201505stations.txt</b> -- Geo coordinates for each WBAN
3. <b>CPH-T-5.xls</b> -- Listing of MSAs and their populations
4. <b>cb_2015_us_cbsa_500k.shp</b> -- Shapefile containing the geographical boundaries of each MSA.
   
## General Strategy
The ```2015precip.txt``` file gives us the hourly precipitation totals for for each weather station (WBANs). The first step is to load the hourly precipitation data, filter out the blank precipitation values, filter out values between 12:00 and 7:00am and total them for each WBAN.

Next we need to find the locations for each WBAN. 2015stations.txt does that for us with longitude and latitude coordinates so we join that with our WBAN precipitation totals.

The next step is to figure out in which MSAs the WBANs belong. The U.S. Census Bureau provides mapping data with geographical boundaries for the MSAs in the form of shapefiles. We join the WBAN precipitation totals that contain our geo coordinates with the shapefile metadata to get our MSAs. There may be several WBANs within an MSA so we need to take the mean precipitation total for all the WBANs in a particular MSA.

The last step is to add the population data in order to perform our wettest population calculation. The file CPH-T-5.xls contains a list of MSA names and their populations according to the 2010 census. We do a join on MSA name with our precipitation totals and add a wetness to score.

## Technologies Used.
The choice of programming language and libraries boils to down to what is the right tool for the job. Typically in a data analysis exercise such as this 75% of the work is data preparation. You need to explore the data, figure out the format, look for bogus values and ensure data integrity. 

Heavily typed languages such as Java can sometimes make this difficult at first as you may not have all the type information. R and Python are frequently used in the data analytics space.  This particular analysis we will be using Python.

The data is not big enough to warrant a big data stack such as Apache Spark or other Hadoop based technologies. Python has a library ideally suited for this analysis called Pandas. Pandas contains a data structure called a DataFrame which stores data in a table like format with an easy to use API. DataFrames are very efficient as many numerical computations are done in native C.

Jupyter Notebooks are an ideal way to perform an analysis such as this. They allow the developer to present their findings with markdown cells along with the ability to display graphs, maps and arbitrary HTML.
