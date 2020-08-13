
# Capstone Project: The Battle of Neighborhood
###  Recommending the Most Valuable Venue for Homebuyers in London

## Table of contents
* [Introduction: Business Problem](#introduction)
* [Data](#data)
* [Methodology](#methodology)
* [Analysis](#analysis)
* [Results and Discussion](#results)
* [Conclusion](#conclusion)

## Introduction <a></a>

### Background
According Working Paper 72 published by London Government, London’s house prices are relatively high comparing to other area in the UK, and have been rising at a fast rate recent years. Meanwhile, housing markets in London have witnessed a number of ups and downs, with volatile house prices in London tending to amplify changes in national house prices. London has experienced several episodes of real house price deflation. From the patterns of previous cycles, it is hard to simply tell whether house prices are approaching a new peak or a downward adjustment.

Recently as a result of the pandemic, increasing unemployment rate, wage cuts, business failures, and job uncertainty also brought huge impact to the house market in London. According to the Nationwide, house prices fell 1.7% in May from the previous month, the largest monthly fall for 11 years, due to the headwinds of Covid-19.

### Business Problem
As a matter of fact, it is of great necessity to integrate data science and machine learning technics with the study of London house market in order to assist clients making sensible and efficient choices.
How could we provide suggestions to our clients based on the study of neighborhoods for either settling or investing is our main business problem. We will recommend profitable venues according to amenities and infrastructures surrounded.

## Data <a></a>

London properties monthly updated price paid data is provided from the HM Land Registry.
Data link: http://landregistry.data.gov.uk/

After finding the list of neighborhoods, we then connect to the Foursquare API to gather information about venues inside each and every neighborhood. 
FourSquare API interface is a location data provider with information about venues. Data retrieved includes venue name, latitude, longitude, category, neighborhood and neighborhood location.  

### Properties Data

Let's first download the properties' data from the HM Land Registry website:
#### Import Libraries


```python
import os
import numpy as np
import pandas as pd
import datetime as dt # Datetime
import json # library to handle JSON files

!conda install -c conda-forge geopy --yes
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe

# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

!conda install -c conda-forge folium=0.5.0 --yes
import folium #import folium # map rendering library

print('Libraries imported.')
```

    Solving environment: done
    
    
    ==> WARNING: A newer version of conda exists. <==
      current version: 4.5.11
      latest version: 4.8.3
    
    Please update conda by running
    
        $ conda update -n base -c defaults conda
    
    
    
    ## Package Plan ##
    
      environment location: /home/nbuser/anaconda3_501
    
      added / updated specs: 
        - geopy
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        geographiclib-1.50         |             py_0          34 KB  conda-forge
        conda-package-handling-1.6.0|   py36h8c4c3a4_2         947 KB  conda-forge
        geopy-2.0.0                |     pyh9f0ad1d_0          63 KB  conda-forge
        python_abi-3.6             |          1_cp36m           4 KB  conda-forge
        conda-4.8.2                |           py36_0         3.0 MB  conda-forge
        ------------------------------------------------------------
                                               Total:         4.0 MB
    
    The following NEW packages will be INSTALLED:
    
        conda-package-handling: 1.6.0-py36h8c4c3a4_2 conda-forge
        geographiclib:          1.50-py_0            conda-forge
        geopy:                  2.0.0-pyh9f0ad1d_0   conda-forge
        python_abi:             3.6-1_cp36m          conda-forge
    
    The following packages will be UPDATED:
    
        conda:                  4.5.11-py36_0                    --> 4.8.2-py36_0 conda-forge
    
    
    Downloading and Extracting Packages
    geographiclib-1.50   | 34 KB     | ##################################### | 100% 
    conda-package-handli | 947 KB    | ##################################### | 100% 
    geopy-2.0.0          | 63 KB     | ##################################### | 100% 
    python_abi-3.6       | 4 KB      | ##################################### | 100% 
    conda-4.8.2          | 3.0 MB    | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done
    Collecting package metadata (repodata.json): done
    Solving environment: | 
    The environment is inconsistent, please check the package plan carefully
    The following packages are causing the inconsistency:
    
      - defaults/linux-64::pytest-doctestplus==0.1.3=py36_0
      - defaults/linux-64::patsy==0.5.1=py36_0
      - defaults/linux-64::pytest-arraydiff==0.2=py36h39e3cac_0
      - defaults/linux-64::hdmedians==0.13=py36h035aef0_0
      - defaults/linux-64::scikit-learn==0.19.2=py37h4989274_0
      - defaults/linux-64::bkcharts==0.2=py36_0
      - defaults/linux-64::blaze==0.11.3=py36_0
      - https://repo.anaconda.com/pkgs/free/linux-64::pymc==2.3.6=np111py36_2
      - defaults/linux-64::pandasql==0.7.3=py36_1
      - defaults/linux-64::mkl_random==1.0.1=py36h4414c95_1
      - defaults/linux-64::statsmodels==0.9.0=py36h035aef0_0
      - defaults/linux-64::odo==0.5.1=py36_0
      - defaults/linux-64::pandas==0.22.0=py36hf484d3e_0
      - defaults/linux-64::dask==0.19.3=py36_0
      - defaults/linux-64::scipy==1.1.0=py36hd20e5f9_0
      - defaults/linux-64::seaborn==0.9.0=py36_0
      - defaults/linux-64::anaconda==5.3.0=py37_0
      - defaults/linux-64::pywavelets==1.0.1=py36hdd07704_0
      - defaults/linux-64::matplotlib==3.0.0=py36h5429711_0
      - defaults/linux-64::holoviews==1.10.7=py36_0
      - defaults/linux-64::numpy==1.11.3=py36h1d66e8a_10
      - defaults/linux-64::pytables==3.4.4=py36he1c29e1_0
      - defaults/linux-64::h5py==2.8.0=py36h39dcb92_0
      - pytorch/linux-64::pytorch==1.3.1=py3.6_cpu_0
      - defaults/linux-64::pytest-astropy==0.4.0=py36_0
      - defaults/linux-64::imageio==2.4.1=py36_0
      - defaults/linux-64::astropy==3.0.4=py36h14c3975_0
      - defaults/linux-64::numexpr==2.6.8=py36hd89afb7_0
      - defaults/linux-64::mkl_fft==1.0.6=py36h7dd41cf_0
      - defaults/linux-64::bokeh==0.13.0=py36_0
      - defaults/linux-64::bottleneck==1.2.1=py36h035aef0_1
      - defaults/linux-64::gdal==2.3.0=py36h04863e7_1
      - defaults/linux-64::scikit-image==0.14.0=py37hf484d3e_1
      - defaults/linux-64::datashape==0.5.4=py36_1
      - defaults/linux-64::numba==0.40.0=py36h962f231_0
      - defaults/linux-64::numpy-base==1.11.3=py36h81de0dd_10
      - pytorch/linux-64::torchvision==0.4.2=py36_cpu
    done
    
    ## Package Plan ##
    
      environment location: /home/nbuser/anaconda3_501
    
      added / updated specs:
        - folium=0.5.0
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        _anaconda_depends-2019.10  |           py36_0           5 KB
        altair-4.1.0               |             py_1         614 KB  conda-forge
        anaconda-custom            |           py36_1           3 KB
        backports.os-0.1.1         |py36h9f0ad1d_1002          15 KB  conda-forge
        branca-0.4.1               |             py_0          26 KB  conda-forge
        ca-certificates-2020.6.20  |       hecda079_0         145 KB  conda-forge
        certifi-2020.6.20          |   py36h9f0ad1d_0         151 KB  conda-forge
        conda-4.8.4                |   py36h9f0ad1d_2         3.1 MB  conda-forge
        folium-0.5.0               |             py_0          45 KB  conda-forge
        fsspec-0.8.0               |             py_0          61 KB  conda-forge
        importlib-metadata-1.7.0   |   py36h9f0ad1d_0          44 KB  conda-forge
        importlib_metadata-1.7.0   |                0           3 KB  conda-forge
        joblib-0.16.0              |             py_0         203 KB  conda-forge
        json5-0.9.4                |     pyh9f0ad1d_0          20 KB  conda-forge
        jupyterlab_server-0.3.2    |             py_0          23 KB  conda-forge
        liblief-0.10.1             |       he1b5a44_0         2.0 MB  conda-forge
        openssl-1.0.2u             |       h516909a_0         3.2 MB  conda-forge
        py-lief-0.10.1             |   py36h831f99a_0         1.1 MB  conda-forge
        pyrsistent-0.16.0          |   py36h8c4c3a4_0          89 KB  conda-forge
        ripgrep-12.1.1             |       h516909a_0         1.8 MB  conda-forge
        scikit-image-0.14.2        |   py36hf484d3e_0        24.0 MB  conda-forge
        scikit-learn-0.20.1        |   py36h4989274_0         5.7 MB
        soupsieve-2.0.1            |   py36h9f0ad1d_0          56 KB  conda-forge
        sphinxcontrib-applehelp-1.0.2|             py_0          28 KB  conda-forge
        sphinxcontrib-devhelp-1.0.2|             py_0          22 KB  conda-forge
        sphinxcontrib-htmlhelp-1.0.3|             py_0          27 KB  conda-forge
        sphinxcontrib-jsmath-1.0.1 |             py_0           7 KB  conda-forge
        sphinxcontrib-qthelp-1.0.3 |             py_0          25 KB  conda-forge
        sphinxcontrib-serializinghtml-1.1.4|             py_0          24 KB  conda-forge
        tbb-2020.1                 |       hc9558a2_0         1.4 MB  conda-forge
        tbb4py-2020.1              |   py36hc9558a2_0         245 KB  conda-forge
        vincent-0.4.4              |             py_1          28 KB  conda-forge
        wurlitzer-2.0.0            |   py36h9f0ad1d_1          12 KB  conda-forge
        zipp-3.1.0                 |             py_0          10 KB  conda-forge
        ------------------------------------------------------------
                                               Total:        44.1 MB
    
    The following NEW packages will be INSTALLED:
    
      _anaconda_depends  pkgs/main/linux-64::_anaconda_depends-2019.10-py36_0
      altair             conda-forge/noarch::altair-4.1.0-py_1
      backports.os       conda-forge/linux-64::backports.os-0.1.1-py36h9f0ad1d_1002
      branca             conda-forge/noarch::branca-0.4.1-py_0
      folium             conda-forge/noarch::folium-0.5.0-py_0
      fsspec             conda-forge/noarch::fsspec-0.8.0-py_0
      importlib-metadata conda-forge/linux-64::importlib-metadata-1.7.0-py36h9f0ad1d_0
      importlib_metadata conda-forge/noarch::importlib_metadata-1.7.0-0
      joblib             conda-forge/noarch::joblib-0.16.0-py_0
      json5              conda-forge/noarch::json5-0.9.4-pyh9f0ad1d_0
      jupyterlab_server  conda-forge/noarch::jupyterlab_server-0.3.2-py_0
      liblief            conda-forge/linux-64::liblief-0.10.1-he1b5a44_0
      py-lief            conda-forge/linux-64::py-lief-0.10.1-py36h831f99a_0
      pyrsistent         conda-forge/linux-64::pyrsistent-0.16.0-py36h8c4c3a4_0
      ripgrep            conda-forge/linux-64::ripgrep-12.1.1-h516909a_0
      soupsieve          conda-forge/linux-64::soupsieve-2.0.1-py36h9f0ad1d_0
      sphinxcontrib-app~ conda-forge/noarch::sphinxcontrib-applehelp-1.0.2-py_0
      sphinxcontrib-dev~ conda-forge/noarch::sphinxcontrib-devhelp-1.0.2-py_0
      sphinxcontrib-htm~ conda-forge/noarch::sphinxcontrib-htmlhelp-1.0.3-py_0
      sphinxcontrib-jsm~ conda-forge/noarch::sphinxcontrib-jsmath-1.0.1-py_0
      sphinxcontrib-qth~ conda-forge/noarch::sphinxcontrib-qthelp-1.0.3-py_0
      sphinxcontrib-ser~ conda-forge/noarch::sphinxcontrib-serializinghtml-1.1.4-py_0
      tbb                conda-forge/linux-64::tbb-2020.1-hc9558a2_0
      tbb4py             conda-forge/linux-64::tbb4py-2020.1-py36hc9558a2_0
      vincent            conda-forge/noarch::vincent-0.4.4-py_1
      wurlitzer          conda-forge/linux-64::wurlitzer-2.0.0-py36h9f0ad1d_1
      zipp               conda-forge/noarch::zipp-3.1.0-py_0
    
    The following packages will be UPDATED:
    
      ca-certificates    pkgs/main::ca-certificates-2018.03.07~ --> conda-forge::ca-certificates-2020.6.20-hecda079_0
      certifi              pkgs/main::certifi-2018.10.15-py36_0 --> conda-forge::certifi-2020.6.20-py36h9f0ad1d_0
      conda                                        4.8.2-py36_0 --> 4.8.4-py36h9f0ad1d_2
      openssl              pkgs/main::openssl-1.0.2p-h14c3975_0 --> conda-forge::openssl-1.0.2u-h516909a_0
      scikit-image       pkgs/main::scikit-image-0.14.0-py37hf~ --> conda-forge::scikit-image-0.14.2-py36hf484d3e_0
      scikit-learn                        0.19.2-py37h4989274_0 --> 0.20.1-py36h4989274_0
    
    The following packages will be DOWNGRADED:
    
      anaconda                                     5.3.0-py37_0 --> custom-py36_1
    
    
    
    Downloading and Extracting Packages
    liblief-0.10.1       | 2.0 MB    | ##################################### | 100% 
    folium-0.5.0         | 45 KB     | ##################################### | 100% 
    scikit-image-0.14.2  | 24.0 MB   | ##################################### | 100% 
    scikit-learn-0.20.1  | 5.7 MB    | ##################################### | 100% 
    soupsieve-2.0.1      | 56 KB     | ##################################### | 100% 
    ripgrep-12.1.1       | 1.8 MB    | ##################################### | 100% 
    sphinxcontrib-htmlhe | 27 KB     | ##################################### | 100% 
    ca-certificates-2020 | 145 KB    | ##################################### | 100% 
    sphinxcontrib-devhel | 22 KB     | ##################################### | 100% 
    conda-4.8.4          | 3.1 MB    | ##################################### | 100% 
    branca-0.4.1         | 26 KB     | ##################################### | 100% 
    sphinxcontrib-appleh | 28 KB     | ##################################### | 100% 
    sphinxcontrib-jsmath | 7 KB      | ##################################### | 100% 
    _anaconda_depends-20 | 5 KB      | ##################################### | 100% 
    json5-0.9.4          | 20 KB     | ##################################### | 100% 
    tbb-2020.1           | 1.4 MB    | ##################################### | 100% 
    wurlitzer-2.0.0      | 12 KB     | ##################################### | 100% 
    pyrsistent-0.16.0    | 89 KB     | ##################################### | 100% 
    py-lief-0.10.1       | 1.1 MB    | ##################################### | 100% 
    altair-4.1.0         | 614 KB    | ##################################### | 100% 
    sphinxcontrib-serial | 24 KB     | ##################################### | 100% 
    zipp-3.1.0           | 10 KB     | ##################################### | 100% 
    fsspec-0.8.0         | 61 KB     | ##################################### | 100% 
    importlib-metadata-1 | 44 KB     | ##################################### | 100% 
    backports.os-0.1.1   | 15 KB     | ##################################### | 100% 
    joblib-0.16.0        | 203 KB    | ##################################### | 100% 
    importlib_metadata-1 | 3 KB      | ##################################### | 100% 
    vincent-0.4.4        | 28 KB     | ##################################### | 100% 
    sphinxcontrib-qthelp | 25 KB     | ##################################### | 100% 
    tbb4py-2020.1        | 245 KB    | ##################################### | 100% 
    openssl-1.0.2u       | 3.2 MB    | ##################################### | 100% 
    certifi-2020.6.20    | 151 KB    | ##################################### | 100% 
    anaconda-custom      | 3 KB      | ##################################### | 100% 
    jupyterlab_server-0. | 23 KB     | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done
    Libraries imported.



```python
import requests
import xml

import matplotlib.pyplot as plt
%matplotlib inline
import warnings
warnings.filterwarnings("ignore")

from sklearn.cluster import KMeans
from bs4 import BeautifulSoup
```

#### Retrieve Data from source
Here we use the data in csv file which containing transfer information in 2019.


```python
df = pd.read_csv("http://prod2.publicdata.landregistry.gov.uk.s3-website-eu-west-1.amazonaws.com/pp-2019.csv")

df.shape
```




    (972285, 16)




```python
df.head(5)
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>{8CAC1318-AC2F-0253-E053-6B04A8C08E51}</th>
      <th>165950</th>
      <th>2019-05-28 00:00</th>
      <th>BS22 7FP</th>
      <th>T</th>
      <th>N</th>
      <th>F</th>
      <th>32</th>
      <th>Unnamed: 8</th>
      <th>KELSTON GARDENS</th>
      <th>Unnamed: 10</th>
      <th>WESTON-SUPER-MARE</th>
      <th>NORTH SOMERSET</th>
      <th>NORTH SOMERSET.1</th>
      <th>A</th>
      <th>A.1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{8CAC1318-AC31-0253-E053-6B04A8C08E51}</td>
      <td>119500</td>
      <td>2019-05-22 00:00</td>
      <td>TA8 2EY</td>
      <td>F</td>
      <td>N</td>
      <td>L</td>
      <td>GROVE HOUSE, 58</td>
      <td>FLAT 1</td>
      <td>BERROW ROAD</td>
      <td>NaN</td>
      <td>BURNHAM-ON-SEA</td>
      <td>SEDGEMOOR</td>
      <td>SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{8CAC1318-AC33-0253-E053-6B04A8C08E51}</td>
      <td>215000</td>
      <td>2019-06-21 00:00</td>
      <td>TA22 9DH</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>5</td>
      <td>NaN</td>
      <td>WEIR HEAD</td>
      <td>NaN</td>
      <td>DULVERTON</td>
      <td>SOMERSET WEST AND TAUNTON</td>
      <td>SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{8CAC1318-AC35-0253-E053-6B04A8C08E51}</td>
      <td>242500</td>
      <td>2019-05-01 00:00</td>
      <td>BS20 7BP</td>
      <td>F</td>
      <td>N</td>
      <td>L</td>
      <td>50</td>
      <td>NaN</td>
      <td>LOWER BURLINGTON ROAD</td>
      <td>PORTISHEAD</td>
      <td>BRISTOL</td>
      <td>NORTH SOMERSET</td>
      <td>NORTH SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{8CAC1318-AC36-0253-E053-6B04A8C08E51}</td>
      <td>318000</td>
      <td>2019-05-09 00:00</td>
      <td>BA3 2RW</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>4</td>
      <td>NaN</td>
      <td>BUSHY COMBE</td>
      <td>MIDSOMER NORTON</td>
      <td>RADSTOCK</td>
      <td>BATH AND NORTH EAST SOMERSET</td>
      <td>BATH AND NORTH EAST SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{8CAC1318-AC37-0253-E053-6B04A8C08E51}</td>
      <td>215000</td>
      <td>2019-05-17 00:00</td>
      <td>BS24 7HZ</td>
      <td>S</td>
      <td>N</td>
      <td>F</td>
      <td>9</td>
      <td>NaN</td>
      <td>OSMOND ROAD</td>
      <td>NaN</td>
      <td>WESTON-SUPER-MARE</td>
      <td>NORTH SOMERSET</td>
      <td>NORTH SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>



#### Data preprocessing
We need to process the dataframe for further usage. Here we aim to group the venues by Street assigned in the dataframe and look into the average price of each street. 

Targetting housebuyers' price expectation, upper limit and lower limit of price can be set to narrow down the venues we are looking at.

Preprocessing the data to finally obtain a dataframe containing the following information:
* Street Name
* Average Price of Venues
* Location Information

**1. Rename the column names**


```python
# Assign meaningful column names
df.columns = ['TUID', 'Price', 'Date_Transfer', 'Postcode', 'Prop_Type', 'Old_New', 'Duration', 'PAON', \
                  'SAON', 'Street', 'Locality', 'City', 'District', 'County', 'PPD_Cat_Type', 'Record_Status']
df.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TUID</th>
      <th>Price</th>
      <th>Date_Transfer</th>
      <th>Postcode</th>
      <th>Prop_Type</th>
      <th>Old_New</th>
      <th>Duration</th>
      <th>PAON</th>
      <th>SAON</th>
      <th>Street</th>
      <th>Locality</th>
      <th>City</th>
      <th>District</th>
      <th>County</th>
      <th>PPD_Cat_Type</th>
      <th>Record_Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{8CAC1318-AC31-0253-E053-6B04A8C08E51}</td>
      <td>119500</td>
      <td>2019-05-22 00:00</td>
      <td>TA8 2EY</td>
      <td>F</td>
      <td>N</td>
      <td>L</td>
      <td>GROVE HOUSE, 58</td>
      <td>FLAT 1</td>
      <td>BERROW ROAD</td>
      <td>NaN</td>
      <td>BURNHAM-ON-SEA</td>
      <td>SEDGEMOOR</td>
      <td>SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{8CAC1318-AC33-0253-E053-6B04A8C08E51}</td>
      <td>215000</td>
      <td>2019-06-21 00:00</td>
      <td>TA22 9DH</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>5</td>
      <td>NaN</td>
      <td>WEIR HEAD</td>
      <td>NaN</td>
      <td>DULVERTON</td>
      <td>SOMERSET WEST AND TAUNTON</td>
      <td>SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{8CAC1318-AC35-0253-E053-6B04A8C08E51}</td>
      <td>242500</td>
      <td>2019-05-01 00:00</td>
      <td>BS20 7BP</td>
      <td>F</td>
      <td>N</td>
      <td>L</td>
      <td>50</td>
      <td>NaN</td>
      <td>LOWER BURLINGTON ROAD</td>
      <td>PORTISHEAD</td>
      <td>BRISTOL</td>
      <td>NORTH SOMERSET</td>
      <td>NORTH SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{8CAC1318-AC36-0253-E053-6B04A8C08E51}</td>
      <td>318000</td>
      <td>2019-05-09 00:00</td>
      <td>BA3 2RW</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>4</td>
      <td>NaN</td>
      <td>BUSHY COMBE</td>
      <td>MIDSOMER NORTON</td>
      <td>RADSTOCK</td>
      <td>BATH AND NORTH EAST SOMERSET</td>
      <td>BATH AND NORTH EAST SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{8CAC1318-AC37-0253-E053-6B04A8C08E51}</td>
      <td>215000</td>
      <td>2019-05-17 00:00</td>
      <td>BS24 7HZ</td>
      <td>S</td>
      <td>N</td>
      <td>F</td>
      <td>9</td>
      <td>NaN</td>
      <td>OSMOND ROAD</td>
      <td>NaN</td>
      <td>WESTON-SUPER-MARE</td>
      <td>NORTH SOMERSET</td>
      <td>NORTH SOMERSET</td>
      <td>A</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>



**2. Select column City which is only London**


```python
df_ld = df.query("City == 'LONDON'")
df_ld.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TUID</th>
      <th>Price</th>
      <th>Date_Transfer</th>
      <th>Postcode</th>
      <th>Prop_Type</th>
      <th>Old_New</th>
      <th>Duration</th>
      <th>PAON</th>
      <th>SAON</th>
      <th>Street</th>
      <th>Locality</th>
      <th>City</th>
      <th>District</th>
      <th>County</th>
      <th>PPD_Cat_Type</th>
      <th>Record_Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>86</th>
      <td>{8CAC1318-AFF3-0253-E053-6B04A8C08E51}</td>
      <td>1100000</td>
      <td>2019-06-07 00:00</td>
      <td>SW19 8BN</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>30</td>
      <td>NaN</td>
      <td>ASHEN GROVE</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>MERTON</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>88</th>
      <td>{8CAC1318-AFF5-0253-E053-6B04A8C08E51}</td>
      <td>905000</td>
      <td>2019-06-20 00:00</td>
      <td>SW19 8BH</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>35</td>
      <td>NaN</td>
      <td>DURNSFORD AVENUE</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>MERTON</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>95</th>
      <td>{8CAC1318-AFFC-0253-E053-6B04A8C08E51}</td>
      <td>1440000</td>
      <td>2019-06-07 00:00</td>
      <td>SW13 8DL</td>
      <td>T</td>
      <td>N</td>
      <td>F</td>
      <td>29</td>
      <td>NaN</td>
      <td>MERTHYR TERRACE</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>RICHMOND UPON THAMES</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>167</th>
      <td>{8CAC1318-B38E-0253-E053-6B04A8C08E51}</td>
      <td>402500</td>
      <td>2019-06-14 00:00</td>
      <td>SW16 4PB</td>
      <td>S</td>
      <td>N</td>
      <td>F</td>
      <td>9</td>
      <td>NaN</td>
      <td>POLLARDS WOOD ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>CROYDON</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>292</th>
      <td>{8F1B26BD-4595-53DB-E053-6C04A8C03649}</td>
      <td>340000</td>
      <td>2019-07-15 00:00</td>
      <td>N1 9BT</td>
      <td>F</td>
      <td>N</td>
      <td>L</td>
      <td>75</td>
      <td>FLAT 4</td>
      <td>CALEDONIAN ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>ISLINGTON</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>



**3. Format transfer date**


```python
df_ld['Date_Transfer'] = df_ld['Date_Transfer'].apply(pd.to_datetime)

# Sort by Date
df_ld.sort_values(by=['Date_Transfer'],ascending=[False],inplace=True)
df_ld.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TUID</th>
      <th>Price</th>
      <th>Date_Transfer</th>
      <th>Postcode</th>
      <th>Prop_Type</th>
      <th>Old_New</th>
      <th>Duration</th>
      <th>PAON</th>
      <th>SAON</th>
      <th>Street</th>
      <th>Locality</th>
      <th>City</th>
      <th>District</th>
      <th>County</th>
      <th>PPD_Cat_Type</th>
      <th>Record_Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>730629</th>
      <td>{A2479555-A33A-74C7-E053-6B04A8C0887D}</td>
      <td>670000</td>
      <td>2019-12-31</td>
      <td>SW18 1UT</td>
      <td>F</td>
      <td>Y</td>
      <td>L</td>
      <td>8</td>
      <td>APARTMENT 1</td>
      <td>OSIERS ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>WANDSWORTH</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>730613</th>
      <td>{A2479555-A326-74C7-E053-6B04A8C0887D}</td>
      <td>600000</td>
      <td>2019-12-31</td>
      <td>SW18 1UX</td>
      <td>F</td>
      <td>Y</td>
      <td>L</td>
      <td>12</td>
      <td>APARTMENT 46</td>
      <td>OSIERS ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>WANDSWORTH</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>730633</th>
      <td>{A2479555-A347-74C7-E053-6B04A8C0887D}</td>
      <td>520000</td>
      <td>2019-12-31</td>
      <td>SW18 1UX</td>
      <td>F</td>
      <td>Y</td>
      <td>L</td>
      <td>12</td>
      <td>APARTMENT 7</td>
      <td>OSIERS ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>WANDSWORTH</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>730636</th>
      <td>{A2479555-A34B-74C7-E053-6B04A8C0887D}</td>
      <td>600000</td>
      <td>2019-12-31</td>
      <td>SW18 1UT</td>
      <td>F</td>
      <td>Y</td>
      <td>L</td>
      <td>8</td>
      <td>APARTMENT 12</td>
      <td>OSIERS ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>WANDSWORTH</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
    <tr>
      <th>819258</th>
      <td>{9FF0D96A-38F5-11ED-E053-6C04A8C06383}</td>
      <td>600000</td>
      <td>2019-12-31</td>
      <td>SW18 1UX</td>
      <td>F</td>
      <td>Y</td>
      <td>L</td>
      <td>FLAT 45, 12</td>
      <td>NaN</td>
      <td>OSIERS ROAD</td>
      <td>NaN</td>
      <td>LONDON</td>
      <td>WANDSWORTH</td>
      <td>GREATER LONDON</td>
      <td>A</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>



**4. Group by Street and calculate street average price**


```python
streets = df_ld['Street'].unique().tolist()
df_grp_price = df_ld.groupby(['Street'])['Price'].mean().reset_index()

df_grp_price.columns = ['Street', 'Avg_Price']
```

Depend on the Client's Budget to set upper limit and lower limit that fits the inquiry


```python
df_price_limit = df_grp_price.query("(Avg_Price >= 2200000) & (Avg_Price <= 2500000)")

df_price_limit.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>Avg_Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>ALBERT EMBANKMENT</td>
      <td>2.283829e+06</td>
    </tr>
    <tr>
      <th>186</th>
      <td>ALBION SQUARE</td>
      <td>2.292500e+06</td>
    </tr>
    <tr>
      <th>258</th>
      <td>ALLEYN PARK</td>
      <td>2.283095e+06</td>
    </tr>
    <tr>
      <th>455</th>
      <td>ARGYLE STREET</td>
      <td>2.300000e+06</td>
    </tr>
    <tr>
      <th>499</th>
      <td>ARTESIAN ROAD</td>
      <td>2.462500e+06</td>
    </tr>
  </tbody>
</table>
</div>



**5. Use geolocator to obtain the latitude and longitutde for each street **


```python
for index, item in df_price_limit.iterrows():
    print(f"index: {index}")
    print(f"item: {item}")
    print(f"item.Street only: {item.Street}")
```

    index: 171
    item: Street       ALBERT EMBANKMENT
    Avg_Price          2.28383e+06
    Name: 171, dtype: object
    item.Street only: ALBERT EMBANKMENT
    index: 186
    item: Street       ALBION SQUARE
    Avg_Price       2.2925e+06
    Name: 186, dtype: object
    item.Street only: ALBION SQUARE
    index: 258
    item: Street       ALLEYN PARK
    Avg_Price     2.2831e+06
    Name: 258, dtype: object
    item.Street only: ALLEYN PARK
    index: 455
    item: Street       ARGYLE STREET
    Avg_Price          2.3e+06
    Name: 455, dtype: object
    item.Street only: ARGYLE STREET
    index: 499
    item: Street       ARTESIAN ROAD
    Avg_Price       2.4625e+06
    Name: 499, dtype: object
    item.Street only: ARTESIAN ROAD
    index: 536
    item: Street       ASHCHURCH GROVE
    Avg_Price          2.425e+06
    Name: 536, dtype: object
    item.Street only: ASHCHURCH GROVE
    index: 680
    item: Street       AYNHOE ROAD
    Avg_Price      2.475e+06
    Name: 680, dtype: object
    item.Street only: AYNHOE ROAD
    index: 989
    item: Street       BEDALE STREET
    Avg_Price       2.3446e+06
    Name: 989, dtype: object
    item.Street only: BEDALE STREET
    index: 1021
    item: Street       BEECHWOOD AVENUE
    Avg_Price             2.5e+06
    Name: 1021, dtype: object
    item.Street only: BEECHWOOD AVENUE
    index: 1179
    item: Street       BETTERTON STREET
    Avg_Price           2.425e+06
    Name: 1179, dtype: object
    item.Street only: BETTERTON STREET
    index: 1180
    item: Street       BETTRIDGE ROAD
    Avg_Price           2.4e+06
    Name: 1180, dtype: object
    item.Street only: BETTRIDGE ROAD
    index: 1220
    item: Street       BILLING ROAD
    Avg_Price         2.4e+06
    Name: 1220, dtype: object
    item.Street only: BILLING ROAD
    index: 1326
    item: Street       BLENHEIM CRESCENT
    Avg_Price          2.37694e+06
    Name: 1326, dtype: object
    item.Street only: BLENHEIM CRESCENT
    index: 1439
    item: Street       BOURDON STREET
    Avg_Price           2.2e+06
    Name: 1439, dtype: object
    item.Street only: BOURDON STREET
    index: 1508
    item: Street       BRADFIELD ROAD
    Avg_Price         2.225e+06
    Name: 1508, dtype: object
    item.Street only: BRADFIELD ROAD
    index: 1746
    item: Street       BROMPTON PLACE
    Avg_Price           2.4e+06
    Name: 1746, dtype: object
    item.Street only: BROMPTON PLACE
    index: 2046
    item: Street       CAITHNESS ROAD
    Avg_Price       2.21833e+06
    Name: 2046, dtype: object
    item.Street only: CAITHNESS ROAD
    index: 2105
    item: Street       CAMBRIDGE TERRACE
    Avg_Price             2.42e+06
    Name: 2105, dtype: object
    item.Street only: CAMBRIDGE TERRACE
    index: 2113
    item: Street       CAMDEN SQUARE
    Avg_Price         2.43e+06
    Name: 2113, dtype: object
    item.Street only: CAMDEN SQUARE
    index: 2125
    item: Street       CAMPDEN HILL ROAD
    Avg_Price           2.3054e+06
    Name: 2125, dtype: object
    item.Street only: CAMPDEN HILL ROAD
    index: 2168
    item: Street       CANONBURY PARK SOUTH
    Avg_Price             2.36175e+06
    Name: 2168, dtype: object
    item.Street only: CANONBURY PARK SOUTH
    index: 2218
    item: Street       CARLISLE PLACE
    Avg_Price         2.284e+06
    Name: 2218, dtype: object
    item.Street only: CARLISLE PLACE
    index: 2223
    item: Street       CARLTON GARDENS
    Avg_Price            2.5e+06
    Name: 2223, dtype: object
    item.Street only: CARLTON GARDENS
    index: 2227
    item: Street       CARLTON ROAD
    Avg_Price     2.26689e+06
    Name: 2227, dtype: object
    item.Street only: CARLTON ROAD
    index: 2395
    item: Street       CHALCOT SQUARE
    Avg_Price       2.32375e+06
    Name: 2395, dtype: object
    item.Street only: CHALCOT SQUARE
    index: 2540
    item: Street       CHELSEA EMBANKMENT
    Avg_Price           2.21375e+06
    Name: 2540, dtype: object
    item.Street only: CHELSEA EMBANKMENT
    index: 2556
    item: Street       CHENISTON GARDENS
    Avg_Price             2.31e+06
    Name: 2556, dtype: object
    item.Street only: CHENISTON GARDENS
    index: 2571
    item: Street       CHESHAM MEWS
    Avg_Price        2.26e+06
    Name: 2571, dtype: object
    item.Street only: CHESHAM MEWS
    index: 2611
    item: Street       CHEVENING ROAD
    Avg_Price       2.47015e+06
    Name: 2611, dtype: object
    item.Street only: CHEVENING ROAD
    index: 2966
    item: Street       COLLINGHAM ROAD
    Avg_Price          2.477e+06
    Name: 2966, dtype: object
    item.Street only: COLLINGHAM ROAD
    index: 3206
    item: Street       COULTER ROAD
    Avg_Price         2.4e+06
    Name: 3206, dtype: object
    item.Street only: COULTER ROAD
    index: 3218
    item: Street       COURT LANE GARDENS
    Avg_Price              2.44e+06
    Name: 3218, dtype: object
    item.Street only: COURT LANE GARDENS
    index: 3230
    item: Street       COURTHOPE ROAD
    Avg_Price          2.24e+06
    Name: 3230, dtype: object
    item.Street only: COURTHOPE ROAD
    index: 3237
    item: Street       COURTNELL STREET
    Avg_Price         2.40955e+06
    Name: 3237, dtype: object
    item.Street only: COURTNELL STREET
    index: 3308
    item: Street       CRAWFORD MEWS
    Avg_Price          2.5e+06
    Name: 3308, dtype: object
    item.Street only: CRAWFORD MEWS
    index: 3319
    item: Street       CREDITON HILL
    Avg_Price      2.26533e+06
    Name: 3319, dtype: object
    item.Street only: CREDITON HILL
    index: 3597
    item: Street       DARTMOUTH PARK AVENUE
    Avg_Price              2.38833e+06
    Name: 3597, dtype: object
    item.Street only: DARTMOUTH PARK AVENUE
    index: 3646
    item: Street       DE VERE GARDENS
    Avg_Price        2.21906e+06
    Name: 3646, dtype: object
    item.Street only: DE VERE GARDENS
    index: 3672
    item: Street       DEEPDALE
    Avg_Price    2.48e+06
    Name: 3672, dtype: object
    item.Street only: DEEPDALE
    index: 3675
    item: Street       DEER PARK ROAD
    Avg_Price          2.45e+06
    Name: 3675, dtype: object
    item.Street only: DEER PARK ROAD
    index: 3761
    item: Street       DEVEREUX LANE
    Avg_Price          2.3e+06
    Name: 3761, dtype: object
    item.Street only: DEVEREUX LANE
    index: 3771
    item: Street       DEVONSHIRE MEWS WEST
    Avg_Price                 2.4e+06
    Name: 3771, dtype: object
    item.Street only: DEVONSHIRE MEWS WEST
    index: 3891
    item: Street       DOVER STREET
    Avg_Price         2.5e+06
    Name: 3891, dtype: object
    item.Street only: DOVER STREET
    index: 3912
    item: Street       DOWNSIDE CRESCENT
    Avg_Price          2.33818e+06
    Name: 3912, dtype: object
    item.Street only: DOWNSIDE CRESCENT
    index: 3974
    item: Street       DUDLEY ROAD
    Avg_Price    2.31558e+06
    Name: 3974, dtype: object
    item.Street only: DUDLEY ROAD
    index: 3982
    item: Street       DUKES LANE
    Avg_Price      2.35e+06
    Name: 3982, dtype: object
    item.Street only: DUKES LANE
    index: 4146
    item: Street       EATON TERRACE MEWS
    Avg_Price               2.4e+06
    Name: 4146, dtype: object
    item.Street only: EATON TERRACE MEWS
    index: 4162
    item: Street       ECCLESTON MEWS
    Avg_Price         2.225e+06
    Name: 4162, dtype: object
    item.Street only: ECCLESTON MEWS
    index: 4165
    item: Street       ECCLESTON SQUARE
    Avg_Price         2.49091e+06
    Name: 4165, dtype: object
    item.Street only: ECCLESTON SQUARE
    index: 4284
    item: Street       ELLERBY STREET
    Avg_Price          2.45e+06
    Name: 4284, dtype: object
    item.Street only: ELLERBY STREET
    index: 4393
    item: Street       ELVASTON PLACE
    Avg_Price         2.352e+06
    Name: 4393, dtype: object
    item.Street only: ELVASTON PLACE
    index: 4454
    item: Street       ENNISMORE GARDENS MEWS
    Avg_Price                   2.2e+06
    Name: 4454, dtype: object
    item.Street only: ENNISMORE GARDENS MEWS
    index: 4502
    item: Street       ESSEX STREET
    Avg_Price     2.46204e+06
    Name: 4502, dtype: object
    item.Street only: ESSEX STREET
    index: 4553
    item: Street       EVERSLEY CRESCENT
    Avg_Price              2.2e+06
    Name: 4553, dtype: object
    item.Street only: EVERSLEY CRESCENT
    index: 4582
    item: Street       EYNELLA ROAD
    Avg_Price       2.296e+06
    Name: 4582, dtype: object
    item.Street only: EYNELLA ROAD
    index: 4713
    item: Street       FENCHURCH STREET
    Avg_Price         2.46012e+06
    Name: 4713, dtype: object
    item.Street only: FENCHURCH STREET
    index: 4851
    item: Street       FLORAL STREET
    Avg_Price      2.44275e+06
    Name: 4851, dtype: object
    item.Street only: FLORAL STREET
    index: 4949
    item: Street       FOUNTAYNE ROAD
    Avg_Price        2.4975e+06
    Name: 4949, dtype: object
    item.Street only: FOUNTAYNE ROAD
    index: 4950
    item: Street       FOURNIER STREET
    Avg_Price           2.45e+06
    Name: 4950, dtype: object
    item.Street only: FOURNIER STREET
    index: 5039
    item: Street       FRISTON STREET
    Avg_Price          2.45e+06
    Name: 5039, dtype: object
    item.Street only: FRISTON STREET
    index: 5218
    item: Street       GERTRUDE STREET
    Avg_Price            2.3e+06
    Name: 5218, dtype: object
    item.Street only: GERTRUDE STREET
    index: 5361
    item: Street       GLOUCESTER PLACE MEWS
    Avg_Price                  2.5e+06
    Name: 5361, dtype: object
    item.Street only: GLOUCESTER PLACE MEWS
    index: 5424
    item: Street       GORDON COTTAGES
    Avg_Price            2.2e+06
    Name: 5424, dtype: object
    item.Street only: GORDON COTTAGES
    index: 5434
    item: Street       GORST ROAD
    Avg_Price       2.3e+06
    Name: 5434, dtype: object
    item.Street only: GORST ROAD
    index: 5469
    item: Street       GRAHAM TERRACE
    Avg_Price           2.2e+06
    Name: 5469, dtype: object
    item.Street only: GRAHAM TERRACE
    index: 5553
    item: Street       GREAT TITCHFIELD STREET
    Avg_Price                  2.395e+06
    Name: 5553, dtype: object
    item.Street only: GREAT TITCHFIELD STREET
    index: 5793
    item: Street       HALLAM STREET
    Avg_Price        2.205e+06
    Name: 5793, dtype: object
    item.Street only: HALLAM STREET
    index: 5801
    item: Street       HALSEY STREET
    Avg_Price        2.387e+06
    Name: 5801, dtype: object
    item.Street only: HALSEY STREET
    index: 5931
    item: Street       HARLEY GARDENS
    Avg_Price          2.28e+06
    Name: 5931, dtype: object
    item.Street only: HARLEY GARDENS
    index: 5944
    item: Street       HARMSWORTH WAY
    Avg_Price       2.24188e+06
    Name: 5944, dtype: object
    item.Street only: HARMSWORTH WAY
    index: 5985
    item: Street       HARWOOD ROAD
    Avg_Price      2.2245e+06
    Name: 5985, dtype: object
    item.Street only: HARWOOD ROAD
    index: 6004
    item: Street       HATCHAM ROAD
    Avg_Price     2.44008e+06
    Name: 6004, dtype: object
    item.Street only: HATCHAM ROAD
    index: 6100
    item: Street       HEATH DRIVE
    Avg_Price      2.388e+06
    Name: 6100, dtype: object
    item.Street only: HEATH DRIVE
    index: 6101
    item: Street       HEATH HURST ROAD
    Avg_Price          2.2475e+06
    Name: 6101, dtype: object
    item.Street only: HEATH HURST ROAD
    index: 6160
    item: Street       HENDERSON ROAD
    Avg_Price          2.39e+06
    Name: 6160, dtype: object
    item.Street only: HENDERSON ROAD
    index: 6298
    item: Street       HIGHBURY NEW PARK
    Avg_Price           2.4975e+06
    Name: 6298, dtype: object
    item.Street only: HIGHBURY NEW PARK
    index: 6346
    item: Street       HILL STREET
    Avg_Price        2.5e+06
    Name: 6346, dtype: object
    item.Street only: HILL STREET
    index: 6382
    item: Street        HILLWAY
    Avg_Price    2.25e+06
    Name: 6382, dtype: object
    item.Street only: HILLWAY
    index: 6421
    item: Street       HOLBORN CLOSE
    Avg_Price          2.2e+06
    Name: 6421, dtype: object
    item.Street only: HOLBORN CLOSE
    index: 6444
    item: Street       HOLLAND PARK ROAD
    Avg_Price             2.35e+06
    Name: 6444, dtype: object
    item.Street only: HOLLAND PARK ROAD
    index: 6687
    item: Street         HYDE VALE
    Avg_Price    2.31517e+06
    Name: 6687, dtype: object
    item.Street only: HYDE VALE
    index: 6767
    item: Street       IRVING ROAD
    Avg_Price        2.5e+06
    Name: 6767, dtype: object
    item.Street only: IRVING ROAD
    index: 6899
    item: Street       JUNCTION PLACE
    Avg_Price           2.2e+06
    Name: 6899, dtype: object
    item.Street only: JUNCTION PLACE
    index: 7055
    item: Street       KILDARE GARDENS
    Avg_Price            2.3e+06
    Name: 7055, dtype: object
    item.Street only: KILDARE GARDENS
    index: 7470
    item: Street       LEAMOUTH ROAD
    Avg_Price      2.38315e+06
    Name: 7470, dtype: object
    item.Street only: LEAMOUTH ROAD
    index: 7516
    item: Street       LEINSTER SQUARE
    Avg_Price         2.2723e+06
    Name: 7516, dtype: object
    item.Street only: LEINSTER SQUARE
    index: 7620
    item: Street       LILYVILLE ROAD
    Avg_Price       2.29167e+06
    Name: 7620, dtype: object
    item.Street only: LILYVILLE ROAD
    index: 7849
    item: Street       LOWER BELGRAVE STREET
    Avg_Price              2.40375e+06
    Name: 7849, dtype: object
    item.Street only: LOWER BELGRAVE STREET
    index: 7953
    item: Street       LYONS PLACE
    Avg_Price     2.3667e+06
    Name: 7953, dtype: object
    item.Street only: LYONS PLACE
    index: 8154
    item: Street       MARESFIELD GARDENS
    Avg_Price           2.24208e+06
    Name: 8154, dtype: object
    item.Street only: MARESFIELD GARDENS
    index: 8186
    item: Street       MARKHAM STREET
    Avg_Price           2.3e+06
    Name: 8186, dtype: object
    item.Street only: MARKHAM STREET
    index: 8511
    item: Street         MILKY WAY
    Avg_Price    2.25718e+06
    Name: 8511, dtype: object
    item.Street only: MILKY WAY
    index: 8671
    item: Street       MORELLA ROAD
    Avg_Price         2.5e+06
    Name: 8671, dtype: object
    item.Street only: MORELLA ROAD
    index: 8758
    item: Street       MOUNTVIEW CLOSE
    Avg_Price        2.23333e+06
    Name: 8758, dtype: object
    item.Street only: MOUNTVIEW CLOSE
    index: 9003
    item: Street       NIGHTINGALE SQUARE
    Avg_Price             2.475e+06
    Name: 9003, dtype: object
    item.Street only: NIGHTINGALE SQUARE
    index: 9047
    item: Street       NORRICE LEA
    Avg_Price      2.355e+06
    Name: 9047, dtype: object
    item.Street only: NORRICE LEA
    index: 9152
    item: Street       OAK HILL PARK MEWS
    Avg_Price               2.4e+06
    Name: 9152, dtype: object
    item.Street only: OAK HILL PARK MEWS
    index: 9252
    item: Street       OLD KENT ROAD
    Avg_Price      2.24288e+06
    Name: 9252, dtype: object
    item.Street only: OLD KENT ROAD
    index: 9301
    item: Street       ONSLOW CRESCENT
    Avg_Price            2.4e+06
    Name: 9301, dtype: object
    item.Street only: ONSLOW CRESCENT
    index: 9337
    item: Street       ORMONDE GATE
    Avg_Price        2.35e+06
    Name: 9337, dtype: object
    item.Street only: ORMONDE GATE
    index: 9418
    item: Street       PADDINGTON STREET
    Avg_Price          2.39321e+06
    Name: 9418, dtype: object
    item.Street only: PADDINGTON STREET
    index: 9433
    item: Street       PALACE COURT
    Avg_Price     2.27367e+06
    Name: 9433, dtype: object
    item.Street only: PALACE COURT
    index: 9512
    item: Street       PARKE ROAD
    Avg_Price      2.25e+06
    Name: 9512, dtype: object
    item.Street only: PARKE ROAD
    index: 9546
    item: Street        PARR'S WAY
    Avg_Price    2.33501e+06
    Name: 9546, dtype: object
    item.Street only: PARR'S WAY
    index: 9550
    item: Street       PARSONS GATE MEWS
    Avg_Price              2.2e+06
    Name: 9550, dtype: object
    item.Street only: PARSONS GATE MEWS
    index: 9584
    item: Street       PAVILION ROAD
    Avg_Price          2.3e+06
    Name: 9584, dtype: object
    item.Street only: PAVILION ROAD
    index: 9653
    item: Street       PEMBRIDGE CRESCENT
    Avg_Price           2.27679e+06
    Name: 9653, dtype: object
    item.Street only: PEMBRIDGE CRESCENT
    index: 9654
    item: Street       PEMBRIDGE MEWS
    Avg_Price        2.2851e+06
    Name: 9654, dtype: object
    item.Street only: PEMBRIDGE MEWS
    index: 9693
    item: Street       PENNINGTON STREET
    Avg_Price          2.25195e+06
    Name: 9693, dtype: object
    item.Street only: PENNINGTON STREET
    index: 9791
    item: Street       PICTON STREET
    Avg_Price         2.35e+06
    Name: 9791, dtype: object
    item.Street only: PICTON STREET
    index: 9807
    item: Street       PINE GROVE
    Avg_Price       2.4e+06
    Name: 9807, dtype: object
    item.Street only: PINE GROVE
    index: 9929
    item: Street       PORTLAND PLACE
    Avg_Price       2.27586e+06
    Name: 9929, dtype: object
    item.Street only: PORTLAND PLACE
    index: 10028
    item: Street       PRINCES SQUARE
    Avg_Price       2.23089e+06
    Name: 10028, dtype: object
    item.Street only: PRINCES SQUARE
    index: 10116
    item: Street       QUEEN ANNE STREET
    Avg_Price          2.44167e+06
    Name: 10116, dtype: object
    item.Street only: QUEEN ANNE STREET
    index: 10119
    item: Street       QUEEN ANNES GROVE
    Avg_Price             2.36e+06
    Name: 10119, dtype: object
    item.Street only: QUEEN ANNES GROVE
    index: 10350
    item: Street       REDCLIFFE MEWS
    Avg_Price       2.41589e+06
    Name: 10350, dtype: object
    item.Street only: REDCLIFFE MEWS
    index: 10628
    item: Street       ROSE SQUARE
    Avg_Price    2.29167e+06
    Name: 10628, dtype: object
    item.Street only: ROSE SQUARE
    index: 10829
    item: Street       RYECROFT STREET
    Avg_Price          2.225e+06
    Name: 10829, dtype: object
    item.Street only: RYECROFT STREET
    index: 10875
    item: Street       SALUSBURY ROAD
    Avg_Price         2.407e+06
    Name: 10875, dtype: object
    item.Street only: SALUSBURY ROAD
    index: 11317
    item: Street       SOUTH END ROW
    Avg_Price         2.35e+06
    Name: 11317, dtype: object
    item.Street only: SOUTH END ROW
    index: 11328
    item: Street       SOUTH PARADE
    Avg_Price       2.425e+06
    Name: 11328, dtype: object
    item.Street only: SOUTH PARADE
    index: 11430
    item: Street       SPRIMONT PLACE
    Avg_Price          2.25e+06
    Name: 11430, dtype: object
    item.Street only: SPRIMONT PLACE
    index: 11488
    item: Street       ST AUBYNS AVENUE
    Avg_Price            2.48e+06
    Name: 11488, dtype: object
    item.Street only: ST AUBYNS AVENUE
    index: 11554
    item: Street       ST JOHN STREET
    Avg_Price       2.28894e+06
    Name: 11554, dtype: object
    item.Street only: ST JOHN STREET
    index: 11677
    item: Street       ST STEPHENS GARDENS
    Avg_Price            2.20317e+06
    Name: 11677, dtype: object
    item.Street only: ST STEPHENS GARDENS
    index: 11843
    item: Street       STRADELLA ROAD
    Avg_Price       2.34425e+06
    Name: 11843, dtype: object
    item.Street only: STRADELLA ROAD
    index: 12065
    item: Street       SYDNEY STREET
    Avg_Price      2.34882e+06
    Name: 12065, dtype: object
    item.Street only: SYDNEY STREET
    index: 12138
    item: Street       TEDWORTH SQUARE
    Avg_Price            2.2e+06
    Name: 12138, dtype: object
    item.Street only: TEDWORTH SQUARE
    index: 12274
    item: Street       THE LITTLE BOLTONS
    Avg_Price              2.25e+06
    Name: 12274, dtype: object
    item.Street only: THE LITTLE BOLTONS
    index: 12277
    item: Street       THE MARLOWES
    Avg_Price         2.5e+06
    Name: 12277, dtype: object
    item.Street only: THE MARLOWES
    index: 12501
    item: Street       TOTTERIDGE COMMON
    Avg_Price              2.2e+06
    Name: 12501, dtype: object
    item.Street only: TOTTERIDGE COMMON
    index: 12740
    item: Street       UPPER MONTAGU STREET
    Avg_Price             2.23143e+06
    Name: 12740, dtype: object
    item.Street only: UPPER MONTAGU STREET
    index: 12922
    item: Street       VIVIAN WAY
    Avg_Price      2.44e+06
    Name: 12922, dtype: object
    item.Street only: VIVIAN WAY
    index: 12960
    item: Street       WALDRON MEWS
    Avg_Price     2.21614e+06
    Name: 12960, dtype: object
    item.Street only: WALDRON MEWS
    index: 13067
    item: Street       WARWICK LANE
    Avg_Price      2.2985e+06
    Name: 13067, dtype: object
    item.Street only: WARWICK LANE
    index: 13170
    item: Street       WELL ROAD
    Avg_Price    2.445e+06
    Name: 13170, dtype: object
    item.Street only: WELL ROAD
    index: 13237
    item: Street       WEST HILL PARK
    Avg_Price        2.4375e+06
    Name: 13237, dtype: object
    item.Street only: WEST HILL PARK
    index: 13238
    item: Street       WEST HILL ROAD
    Avg_Price          2.43e+06
    Name: 13238, dtype: object
    item.Street only: WEST HILL ROAD
    index: 13246
    item: Street       WEST SQUARE
    Avg_Price        2.3e+06
    Name: 13246, dtype: object
    item.Street only: WEST SQUARE
    index: 13248
    item: Street       WEST TEMPLE SHEEN
    Avg_Price          2.46733e+06
    Name: 13248, dtype: object
    item.Street only: WEST TEMPLE SHEEN
    index: 13386
    item: Street       WHITE HART LANE
    Avg_Price        2.29838e+06
    Name: 13386, dtype: object
    item.Street only: WHITE HART LANE
    index: 13418
    item: Street       WHITTAKER STREET
    Avg_Price            2.25e+06
    Name: 13418, dtype: object
    item.Street only: WHITTAKER STREET
    index: 13737
    item: Street       WOODYARD LANE
    Avg_Price         2.27e+06
    Name: 13737, dtype: object
    item.Street only: WOODYARD LANE
    index: 13795
    item: Street       WYATT DRIVE
    Avg_Price        2.4e+06
    Name: 13795, dtype: object
    item.Street only: WYATT DRIVE



```python
geolocator = Nominatim(user_agent="http")
```


```python
# add in latitude and longitude column
df_price_limit['Latitude'] = df_price_limit['Street'].apply(geolocator.geocode).apply(lambda x: x.latitude)
df_price_limit['Longitude'] = df_price_limit['Street'].apply(geolocator.geocode).apply(lambda x: x.longitude)

print(df_price_limit.shape)
df_p = df_price_limit
df_p.head()
```

    (144, 4)





<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>Avg_Price</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>ALBERT EMBANKMENT</td>
      <td>2.283829e+06</td>
      <td>51.493213</td>
      <td>-0.121361</td>
    </tr>
    <tr>
      <th>186</th>
      <td>ALBION SQUARE</td>
      <td>2.292500e+06</td>
      <td>-41.273758</td>
      <td>173.289393</td>
    </tr>
    <tr>
      <th>258</th>
      <td>ALLEYN PARK</td>
      <td>2.283095e+06</td>
      <td>51.492742</td>
      <td>-0.371967</td>
    </tr>
    <tr>
      <th>455</th>
      <td>ARGYLE STREET</td>
      <td>2.300000e+06</td>
      <td>22.319036</td>
      <td>114.167841</td>
    </tr>
    <tr>
      <th>499</th>
      <td>ARTESIAN ROAD</td>
      <td>2.462500e+06</td>
      <td>41.725304</td>
      <td>-88.205529</td>
    </tr>
  </tbody>
</table>
</div>



### Map of London


```python
address = 'London,UK'

location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print('The Geograpical Co-ordinate of Seattle,Washington are {}, {}.'.format(latitude, longitude))
```

    The Geograpical Co-ordinate of Seattle,Washington are 51.5073219, -0.1276474.



```python
map_london = folium.Map(location=[latitude, longitude], zoom_start=11)

# add markers to map
for lat, lng, street in zip(df_p['Latitude'], df_p['Longitude'], df_p['Street']):
    label = '{}'.format(street)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        color='blue',
        fill=True,
        fill_color='#3186cc',
        fill_opacity=0.7,
        parse_html=False).add_to(map_london)  
    
map_london
```




<div style="width: 100%;"><div style="position: relative; width: 100%; height: 0; padding-bottom: 60%;"><span style="color: #565656;">Make this Notebook Trusted to load map: File -&gt; Trust Notebook</span>&lt;iframe allowfullscreen="" data-html="PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYycsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNTEuNTA3MzIxOSwtMC4xMjc2NDc0XSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHpvb206IDExLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbWF4Qm91bmRzOiBib3VuZHMsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBsYXllcnM6IFtdLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgd29ybGRDb3B5SnVtcDogZmFsc2UsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBjcnM6IEwuQ1JTLkVQU0czODU3CiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgdGlsZV9sYXllcl8wNjk1YmI4YTE1YWM0MGE4OTQwZjMzODk1NWEzYWU3MSA9IEwudGlsZUxheWVyKAogICAgICAgICAgICAgICAgJ2h0dHBzOi8ve3N9LnRpbGUub3BlbnN0cmVldG1hcC5vcmcve3p9L3t4fS97eX0ucG5nJywKICAgICAgICAgICAgICAgIHsKICAiYXR0cmlidXRpb24iOiBudWxsLAogICJkZXRlY3RSZXRpbmEiOiBmYWxzZSwKICAibWF4Wm9vbSI6IDE4LAogICJtaW5ab29tIjogMSwKICAibm9XcmFwIjogZmFsc2UsCiAgInN1YmRvbWFpbnMiOiAiYWJjIgp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTlmNjU3ZGI1OWJlNDE2ZDkxZmIwNjJhMTQyMWZhNDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTMyMTI2LC0wLjEyMTM2MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zMGNiMzA5N2Q2MzY0ZmExYmI4OTFhYWNiN2IxOWY0OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zOWY3YzEyMTJhOTk0MTE3YjgwOTE2OTFjNDA5YWVhNCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzlmN2MxMjEyYTk5NDExN2I4MDkxNjkxYzQwOWFlYTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFMQkVSVCBFTUJBTktNRU5UPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zMGNiMzA5N2Q2MzY0ZmExYmI4OTFhYWNiN2IxOWY0OC5zZXRDb250ZW50KGh0bWxfMzlmN2MxMjEyYTk5NDExN2I4MDkxNjkxYzQwOWFlYTQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTlmNjU3ZGI1OWJlNDE2ZDkxZmIwNjJhMTQyMWZhNDIuYmluZFBvcHVwKHBvcHVwXzMwY2IzMDk3ZDYzNjRmYTFiYjg5MWFhY2I3YjE5ZjQ4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U2YmQ3ZDE4NzA0YzRlNzViZjAwOTI0NmY5ZTg2YzVhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTQxLjI3Mzc1NzU1LDE3My4yODkzOTMyMzkxMDM1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zMDNiZTc2ZDBlNTQ0MDAzOTc2YTI0MjU5ODU0MDY1ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iOWE1YTYzMzFmZDM0ZDRkYWU3Nzk3NjdmYjlmOTI1MiA9ICQoJzxkaXYgaWQ9Imh0bWxfYjlhNWE2MzMxZmQzNGQ0ZGFlNzc5NzY3ZmI5ZjkyNTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFMQklPTiBTUVVBUkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMwM2JlNzZkMGU1NDQwMDM5NzZhMjQyNTk4NTQwNjVmLnNldENvbnRlbnQoaHRtbF9iOWE1YTYzMzFmZDM0ZDRkYWU3Nzk3NjdmYjlmOTI1Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNmJkN2QxODcwNGM0ZTc1YmYwMDkyNDZmOWU4NmM1YS5iaW5kUG9wdXAocG9wdXBfMzAzYmU3NmQwZTU0NDAwMzk3NmEyNDI1OTg1NDA2NWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWExNjJlN2Q0NGQ3NGJhOWI0YmEwOTQ3MDk3YmU2NTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTI3NDE2LC0wLjM3MTk2NzRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDk2NjdkZDUzMTQ2NDc0ODhiZDdiNTA0MDhkMTI0MmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmEzNGRjMDY4NWQwNDBhOTlhNTNjMGFlYjU0MTVhMzUgPSAkKCc8ZGl2IGlkPSJodG1sXzZhMzRkYzA2ODVkMDQwYTk5YTUzYzBhZWI1NDE1YTM1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BTExFWU4gUEFSSzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDk2NjdkZDUzMTQ2NDc0ODhiZDdiNTA0MDhkMTI0MmMuc2V0Q29udGVudChodG1sXzZhMzRkYzA2ODVkMDQwYTk5YTUzYzBhZWI1NDE1YTM1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlhMTYyZTdkNDRkNzRiYTliNGJhMDk0NzA5N2JlNjU0LmJpbmRQb3B1cChwb3B1cF9kOTY2N2RkNTMxNDY0NzQ4OGJkN2I1MDQwOGQxMjQyYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zYjc1ZWM5MjY0Yzk0YzFkYjIzNWI3MjhkOWIyOGFiMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzIyLjMxOTAzNjIsMTE0LjE2Nzg0MTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjg0ZWUxMzJiYmQ1NGY4YzgyZjgwNzkxMjk1YjVmMTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjI4ZjE5OGQ2Nzk1NDMwNGE5MmI3ODU1MWIzNWQ2YTggPSAkKCc8ZGl2IGlkPSJodG1sXzIyOGYxOThkNjc5NTQzMDRhOTJiNzg1NTFiMzVkNmE4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BUkdZTEUgU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iODRlZTEzMmJiZDU0ZjhjODJmODA3OTEyOTViNWYxOC5zZXRDb250ZW50KGh0bWxfMjI4ZjE5OGQ2Nzk1NDMwNGE5MmI3ODU1MWIzNWQ2YTgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2I3NWVjOTI2NGM5NGMxZGIyMzViNzI4ZDliMjhhYjAuYmluZFBvcHVwKHBvcHVwX2I4NGVlMTMyYmJkNTRmOGM4MmY4MDc5MTI5NWI1ZjE4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2QzMTY4OTBmNDU3MDQ3YzU4ODUwZGViZjU3MjVkYTllID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuNzI1MzA0MSwtODguMjA1NTI5Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NTliNDRhODRlYmY0NjJlYjE2NDFmYzA3MzMyYjQwMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iODg5Mzg3MTlmMmQ0MGQxYjUyMmQ0ZjFmMzQ3N2FmNCA9ICQoJzxkaXYgaWQ9Imh0bWxfYjg4OTM4NzE5ZjJkNDBkMWI1MjJkNGYxZjM0NzdhZjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFSVEVTSUFOIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg1OWI0NGE4NGViZjQ2MmViMTY0MWZjMDczMzJiNDAzLnNldENvbnRlbnQoaHRtbF9iODg5Mzg3MTlmMmQ0MGQxYjUyMmQ0ZjFmMzQ3N2FmNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMzE2ODkwZjQ1NzA0N2M1ODg1MGRlYmY1NzI1ZGE5ZS5iaW5kUG9wdXAocG9wdXBfODU5YjQ0YTg0ZWJmNDYyZWIxNjQxZmMwNzMzMmI0MDMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODI0YTg3MWVhNDVjNGYxYmJmM2ZjOWMwYzdlYzkwZTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MDExMjA4LC0wLjI0MTQxMDhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTcwMzIwZjRiNWJlNDMwZDlmYzNhNWUxNzQzMmZmNzIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOGUzZDRhODNjNDU2NDhjMDlmZWMzZDA5MDM4NDNjNWMgPSAkKCc8ZGl2IGlkPSJodG1sXzhlM2Q0YTgzYzQ1NjQ4YzA5ZmVjM2QwOTAzODQzYzVjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BU0hDSFVSQ0ggR1JPVkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U3MDMyMGY0YjViZTQzMGQ5ZmMzYTVlMTc0MzJmZjcyLnNldENvbnRlbnQoaHRtbF84ZTNkNGE4M2M0NTY0OGMwOWZlYzNkMDkwMzg0M2M1Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84MjRhODcxZWE0NWM0ZjFiYmYzZmM5YzBjN2VjOTBlMy5iaW5kUG9wdXAocG9wdXBfZTcwMzIwZjRiNWJlNDMwZDlmYzNhNWUxNzQzMmZmNzIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDZhMGE3YTNlN2NiNGUzMmE4NzRhN2RmNzE5ZjY3MzkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi4wMTg4MDY0LC0xLjMwNTk0NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTNkOGE3NTlhMThmNGM2MGFiMzc3MWMzOWRlZDZmM2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWJhNTcxOWE0NjhlNDQxMTllNTkxZDg4YTcyZjMxMTcgPSAkKCc8ZGl2IGlkPSJodG1sXzFiYTU3MTlhNDY4ZTQ0MTE5ZTU5MWQ4OGE3MmYzMTE3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BWU5IT0UgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTNkOGE3NTlhMThmNGM2MGFiMzc3MWMzOWRlZDZmM2Muc2V0Q29udGVudChodG1sXzFiYTU3MTlhNDY4ZTQ0MTE5ZTU5MWQ4OGE3MmYzMTE3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA2YTBhN2EzZTdjYjRlMzJhODc0YTdkZjcxOWY2NzM5LmJpbmRQb3B1cChwb3B1cF9lM2Q4YTc1OWExOGY0YzYwYWIzNzcxYzM5ZGVkNmYzYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yNjBiNDEwMWJmMDI0ODYzYWMyNTdjZTk2MmU5ZDc1NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzU0LjgxNTM4NjYsLTEuNDU0ODk1MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNTkxMTFiN2E1MWE0OGFlYWU1NjZmM2ZiMTE1MWUwMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kNWY4ZWRiMDBlMjk0NDk5YTg0NWExYzVmZTljYWE2NCA9ICQoJzxkaXYgaWQ9Imh0bWxfZDVmOGVkYjAwZTI5NDQ5OWE4NDVhMWM1ZmU5Y2FhNjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJFREFMRSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI1OTExMWI3YTUxYTQ4YWVhZTU2NmYzZmIxMTUxZTAyLnNldENvbnRlbnQoaHRtbF9kNWY4ZWRiMDBlMjk0NDk5YTg0NWExYzVmZTljYWE2NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yNjBiNDEwMWJmMDI0ODYzYWMyNTdjZTk2MmU5ZDc1Ny5iaW5kUG9wdXAocG9wdXBfMjU5MTExYjdhNTFhNDhhZWFlNTY2ZjNmYjExNTFlMDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGQxZDlmODUzNjBmNDhkNmFmOWJjYjM5MDI5NzVlYjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1NC44NTU1Mjc4NSwtNi4yOTA1ODA2OTAxMTkwNTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmU3MzJhMGFiZTBlNDhiM2IyNjQwOWY2Yjc0MjExZTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzgwOWM3ZDhjNWQ5NGRkMGE4MTcwODUxY2EyNGQ4OTUgPSAkKCc8ZGl2IGlkPSJodG1sXzM4MDljN2Q4YzVkOTRkZDBhODE3MDg1MWNhMjRkODk1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CRUVDSFdPT0QgQVZFTlVFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZTczMmEwYWJlMGU0OGIzYjI2NDA5ZjZiNzQyMTFlNC5zZXRDb250ZW50KGh0bWxfMzgwOWM3ZDhjNWQ5NGRkMGE4MTcwODUxY2EyNGQ4OTUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGQxZDlmODUzNjBmNDhkNmFmOWJjYjM5MDI5NzVlYjkuYmluZFBvcHVwKHBvcHVwXzZlNzMyYTBhYmUwZTQ4YjNiMjY0MDlmNmI3NDIxMWU0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VhMTQ3NzY2YzBlYjQ0ODk4ZjkzNGI5MjQyZGU0OGM0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTE0OTU3NiwtMC4xMjM4OTQ2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZhNTlmYjQ4MGJkNTRkYzg4NGJjMjBhMWY0YjIwNGNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YxZGVmODkxYjZkZTQzNDg5YWNlY2Q0Nzc5NGY4YTM4ID0gJCgnPGRpdiBpZD0iaHRtbF9mMWRlZjg5MWI2ZGU0MzQ4OWFjZWNkNDc3OTRmOGEzOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QkVUVEVSVE9OIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmE1OWZiNDgwYmQ1NGRjODg0YmMyMGExZjRiMjA0Y2Yuc2V0Q29udGVudChodG1sX2YxZGVmODkxYjZkZTQzNDg5YWNlY2Q0Nzc5NGY4YTM4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VhMTQ3NzY2YzBlYjQ0ODk4ZjkzNGI5MjQyZGU0OGM0LmJpbmRQb3B1cChwb3B1cF9mYTU5ZmI0ODBiZDU0ZGM4ODRiYzIwYTFmNGIyMDRjZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMzVkYWRmZDUwOTM0NjBiYWJhZjE4MjAzMjQ1NWFiNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzU3LjAzMjAzNzYsLTIuMTQ3Njk4NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xNTg0MjFkNzM1MGU0OGYyYWRhMDAzYzVjODkzZmE5ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZjY3YTdlNzU3MDM0YThkOTBhMGEyZDIwYTQyNzE4MSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmY2N2E3ZTc1NzAzNGE4ZDkwYTBhMmQyMGE0MjcxODEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJFVFRSSURHRSBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xNTg0MjFkNzM1MGU0OGYyYWRhMDAzYzVjODkzZmE5ZS5zZXRDb250ZW50KGh0bWxfMmY2N2E3ZTc1NzAzNGE4ZDkwYTBhMmQyMGE0MjcxODEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTM1ZGFkZmQ1MDkzNDYwYmFiYWYxODIwMzI0NTVhYjYuYmluZFBvcHVwKHBvcHVwXzE1ODQyMWQ3MzUwZTQ4ZjJhZGEwMDNjNWM4OTNmYTllKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA5YzYxOWE3ZTE3YzQzYzE4MTQwODI3OWIwZjMwMWYwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDQuOTAyMDQ2LC02OS4wNzYzMzNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODI1YzNiYjQ5ODZiNDEwMjk5OGUwZDM2NDQwNWU4ZjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODFmNzc3NDhjZWRjNDNjZmE0ZGZlMDE5YWI1YjI3NzUgPSAkKCc8ZGl2IGlkPSJodG1sXzgxZjc3NzQ4Y2VkYzQzY2ZhNGRmZTAxOWFiNWIyNzc1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CSUxMSU5HIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgyNWMzYmI0OTg2YjQxMDI5OThlMGQzNjQ0MDVlOGY1LnNldENvbnRlbnQoaHRtbF84MWY3Nzc0OGNlZGM0M2NmYTRkZmUwMTlhYjViMjc3NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wOWM2MTlhN2UxN2M0M2MxODE0MDgyNzliMGYzMDFmMC5iaW5kUG9wdXAocG9wdXBfODI1YzNiYjQ5ODZiNDEwMjk5OGUwZDM2NDQwNWU4ZjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTdlYmRiNDg2Nzk3NGFhNWJjNjRjOWZhYjhkMTYzOWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1OC4zOTE4MjUzLC0xMjQuODI1Njc4OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMjY4NjcyYWZlYjU0MzBmYmU4ZDRlZDM0ODJhY2E5NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNmUwZGM2ZjdmNDM0ZGJjYjFjOWFlYWQxOTIyODljOSA9ICQoJzxkaXYgaWQ9Imh0bWxfYTZlMGRjNmY3ZjQzNGRiY2IxYzlhZWFkMTkyMjg5YzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJMRU5IRUlNIENSRVNDRU5UPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMjY4NjcyYWZlYjU0MzBmYmU4ZDRlZDM0ODJhY2E5Ny5zZXRDb250ZW50KGh0bWxfYTZlMGRjNmY3ZjQzNGRiY2IxYzlhZWFkMTkyMjg5YzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTdlYmRiNDg2Nzk3NGFhNWJjNjRjOWZhYjhkMTYzOWUuYmluZFBvcHVwKHBvcHVwX2YyNjg2NzJhZmViNTQzMGZiZThkNGVkMzQ4MmFjYTk3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc5NjY2MTViOWFjZTQ1ZWU5OGE2NjM1ZmZiZGM3MzgwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuNDg3OTQyMiwtMi4yMTUxODQ3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdkMGFmODJjNDRiMzQwY2NiOGI1ZjQyMzRlODVjODlkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI4ZjY4ZDE3MmFjNDRjYzNhYTViMTYzMjhkZDQ3ZDMyID0gJCgnPGRpdiBpZD0iaHRtbF8yOGY2OGQxNzJhYzQ0Y2MzYWE1YjE2MzI4ZGQ0N2QzMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Qk9VUkRPTiBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdkMGFmODJjNDRiMzQwY2NiOGI1ZjQyMzRlODVjODlkLnNldENvbnRlbnQoaHRtbF8yOGY2OGQxNzJhYzQ0Y2MzYWE1YjE2MzI4ZGQ0N2QzMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83OTY2NjE1YjlhY2U0NWVlOThhNjYzNWZmYmRjNzM4MC5iaW5kUG9wdXAocG9wdXBfN2QwYWY4MmM0NGIzNDBjY2I4YjVmNDIzNGU4NWM4OWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzgzNzNmYmQ5MmNkNGE2MmIzYTAwM2JiZDExYWM0MDUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1My40MDM4OTcsLTEuNDk4NDIwNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wM2JiOWU2YjNiNWI0YzQ5YmRlNmNmOTljNDlmMzczNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNjgwYmJiMGQ2NTc0ZTlmOTkwNDViZTFmOTliM2ZhNyA9ICQoJzxkaXYgaWQ9Imh0bWxfZjY4MGJiYjBkNjU3NGU5Zjk5MDQ1YmUxZjk5YjNmYTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJSQURGSUVMRCBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wM2JiOWU2YjNiNWI0YzQ5YmRlNmNmOTljNDlmMzczNy5zZXRDb250ZW50KGh0bWxfZjY4MGJiYjBkNjU3NGU5Zjk5MDQ1YmUxZjk5YjNmYTcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzgzNzNmYmQ5MmNkNGE2MmIzYTAwM2JiZDExYWM0MDUuYmluZFBvcHVwKHBvcHVwXzAzYmI5ZTZiM2I1YjRjNDliZGU2Y2Y5OWM0OWYzNzM3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VlNzhkM2U5MDA0MTQ0YjFhZTFlMWNjYjMxYzhlMzQ1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuODM5MDY0LC03My4zOTU5NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2U0NmJiMmFmNTYzNDkyN2E0Mjc3MDllZjhiYzQ3MGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDFhMWIwODk2Mjg4NGE3ZWExYTEzMzBmNWExNzljY2UgPSAkKCc8ZGl2IGlkPSJodG1sXzQxYTFiMDg5NjI4ODRhN2VhMWExMzMwZjVhMTc5Y2NlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CUk9NUFRPTiBQTEFDRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfY2U0NmJiMmFmNTYzNDkyN2E0Mjc3MDllZjhiYzQ3MGMuc2V0Q29udGVudChodG1sXzQxYTFiMDg5NjI4ODRhN2VhMWExMzMwZjVhMTc5Y2NlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VlNzhkM2U5MDA0MTQ0YjFhZTFlMWNjYjMxYzhlMzQ1LmJpbmRQb3B1cChwb3B1cF9jZTQ2YmIyYWY1NjM0OTI3YTQyNzcwOWVmOGJjNDcwYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lYjFmZDU1ZDY5MTY0YzAxOWI1NWJmY2EwN2I3MGYwMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQxNzAyMjcsLTAuMTUwOTI4Nl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZjBjODY4MjU5MGQ0MTg2ODZmOTlhNjAzNDJkMWU1MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jOWQwOWU3ZTY2MDI0MjlmYmIwYjZjNDg4NzM1MGI0MSA9ICQoJzxkaXYgaWQ9Imh0bWxfYzlkMDllN2U2NjAyNDI5ZmJiMGI2YzQ4ODczNTBiNDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBSVRITkVTUyBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zZjBjODY4MjU5MGQ0MTg2ODZmOTlhNjAzNDJkMWU1Mi5zZXRDb250ZW50KGh0bWxfYzlkMDllN2U2NjAyNDI5ZmJiMGI2YzQ4ODczNTBiNDEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWIxZmQ1NWQ2OTE2NGMwMTliNTViZmNhMDdiNzBmMDMuYmluZFBvcHVwKHBvcHVwXzNmMGM4NjgyNTkwZDQxODY4NmY5OWE2MDM0MmQxZTUyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIwYjZlZTAzZmY3ZjRjNzg5NzliZmUwYjc4MGNjMWU3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTQxLjI5NzMxNTIsMTc0Ljc4MTg2NTZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWQ4ZmU4NTA5MDQyNDAyZmIwMTcxNGRiMjE4NGFjOTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmVkMDU3Yzc5M2NkNGVjMmE2MWYxNmNjNGIxY2JjYWEgPSAkKCc8ZGl2IGlkPSJodG1sX2ZlZDA1N2M3OTNjZDRlYzJhNjFmMTZjYzRiMWNiY2FhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQU1CUklER0UgVEVSUkFDRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWQ4ZmU4NTA5MDQyNDAyZmIwMTcxNGRiMjE4NGFjOTYuc2V0Q29udGVudChodG1sX2ZlZDA1N2M3OTNjZDRlYzJhNjFmMTZjYzRiMWNiY2FhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIwYjZlZTAzZmY3ZjRjNzg5NzliZmUwYjc4MGNjMWU3LmJpbmRQb3B1cChwb3B1cF85ZDhmZTg1MDkwNDI0MDJmYjAxNzE0ZGIyMTg0YWM5Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMDk2ZTBiNGNlNTM0OTcyYWM4YWE2OGU4ZjczZGEwMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjkwMTM1ODY1MDAwMDAwNiwtODAuNjMzNDc2NzYwMDU4MjJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWMyZmE0NWY3MDE5NDJlOGI4YzBhMjI0NWMzOGYxOTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzdiY2RmODMyMmM0NGExZjk0OWQwYjI5MTFlYjdkZGYgPSAkKCc8ZGl2IGlkPSJodG1sXzM3YmNkZjgzMjJjNDRhMWY5NDlkMGIyOTExZWI3ZGRmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQU1ERU4gU1FVQVJFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85YzJmYTQ1ZjcwMTk0MmU4YjhjMGEyMjQ1YzM4ZjE5My5zZXRDb250ZW50KGh0bWxfMzdiY2RmODMyMmM0NGExZjk0OWQwYjI5MTFlYjdkZGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzA5NmUwYjRjZTUzNDk3MmFjOGFhNjhlOGY3M2RhMDIuYmluZFBvcHVwKHBvcHVwXzljMmZhNDVmNzAxOTQyZThiOGMwYTIyNDVjMzhmMTkzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U0OTg1MzdjYzE5ZDRlOGJhODIyZGY2MzE4YzE3ZDk2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTA2MzUxNSwtMC4xOTg4NTMxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NhNjJmZWNiM2Q2MjQzNDNhMmM0Y2FjNThjOGI2N2Y5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVkY2I4ZjkxYmEzMjQ2Yzc4NjgyMGU1N2IzZjY3YzQ2ID0gJCgnPGRpdiBpZD0iaHRtbF81ZGNiOGY5MWJhMzI0NmM3ODY4MjBlNTdiM2Y2N2M0NiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q0FNUERFTiBISUxMIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NhNjJmZWNiM2Q2MjQzNDNhMmM0Y2FjNThjOGI2N2Y5LnNldENvbnRlbnQoaHRtbF81ZGNiOGY5MWJhMzI0NmM3ODY4MjBlNTdiM2Y2N2M0Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNDk4NTM3Y2MxOWQ0ZThiYTgyMmRmNjMxOGMxN2Q5Ni5iaW5kUG9wdXAocG9wdXBfY2E2MmZlY2IzZDYyNDM0M2EyYzRjYWM1OGM4YjY3ZjkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmJmZDcxNjNlZDNjNDM1ZDgyY2RiYTU2MGRlZDI2OTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NDUyOTczLC0wLjA5NDQzNDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWVkNzQzMDRkN2FiNGU1Y2I4MjEwMTNjNGRiNzRjMTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNThlNmE1MGQyOTU1NDMyNWIwYWM0NDEyMTIyNzQzYmYgPSAkKCc8ZGl2IGlkPSJodG1sXzU4ZTZhNTBkMjk1NTQzMjViMGFjNDQxMjEyMjc0M2JmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQU5PTkJVUlkgUEFSSyBTT1VUSDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWVkNzQzMDRkN2FiNGU1Y2I4MjEwMTNjNGRiNzRjMTUuc2V0Q29udGVudChodG1sXzU4ZTZhNTBkMjk1NTQzMjViMGFjNDQxMjEyMjc0M2JmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZiZmQ3MTYzZWQzYzQzNWQ4MmNkYmE1NjBkZWQyNjk0LmJpbmRQb3B1cChwb3B1cF85ZWQ3NDMwNGQ3YWI0ZTVjYjgyMTAxM2M0ZGI3NGMxNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MDM0ZTc2ODUxODI0ZjJlYjJiMGQ5MjA2NjkwNGNlOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM1LjA1MzEyNDg1LC04MS45NDE1Njk4MDE4MDA1MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZDhlYTc2MGY5ZWM0Y2I1ODUwMzg4YTkxNzEyNzJlNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83YzBmOGQxYmI5NDY0ZjcxYjkyOGYzMmNlMWZhMmNmYSA9ICQoJzxkaXYgaWQ9Imh0bWxfN2MwZjhkMWJiOTQ2NGY3MWI5MjhmMzJjZTFmYTJjZmEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBUkxJU0xFIFBMQUNFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kZDhlYTc2MGY5ZWM0Y2I1ODUwMzg4YTkxNzEyNzJlNC5zZXRDb250ZW50KGh0bWxfN2MwZjhkMWJiOTQ2NGY3MWI5MjhmMzJjZTFmYTJjZmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzAzNGU3Njg1MTgyNGYyZWIyYjBkOTIwNjY5MDRjZTguYmluZFBvcHVwKHBvcHVwX2RkOGVhNzYwZjllYzRjYjU4NTAzODhhOTE3MTI3MmU0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU0YjhkMmZkYzc5YzRhZTQ4ZjMyOTMzMGQ1NGQ3M2Y1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTM3LjgwMTk0MzM1LDE0NC45NzE5NzAxNzEwMTcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzcxMDNhMTgxOWNhMjQyYjhhMTNkZTUwNTU5MmM5NzYxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M5NGU5NmRjNDkwNjQzM2M4NjllMGVjNjI5NTM5NmU5ID0gJCgnPGRpdiBpZD0iaHRtbF9jOTRlOTZkYzQ5MDY0MzNjODY5ZTBlYzYyOTUzOTZlOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q0FSTFRPTiBHQVJERU5TPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MTAzYTE4MTljYTI0MmI4YTEzZGU1MDU1OTJjOTc2MS5zZXRDb250ZW50KGh0bWxfYzk0ZTk2ZGM0OTA2NDMzYzg2OWUwZWM2Mjk1Mzk2ZTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTRiOGQyZmRjNzljNGFlNDhmMzI5MzMwZDU0ZDczZjUuYmluZFBvcHVwKHBvcHVwXzcxMDNhMTgxOWNhMjQyYjhhMTNkZTUwNTU5MmM5NzYxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I4MzQ3ZjVjZTMwYzRmMjViYWE1NmI5M2E2N2ZiMDIzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzEuMzY2MjY2LC04My45MzU0ODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTgxODE3MjZiNTRhNGE2NWI0MDJiYjBmNTM0MGQxM2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzhiY2M3OTlhZDNiNGEyODgwMTM1YmMwMDZhNGYzMjggPSAkKCc8ZGl2IGlkPSJodG1sXzc4YmNjNzk5YWQzYjRhMjg4MDEzNWJjMDA2YTRmMzI4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQVJMVE9OIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk4MTgxNzI2YjU0YTRhNjViNDAyYmIwZjUzNDBkMTNjLnNldENvbnRlbnQoaHRtbF83OGJjYzc5OWFkM2I0YTI4ODAxMzViYzAwNmE0ZjMyOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iODM0N2Y1Y2UzMGM0ZjI1YmFhNTZiOTNhNjdmYjAyMy5iaW5kUG9wdXAocG9wdXBfOTgxODE3MjZiNTRhNGE2NWI0MDJiYjBmNTM0MGQxM2MpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzhkNDI5YmU3ZmJjNGNiYmEzYzRlOGY5YjhiMmQ1NDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NDEwODc2LC0wLjE1NTkyNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmYzYmYzMDhmN2ZlNGU3NGE4MmI3NTY2OThlN2YxNDggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTcyMjJlNzNjYTUwNDkxNWJhNTEzOTI1MTIwMTMzNmMgPSAkKCc8ZGl2IGlkPSJodG1sXzE3MjIyZTczY2E1MDQ5MTViYTUxMzkyNTEyMDEzMzZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DSEFMQ09UIFNRVUFSRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmYzYmYzMDhmN2ZlNGU3NGE4MmI3NTY2OThlN2YxNDguc2V0Q29udGVudChodG1sXzE3MjIyZTczY2E1MDQ5MTViYTUxMzkyNTEyMDEzMzZjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M4ZDQyOWJlN2ZiYzRjYmJhM2M0ZThmOWI4YjJkNTQzLmJpbmRQb3B1cChwb3B1cF82ZjNiZjMwOGY3ZmU0ZTc0YTgyYjc1NjY5OGU3ZjE0OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kZWIwN2E2ZWIwMjQ0M2JjODg1ZTVkNjA2MWNjMDIxYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ4Mjc4NzUsLTAuMTcwNDkxNF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80NjEzM2U1MzllZGI0YzJkODJkYTY5YjY5Mzc2Nzg4ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mODY0YmIxZDJmMjY0YzFkYjhmZjMzMjIyZWJjN2RhYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjg2NGJiMWQyZjI2NGMxZGI4ZmYzMzIyMmViYzdkYWEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNIRUxTRUEgRU1CQU5LTUVOVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDYxMzNlNTM5ZWRiNGMyZDgyZGE2OWI2OTM3Njc4OGQuc2V0Q29udGVudChodG1sX2Y4NjRiYjFkMmYyNjRjMWRiOGZmMzMyMjJlYmM3ZGFhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RlYjA3YTZlYjAyNDQzYmM4ODVlNWQ2MDYxY2MwMjFiLmJpbmRQb3B1cChwb3B1cF80NjEzM2U1MzllZGI0YzJkODJkYTY5YjY5Mzc2Nzg4ZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MTk2NmM1YWJiNTA0MDZkYmQ5NDA0NWJlYjhjOGYwNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5ODcyNTcsLTAuMTkyODE0OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80MzY1MGQxN2JlZWQ0YjZjYjNlM2Q3M2FiOWFkMjRiYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85ZDIzODQ0N2Y5MjY0NWFmYjhhNzFkYmU4MDA3YmQ5YyA9ICQoJzxkaXYgaWQ9Imh0bWxfOWQyMzg0NDdmOTI2NDVhZmI4YTcxZGJlODAwN2JkOWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNIRU5JU1RPTiBHQVJERU5TPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MzY1MGQxN2JlZWQ0YjZjYjNlM2Q3M2FiOWFkMjRiYi5zZXRDb250ZW50KGh0bWxfOWQyMzg0NDdmOTI2NDVhZmI4YTcxZGJlODAwN2JkOWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDE5NjZjNWFiYjUwNDA2ZGJkOTQwNDViZWI4YzhmMDcuYmluZFBvcHVwKHBvcHVwXzQzNjUwZDE3YmVlZDRiNmNiM2UzZDczYWI5YWQyNGJiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNjMWFhYTY2OTliYzQwN2ViNDBmZjE0ZDJiYjRlZjZiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTMyLjA5MTQ4MywxMTUuOTA0MjY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mYmM2MTFmMDhkMTg0YWNkOTE2N2IwNGJmNmJkNmNlZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NDAzMDBiOGEzZDI0NTBhYWVmOWI3MzkyMzcxZTA3ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNzQwMzAwYjhhM2QyNDUwYWFlZjliNzM5MjM3MWUwN2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNIRVNIQU0gTUVXUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmJjNjExZjA4ZDE4NGFjZDkxNjdiMDRiZjZiZDZjZWQuc2V0Q29udGVudChodG1sXzc0MDMwMGI4YTNkMjQ1MGFhZWY5YjczOTIzNzFlMDdkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNjMWFhYTY2OTliYzQwN2ViNDBmZjE0ZDJiYjRlZjZiLmJpbmRQb3B1cChwb3B1cF9mYmM2MTFmMDhkMTg0YWNkOTE2N2IwNGJmNmJkNmNlZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84Mzk0ZTRjOTY5MzA0NGVhYmE1OTdmODNkYzhhMGUzYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjI4NDMxNjQsMC4xNTAzMzk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MyNWM5NzU2NDAzODQ1MzJiNTAxZGYwZjY4MGNmYjdjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgzNDU4MzNiZmZmYTRhODI5OTc0ZWMxZGUyMzA3ZmUyID0gJCgnPGRpdiBpZD0iaHRtbF84MzQ1ODMzYmZmZmE0YTgyOTk3NGVjMWRlMjMwN2ZlMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q0hFVkVOSU5HIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MyNWM5NzU2NDAzODQ1MzJiNTAxZGYwZjY4MGNmYjdjLnNldENvbnRlbnQoaHRtbF84MzQ1ODMzYmZmZmE0YTgyOTk3NGVjMWRlMjMwN2ZlMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84Mzk0ZTRjOTY5MzA0NGVhYmE1OTdmODNkYzhhMGUzYy5iaW5kUG9wdXAocG9wdXBfYzI1Yzk3NTY0MDM4NDUzMmI1MDFkZjBmNjgwY2ZiN2MpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWJjZDA5MzdmYzI2NDQ1MWE2NjIyMTI1MDk3NmRiNGQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi42MTc0MDU0LC0xLjE1NzI0MDhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWY2N2UxODhkYWU3NGFhZjg0NDBmNzgwZjBlOTM3ZjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTllZmVhYzc0OWJlNDdhZWJiOTMwZGU2M2RjMzkzM2YgPSAkKCc8ZGl2IGlkPSJodG1sX2U5ZWZlYWM3NDliZTQ3YWViYjkzMGRlNjNkYzM5MzNmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DT0xMSU5HSEFNIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FmNjdlMTg4ZGFlNzRhYWY4NDQwZjc4MGYwZTkzN2Y1LnNldENvbnRlbnQoaHRtbF9lOWVmZWFjNzQ5YmU0N2FlYmI5MzBkZTYzZGMzOTMzZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YmNkMDkzN2ZjMjY0NDUxYTY2MjIxMjUwOTc2ZGI0ZC5iaW5kUG9wdXAocG9wdXBfYWY2N2UxODhkYWU3NGFhZjg0NDBmNzgwZjBlOTM3ZjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzhjOGEwNGM0ZTFhNGUzYmI5NWEzYTM2ZGJmY2JjYWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zNTk2ODYxLDEuMDk3OTMwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZjNkZjNmYzNlNTQ0YmE4YmM3MTBjYzlmNTg1ZTlkNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83Y2VjNjg2Nzg5ZTk0ZDk4OTE3Zjg4MzJjNWE0OGFlYSA9ICQoJzxkaXYgaWQ9Imh0bWxfN2NlYzY4Njc4OWU5NGQ5ODkxN2Y4ODMyYzVhNDhhZWEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNPVUxURVIgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2YzZGYzZmMzZTU0NGJhOGJjNzEwY2M5ZjU4NWU5ZDcuc2V0Q29udGVudChodG1sXzdjZWM2ODY3ODllOTRkOTg5MTdmODgzMmM1YTQ4YWVhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M4YzhhMDRjNGUxYTRlM2JiOTVhM2EzNmRiZmNiY2FmLmJpbmRQb3B1cChwb3B1cF8zZjNkZjNmYzNlNTQ0YmE4YmM3MTBjYzlmNTg1ZTlkNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MjM1YWIwMWMzYjI0OWY1Yjk4NzVkY2YxZWZhYWVmNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ0ODUwMDIsLTAuMDgwMTAxNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lYjQ1NDY1OGNkMzI0OWVmYTFkZWVjOTU0MTY5ODU5OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yNzU2YjQ2OTBiM2I0ZjA4YjJjYjAxOTI3YTc0ZGMyNyA9ICQoJzxkaXYgaWQ9Imh0bWxfMjc1NmI0NjkwYjNiNGYwOGIyY2IwMTkyN2E3NGRjMjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNPVVJUIExBTkUgR0FSREVOUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWI0NTQ2NThjZDMyNDllZmExZGVlYzk1NDE2OTg1OTkuc2V0Q29udGVudChodG1sXzI3NTZiNDY5MGIzYjRmMDhiMmNiMDE5MjdhNzRkYzI3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgyMzVhYjAxYzNiMjQ5ZjViOTg3NWRjZjFlZmFhZWY2LmJpbmRQb3B1cChwb3B1cF9lYjQ1NDY1OGNkMzI0OWVmYTFkZWVjOTU0MTY5ODU5OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xN2RjZGYyMjcwNDA0NjI0YjA4YzAzNzBjYTkzMzlhOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUzODQzOTMsLTAuMzQ3ODg2OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xYWI3NWMyZjA0Nzg0MzQ3YmEwOWJhYTNhOWJjZTEzZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81Yzk5ODJmYWQyZDA0M2U5YTE0MmI0NzhiYmMxOGQ1YiA9ICQoJzxkaXYgaWQ9Imh0bWxfNWM5OTgyZmFkMmQwNDNlOWExNDJiNDc4YmJjMThkNWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNPVVJUSE9QRSBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xYWI3NWMyZjA0Nzg0MzQ3YmEwOWJhYTNhOWJjZTEzZS5zZXRDb250ZW50KGh0bWxfNWM5OTgyZmFkMmQwNDNlOWExNDJiNDc4YmJjMThkNWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTdkY2RmMjI3MDQwNDYyNGIwOGMwMzcwY2E5MzM5YTkuYmluZFBvcHVwKHBvcHVwXzFhYjc1YzJmMDQ3ODQzNDdiYTA5YmFhM2E5YmNlMTNlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcwODQ3OGQyMGMxNzRlMTM4MDcyMWRkZTQyNGU0MGY3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTE2MjMyMSwtMC4xOTg4MjExXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUxYjNlMDRhN2Q5NDQ4NDY5MzNhMTE0MTFlMjk1NGZiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U4OGE4YjIwYTQ3NTQ1OGNiMjQ5NDg2NzEyYjY1ZDg1ID0gJCgnPGRpdiBpZD0iaHRtbF9lODhhOGIyMGE0NzU0NThjYjI0OTQ4NjcxMmI2NWQ4NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q09VUlRORUxMIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTFiM2UwNGE3ZDk0NDg0NjkzM2ExMTQxMWUyOTU0ZmIuc2V0Q29udGVudChodG1sX2U4OGE4YjIwYTQ3NTQ1OGNiMjQ5NDg2NzEyYjY1ZDg1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzcwODQ3OGQyMGMxNzRlMTM4MDcyMWRkZTQyNGU0MGY3LmJpbmRQb3B1cChwb3B1cF81MWIzZTA0YTdkOTQ0ODQ2OTMzYTExNDExZTI5NTRmYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zOTZiZWNkODM4NDI0MjQ5OTJkYmMzZTgzM2MwZjEzMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQxODE4OTgsLTAuMjM3Mzg0OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mZWUwNTZlNjVjOWM0ODdlODQyMmU2OTJlOWE1NzlmMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYzZhY2M2ZjU4MDM0ZDUzYTEyM2FhMjkzMGZlYTNkYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZWM2YWNjNmY1ODAzNGQ1M2ExMjNhYTI5MzBmZWEzZGEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNSQVdGT1JEIE1FV1M8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ZlZTA1NmU2NWM5YzQ4N2U4NDIyZTY5MmU5YTU3OWYwLnNldENvbnRlbnQoaHRtbF9lYzZhY2M2ZjU4MDM0ZDUzYTEyM2FhMjkzMGZlYTNkYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zOTZiZWNkODM4NDI0MjQ5OTJkYmMzZTgzM2MwZjEzMi5iaW5kUG9wdXAocG9wdXBfZmVlMDU2ZTY1YzljNDg3ZTg0MjJlNjkyZTlhNTc5ZjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDBkZmQyNTY3ZGI5NGUwNGI2NzllYTA2YWI1NDExYTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NTExMjgxLC0wLjE4OTAyNjJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTMzMDRhYjgwMTM5NGFkMjg5ZDFiNWUyNTUwZmY5ZGEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjg3ZDNkODlmNzMyNGM5YmI1YzVkYjFlMDA3MjRhNzQgPSAkKCc8ZGl2IGlkPSJodG1sX2Y4N2QzZDg5ZjczMjRjOWJiNWM1ZGIxZTAwNzI0YTc0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DUkVESVRPTiBISUxMPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMzMwNGFiODAxMzk0YWQyODlkMWI1ZTI1NTBmZjlkYS5zZXRDb250ZW50KGh0bWxfZjg3ZDNkODlmNzMyNGM5YmI1YzVkYjFlMDA3MjRhNzQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDBkZmQyNTY3ZGI5NGUwNGI2NzllYTA2YWI1NDExYTMuYmluZFBvcHVwKHBvcHVwX2UzMzA0YWI4MDEzOTRhZDI4OWQxYjVlMjU1MGZmOWRhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M1MjlhYTNjNDhiNDRiNjZiYjZkYTQyMjU1MjBhOWY3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTYxNDIwMiwtMC4xNDIyNDk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdmZmVmNjRkYTA0ZjQ0Y2Q5MDRhY2RiMDVhNWRkMzUzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY3YmZhNDBiMTFmNzRjZDdhN2U5ODU3MDU4NGQ3ZDZjID0gJCgnPGRpdiBpZD0iaHRtbF82N2JmYTQwYjExZjc0Y2Q3YTdlOTg1NzA1ODRkN2Q2YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+REFSVE1PVVRIIFBBUksgQVZFTlVFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZmZlZjY0ZGEwNGY0NGNkOTA0YWNkYjA1YTVkZDM1My5zZXRDb250ZW50KGh0bWxfNjdiZmE0MGIxMWY3NGNkN2E3ZTk4NTcwNTg0ZDdkNmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzUyOWFhM2M0OGI0NGI2NmJiNmRhNDIyNTUyMGE5ZjcuYmluZFBvcHVwKHBvcHVwXzdmZmVmNjRkYTA0ZjQ0Y2Q5MDRhY2RiMDVhNWRkMzUzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FlZjhmMTZiMGU0YjQ4NzliNzY0ZjIyY2FkMTcxZDQxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDk5NTUyMywtMC4xODQ4N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mZmFmOGIyZGJjNTc0NjEwYmI4NmU0OGU1YzVlYzA1MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNDUyMGQzMzNmNmU0ZDliOWI2YWU1MGU3MTA4NDYzYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjQ1MjBkMzMzZjZlNGQ5YjliNmFlNTBlNzEwODQ2M2IiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRFIFZFUkUgR0FSREVOUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmZhZjhiMmRiYzU3NDYxMGJiODZlNDhlNWM1ZWMwNTAuc2V0Q29udGVudChodG1sX2Y0NTIwZDMzM2Y2ZTRkOWI5YjZhZTUwZTcxMDg0NjNiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FlZjhmMTZiMGU0YjQ4NzliNzY0ZjIyY2FkMTcxZDQxLmJpbmRQb3B1cChwb3B1cF9mZmFmOGIyZGJjNTc0NjEwYmI4NmU0OGU1YzVlYzA1MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83ODIxNjZjMjMxOWI0ZDA4OGVhMTU0MDM5NTc3MmUzNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjc3MjIxNDA1LC0yLjY4ODU2MDYxNjU4Njg3ODNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmU5NTRhMGY5YzAyNDRkMjk5NTJiYjJiN2MyODg3NDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTI2OGRlOTcwNTQxNDlhODkyMzQwZTIwYmQ3YTBkODkgPSAkKCc8ZGl2IGlkPSJodG1sXzkyNjhkZTk3MDU0MTQ5YTg5MjM0MGUyMGJkN2EwZDg5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERUVQREFMRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmU5NTRhMGY5YzAyNDRkMjk5NTJiYjJiN2MyODg3NDUuc2V0Q29udGVudChodG1sXzkyNjhkZTk3MDU0MTQ5YTg5MjM0MGUyMGJkN2EwZDg5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc4MjE2NmMyMzE5YjRkMDg4ZWExNTQwMzk1NzcyZTM1LmJpbmRQb3B1cChwb3B1cF82ZTk1NGEwZjljMDI0NGQyOTk1MmJiMmI3YzI4ODc0NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMWFiYWE1MWI4ZDQ0MThiYmYyNWY4ZjJiODBiY2ZjYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUyLjcwNzQ3NjE1MDAwMDAwNSwtMi41MjY3OTQ1NTcxNjgxNDhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWEzMzAzZTVhNWNmNGE5MjliODc5MDk0ODc0N2YyYzQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTZmNmYxMzIyMWU5NGE0YzlmZWVkZjRmMmFhMmQzMmMgPSAkKCc8ZGl2IGlkPSJodG1sXzk2ZjZmMTMyMjFlOTRhNGM5ZmVlZGY0ZjJhYTJkMzJjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERUVSIFBBUksgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWEzMzAzZTVhNWNmNGE5MjliODc5MDk0ODc0N2YyYzQuc2V0Q29udGVudChodG1sXzk2ZjZmMTMyMjFlOTRhNGM5ZmVlZGY0ZjJhYTJkMzJjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QxYWJhYTUxYjhkNDQxOGJiZjI1ZjhmMmI4MGJjZmNjLmJpbmRQb3B1cChwb3B1cF9hYTMzMDNlNWE1Y2Y0YTkyOWI4NzkwOTQ4NzQ3ZjJjNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mZWRmYWJmMGJlZDk0N2IyYTY4NTk2YzQ1NTkxM2NmYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM1LjU1NTA5NjYsLTgyLjQ3ODgwNTZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDY1YTg3NzI1MmFhNDI3YjgwODM2NTNmOGYyYTNkN2UgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTNkNDVkYzc0N2NhNGEyM2EyYTE1ZWZlN2E4NDQwZmMgPSAkKCc8ZGl2IGlkPSJodG1sXzEzZDQ1ZGM3NDdjYTRhMjNhMmExNWVmZTdhODQ0MGZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERVZFUkVVWCBMQU5FPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNjVhODc3MjUyYWE0MjdiODA4MzY1M2Y4ZjJhM2Q3ZS5zZXRDb250ZW50KGh0bWxfMTNkNDVkYzc0N2NhNGEyM2EyYTE1ZWZlN2E4NDQwZmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmVkZmFiZjBiZWQ5NDdiMmE2ODU5NmM0NTU5MTNjZmMuYmluZFBvcHVwKHBvcHVwX2Q2NWE4NzcyNTJhYTQyN2I4MDgzNjUzZjhmMmEzZDdlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JjNjVjNjMzZGVkZjRhOWY4ZDQxZWU1MGYxYWM4YmIyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTIyMjMwNiwtMC4xNDkxNjc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQ4ZTE4Y2ZiMDQxZDRlYmFiMTIxOTY4ZGEyYThlYzczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JhMjIxNDkwNzg0NjQ4OWM5NGQyNmFkY2Q2YmI4YWVlID0gJCgnPGRpdiBpZD0iaHRtbF9iYTIyMTQ5MDc4NDY0ODljOTRkMjZhZGNkNmJiOGFlZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+REVWT05TSElSRSBNRVdTIFdFU1Q8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ4ZTE4Y2ZiMDQxZDRlYmFiMTIxOTY4ZGEyYThlYzczLnNldENvbnRlbnQoaHRtbF9iYTIyMTQ5MDc4NDY0ODljOTRkMjZhZGNkNmJiOGFlZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iYzY1YzYzM2RlZGY0YTlmOGQ0MWVlNTBmMWFjOGJiMi5iaW5kUG9wdXAocG9wdXBfNDhlMThjZmIwNDFkNGViYWIxMjE5NjhkYTJhOGVjNzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2Y1NGUxYTJmZDhiNDJiOTkzYTRmMGQ2OTE1MTMzOGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MDgzOTM0LC0wLjE0MjE2MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWYxNGY4ZjUyNGE3NDk5MGI1MTc3OWFmMGM4Mzg4YjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWViZTNiOWY2NWEwNGI4ZGEyMTUwZTQ5MmRkYjJlOTQgPSAkKCc8ZGl2IGlkPSJodG1sXzVlYmUzYjlmNjVhMDRiOGRhMjE1MGU0OTJkZGIyZTk0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ET1ZFUiBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFmMTRmOGY1MjRhNzQ5OTBiNTE3NzlhZjBjODM4OGI4LnNldENvbnRlbnQoaHRtbF81ZWJlM2I5ZjY1YTA0YjhkYTIxNTBlNDkyZGRiMmU5NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZjU0ZTFhMmZkOGI0MmI5OTNhNGYwZDY5MTUxMzM4Yy5iaW5kUG9wdXAocG9wdXBfMWYxNGY4ZjUyNGE3NDk5MGI1MTc3OWFmMGM4Mzg4YjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTA4MDYxZTkzMWJlNGMwMDk4MTViZmU0MDcyOTkzMGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MjYwODczLC0wLjMyNzg2NTZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzFmYWNiOGYyZjMzNGM0NmFlNzFjOWRiZDQyNTM1YmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjk2YzFmMjQ2ZWMxNGFmNjliMjYxODAzMzY5YTU2NTEgPSAkKCc8ZGl2IGlkPSJodG1sX2Y5NmMxZjI0NmVjMTRhZjY5YjI2MTgwMzM2OWE1NjUxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ET1dOU0lERSBDUkVTQ0VOVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzFmYWNiOGYyZjMzNGM0NmFlNzFjOWRiZDQyNTM1YmYuc2V0Q29udGVudChodG1sX2Y5NmMxZjI0NmVjMTRhZjY5YjI2MTgwMzM2OWE1NjUxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzUwODA2MWU5MzFiZTRjMDA5ODE1YmZlNDA3Mjk5MzBiLmJpbmRQb3B1cChwb3B1cF9jMWZhY2I4ZjJmMzM0YzQ2YWU3MWM5ZGJkNDI1MzViZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMjAzZGFiYmE1Y2I0NzIxODRiM2MxMWQ3YjUwMTM5MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQyLjI3NjAxNTgsLTcxLjQyODU1MDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGM0MDY1NjBjNGE3NDk3Mjk4YzBiNGRmNDYyYTNjYzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWE3YTRiN2JkOWQ4NDIwODk5MGE5ODMxYmFiYzkxOWQgPSAkKCc8ZGl2IGlkPSJodG1sXzVhN2E0YjdiZDlkODQyMDg5OTBhOTgzMWJhYmM5MTlkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EVURMRVkgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGM0MDY1NjBjNGE3NDk3Mjk4YzBiNGRmNDYyYTNjYzAuc2V0Q29udGVudChodG1sXzVhN2E0YjdiZDlkODQyMDg5OTBhOTgzMWJhYmM5MTlkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2YyMDNkYWJiYTVjYjQ3MjE4NGIzYzExZDdiNTAxMzkxLmJpbmRQb3B1cChwb3B1cF80YzQwNjU2MGM0YTc0OTcyOThjMGI0ZGY0NjJhM2NjMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZGZlYjBiMGIwZTA0MDQ5OWE0NWU4NGViODg4ZDVhOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzU0LjExNjQwMzksLTAuMzk1Nzg0Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zMmU2M2ZiMWMxZTY0MDU5OTAxMDRiNWE1ODVjYTA4NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNjE2M2JiNTYzMDg0MzViOGExZmYzZjVhZjRhNmRkMCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzYxNjNiYjU2MzA4NDM1YjhhMWZmM2Y1YWY0YTZkZDAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRVS0VTIExBTkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMyZTYzZmIxYzFlNjQwNTk5MDEwNGI1YTU4NWNhMDg0LnNldENvbnRlbnQoaHRtbF8zNjE2M2JiNTYzMDg0MzViOGExZmYzZjVhZjRhNmRkMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZGZlYjBiMGIwZTA0MDQ5OWE0NWU4NGViODg4ZDVhOC5iaW5kUG9wdXAocG9wdXBfMzJlNjNmYjFjMWU2NDA1OTkwMTA0YjVhNTg1Y2EwODQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODFiNjRmY2I0ZjA5NGYwZmI0NWRhMDllZDQxMTliMjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTQ0MDQxLC0wLjE1NTI5MTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTdhMGNkY2Y4ODJjNDhjMWI4YzZkZjNlNTFmNmNlNjQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzk1MWE4OWVmMjM3NDE4N2I5ZmI2YmJmNjQzYWI4MGEgPSAkKCc8ZGl2IGlkPSJodG1sX2M5NTFhODllZjIzNzQxODdiOWZiNmJiZjY0M2FiODBhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FQVRPTiBURVJSQUNFIE1FV1M8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE3YTBjZGNmODgyYzQ4YzFiOGM2ZGYzZTUxZjZjZTY0LnNldENvbnRlbnQoaHRtbF9jOTUxYTg5ZWYyMzc0MTg3YjlmYjZiYmY2NDNhYjgwYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84MWI2NGZjYjRmMDk0ZjBmYjQ1ZGEwOWVkNDExOWIyMi5iaW5kUG9wdXAocG9wdXBfMTdhMGNkY2Y4ODJjNDhjMWI4YzZkZjNlNTFmNmNlNjQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWYwOGU0MDhkNzg0NDkzNGFlMWQ5Y2I2YjllYTUxYjMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTc5MDA3LC0wLjE1MDQzM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MjA5ZmQwMTU3Njc0NWI0YjU2MGU1MzAzYTVjMzIyMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNWNmZTQ5ZWMyNDE0ZmIxOTk5YTIyNDI4NmI1NDAzOCA9ICQoJzxkaXYgaWQ9Imh0bWxfYTVjZmU0OWVjMjQxNGZiMTk5OWEyMjQyODZiNTQwMzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVDQ0xFU1RPTiBNRVdTPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MjA5ZmQwMTU3Njc0NWI0YjU2MGU1MzAzYTVjMzIyMy5zZXRDb250ZW50KGh0bWxfYTVjZmU0OWVjMjQxNGZiMTk5OWEyMjQyODZiNTQwMzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWYwOGU0MDhkNzg0NDkzNGFlMWQ5Y2I2YjllYTUxYjMuYmluZFBvcHVwKHBvcHVwXzkyMDlmZDAxNTc2NzQ1YjRiNTYwZTUzMDNhNWMzMjIzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE4N2EwNzNiMWJlZjQzNmZiNDVkYzZkNWYxZDU4ZGNkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDkxNjA1NywtMC4xNDUzNDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjMzNzkxMGJiZDJhNGQ4YmI2NGJhYzEyZTY3MDlhOGUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGQyNWUxNzhlNGE5NGVjN2EwOGJkNjk5ZWFlYzFhM2YgPSAkKCc8ZGl2IGlkPSJodG1sXzRkMjVlMTc4ZTRhOTRlYzdhMDhiZDY5OWVhZWMxYTNmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FQ0NMRVNUT04gU1FVQVJFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMzM3OTEwYmJkMmE0ZDhiYjY0YmFjMTJlNjcwOWE4ZS5zZXRDb250ZW50KGh0bWxfNGQyNWUxNzhlNGE5NGVjN2EwOGJkNjk5ZWFlYzFhM2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTg3YTA3M2IxYmVmNDM2ZmI0NWRjNmQ1ZjFkNThkY2QuYmluZFBvcHVwKHBvcHVwX2YzMzc5MTBiYmQyYTRkOGJiNjRiYWMxMmU2NzA5YThlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI1ZTI0ZjRlM2RlYTQ4NzA4Nzg2MjU5NDk3NDA1ZWYwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDc0OTgwMywtMC4yMTc2MDA1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJhNzkwMmNkZGQ4MDRlMTdiMjY5ZmY2MmM5MmEyYzUwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMwZGU1ZTQxNzUwOTQzZTc4NmQzZWQ5NThlNWRlYmY2ID0gJCgnPGRpdiBpZD0iaHRtbF8zMGRlNWU0MTc1MDk0M2U3ODZkM2VkOTU4ZTVkZWJmNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RUxMRVJCWSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJhNzkwMmNkZGQ4MDRlMTdiMjY5ZmY2MmM5MmEyYzUwLnNldENvbnRlbnQoaHRtbF8zMGRlNWU0MTc1MDk0M2U3ODZkM2VkOTU4ZTVkZWJmNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yNWUyNGY0ZTNkZWE0ODcwODc4NjI1OTQ5NzQwNWVmMC5iaW5kUG9wdXAocG9wdXBfMmE3OTAyY2RkZDgwNGUxN2IyNjlmZjYyYzkyYTJjNTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDU4MjVlYmIyZjc5NDc3NzgyNDJmZGViZGY2MWU3MWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTc3OTM4LC0wLjE4MTU0NTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTQ4MjY2ZGI1ZTljNGRmNjllYWVlZmZmYjQ5NmU0MjQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOGFjNmY1NjkzMzdjNGE3MmIyMDVhZDE0ZmQxODE0ZTggPSAkKCc8ZGl2IGlkPSJodG1sXzhhYzZmNTY5MzM3YzRhNzJiMjA1YWQxNGZkMTgxNGU4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FTFZBU1RPTiBQTEFDRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTQ4MjY2ZGI1ZTljNGRmNjllYWVlZmZmYjQ5NmU0MjQuc2V0Q29udGVudChodG1sXzhhYzZmNTY5MzM3YzRhNzJiMjA1YWQxNGZkMTgxNGU4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ1ODI1ZWJiMmY3OTQ3Nzc4MjQyZmRlYmRmNjFlNzFjLmJpbmRQb3B1cChwb3B1cF9hNDgyNjZkYjVlOWM0ZGY2OWVhZWVmZmZiNDk2ZTQyNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NzBlMThlYjE4MjM0YTdmOTM0MjMyMmJkNjk5MzU0ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5ODc3MTksLTAuMTcwMzk2Nl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iYTgxYjNmMDllMjA0MTUxODI1MmEyNDgwMTdlZDcyYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZTIwOTA0MzZjOGE0NGNiOTBmY2VmYTBmYzEwNDEwYyA9ICQoJzxkaXYgaWQ9Imh0bWxfY2UyMDkwNDM2YzhhNDRjYjkwZmNlZmEwZmMxMDQxMGMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVOTklTTU9SRSBHQVJERU5TIE1FV1M8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2JhODFiM2YwOWUyMDQxNTE4MjUyYTI0ODAxN2VkNzJhLnNldENvbnRlbnQoaHRtbF9jZTIwOTA0MzZjOGE0NGNiOTBmY2VmYTBmYzEwNDEwYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82NzBlMThlYjE4MjM0YTdmOTM0MjMyMmJkNjk5MzU0Zi5iaW5kUG9wdXAocG9wdXBfYmE4MWIzZjA5ZTIwNDE1MTgyNTJhMjQ4MDE3ZWQ3MmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODcxYzQxN2EwNGRkNDZkMGJhYmY2OGVjZmJkNDM1MTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43MTgzOTgyLC03My45ODc0MjM2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NiNjA1OGU4YWRlNTQxMGRiMDRlMWM3OTk4NTMwYzczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y0ZGRlNDZmMTgwMzRjNThiODdkMjlmMDcwZDAxZDljID0gJCgnPGRpdiBpZD0iaHRtbF9mNGRkZTQ2ZjE4MDM0YzU4Yjg3ZDI5ZjA3MGQwMWQ5YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RVNTRVggU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jYjYwNThlOGFkZTU0MTBkYjA0ZTFjNzk5ODUzMGM3My5zZXRDb250ZW50KGh0bWxfZjRkZGU0NmYxODAzNGM1OGI4N2QyOWYwNzBkMDFkOWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODcxYzQxN2EwNGRkNDZkMGJhYmY2OGVjZmJkNDM1MTQuYmluZFBvcHVwKHBvcHVwX2NiNjA1OGU4YWRlNTQxMGRiMDRlMWM3OTk4NTMwYzczKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU5MGNlZmY2NTY5YjQxYTRiM2RkY2UwN2Q0ZDJmMTI5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTY5MTg5MywtMC40MjEyMDU3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2QxNDczOGM1YTExNjRhMzJhNDFkMTM1YThmNTYyYzZhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg4ZmI0OGMzM2M3YzRmNzM4YmRjZTUwZmI2NGY4MjM3ID0gJCgnPGRpdiBpZD0iaHRtbF84OGZiNDhjMzNjN2M0ZjczOGJkY2U1MGZiNjRmODIzNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RVZFUlNMRVkgQ1JFU0NFTlQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QxNDczOGM1YTExNjRhMzJhNDFkMTM1YThmNTYyYzZhLnNldENvbnRlbnQoaHRtbF84OGZiNDhjMzNjN2M0ZjczOGJkY2U1MGZiNjRmODIzNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81OTBjZWZmNjU2OWI0MWE0YjNkZGNlMDdkNGQyZjEyOS5iaW5kUG9wdXAocG9wdXBfZDE0NzM4YzVhMTE2NGEzMmE0MWQxMzVhOGY1NjJjNmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjZkNzgxOGZhZDk5NGU1OGI1ZTUwY2Y1NzE4ODQyNDYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NDg3MjQ3LC0wLjA3NjY0MDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTMyNzcwZDE1YTkwNGRhZDliYTExODY1ZTk0YWZlYWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOGJlNTg5YWY3YzI2NDRiOTkwMTQ0ZDFhMjUwZmY2OTYgPSAkKCc8ZGl2IGlkPSJodG1sXzhiZTU4OWFmN2MyNjQ0Yjk5MDE0NGQxYTI1MGZmNjk2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FWU5FTExBIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkzMjc3MGQxNWE5MDRkYWQ5YmExMTg2NWU5NGFmZWFhLnNldENvbnRlbnQoaHRtbF84YmU1ODlhZjdjMjY0NGI5OTAxNDRkMWEyNTBmZjY5Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yNmQ3ODE4ZmFkOTk0ZTU4YjVlNTBjZjU3MTg4NDI0Ni5iaW5kUG9wdXAocG9wdXBfOTMyNzcwZDE1YTkwNGRhZDliYTExODY1ZTk0YWZlYWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmNhNmIzMDU5NTBiNGIyYWJiYzg0MTc3NTRkOWU0MjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MTIwNTM3LC0wLjA4MDE4NDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2JkOWQ5NzEwNDI4NGVlZTk5ZTQwODEwMTkzODI1MTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTJlYjc2NTJkM2QxNDY1ZjlhZDI0OWI3YmFkZTBmNzggPSAkKCc8ZGl2IGlkPSJodG1sX2UyZWI3NjUyZDNkMTQ2NWY5YWQyNDliN2JhZGUwZjc4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GRU5DSFVSQ0ggU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YmQ5ZDk3MTA0Mjg0ZWVlOTllNDA4MTAxOTM4MjUxMi5zZXRDb250ZW50KGh0bWxfZTJlYjc2NTJkM2QxNDY1ZjlhZDI0OWI3YmFkZTBmNzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmNhNmIzMDU5NTBiNGIyYWJiYzg0MTc3NTRkOWU0MjkuYmluZFBvcHVwKHBvcHVwXzdiZDlkOTcxMDQyODRlZWU5OWU0MDgxMDE5MzgyNTEyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU0Y2ZmODBkYzU2MDRhOGVhYTBjMTBlZDQxOWM2NDE1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMzIxMTMwNCwtNzEuMjA2MDc4OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ZTNhZDZiNmVkZWI0N2I3YTg5NmFkMWQ1Y2Y1OWFlMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNDVlYTJiNTliZDA0NTM4YTkxOWJkMmYyMGM5NDBiMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMTQ1ZWEyYjU5YmQwNDUzOGE5MTliZDJmMjBjOTQwYjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZMT1JBTCBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRlM2FkNmI2ZWRlYjQ3YjdhODk2YWQxZDVjZjU5YWUyLnNldENvbnRlbnQoaHRtbF8xNDVlYTJiNTliZDA0NTM4YTkxOWJkMmYyMGM5NDBiMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NGNmZjgwZGM1NjA0YThlYWEwYzEwZWQ0MTljNjQxNS5iaW5kUG9wdXAocG9wdXBfNGUzYWQ2YjZlZGViNDdiN2E4OTZhZDFkNWNmNTlhZTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDMyODhmY2E3ODJmNGQ2MGI0NDJmOGU1OTQwZTFlZTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NjI0MTIzLC0wLjA2NzEyMDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjMwYTFlNGIzODc5NGE3NWI0ZjgyMTk0OGE0NTk5OWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTllNzdiNjM4NGUzNDE5MzhhYzY4Mjk3M2M2YzI3YTcgPSAkKCc8ZGl2IGlkPSJodG1sX2U5ZTc3YjYzODRlMzQxOTM4YWM2ODI5NzNjNmMyN2E3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GT1VOVEFZTkUgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjMwYTFlNGIzODc5NGE3NWI0ZjgyMTk0OGE0NTk5OWUuc2V0Q29udGVudChodG1sX2U5ZTc3YjYzODRlMzQxOTM4YWM2ODI5NzNjNmMyN2E3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQzMjg4ZmNhNzgyZjRkNjBiNDQyZjhlNTk0MGUxZWUwLmJpbmRQb3B1cChwb3B1cF9iMzBhMWU0YjM4Nzk0YTc1YjRmODIxOTQ4YTQ1OTk5ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MjYxNzM2YmNlZmM0ZjJkYmMzZTY2M2UxNDkzM2ZkMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUxOTI0MzMsLTAuMDcyNTIwM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZmU0OWU3OWY3ZjM0MWEwYjlhZTRkNmI3YTgyMTYzZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81Nzg2YWU0NmY3NDI0MzkwOGIxZjZmMDc0MjQxNDlmZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNTc4NmFlNDZmNzQyNDM5MDhiMWY2ZjA3NDI0MTQ5ZmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZPVVJOSUVSIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGZlNDllNzlmN2YzNDFhMGI5YWU0ZDZiN2E4MjE2M2Yuc2V0Q29udGVudChodG1sXzU3ODZhZTQ2Zjc0MjQzOTA4YjFmNmYwNzQyNDE0OWZmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzcyNjE3MzZiY2VmYzRmMmRiYzNlNjYzZTE0OTMzZmQyLmJpbmRQb3B1cChwb3B1cF9kZmU0OWU3OWY3ZjM0MWEwYjlhZTRkNmI3YTgyMTYzZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNDI4MzJkMWM0YmI0MWUzYjliNThlYTA2OTgxNDQ0NiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ2OTY1MzksLTAuMTkxNDQzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ODk2OWRhZGYxYmY0NDMzOGE3MTI2MzcwNTg0NGRmYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZWFiN2IyNGNjMWE0NGNkYjJmZGM4NTViMDcxOTNmMyA9ICQoJzxkaXYgaWQ9Imh0bWxfNGVhYjdiMjRjYzFhNDRjZGIyZmRjODU1YjA3MTkzZjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZSSVNUT04gU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ODk2OWRhZGYxYmY0NDMzOGE3MTI2MzcwNTg0NGRmYS5zZXRDb250ZW50KGh0bWxfNGVhYjdiMjRjYzFhNDRjZGIyZmRjODU1YjA3MTkzZjMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDQyODMyZDFjNGJiNDFlM2I5YjU4ZWEwNjk4MTQ0NDYuYmluZFBvcHVwKHBvcHVwXzc4OTY5ZGFkZjFiZjQ0MzM4YTcxMjYzNzA1ODQ0ZGZhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y2ZWI2OWUwZjY3YTRmZWU4YTU4MmU3NjA4MTk4MTZiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuMTg4NTI1LC05Ni4xNzUwMDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2RkN2I0Y2QzZGE3NGUxZmFiZDVjYTllMDUxOWJmZWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDA4ODQ3ODMyYjZhNDQwOGIxY2VhYTYwZWQxNjA1MmEgPSAkKCc8ZGl2IGlkPSJodG1sXzAwODg0NzgzMmI2YTQ0MDhiMWNlYWE2MGVkMTYwNTJhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HRVJUUlVERSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNkZDdiNGNkM2RhNzRlMWZhYmQ1Y2E5ZTA1MTliZmVmLnNldENvbnRlbnQoaHRtbF8wMDg4NDc4MzJiNmE0NDA4YjFjZWFhNjBlZDE2MDUyYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNmViNjllMGY2N2E0ZmVlOGE1ODJlNzYwODE5ODE2Yi5iaW5kUG9wdXAocG9wdXBfM2RkN2I0Y2QzZGE3NGUxZmFiZDVjYTllMDUxOWJmZWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjNmYmMxNTY5MzliNGY2NDk1NmQxZjIxYTZiZDMxZGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MTczOTAzLC0wLjE1ODI3MTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWUwYThlNWVmN2FiNDNhOTlkNmUxNDFiYzNmNTg1NmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWMyMTNmY2QzYTRhNGQzMWE5NDc5OGE4Zjg3OGU1NDIgPSAkKCc8ZGl2IGlkPSJodG1sXzFjMjEzZmNkM2E0YTRkMzFhOTQ3OThhOGY4NzhlNTQyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HTE9VQ0VTVEVSIFBMQUNFIE1FV1M8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzllMGE4ZTVlZjdhYjQzYTk5ZDZlMTQxYmMzZjU4NTZjLnNldENvbnRlbnQoaHRtbF8xYzIxM2ZjZDNhNGE0ZDMxYTk0Nzk4YThmODc4ZTU0Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yM2ZiYzE1NjkzOWI0ZjY0OTU2ZDFmMjFhNmJkMzFkZS5iaW5kUG9wdXAocG9wdXBfOWUwYThlNWVmN2FiNDNhOTlkNmUxNDFiYzNmNTg1NmMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODUzMjVmZTQ3MTg1NGMxZDk2N2E0N2RjYzcyM2FjMGQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zMTY1MjI4LDAuNzA0NjM3N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMzM5YWJjYTM4N2U0YWRmYTUxYzQyYjAwNTI3YWRlMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMGNjY2ZhZjk2NTQ0ZTQ4OWQyMmMyNWFiZmM2NTM1ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMDBjY2NmYWY5NjU0NGU0ODlkMjJjMjVhYmZjNjUzNWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdPUkRPTiBDT1RUQUdFUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjMzOWFiY2EzODdlNGFkZmE1MWM0MmIwMDUyN2FkZTAuc2V0Q29udGVudChodG1sXzAwY2NjZmFmOTY1NDRlNDg5ZDIyYzI1YWJmYzY1MzVmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzg1MzI1ZmU0NzE4NTRjMWQ5NjdhNDdkY2M3MjNhYzBkLmJpbmRQb3B1cChwb3B1cF8yMzM5YWJjYTM4N2U0YWRmYTUxYzQyYjAwNTI3YWRlMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kZTYzOTViNzUyNjg0NGMzYTY5ZDRkM2FhYjI0NTNmNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ1Mjg5NTUsLTAuMTYzODE5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2QwMzMxMTdmOWI2ZDRkNTc4OTBlMTJkODZkMTM0MmRiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2EyM2ZiOTc2NjA5OTQzODlhYmUxYTZiMjg2Y2UzMDRiID0gJCgnPGRpdiBpZD0iaHRtbF9hMjNmYjk3NjYwOTk0Mzg5YWJlMWE2YjI4NmNlMzA0YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R09SU1QgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDAzMzExN2Y5YjZkNGQ1Nzg5MGUxMmQ4NmQxMzQyZGIuc2V0Q29udGVudChodG1sX2EyM2ZiOTc2NjA5OTQzODlhYmUxYTZiMjg2Y2UzMDRiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RlNjM5NWI3NTI2ODQ0YzNhNjlkNGQzYWFiMjQ1M2Y1LmJpbmRQb3B1cChwb3B1cF9kMDMzMTE3ZjliNmQ0ZDU3ODkwZTEyZDg2ZDEzNDJkYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNjk2NTdmOGRjNzY0NzQ1YWU0NGQwYzcyNjdmNjgwYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ0OTU3NDQsMC4xMTQxMjIzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RiOWZiYTA2YjlkZTQ1MWJiMzNhOTMyYTRiNzY0NWRhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E5NGQ5MGU4MGQ5MjQwNWNiZTEyZTU1ZTJjNTIxYzhlID0gJCgnPGRpdiBpZD0iaHRtbF9hOTRkOTBlODBkOTI0MDVjYmUxMmU1NWUyYzUyMWM4ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R1JBSEFNIFRFUlJBQ0U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RiOWZiYTA2YjlkZTQ1MWJiMzNhOTMyYTRiNzY0NWRhLnNldENvbnRlbnQoaHRtbF9hOTRkOTBlODBkOTI0MDVjYmUxMmU1NWUyYzUyMWM4ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNjk2NTdmOGRjNzY0NzQ1YWU0NGQwYzcyNjdmNjgwYy5iaW5kUG9wdXAocG9wdXBfZGI5ZmJhMDZiOWRlNDUxYmIzM2E5MzJhNGI3NjQ1ZGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmEzMmU4YTc1YTdhNDQwYTk1MzA0OTY1ZDVhNjc0OWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MjA0MTI4LC0wLjE0MTQwMjJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWFiOTYyMzRmODA5NDZjMzk3NDE3ZDIxZTdkZTViZDkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWIyM2M2MzQ2MjMwNDlkNjljMTJiOGM1YTc5YzNjNDEgPSAkKCc8ZGl2IGlkPSJodG1sXzViMjNjNjM0NjIzMDQ5ZDY5YzEyYjhjNWE3OWMzYzQxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HUkVBVCBUSVRDSEZJRUxEIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWFiOTYyMzRmODA5NDZjMzk3NDE3ZDIxZTdkZTViZDkuc2V0Q29udGVudChodG1sXzViMjNjNjM0NjIzMDQ5ZDY5YzEyYjhjNWE3OWMzYzQxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JhMzJlOGE3NWE3YTQ0MGE5NTMwNDk2NWQ1YTY3NDllLmJpbmRQb3B1cChwb3B1cF81YWI5NjIzNGY4MDk0NmMzOTc0MTdkMjFlN2RlNWJkOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zMmE0OWZhNDczN2Y0YzBkYWRkNDFhN2UxMTJkZDY2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjU2NzcyMjQsLTIuMzA3Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzAzN2EwNzRmNDM3ZTQ4NmQ4NTk4ZjdlZTE3MDliYzYyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZkOTMzNmUxNzQzODQ4MGRiYTFjMmZkZjM2ZTc0ZjFiID0gJCgnPGRpdiBpZD0iaHRtbF9mZDkzMzZlMTc0Mzg0ODBkYmExYzJmZGYzNmU3NGYxYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFMTEFNIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDM3YTA3NGY0MzdlNDg2ZDg1OThmN2VlMTcwOWJjNjIuc2V0Q29udGVudChodG1sX2ZkOTMzNmUxNzQzODQ4MGRiYTFjMmZkZjM2ZTc0ZjFiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMyYTQ5ZmE0NzM3ZjRjMGRhZGQ0MWE3ZTExMmRkNjZjLmJpbmRQb3B1cChwb3B1cF8wMzdhMDc0ZjQzN2U0ODZkODU5OGY3ZWUxNzA5YmM2Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mYTcyNDAxMTc0ODg0OTExYTBjYjAyNWUxOGZmMThjMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQwLjY4NTkxOTMsLTczLjkxNTg1Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hZDVhMjU5OTc1Y2U0MTFjYWY4NjBjYzM1YzVjMGM3OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kYjU0NzMyZTk5NWU0ZWY4YjM3Y2U1OWIyZWRmNjhlYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZGI1NDczMmU5OTVlNGVmOGIzN2NlNTliMmVkZjY4ZWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhBTFNFWSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FkNWEyNTk5NzVjZTQxMWNhZjg2MGNjMzVjNWMwYzc4LnNldENvbnRlbnQoaHRtbF9kYjU0NzMyZTk5NWU0ZWY4YjM3Y2U1OWIyZWRmNjhlYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mYTcyNDAxMTc0ODg0OTExYTBjYjAyNWUxOGZmMThjMi5iaW5kUG9wdXAocG9wdXBfYWQ1YTI1OTk3NWNlNDExY2FmODYwY2MzNWM1YzBjNzgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzdlNGRkY2ZkMTUzNDEzNWFlZmI1YTJlYjI5Zjk0YzkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zNjI2OTc1LDAuMDg0MDczN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84OTkwOTJmOGY4OTA0NTJjYWU0MDRhN2I3ODQwOTc1NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xYmY4Njk2NDMwZDY0YjBiYjY1ZWJkNmU2ZjgzYjU5ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMWJmODY5NjQzMGQ2NGIwYmI2NWViZDZlNmY4M2I1OWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhBUkxFWSBHQVJERU5TPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84OTkwOTJmOGY4OTA0NTJjYWU0MDRhN2I3ODQwOTc1NS5zZXRDb250ZW50KGh0bWxfMWJmODY5NjQzMGQ2NGIwYmI2NWViZDZlNmY4M2I1OWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzdlNGRkY2ZkMTUzNDEzNWFlZmI1YTJlYjI5Zjk0YzkuYmluZFBvcHVwKHBvcHVwXzg5OTA5MmY4Zjg5MDQ1MmNhZTQwNGE3Yjc4NDA5NzU1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzAyM2U4NmU1YTNkMDQxNzFiMzJmYzFlNWI5MzljMDJiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNjMxNTYyMiwtMC4xOTUwMjU4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2EwNGU2NGQ4ZmVlYTQxYmFhN2JiMjhiYjU4N2NmYjM5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUzZjQ1OWMxNmViZTQyMGE4NWIxYzAxYzZkZmMzOGE3ID0gJCgnPGRpdiBpZD0iaHRtbF81M2Y0NTljMTZlYmU0MjBhODViMWMwMWM2ZGZjMzhhNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFSTVNXT1JUSCBXQVk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2EwNGU2NGQ4ZmVlYTQxYmFhN2JiMjhiYjU4N2NmYjM5LnNldENvbnRlbnQoaHRtbF81M2Y0NTljMTZlYmU0MjBhODViMWMwMWM2ZGZjMzhhNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMjNlODZlNWEzZDA0MTcxYjMyZmMxZTViOTM5YzAyYi5iaW5kUG9wdXAocG9wdXBfYTA0ZTY0ZDhmZWVhNDFiYWE3YmIyOGJiNTg3Y2ZiMzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTMxMWYxOWIxNWRjNDlkNzllZGI2OGQ0NDE5YThkNDAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0Mi4zMDUzOTUsLTcxLjMzODA1NzNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmY3NWNjNDYwYjI4NDE0ODg2NDQxOWJkNjQyM2E2MTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDUyNTg2NjdkMjdjNGZiM2JkYTcwOGE0ZTA4OTFjNTQgPSAkKCc8ZGl2IGlkPSJodG1sXzQ1MjU4NjY3ZDI3YzRmYjNiZGE3MDhhNGUwODkxYzU0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IQVJXT09EIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJmNzVjYzQ2MGIyODQxNDg4NjQ0MTliZDY0MjNhNjE5LnNldENvbnRlbnQoaHRtbF80NTI1ODY2N2QyN2M0ZmIzYmRhNzA4YTRlMDg5MWM1NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMzExZjE5YjE1ZGM0OWQ3OWVkYjY4ZDQ0MTlhOGQ0MC5iaW5kUG9wdXAocG9wdXBfMmY3NWNjNDYwYjI4NDE0ODg2NDQxOWJkNjQyM2E2MTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTFiN2RkNzk1NWJlNGVhZjk0ZWFjOTE5MTFjZWY1NWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODM0ODQ4LC0wLjA1NDcwNzNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmEzNDRjYTViYmNkNGVhY2FjYjUzYmYxZmQzYmQ4MGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzU1M2ViZDczYjcxNDJmYmI2MjVjNTBiNTFjZGYyMmIgPSAkKCc8ZGl2IGlkPSJodG1sXzc1NTNlYmQ3M2I3MTQyZmJiNjI1YzUwYjUxY2RmMjJiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IQVRDSEFNIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJhMzQ0Y2E1YmJjZDRlYWNhY2I1M2JmMWZkM2JkODBjLnNldENvbnRlbnQoaHRtbF83NTUzZWJkNzNiNzE0MmZiYjYyNWM1MGI1MWNkZjIyYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81MWI3ZGQ3OTU1YmU0ZWFmOTRlYWM5MTkxMWNlZjU1Zi5iaW5kUG9wdXAocG9wdXBfMmEzNDRjYTViYmNkNGVhY2FjYjUzYmYxZmQzYmQ4MGMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTNhMGFjMDU0MjA5NGU1OWJkMTA1Mzk5ZTg3OTA4MGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi43MDAzMDcxLC0yLjk2NDkxODJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjQ2OGViNDY3OGI4NGVjNThkNTk0YjI1MWVhNzQ3MjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjdmNTMxZjVkNGQ2NDY1NWFjNDk0OTg2ODA2N2RhZGQgPSAkKCc8ZGl2IGlkPSJodG1sXzI3ZjUzMWY1ZDRkNjQ2NTVhYzQ5NDk4NjgwNjdkYWRkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IRUFUSCBEUklWRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjQ2OGViNDY3OGI4NGVjNThkNTk0YjI1MWVhNzQ3Mjkuc2V0Q29udGVudChodG1sXzI3ZjUzMWY1ZDRkNjQ2NTVhYzQ5NDk4NjgwNjdkYWRkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzkzYTBhYzA1NDIwOTRlNTliZDEwNTM5OWU4NzkwODBjLmJpbmRQb3B1cChwb3B1cF9mNDY4ZWI0Njc4Yjg0ZWM1OGQ1OTRiMjUxZWE3NDcyOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YTljOGE0ODQwNTY0MjJkYjk1YjE0NTY1NmFkNmQ1OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjU1NDk4ODgsLTAuMTY3NTExMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hN2ViNTJjNGM1MmU0Y2M4ODM2ODBmNTkzMTkxZWZlMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MTE0OGM2MTY5MDQ0ZmRlYjgxNmQ5ZDViYTczYTg2NSA9ICQoJzxkaXYgaWQ9Imh0bWxfOTExNDhjNjE2OTA0NGZkZWI4MTZkOWQ1YmE3M2E4NjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhFQVRIIEhVUlNUIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E3ZWI1MmM0YzUyZTRjYzg4MzY4MGY1OTMxOTFlZmUzLnNldENvbnRlbnQoaHRtbF85MTE0OGM2MTY5MDQ0ZmRlYjgxNmQ5ZDViYTczYTg2NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81YTljOGE0ODQwNTY0MjJkYjk1YjE0NTY1NmFkNmQ1OS5iaW5kUG9wdXAocG9wdXBfYTdlYjUyYzRjNTJlNGNjODgzNjgwZjU5MzE5MWVmZTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDJmNDg4Mzg4MmVkNGQ2NGIzN2Y4ODMyZWI1ZTQ4MTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4yODI3NDMzLC0xMTkuMTY3MzAyMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81YzFjNDY0NTJkYTM0YjBiYTgwMTc0ZjM5NDM3ZTc1ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYTM3YzYyZjY1OWM0Zjk4YTVhNzBlMDY2YTc1NjQ2ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfM2EzN2M2MmY2NTljNGY5OGE1YTcwZTA2NmE3NTY0NmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhFTkRFUlNPTiBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81YzFjNDY0NTJkYTM0YjBiYTgwMTc0ZjM5NDM3ZTc1Zi5zZXRDb250ZW50KGh0bWxfM2EzN2M2MmY2NTljNGY5OGE1YTcwZTA2NmE3NTY0NmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDJmNDg4Mzg4MmVkNGQ2NGIzN2Y4ODMyZWI1ZTQ4MTMuYmluZFBvcHVwKHBvcHVwXzVjMWM0NjQ1MmRhMzRiMGJhODAxNzRmMzk0MzdlNzVmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E0MTcyN2U2NDEwMzQzZTc5ODdkNGQ3NzAzNDU4ODRkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTQ4MTA2OSwtMC4wOTgwMDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDIyOGQ4NjQwNDIxNDBlYWEyNTQ4ZDk1MTMzNGE2OTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzllMDU5YmM3MmU5NDQ4NmFjZTY4Njc3NGQ0Njg5NTggPSAkKCc8ZGl2IGlkPSJodG1sXzc5ZTA1OWJjNzJlOTQ0ODZhY2U2ODY3NzRkNDY4OTU4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ISUdIQlVSWSBORVcgUEFSSzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDIyOGQ4NjQwNDIxNDBlYWEyNTQ4ZDk1MTMzNGE2OTMuc2V0Q29udGVudChodG1sXzc5ZTA1OWJjNzJlOTQ0ODZhY2U2ODY3NzRkNDY4OTU4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E0MTcyN2U2NDEwMzQzZTc5ODdkNGQ3NzAzNDU4ODRkLmJpbmRQb3B1cChwb3B1cF9kMjI4ZDg2NDA0MjE0MGVhYTI1NDhkOTUxMzM0YTY5Myk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80NTA1NWU5ZDU0NzQ0ZDBjYTJlYmUyOWI4OTQ4N2NkZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjg4MjY3NDgsLTguMDgyNzcwNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZWM0Y2JhYjA5ZmI0NTM0YTRmZWM3NzNjZjU5YTFmYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NTUzODFhNDAxYjk0NDQwOGE0YjcwYjI0ODhmOTA1NyA9ICQoJzxkaXYgaWQ9Imh0bWxfOTU1MzgxYTQwMWI5NDQ0MDhhNGI3MGIyNDg4ZjkwNTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhJTEwgU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZWM0Y2JhYjA5ZmI0NTM0YTRmZWM3NzNjZjU5YTFmYy5zZXRDb250ZW50KGh0bWxfOTU1MzgxYTQwMWI5NDQ0MDhhNGI3MGIyNDg4ZjkwNTcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDUwNTVlOWQ1NDc0NGQwY2EyZWJlMjliODk0ODdjZGUuYmluZFBvcHVwKHBvcHVwXzdlYzRjYmFiMDlmYjQ1MzRhNGZlYzc3M2NmNTlhMWZjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FjNjhkY2E0MWU4ODQ1YTBiZWY3MThhYzZlMmFkYzhiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTAuNjc1NzA1LC0xLjA5OTU4NzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNThhOWJmMTFmNzUyNDBkMjlmZTRjMGQxODFmMGVlMTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDVlZjJhMWY3NzZhNDY0NmJkZWQxM2ZiZTFiNDQyZWQgPSAkKCc8ZGl2IGlkPSJodG1sXzQ1ZWYyYTFmNzc2YTQ2NDZiZGVkMTNmYmUxYjQ0MmVkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ISUxMV0FZPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81OGE5YmYxMWY3NTI0MGQyOWZlNGMwZDE4MWYwZWUxOC5zZXRDb250ZW50KGh0bWxfNDVlZjJhMWY3NzZhNDY0NmJkZWQxM2ZiZTFiNDQyZWQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWM2OGRjYTQxZTg4NDVhMGJlZjcxOGFjNmUyYWRjOGIuYmluZFBvcHVwKHBvcHVwXzU4YTliZjExZjc1MjQwZDI5ZmU0YzBkMTgxZjBlZTE4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY0ZmI4OWNiZmUyMTQyMWRhZDE0MGJmM2E4YTA1MWQyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTIuOTg1NjEzNywtMS4yMzA4MjM5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzIzZDM5NmRhNmU0MjQ1MmM4YmMyZTU1NDM4ZDdiYTdkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RmNzIzYjQ4YjEyNDQ0OTk4MjhlOWEwNDhkNjA3MjEzID0gJCgnPGRpdiBpZD0iaHRtbF9kZjcyM2I0OGIxMjQ0NDk5ODI4ZTlhMDQ4ZDYwNzIxMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SE9MQk9STiBDTE9TRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjNkMzk2ZGE2ZTQyNDUyYzhiYzJlNTU0MzhkN2JhN2Quc2V0Q29udGVudChodG1sX2RmNzIzYjQ4YjEyNDQ0OTk4MjhlOWEwNDhkNjA3MjEzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY0ZmI4OWNiZmUyMTQyMWRhZDE0MGJmM2E4YTA1MWQyLmJpbmRQb3B1cChwb3B1cF8yM2QzOTZkYTZlNDI0NTJjOGJjMmU1NTQzOGQ3YmE3ZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYTcwZGVhNTgyNzQ0NjdmYmUxYmM3NDc3MzE1Nzc4NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5ODc1MjUsLTAuMjAyMTI5NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ZjgzNWE0NjkwYTU0Y2JlYjUwOTZkNDkwMmI4YmVlNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wZGE1ZTI3NDA4NTA0NzEwODliN2U2MWNmZmY4MmUxNiA9ICQoJzxkaXYgaWQ9Imh0bWxfMGRhNWUyNzQwODUwNDcxMDg5YjdlNjFjZmZmODJlMTYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhPTExBTkQgUEFSSyBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80ZjgzNWE0NjkwYTU0Y2JlYjUwOTZkNDkwMmI4YmVlNy5zZXRDb250ZW50KGh0bWxfMGRhNWUyNzQwODUwNDcxMDg5YjdlNjFjZmZmODJlMTYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmE3MGRlYTU4Mjc0NDY3ZmJlMWJjNzQ3NzMxNTc3ODQuYmluZFBvcHVwKHBvcHVwXzRmODM1YTQ2OTBhNTRjYmViNTA5NmQ0OTAyYjhiZWU3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM1NTkyZGNkNWY3ODQ3YWNhNmMyMWY2OTU3M2Y5MjZlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDc1MDA1MSwtMC4wMDY2MDQ3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEzYjE3NjRjYzk5YjQzZTM5ZmQ5NWY1ZjU1MjUwMzBhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ4ZjVjNTVkNmZkMjQwYzBiMTY1OGYxOTYwNDU4OTczID0gJCgnPGRpdiBpZD0iaHRtbF80OGY1YzU1ZDZmZDI0MGMwYjE2NThmMTk2MDQ1ODk3MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SFlERSBWQUxFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xM2IxNzY0Y2M5OWI0M2UzOWZkOTVmNWY1NTI1MDMwYS5zZXRDb250ZW50KGh0bWxfNDhmNWM1NWQ2ZmQyNDBjMGIxNjU4ZjE5NjA0NTg5NzMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzU1OTJkY2Q1Zjc4NDdhY2E2YzIxZjY5NTczZjkyNmUuYmluZFBvcHVwKHBvcHVwXzEzYjE3NjRjYzk5YjQzZTM5ZmQ5NWY1ZjU1MjUwMzBhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Q2ODAwYzg3MGVhYTQ0NDY4YWFjZDA3YmU3ZWFhMzcyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMzA5MzY5OCwtNzEuMzQ5MDIwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kM2ZhZGRmYjFiNWU0MmY3OTNjYWVjNGY1YTcwNTYyOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lZjVlMmM3NDYxYzA0NTAzYjRmZTY2NGRjZWY0ZmUzNCA9ICQoJzxkaXYgaWQ9Imh0bWxfZWY1ZTJjNzQ2MWMwNDUwM2I0ZmU2NjRkY2VmNGZlMzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPklSVklORyBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kM2ZhZGRmYjFiNWU0MmY3OTNjYWVjNGY1YTcwNTYyOS5zZXRDb250ZW50KGh0bWxfZWY1ZTJjNzQ2MWMwNDUwM2I0ZmU2NjRkY2VmNGZlMzQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDY4MDBjODcwZWFhNDQ0NjhhYWNkMDdiZTdlYWEzNzIuYmluZFBvcHVwKHBvcHVwX2QzZmFkZGZiMWI1ZTQyZjc5M2NhZWM0ZjVhNzA1NjI5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVhYmMxOTUyMjEwYzRkYjE5NTgzZDQwOWQwZGZlMWFiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMTQuNjgyMTA5NTUsMTIxLjAyNTc4NzU0MTg1ODY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzllMGJiNGQyY2IwNDRmNDRhMTNmY2IyYWI0OTA1ZTFiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZlNGZhMmYzYTEzYzQzZWJiOTBmNmEwYWNkNDBkZjAzID0gJCgnPGRpdiBpZD0iaHRtbF82ZTRmYTJmM2ExM2M0M2ViYjkwZjZhMGFjZDQwZGYwMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SlVOQ1RJT04gUExBQ0U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzllMGJiNGQyY2IwNDRmNDRhMTNmY2IyYWI0OTA1ZTFiLnNldENvbnRlbnQoaHRtbF82ZTRmYTJmM2ExM2M0M2ViYjkwZjZhMGFjZDQwZGYwMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81YWJjMTk1MjIxMGM0ZGIxOTU4M2Q0MDlkMGRmZTFhYi5iaW5kUG9wdXAocG9wdXBfOWUwYmI0ZDJjYjA0NGY0NGExM2ZjYjJhYjQ5MDVlMWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTEzMzYwNGM4Zjk0NGIyY2I4ODZmYmY0MjViYzU0NGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MTYyOTA1LC0wLjE5MzIyNDQ4NDQ5NjIwNzU1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FkYjkyNDg3NzllMTQ5Y2FhM2ZkNzdkNjJlMDRhNWMzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U5NjcxYmFlNjMxODQ2OTU4Y2NlYzA2M2Y4MzZhMjhmID0gJCgnPGRpdiBpZD0iaHRtbF9lOTY3MWJhZTYzMTg0Njk1OGNjZWMwNjNmODM2YTI4ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S0lMREFSRSBHQVJERU5TPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hZGI5MjQ4Nzc5ZTE0OWNhYTNmZDc3ZDYyZTA0YTVjMy5zZXRDb250ZW50KGh0bWxfZTk2NzFiYWU2MzE4NDY5NThjY2VjMDYzZjgzNmEyOGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTEzMzYwNGM4Zjk0NGIyY2I4ODZmYmY0MjViYzU0NGUuYmluZFBvcHVwKHBvcHVwX2FkYjkyNDg3NzllMTQ5Y2FhM2ZkNzdkNjJlMDRhNWMzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IxMDkzYWI4MDJlZDRhMmViMTI2MDdiMDE3M2ZkNGZmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTEyNTcxNCwyLjk2ZS0wNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82YjY2MjcxMmE0NWI0NTMwYWI2YzI3NjE5NGFhZTAyYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iNjlkMmQ5MDg0MTc0ZDNmYTM0OGY0MzU1YTQzOTYzNiA9ICQoJzxkaXYgaWQ9Imh0bWxfYjY5ZDJkOTA4NDE3NGQzZmEzNDhmNDM1NWE0Mzk2MzYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxFQU1PVVRIIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZiNjYyNzEyYTQ1YjQ1MzBhYjZjMjc2MTk0YWFlMDJjLnNldENvbnRlbnQoaHRtbF9iNjlkMmQ5MDg0MTc0ZDNmYTM0OGY0MzU1YTQzOTYzNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iMTA5M2FiODAyZWQ0YTJlYjEyNjA3YjAxNzNmZDRmZi5iaW5kUG9wdXAocG9wdXBfNmI2NjI3MTJhNDViNDUzMGFiNmMyNzYxOTRhYWUwMmMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmM3MTAyZGYxMTYzNDk0MTlkNDMxYjBkODA4NGUxNmIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1My4zMjMwNjM4LC02LjI2NzM2NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWQzOTU5MGIxNmY4NGIzM2FiMmM5YmY4NzAxNmI0ZTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGE2MzBiNTcyYTY2NDI3ZDk2OTA5NzIxNzMzMjU4NWEgPSAkKCc8ZGl2IGlkPSJodG1sXzRhNjMwYjU3MmE2NjQyN2Q5NjkwOTcyMTczMzI1ODVhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MRUlOU1RFUiBTUVVBUkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFkMzk1OTBiMTZmODRiMzNhYjJjOWJmODcwMTZiNGU2LnNldENvbnRlbnQoaHRtbF80YTYzMGI1NzJhNjY0MjdkOTY5MDk3MjE3MzMyNTg1YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yYzcxMDJkZjExNjM0OTQxOWQ0MzFiMGQ4MDg0ZTE2Yi5iaW5kUG9wdXAocG9wdXBfMWQzOTU5MGIxNmY4NGIzM2FiMmM5YmY4NzAxNmI0ZTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTYyYmJhYWM3MDQ4NGUzZTg1YWRhZGNkZDA2ZDRjZjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NzY4NDA3LC0wLjIwNDY1NzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmRlNTMxMDhkMDUyNGJlOGI2MzVhNmU3ODIyNTM3ZjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDFmY2YyN2FlMzYwNDcxZWJiNWE3ZTA0YzMxMWFjMDQgPSAkKCc8ZGl2IGlkPSJodG1sX2QxZmNmMjdhZTM2MDQ3MWViYjVhN2UwNGMzMTFhYzA0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MSUxZVklMTEUgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmRlNTMxMDhkMDUyNGJlOGI2MzVhNmU3ODIyNTM3ZjIuc2V0Q29udGVudChodG1sX2QxZmNmMjdhZTM2MDQ3MWViYjVhN2UwNGMzMTFhYzA0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U2MmJiYWFjNzA0ODRlM2U4NWFkYWRjZGQwNmQ0Y2YxLmJpbmRQb3B1cChwb3B1cF9iZGU1MzEwOGQwNTI0YmU4YjYzNWE2ZTc4MjI1MzdmMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ZTAyOTM3MzM1NjQ0M2U4YjE1ZTU4NzVmMWI2MGI2ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5NjYxNjcsLTAuMTQ4MDI2Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jZjY3ODAxOWVjMTM0YTdmOGVjNjRhMWY1MDQwMGE1OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNTY0YWNlNGVhYWI0NWY2ODViZWViMTMzYWU1MjFjYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjU2NGFjZTRlYWFiNDVmNjg1YmVlYjEzM2FlNTIxY2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxPV0VSIEJFTEdSQVZFIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfY2Y2NzgwMTllYzEzNGE3ZjhlYzY0YTFmNTA0MDBhNTguc2V0Q29udGVudChodG1sX2Y1NjRhY2U0ZWFhYjQ1ZjY4NWJlZWIxMzNhZTUyMWNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVlMDI5MzczMzU2NDQzZThiMTVlNTg3NWYxYjYwYjZlLmJpbmRQb3B1cChwb3B1cF9jZjY3ODAxOWVjMTM0YTdmOGVjNjRhMWY1MDQwMGE1OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMjU4NzRmMzE2YmY0ODE3YmYxY2JkMzdhY2QwMjFjMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjU1MTM3NzEsLTEwMS42MDc5NTIxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2VlOTE2MGYwOTYxMjQ5YjI5MjMxNGI4ZmI5Yjc2N2M1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzlmNWMxNjM2M2UzZTQ5ODM5YTZlMzYyZTNmZjJjMDRiID0gJCgnPGRpdiBpZD0iaHRtbF85ZjVjMTYzNjNlM2U0OTgzOWE2ZTM2MmUzZmYyYzA0YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TFlPTlMgUExBQ0U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2VlOTE2MGYwOTYxMjQ5YjI5MjMxNGI4ZmI5Yjc2N2M1LnNldENvbnRlbnQoaHRtbF85ZjVjMTYzNjNlM2U0OTgzOWE2ZTM2MmUzZmYyYzA0Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMjU4NzRmMzE2YmY0ODE3YmYxY2JkMzdhY2QwMjFjMS5iaW5kUG9wdXAocG9wdXBfZWU5MTYwZjA5NjEyNDliMjkyMzE0YjhmYjliNzY3YzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjk4YzY1Njc4ZTk2NDFkYmI2OTJkYzA3ZTg5ZTViMTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NTA4MjIzLC0wLjE3NzczOTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjNhNmU5NTYxYWQ2NDUxYWI2Y2Y5NDczM2Y1MGM1YjMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTBiMmQ5ZDVlYjg0NDYxMzhhMTQyMjY0MTZhODE5ZmUgPSAkKCc8ZGl2IGlkPSJodG1sXzkwYjJkOWQ1ZWI4NDQ2MTM4YTE0MjI2NDE2YTgxOWZlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NQVJFU0ZJRUxEIEdBUkRFTlM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IzYTZlOTU2MWFkNjQ1MWFiNmNmOTQ3MzNmNTBjNWIzLnNldENvbnRlbnQoaHRtbF85MGIyZDlkNWViODQ0NjEzOGExNDIyNjQxNmE4MTlmZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iOThjNjU2NzhlOTY0MWRiYjY5MmRjMDdlODllNWIxNC5iaW5kUG9wdXAocG9wdXBfYjNhNmU5NTYxYWQ2NDUxYWI2Y2Y5NDczM2Y1MGM1YjMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTJhYjI2M2IyNmIyNGY4OThjNjMwZDA1NGRmYzljMDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszMy44NTE4MzE3LC0xMTcuMjMyMzMxOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jODRmZjA0MmVjYTA0ZjUyYjZjMjBiZGI0NGFkYzIzYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84ZGJiOTIwZGY2MDE0MTkzOGYwZjllYzk4ZWIyZDQwMCA9ICQoJzxkaXYgaWQ9Imh0bWxfOGRiYjkyMGRmNjAxNDE5MzhmMGY5ZWM5OGViMmQ0MDAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1BUktIQU0gU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jODRmZjA0MmVjYTA0ZjUyYjZjMjBiZGI0NGFkYzIzYi5zZXRDb250ZW50KGh0bWxfOGRiYjkyMGRmNjAxNDE5MzhmMGY5ZWM5OGViMmQ0MDApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTJhYjI2M2IyNmIyNGY4OThjNjMwZDA1NGRmYzljMDguYmluZFBvcHVwKHBvcHVwX2M4NGZmMDQyZWNhMDRmNTJiNmMyMGJkYjQ0YWRjMjNiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZhNzZlYmRhMGJjMDQyNjA4ZTBjOTA1MWIwYWQ4YTg1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTcuMTI4MDM4OCw1Mi43NDQ4MzkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I3YmQ5NTVlZjg2ZjRjMWE5OTJkYjIwNzQ3YTEwMjA0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzYwODRjMjc3YmVmMjQyNGRiNjc2ZDE4MGIyNmI5MDhmID0gJCgnPGRpdiBpZD0iaHRtbF82MDg0YzI3N2JlZjI0MjRkYjY3NmQxODBiMjZiOTA4ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TUlMS1kgV0FZPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iN2JkOTU1ZWY4NmY0YzFhOTkyZGIyMDc0N2ExMDIwNC5zZXRDb250ZW50KGh0bWxfNjA4NGMyNzdiZWYyNDI0ZGI2NzZkMTgwYjI2YjkwOGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmE3NmViZGEwYmMwNDI2MDhlMGM5MDUxYjBhZDhhODUuYmluZFBvcHVwKHBvcHVwX2I3YmQ5NTVlZjg2ZjRjMWE5OTJkYjIwNzQ3YTEwMjA0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I2MjQ5NGY5ZWFmYTRlMjZhMmM1ZDhhZTE4MjU1Y2FiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDUwMzM1NCwtMC4xNjE4NTE0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkZGQ4OGM1Y2M4YzRmNmViNGY2NWJhY2YzMGUxYjk3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2MwYTQ5MzhjNTY2NjQyYzg4YzdiOTEzZjBmOTZhM2Y1ID0gJCgnPGRpdiBpZD0iaHRtbF9jMGE0OTM4YzU2NjY0MmM4OGM3YjkxM2YwZjk2YTNmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TU9SRUxMQSBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZGRkODhjNWNjOGM0ZjZlYjRmNjViYWNmMzBlMWI5Ny5zZXRDb250ZW50KGh0bWxfYzBhNDkzOGM1NjY2NDJjODhjN2I5MTNmMGY5NmEzZjUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjYyNDk0ZjllYWZhNGUyNmEyYzVkOGFlMTgyNTVjYWIuYmluZFBvcHVwKHBvcHVwXzlkZGQ4OGM1Y2M4YzRmNmViNGY2NWJhY2YzMGUxYjk3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMzMWNiZTUzNGIxNjQyYzBiZGYyODFkNGY3NTZiY2JlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuMTQwMDA0NSwtMS4xODY0NDE2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI5N2E1ODUzZTUzZTQxODA4Y2M0NzAzMGZkODFhMGQ2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY5YWM2ZDAwYmI3YTQ3MTJhYTZmYzMyOWFlM2M1ZDhhID0gJCgnPGRpdiBpZD0iaHRtbF82OWFjNmQwMGJiN2E0NzEyYWE2ZmMzMjlhZTNjNWQ4YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TU9VTlRWSUVXIENMT1NFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yOTdhNTg1M2U1M2U0MTgwOGNjNDcwMzBmZDgxYTBkNi5zZXRDb250ZW50KGh0bWxfNjlhYzZkMDBiYjdhNDcxMmFhNmZjMzI5YWUzYzVkOGEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzMxY2JlNTM0YjE2NDJjMGJkZjI4MWQ0Zjc1NmJjYmUuYmluZFBvcHVwKHBvcHVwXzI5N2E1ODUzZTUzZTQxODA4Y2M0NzAzMGZkODFhMGQ2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IxZGI1ZTAyMzQyNjQ3NzJhY2Q3ZWYwZjkxY2JkNWY0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDQ4MTk4MywtMC4xNTY1MDM2ODY4MjIwNjA0Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMWIzODMzY2M4OTg0MWY5YWZhMDAwZTFiNTgwYjAyMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81OWM3OTlhOGI2Zjc0NDU4YmRmZDkyMTg1ODQwMzhhZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNTljNzk5YThiNmY3NDQ1OGJkZmQ5MjE4NTg0MDM4YWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5JR0hUSU5HQUxFIFNRVUFSRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjFiMzgzM2NjODk4NDFmOWFmYTAwMGUxYjU4MGIwMjEuc2V0Q29udGVudChodG1sXzU5Yzc5OWE4YjZmNzQ0NThiZGZkOTIxODU4NDAzOGFkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2IxZGI1ZTAyMzQyNjQ3NzJhY2Q3ZWYwZjkxY2JkNWY0LmJpbmRQb3B1cChwb3B1cF8yMWIzODMzY2M4OTg0MWY5YWZhMDAwZTFiNTgwYjAyMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNjA5ZDcxYWE3Mzg0MTJlOTJiOTYzZDljZDFkZTYyMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjU4MTM1OTgsLTAuMTczNjc2Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZGIxNzNmMzJjNGQ0MjNmOTg4ZGI3ZDAxZDc4YTg2NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZDcxNjMzNDg3ZWQ0NTljYjVjZmUxOGUwOGQ2ZGYwOSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGQ3MTYzMzQ4N2VkNDU5Y2I1Y2ZlMThlMDhkNmRmMDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5PUlJJQ0UgTEVBPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZGIxNzNmMzJjNGQ0MjNmOTg4ZGI3ZDAxZDc4YTg2Ny5zZXRDb250ZW50KGh0bWxfNGQ3MTYzMzQ4N2VkNDU5Y2I1Y2ZlMThlMDhkNmRmMDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzYwOWQ3MWFhNzM4NDEyZTkyYjk2M2Q5Y2QxZGU2MjAuYmluZFBvcHVwKHBvcHVwXzdkYjE3M2YzMmM0ZDQyM2Y5ODhkYjdkMDFkNzhhODY3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U2NzFjMjQyMTRkZTQ4ODk5ZjY0MDQyN2YyMTBjNWJlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTU3MTE3NCwtMC4xODM2MDQ2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBlZmJiN2YwZmQ4NjRhZjY4ZjdjYTIxYmUwODBlNDlmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE0YjNiYWJkNjk0NjQyYWU4NWJiNjQ0ZTg4MDUyMWEyID0gJCgnPGRpdiBpZD0iaHRtbF8xNGIzYmFiZDY5NDY0MmFlODViYjY0NGU4ODA1MjFhMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+T0FLIEhJTEwgUEFSSyBNRVdTPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZWZiYjdmMGZkODY0YWY2OGY3Y2EyMWJlMDgwZTQ5Zi5zZXRDb250ZW50KGh0bWxfMTRiM2JhYmQ2OTQ2NDJhZTg1YmI2NDRlODgwNTIxYTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTY3MWMyNDIxNGRlNDg4OTlmNjQwNDI3ZjIxMGM1YmUuYmluZFBvcHVwKHBvcHVwXzBlZmJiN2YwZmQ4NjRhZjY4ZjdjYTIxYmUwODBlNDlmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VjM2UyMmFjZWI2ZTQ5ZjhhYTMyNTY0NGFiNTExY2M4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDkwMDY5NCwtMC4wODAxMzY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZjNmI2M2NmNzY1MzQ4OWZiMmQyOWFlZmM5Nzg2MjFkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RkNmYwZjYzYTE2NDRlNWU5NDQxMWRlZmFiYmZlMTBhID0gJCgnPGRpdiBpZD0iaHRtbF9kZDZmMGY2M2ExNjQ0ZTVlOTQ0MTFkZWZhYmJmZTEwYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+T0xEIEtFTlQgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmM2YjYzY2Y3NjUzNDg5ZmIyZDI5YWVmYzk3ODYyMWQuc2V0Q29udGVudChodG1sX2RkNmYwZjYzYTE2NDRlNWU5NDQxMWRlZmFiYmZlMTBhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VjM2UyMmFjZWI2ZTQ5ZjhhYTMyNTY0NGFiNTExY2M4LmJpbmRQb3B1cChwb3B1cF9mYzZiNjNjZjc2NTM0ODlmYjJkMjlhZWZjOTc4NjIxZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84NTM3ZjZiYjA2MDY0N2Y2ODJjNGE1YTJlMjdjZjdhMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQ1LjQwNTE2MjQsLTc1LjY3MTMxNzNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzIyZTRjMDFjODFjNDdjY2FkZThjNTQzMTY4OTVhZjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTVkZjQwZTUzZDFmNDM0ZjhmZjNjMjRiZWI3OWViYTQgPSAkKCc8ZGl2IGlkPSJodG1sX2E1ZGY0MGU1M2QxZjQzNGY4ZmYzYzI0YmViNzllYmE0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5PTlNMT1cgQ1JFU0NFTlQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MyMmU0YzAxYzgxYzQ3Y2NhZGU4YzU0MzE2ODk1YWY2LnNldENvbnRlbnQoaHRtbF9hNWRmNDBlNTNkMWY0MzRmOGZmM2MyNGJlYjc5ZWJhNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NTM3ZjZiYjA2MDY0N2Y2ODJjNGE1YTJlMjdjZjdhMS5iaW5kUG9wdXAocG9wdXBfYzIyZTRjMDFjODFjNDdjY2FkZThjNTQzMTY4OTVhZjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTk1OTRhMGE5ZWJhNGRjY2E0Y2FjMGQ0MjM4MTk2YjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODc2Mzc1LC0wLjE2MTM4MThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTM2MWNkMzQ3YjExNDgwMGFlMDZlMGM2YTJlNTY0ZDkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGJiMjJlMDhlZGNhNDk0MTg2NWJiN2E4MWU0YTc0YjAgPSAkKCc8ZGl2IGlkPSJodG1sXzBiYjIyZTA4ZWRjYTQ5NDE4NjViYjdhODFlNGE3NGIwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5PUk1PTkRFIEdBVEU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkzNjFjZDM0N2IxMTQ4MDBhZTA2ZTBjNmEyZTU2NGQ5LnNldENvbnRlbnQoaHRtbF8wYmIyMmUwOGVkY2E0OTQxODY1YmI3YTgxZTRhNzRiMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xOTU5NGEwYTllYmE0ZGNjYTRjYWMwZDQyMzgxOTZiNS5iaW5kUG9wdXAocG9wdXBfOTM2MWNkMzQ3YjExNDgwMGFlMDZlMGM2YTJlNTY0ZDkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTJhNmE5YzJkMTM1NDAzYzkyY2Q1ZWYxN2NiM2RkOGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFstMzcuNTgwMDYwMiwxNDQuOTIwNzMyOV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNzM2NDlkODE5Mzg0NWFhYTZhM2M5MjE3NzkzZjhlYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81NGYzMjM4YzkxNmU0N2VmOTc3NjUxNmM4YzM5MGQ3MiA9ICQoJzxkaXYgaWQ9Imh0bWxfNTRmMzIzOGM5MTZlNDdlZjk3NzY1MTZjOGMzOTBkNzIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBRERJTkdUT04gU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yNzM2NDlkODE5Mzg0NWFhYTZhM2M5MjE3NzkzZjhlYy5zZXRDb250ZW50KGh0bWxfNTRmMzIzOGM5MTZlNDdlZjk3NzY1MTZjOGMzOTBkNzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTJhNmE5YzJkMTM1NDAzYzkyY2Q1ZWYxN2NiM2RkOGMuYmluZFBvcHVwKHBvcHVwXzI3MzY0OWQ4MTkzODQ1YWFhNmEzYzkyMTc3OTNmOGVjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y1NjhlNzlkNzI1OTQxYzlhMWVmZDMwZDJiODIxMjg4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzUuNzM3MTU4MiwtNzguODI2NTYzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMTcwOWQyZTUwODk0OTQ0ODQ4NDcxNzQxMjUxZTZhNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMWE4YThhNDJiNTI0NzJlYWI2NjhlZDhhZWUzY2YxYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjFhOGE4YTQyYjUyNDcyZWFiNjY4ZWQ4YWVlM2NmMWEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBTEFDRSBDT1VSVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDE3MDlkMmU1MDg5NDk0NDg0ODQ3MTc0MTI1MWU2YTQuc2V0Q29udGVudChodG1sX2YxYThhOGE0MmI1MjQ3MmVhYjY2OGVkOGFlZTNjZjFhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y1NjhlNzlkNzI1OTQxYzlhMWVmZDMwZDJiODIxMjg4LmJpbmRQb3B1cChwb3B1cF8wMTcwOWQyZTUwODk0OTQ0ODQ4NDcxNzQxMjUxZTZhNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zM2I3NjgxZWUwOTM0YmY5OWJhMzdmMzNiM2U1ZDgzZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjY5MDI4OTQsLTIuNTY4NTI5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yYzVmMjk2OWEyY2I0Y2U3YTQ1Y2E2NmYwYWIwZGY0NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lOWU2YTljYWE0MGU0YjlkYjJhZmUwMTY0ZmE3ZDM2NiA9ICQoJzxkaXYgaWQ9Imh0bWxfZTllNmE5Y2FhNDBlNGI5ZGIyYWZlMDE2NGZhN2QzNjYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBUktFIFJPQUQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJjNWYyOTY5YTJjYjRjZTdhNDVjYTY2ZjBhYjBkZjQ2LnNldENvbnRlbnQoaHRtbF9lOWU2YTljYWE0MGU0YjlkYjJhZmUwMTY0ZmE3ZDM2Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zM2I3NjgxZWUwOTM0YmY5OWJhMzdmMzNiM2U1ZDgzZC5iaW5kUG9wdXAocG9wdXBfMmM1ZjI5NjlhMmNiNGNlN2E0NWNhNjZmMGFiMGRmNDYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjk0ZmY3OGIzMWM3NGJkZWE1ODc5NWY5ZTk5NjM2NzMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi45MjMzMiwtMS4xOTkxNzAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY2MDI5NzlhYmEwMjQ2NDZhYWM2NDViMjgwNzMyYWQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ5ZjM3ZTE0YTEzZjQyOGFhNjg1Njc1YzBmNGJjNTFmID0gJCgnPGRpdiBpZD0iaHRtbF80OWYzN2UxNGExM2Y0MjhhYTY4NTY3NWMwZjRiYzUxZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEFSUiYjMzk7UyBXQVk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY2MDI5NzlhYmEwMjQ2NDZhYWM2NDViMjgwNzMyYWQxLnNldENvbnRlbnQoaHRtbF80OWYzN2UxNGExM2Y0MjhhYTY4NTY3NWMwZjRiYzUxZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iOTRmZjc4YjMxYzc0YmRlYTU4Nzk1ZjllOTk2MzY3My5iaW5kUG9wdXAocG9wdXBfNjYwMjk3OWFiYTAyNDY0NmFhYzY0NWIyODA3MzJhZDEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjcwNmI0NGU0OGJlNGFkNTliOGNmMGNkNzFkZDliNzMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NzE4Njg0LC0wLjE5OTgxNzFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDYwNWE2MTU1NWYxNDRkZGEzZDhkZDIzOGZiYzcyNzUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjI2ODg2ZjgzZTc1NDdjM2E1MTY0MGM2MmE0YmM2ODYgPSAkKCc8ZGl2IGlkPSJodG1sXzYyNjg4NmY4M2U3NTQ3YzNhNTE2NDBjNjJhNGJjNjg2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QQVJTT05TIEdBVEUgTUVXUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDYwNWE2MTU1NWYxNDRkZGEzZDhkZDIzOGZiYzcyNzUuc2V0Q29udGVudChodG1sXzYyNjg4NmY4M2U3NTQ3YzNhNTE2NDBjNjJhNGJjNjg2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY3MDZiNDRlNDhiZTRhZDU5YjhjZjBjZDcxZGQ5YjczLmJpbmRQb3B1cChwb3B1cF8wNjA1YTYxNTU1ZjE0NGRkYTNkOGRkMjM4ZmJjNzI3NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kOWJhMjliYzYzMDM0Yzk0OTI2YmI2N2YzMmU1MGNkYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjA4NjU5ODQsMS4xNzY0ODg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2JhZTZjZDAyZWE0YzRiODdhMjM0MjQwOGMzNzQ5MWE4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzExMGIxZmFjYjU1YzQwYjdiODU4N2NlMGViZDNkZjRhID0gJCgnPGRpdiBpZD0iaHRtbF8xMTBiMWZhY2I1NWM0MGI3Yjg1ODdjZTBlYmQzZGY0YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEFWSUxJT04gUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmFlNmNkMDJlYTRjNGI4N2EyMzQyNDA4YzM3NDkxYTguc2V0Q29udGVudChodG1sXzExMGIxZmFjYjU1YzQwYjdiODU4N2NlMGViZDNkZjRhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q5YmEyOWJjNjMwMzRjOTQ5MjZiYjY3ZjMyZTUwY2RjLmJpbmRQb3B1cChwb3B1cF9iYWU2Y2QwMmVhNGM0Yjg3YTIzNDI0MDhjMzc0OTFhOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MDRiNmRlOGRiY2M0NzlhYWE3OWZjNzM1MDYwMjdmMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUxMTY0ODYsLTAuMTk3NzkyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZmUxNmI2MWVmNjA0MTYzODUzNjdjNDIzZWY4NzRhMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZmNiMzhmNTQ0ZTQ0NWRkYjIwOWVhYzVlNTdhNjM2NiA9ICQoJzxkaXYgaWQ9Imh0bWxfNWZjYjM4ZjU0NGU0NDVkZGIyMDllYWM1ZTU3YTYzNjYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBFTUJSSURHRSBDUkVTQ0VOVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2ZlMTZiNjFlZjYwNDE2Mzg1MzY3YzQyM2VmODc0YTMuc2V0Q29udGVudChodG1sXzVmY2IzOGY1NDRlNDQ1ZGRiMjA5ZWFjNWU1N2E2MzY2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYwNGI2ZGU4ZGJjYzQ3OWFhYTc5ZmM3MzUwNjAyN2YxLmJpbmRQb3B1cChwb3B1cF83ZmUxNmI2MWVmNjA0MTYzODUzNjdjNDIzZWY4NzRhMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yZTBhNDU3YzIzYWQ0YzlhOGZmMmU4MzM0NDAxYjUzOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUxMjM2MTksLTAuMTk4Mjk0N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84YmFhYzlkNTU1Mzc0ZTc5YWY5NDMyY2ViZDFlMGVhMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MGE5M2UwMmEyNWM0MjlkYWRhMWM5ZjE3YjFmZDJhMiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTBhOTNlMDJhMjVjNDI5ZGFkYTFjOWYxN2IxZmQyYTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBFTUJSSURHRSBNRVdTPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84YmFhYzlkNTU1Mzc0ZTc5YWY5NDMyY2ViZDFlMGVhMi5zZXRDb250ZW50KGh0bWxfOTBhOTNlMDJhMjVjNDI5ZGFkYTFjOWYxN2IxZmQyYTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmUwYTQ1N2MyM2FkNGM5YThmZjJlODMzNDQwMWI1MzkuYmluZFBvcHVwKHBvcHVwXzhiYWFjOWQ1NTUzNzRlNzlhZjk0MzJjZWJkMWUwZWEyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhhYTFjYmFjMmY2YTQwMmJiODYwZjEzOTliNWM3MTNiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuNzUwMDgyNSwtMC4zMjM0MzMzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk2MTM0ZmVlYjM5ZTQ4YjE5YTY3NmViOTM2ZjkwMjgzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEyMTljMmY0OTczYjRmYjlhMDZkMDM1ZGMwYWFkNWE5ID0gJCgnPGRpdiBpZD0iaHRtbF8xMjE5YzJmNDk3M2I0ZmI5YTA2ZDAzNWRjMGFhZDVhOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEVOTklOR1RPTiBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk2MTM0ZmVlYjM5ZTQ4YjE5YTY3NmViOTM2ZjkwMjgzLnNldENvbnRlbnQoaHRtbF8xMjE5YzJmNDk3M2I0ZmI5YTA2ZDAzNWRjMGFhZDVhOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YWExY2JhYzJmNmE0MDJiYjg2MGYxMzk5YjVjNzEzYi5iaW5kUG9wdXAocG9wdXBfOTYxMzRmZWViMzllNDhiMTlhNjc2ZWI5MzZmOTAyODMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmRmZWI0OTE4NDQ1NDcwYmJiMGU1ZDEwMWVhZjk2NjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi4wMDEzNjA0LC0wLjgwNDI3NzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2MxMDg4YThkY2M0NDdmNTllYmQ0MTJhYmYzY2IwMzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWVmNjVjMWZlYmU5NDI3ZDg2ZmNlZWI4ZTk1OTQ3Y2QgPSAkKCc8ZGl2IGlkPSJodG1sXzllZjY1YzFmZWJlOTQyN2Q4NmZjZWViOGU5NTk0N2NkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QSUNUT04gU1RSRUVUPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zYzEwODhhOGRjYzQ0N2Y1OWViZDQxMmFiZjNjYjAzMC5zZXRDb250ZW50KGh0bWxfOWVmNjVjMWZlYmU5NDI3ZDg2ZmNlZWI4ZTk1OTQ3Y2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmRmZWI0OTE4NDQ1NDcwYmJiMGU1ZDEwMWVhZjk2NjUuYmluZFBvcHVwKHBvcHVwXzNjMTA4OGE4ZGNjNDQ3ZjU5ZWJkNDEyYWJmM2NiMDMwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JkZjliMjY2ZTdlNDQwYzM4MjFkY2Q1Yjk5Njg2YWM1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzkuMzQ5MzM4OSwtMTIzLjgxNDE4MjFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWRmM2ZlNjUxNzllNGRhNDg5YTY5MTNiOWQ2ZjdjNDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjkwOGY4NjgzNjg1NGZiZGI1OTU1MDRkZjUyNzRiNjkgPSAkKCc8ZGl2IGlkPSJodG1sXzY5MDhmODY4MzY4NTRmYmRiNTk1NTA0ZGY1Mjc0YjY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QSU5FIEdST1ZFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hZGYzZmU2NTE3OWU0ZGE0ODlhNjkxM2I5ZDZmN2M0NS5zZXRDb250ZW50KGh0bWxfNjkwOGY4NjgzNjg1NGZiZGI1OTU1MDRkZjUyNzRiNjkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmRmOWIyNjZlN2U0NDBjMzgyMWRjZDViOTk2ODZhYzUuYmluZFBvcHVwKHBvcHVwX2FkZjNmZTY1MTc5ZTRkYTQ4OWE2OTEzYjlkNmY3YzQ1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I4NmQ3ZmM2MGRlNzQ3YjdiODdkOTIzZDg0MmQzZTNiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuMDIzMjc1NywtMTA1LjI4MzEwOTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGQ0ZmEzOTI1Zjg0NDkzNzkyNDlhNGFmYmY4NGVhMmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmMzNjlkMjI3NjI0NDhhNjgwM2E4NmZiOTM1ODVlYzcgPSAkKCc8ZGl2IGlkPSJodG1sXzZjMzY5ZDIyNzYyNDQ4YTY4MDNhODZmYjkzNTg1ZWM3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QT1JUTEFORCBQTEFDRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGQ0ZmEzOTI1Zjg0NDkzNzkyNDlhNGFmYmY4NGVhMmEuc2V0Q29udGVudChodG1sXzZjMzY5ZDIyNzYyNDQ4YTY4MDNhODZmYjkzNTg1ZWM3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I4NmQ3ZmM2MGRlNzQ3YjdiODdkOTIzZDg0MmQzZTNiLmJpbmRQb3B1cChwb3B1cF9kZDRmYTM5MjVmODQ0OTM3OTI0OWE0YWZiZjg0ZWEyYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hNDU2ZGU3ZWQ5OTQ0OGIxOGJjMGY3ODRmMjc2MTg0YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjI1NDY5NjYsLTc5Ljg2Nzc5NDk3Mjk3Mjk3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk3MDkyMWUyMjdkZjRhNjA4NzI1ODdhYjBmNDY4MDc5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzYyMzBkOGY2YWZkNTRmZWJiNmE5ZTJlNjM3Yjk0MjA0ID0gJCgnPGRpdiBpZD0iaHRtbF82MjMwZDhmNmFmZDU0ZmViYjZhOWUyZTYzN2I5NDIwNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UFJJTkNFUyBTUVVBUkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk3MDkyMWUyMjdkZjRhNjA4NzI1ODdhYjBmNDY4MDc5LnNldENvbnRlbnQoaHRtbF82MjMwZDhmNmFmZDU0ZmViYjZhOWUyZTYzN2I5NDIwNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hNDU2ZGU3ZWQ5OTQ0OGIxOGJjMGY3ODRmMjc2MTg0Yi5iaW5kUG9wdXAocG9wdXBfOTcwOTIxZTIyN2RmNGE2MDg3MjU4N2FiMGY0NjgwNzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzczYTJkOTliMTg3NDQxNWIwY2E1NzVkMTdmOTBiN2YgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi4wNjQxMzExLC0wLjc5NjI2NjddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWU3NzQ1ODNjNDgxNGE0NjkyZTlkNjRkMTY2M2M5N2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2FmMDkyNzI2ZGZkNGE5NWJmZTEzYTk5OTY5NzkyNGYgPSAkKCc8ZGl2IGlkPSJodG1sXzdhZjA5MjcyNmRmZDRhOTViZmUxM2E5OTk2OTc5MjRmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RVUVFTiBBTk5FIFNUUkVFVDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWU3NzQ1ODNjNDgxNGE0NjkyZTlkNjRkMTY2M2M5N2Muc2V0Q29udGVudChodG1sXzdhZjA5MjcyNmRmZDRhOTViZmUxM2E5OTk2OTc5MjRmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc3M2EyZDk5YjE4NzQ0MTViMGNhNTc1ZDE3ZjkwYjdmLmJpbmRQb3B1cChwb3B1cF9hZTc3NDU4M2M0ODE0YTQ2OTJlOWQ2NGQxNjYzYzk3Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hY2I4MjQwZGQxZTE0NjM2OGVkMWI1ODQ0NjJlNDE5NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUwNDQ4OTYsLTAuMjk4MTQwNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hY2U0M2M3ZTRkYTY0M2Q1YTA0NTRjMTQyMmVlOWQyYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wOWY0N2Y1OWI3NDE0NzU0YTY1OGE2ZmE2N2UzMWE4ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfMDlmNDdmNTliNzQxNDc1NGE2NThhNmZhNjdlMzFhOGQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlFVRUVOIEFOTkVTIEdST1ZFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hY2U0M2M3ZTRkYTY0M2Q1YTA0NTRjMTQyMmVlOWQyYy5zZXRDb250ZW50KGh0bWxfMDlmNDdmNTliNzQxNDc1NGE2NThhNmZhNjdlMzFhOGQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWNiODI0MGRkMWUxNDYzNjhlZDFiNTg0NDYyZTQxOTcuYmluZFBvcHVwKHBvcHVwX2FjZTQzYzdlNGRhNjQzZDVhMDQ1NGMxNDIyZWU5ZDJjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU3YTljNGExNDMxZDQzYTliZjM0MjVhMzNlZDkzYjQ5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDg2ODkwMSwtMC4xODY3MjY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhiY2E4ZDkwNDExZjQ3OWJiZDZhOWFhYzgzMGY1Y2VhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RhNjYzMmM5MTViMjQ0NDU5YjcyMTJjYjNiNGYxNzRlID0gJCgnPGRpdiBpZD0iaHRtbF9kYTY2MzJjOTE1YjI0NDQ1OWI3MjEyY2IzYjRmMTc0ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UkVEQ0xJRkZFIE1FV1M8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhiY2E4ZDkwNDExZjQ3OWJiZDZhOWFhYzgzMGY1Y2VhLnNldENvbnRlbnQoaHRtbF9kYTY2MzJjOTE1YjI0NDQ1OWI3MjEyY2IzYjRmMTc0ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81N2E5YzRhMTQzMWQ0M2E5YmYzNDI1YTMzZWQ5M2I0OS5iaW5kUG9wdXAocG9wdXBfOGJjYThkOTA0MTFmNDc5YmJkNmE5YWFjODMwZjVjZWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2YzNjhlNjhmOGU3NDFmOWIxN2E3ZWViNjEyZTllNDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszOC44Mjg4ODE3LC03Ny4wNDU1Njc0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzgxMDQ1NzJkZTNkODRmN2U4ZWYyMGI2YmZiODhlZDdiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhlMzYwNThiZDg0MzRiYTRhZjIzOTliNmU2MmZhYTI5ID0gJCgnPGRpdiBpZD0iaHRtbF84ZTM2MDU4YmQ4NDM0YmE0YWYyMzk5YjZlNjJmYWEyOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Uk9TRSBTUVVBUkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgxMDQ1NzJkZTNkODRmN2U4ZWYyMGI2YmZiODhlZDdiLnNldENvbnRlbnQoaHRtbF84ZTM2MDU4YmQ4NDM0YmE0YWYyMzk5YjZlNjJmYWEyOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZjM2OGU2OGY4ZTc0MWY5YjE3YTdlZWI2MTJlOWU0OC5iaW5kUG9wdXAocG9wdXBfODEwNDU3MmRlM2Q4NGY3ZThlZjIwYjZiZmI4OGVkN2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYWVjYTYxMTQ0ZTBmNGRiNzk1YzQ5NGFkZWNhOGQxYjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1My40ODE0OTE1LC0yLjExMDI3ODRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2E3ZTc4Njc0ZDkyNDFjODk0NWZhZDVjZDNhYjFmMzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjNiZTE1NDA0ZGFiNDllNjkxYWJmOGY2ZWRkMjJlMDQgPSAkKCc8ZGl2IGlkPSJodG1sX2IzYmUxNTQwNGRhYjQ5ZTY5MWFiZjhmNmVkZDIyZTA0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SWUVDUk9GVCBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNhN2U3ODY3NGQ5MjQxYzg5NDVmYWQ1Y2QzYWIxZjMxLnNldENvbnRlbnQoaHRtbF9iM2JlMTU0MDRkYWI0OWU2OTFhYmY4ZjZlZGQyMmUwNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hZWNhNjExNDRlMGY0ZGI3OTVjNDk0YWRlY2E4ZDFiMi5iaW5kUG9wdXAocG9wdXBfM2E3ZTc4Njc0ZDkyNDFjODk0NWZhZDVjZDNhYjFmMzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzNjNGQ4YjI1ZGI4NDg0NzlhMGZjZjg1ZGE0MDRmY2UgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MzMyNjEsLTAuMjAzNjM3MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82Yjk2NDMwYzc4MTg0YWFiYTJlOGEwMmUyYmE4Y2Q4ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84YmRjNTBiODZlYzE0NTE4OWE0NzMyNzdiMDc3ZDFkZiA9ICQoJzxkaXYgaWQ9Imh0bWxfOGJkYzUwYjg2ZWMxNDUxODlhNDczMjc3YjA3N2QxZGYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNBTFVTQlVSWSBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82Yjk2NDMwYzc4MTg0YWFiYTJlOGEwMmUyYmE4Y2Q4Zi5zZXRDb250ZW50KGh0bWxfOGJkYzUwYjg2ZWMxNDUxODlhNDczMjc3YjA3N2QxZGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzNjNGQ4YjI1ZGI4NDg0NzlhMGZjZjg1ZGE0MDRmY2UuYmluZFBvcHVwKHBvcHVwXzZiOTY0MzBjNzgxODRhYWJhMmU4YTAyZTJiYThjZDhmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RjZmU2ZWEwYWM0NDRmMTZhMmMyY2U3ZmJkZGE1MmU4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDk4NzQ2MywtMC4xODkwNzg3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ExOWJiODlmNjMzYzRhMmZhOTkxNGZlMTAyYmJhZmEwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JjY2YxNjA1NWMwNTQ3NWE5ZTE0NGQyMmM1ZGVhYjcyID0gJCgnPGRpdiBpZD0iaHRtbF9iY2NmMTYwNTVjMDU0NzVhOWUxNDRkMjJjNWRlYWI3MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U09VVEggRU5EIFJPVzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTE5YmI4OWY2MzNjNGEyZmE5OTE0ZmUxMDJiYmFmYTAuc2V0Q29udGVudChodG1sX2JjY2YxNjA1NWMwNTQ3NWE5ZTE0NGQyMmM1ZGVhYjcyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RjZmU2ZWEwYWM0NDRmMTZhMmMyY2U3ZmJkZGE1MmU4LmJpbmRQb3B1cChwb3B1cF9hMTliYjg5ZjYzM2M0YTJmYTk5MTRmZTEwMmJiYWZhMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MDc3YmE4YTAxMzI0NzU2OTEzNTkxYzFkZWU0MGEzYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy00Mi4yOTgzMDQ4LDE0Ny4zNzE2NDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTY0NDg3YjdlYzkyNDNhMzk4ZDhlYWY2YTY4NmM1ZjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTI5NWM4MzdjMjU2NGM0OTgyNDkxYmM3NWU5OGE2ODEgPSAkKCc8ZGl2IGlkPSJodG1sXzEyOTVjODM3YzI1NjRjNDk4MjQ5MWJjNzVlOThhNjgxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TT1VUSCBQQVJBREU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U2NDQ4N2I3ZWM5MjQzYTM5OGQ4ZWFmNmE2ODZjNWY2LnNldENvbnRlbnQoaHRtbF8xMjk1YzgzN2MyNTY0YzQ5ODI0OTFiYzc1ZTk4YTY4MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MDc3YmE4YTAxMzI0NzU2OTEzNTkxYzFkZWU0MGEzYS5iaW5kUG9wdXAocG9wdXBfZTY0NDg3YjdlYzkyNDNhMzk4ZDhlYWY2YTY4NmM1ZjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNGJjYWQxYzU1OTZjNDQ5MTgzMzFjMGM3Nzk0NDE4YzUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTA4NTU0LC0wLjE2NDYxMDVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODUwODBhM2Q5NTgyNGQzMjgyZDRiYTMxNmI3YzE5ZjcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDcxM2E2N2FiNzYxNDQ5NDg5NTUyZjkwNzk5YTc4NGQgPSAkKCc8ZGl2IGlkPSJodG1sXzQ3MTNhNjdhYjc2MTQ0OTQ4OTU1MmY5MDc5OWE3ODRkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TUFJJTU9OVCBQTEFDRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODUwODBhM2Q5NTgyNGQzMjgyZDRiYTMxNmI3YzE5Zjcuc2V0Q29udGVudChodG1sXzQ3MTNhNjdhYjc2MTQ0OTQ4OTU1MmY5MDc5OWE3ODRkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRiY2FkMWM1NTk2YzQ0OTE4MzMxYzBjNzc5NDQxOGM1LmJpbmRQb3B1cChwb3B1cF84NTA4MGEzZDk1ODI0ZDMyODJkNGJhMzE2YjdjMTlmNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MjY5MmRiNjcyZTI0NDIxODk1MjA0MGIwYjQ3OGYxOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQyNzExNTgsLTAuMjA4NTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzQ3NDliZDk4ZDM4NGYwNmI2MTU0MmJiOTZiZDc4NzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjBhZTFhOTZiOTQ5NGI4OTkxYjA1ZTUzZjYxZGYxNzAgPSAkKCc8ZGl2IGlkPSJodG1sXzIwYWUxYTk2Yjk0OTRiODk5MWIwNWU1M2Y2MWRmMTcwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TVCBBVUJZTlMgQVZFTlVFPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jNDc0OWJkOThkMzg0ZjA2YjYxNTQyYmI5NmJkNzg3MC5zZXRDb250ZW50KGh0bWxfMjBhZTFhOTZiOTQ5NGI4OTkxYjA1ZTUzZjYxZGYxNzApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODI2OTJkYjY3MmUyNDQyMTg5NTIwNDBiMGI0NzhmMTguYmluZFBvcHVwKHBvcHVwX2M0NzQ5YmQ5OGQzODRmMDZiNjE1NDJiYjk2YmQ3ODcwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Q4NGU4NDY2MGRkZTQ0MDQ5YmFjZWUzMzFkZTJjNjJkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNzU2Mjk1OSwtMS4yNjE5MTg0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkMjBhNGM5MTRlNDRmODdiMzc0NGNhMGNkMTAwNmNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA0ZjBkYWViNjMxYzQzMDBhZDRmMDlhZWU4OWQzZjU5ID0gJCgnPGRpdiBpZD0iaHRtbF8wNGYwZGFlYjYzMWM0MzAwYWQ0ZjA5YWVlODlkM2Y1OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U1QgSk9ITiBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlkMjBhNGM5MTRlNDRmODdiMzc0NGNhMGNkMTAwNmNmLnNldENvbnRlbnQoaHRtbF8wNGYwZGFlYjYzMWM0MzAwYWQ0ZjA5YWVlODlkM2Y1OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kODRlODQ2NjBkZGU0NDA0OWJhY2VlMzMxZGUyYzYyZC5iaW5kUG9wdXAocG9wdXBfOWQyMGE0YzkxNGU0NGY4N2IzNzQ0Y2EwY2QxMDA2Y2YpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWM3ZDEwMWJiYmU1NDEwYWIxODc4ODg4OWY2Yjg4NDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1NC4zMjkyMjc5LC0xLjQyOTEyMDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWM1Y2VmMzA0YjNhNDdjMzk0NmRhOWFkYTUyZTQyYTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmRkYmQzMWQwMTYxNDE5NDg4MTFhMjg4N2EzZGRhNWEgPSAkKCc8ZGl2IGlkPSJodG1sX2ZkZGJkMzFkMDE2MTQxOTQ4ODExYTI4ODdhM2RkYTVhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TVCBTVEVQSEVOUyBHQVJERU5TPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lYzVjZWYzMDRiM2E0N2MzOTQ2ZGE5YWRhNTJlNDJhNi5zZXRDb250ZW50KGh0bWxfZmRkYmQzMWQwMTYxNDE5NDg4MTFhMjg4N2EzZGRhNWEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWM3ZDEwMWJiYmU1NDEwYWIxODc4ODg4OWY2Yjg4NDIuYmluZFBvcHVwKHBvcHVwX2VjNWNlZjMwNGIzYTQ3YzM5NDZkYTlhZGE1MmU0MmE2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk2MWJiZTcwYjZhNTQxMGE4MjczYmU3ZmRiYjA1M2IzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuMDk4MTMzMiwtMTE4LjQ1NjczMzJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzMwY2UwNzY3OTc5NGYyNTk5NjFkMTIyY2VjZTMxYTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTgyZDFkMjNiODg0NGRjZmIwZTgyNWJmYjYwYWE3Y2UgPSAkKCc8ZGl2IGlkPSJodG1sXzE4MmQxZDIzYjg4NDRkY2ZiMGU4MjViZmI2MGFhN2NlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TVFJBREVMTEEgUk9BRDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzMwY2UwNzY3OTc5NGYyNTk5NjFkMTIyY2VjZTMxYTkuc2V0Q29udGVudChodG1sXzE4MmQxZDIzYjg4NDRkY2ZiMGU4MjViZmI2MGFhN2NlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzk2MWJiZTcwYjZhNTQxMGE4MjczYmU3ZmRiYjA1M2IzLmJpbmRQb3B1cChwb3B1cF9jMzBjZTA3Njc5Nzk0ZjI1OTk2MWQxMjJjZWNlMzFhOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMWQwNjg5MmEzODE0ZmQwOWM5YmEwN2EwNjQyNWRhNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy0yOS44MjM5NzIsMzAuOTE5MjQ3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mYTNmZGYxZjBiYWI0MTg2YmE2ZjUzMzNmYmMyNDQxYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZTgyODNjOGY4NWY0ZTUzOGQ5MjAxNTI3MDZjNGFjZiA9ICQoJzxkaXYgaWQ9Imh0bWxfN2U4MjgzYzhmODVmNGU1MzhkOTIwMTUyNzA2YzRhY2YiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNZRE5FWSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ZhM2ZkZjFmMGJhYjQxODZiYTZmNTMzM2ZiYzI0NDFiLnNldENvbnRlbnQoaHRtbF83ZTgyODNjOGY4NWY0ZTUzOGQ5MjAxNTI3MDZjNGFjZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jMWQwNjg5MmEzODE0ZmQwOWM5YmEwN2EwNjQyNWRhNy5iaW5kUG9wdXAocG9wdXBfZmEzZmRmMWYwYmFiNDE4NmJhNmY1MzMzZmJjMjQ0MWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTg1MzM2YzYzZTFmNDhiMWE0Mzk3ZDM1NmQ0NmY1Y2QgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODcwMTIxLC0wLjE2Mjc3MjJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2ZiNzg3NTA1MTBkNDgwZmE4MTM1ZGNhY2Q3Njk0ZmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTY3Y2I1MWVmYjc4NDliOWFiNmRhMmJlZWMwMjYwODcgPSAkKCc8ZGl2IGlkPSJodG1sX2E2N2NiNTFlZmI3ODQ5YjlhYjZkYTJiZWVjMDI2MDg3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5URURXT1JUSCBTUVVBUkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNmYjc4NzUwNTEwZDQ4MGZhODEzNWRjYWNkNzY5NGZjLnNldENvbnRlbnQoaHRtbF9hNjdjYjUxZWZiNzg0OWI5YWI2ZGEyYmVlYzAyNjA4Nyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85ODUzMzZjNjNlMWY0OGIxYTQzOTdkMzU2ZDQ2ZjVjZC5iaW5kUG9wdXAocG9wdXBfM2ZiNzg3NTA1MTBkNDgwZmE4MTM1ZGNhY2Q3Njk0ZmMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzExNWQ0ZGFlMjNlNGEwMzg3MjRjZjQzZmM1MDQwMmMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODg2ODczLC0wLjE4NjQyNDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTkxMTg1ZTA3ODZmNDlhNWIxMTM4NTUzMmQwMGY4NTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjUzOTdiOTYxZWQxNGM2Mjk0ODU3YzA1MDk2ZGY4ZDAgPSAkKCc8ZGl2IGlkPSJodG1sX2Y1Mzk3Yjk2MWVkMTRjNjI5NDg1N2MwNTA5NmRmOGQwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5USEUgTElUVExFIEJPTFRPTlM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE5MTE4NWUwNzg2ZjQ5YTViMTEzODU1MzJkMDBmODUzLnNldENvbnRlbnQoaHRtbF9mNTM5N2I5NjFlZDE0YzYyOTQ4NTdjMDUwOTZkZjhkMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jMTE1ZDRkYWUyM2U0YTAzODcyNGNmNDNmYzUwNDAyYy5iaW5kUG9wdXAocG9wdXBfMTkxMTg1ZTA3ODZmNDlhNWIxMTM4NTUzMmQwMGY4NTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTIwNjVhOTExYzI5NDQ4MDk3MDkxZTQ3NjZmNWMwMWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS43NTQ0MTE2LC0wLjQ3MjYwNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzExM2YwZDM2YTU4NGI5ZTkyNWFkOTY5NWM4ZmUzZWQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzE0MzQ3Yjk3YzJlNDVjMWFkOTEwYWU2MmI4MjMzMTcgPSAkKCc8ZGl2IGlkPSJodG1sXzcxNDM0N2I5N2MyZTQ1YzFhZDkxMGFlNjJiODIzMzE3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5USEUgTUFSTE9XRVM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MxMTNmMGQzNmE1ODRiOWU5MjVhZDk2OTVjOGZlM2VkLnNldENvbnRlbnQoaHRtbF83MTQzNDdiOTdjMmU0NWMxYWQ5MTBhZTYyYjgyMzMxNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lMjA2NWE5MTFjMjk0NDgwOTcwOTFlNDc2NmY1YzAxZS5iaW5kUG9wdXAocG9wdXBfYzExM2YwZDM2YTU4NGI5ZTkyNWFkOTY5NWM4ZmUzZWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYWI0MmExMGEyNDAzNDMwNjk2NTNmOWQ2YmEyODg0MjcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS42MzIzODY0NSwtMC4yMTg4MjkxNDM2NDc4NDk0N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wN2M0YzZlYWUwMzc0NjQyYmU3NmZlZDIzZDBlMThiYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNTRlYTNiYjRjN2M0MzM1OTNhMGJhNTA0NjZkMmMzMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTU0ZWEzYmI0YzdjNDMzNTkzYTBiYTUwNDY2ZDJjMzIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRPVFRFUklER0UgQ09NTU9OPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wN2M0YzZlYWUwMzc0NjQyYmU3NmZlZDIzZDBlMThiYy5zZXRDb250ZW50KGh0bWxfMTU0ZWEzYmI0YzdjNDMzNTkzYTBiYTUwNDY2ZDJjMzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWI0MmExMGEyNDAzNDMwNjk2NTNmOWQ2YmEyODg0MjcuYmluZFBvcHVwKHBvcHVwXzA3YzRjNmVhZTAzNzQ2NDJiZTc2ZmVkMjNkMGUxOGJjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RlM2Y0NzkzMDJiMDQzNmE5NDE3MjU4MGNiM2ZjOGUwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTIwMTc1NywtMC4xNjA1MTY4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RjYmNhMjEwNmQyMzQ4NzRiNWFjMmYxYzJiYjljNGVlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZlMzU4MmE1ZmFmMTRkZTE5NzE4YjJjOTE0OWM2Y2IxID0gJCgnPGRpdiBpZD0iaHRtbF9mZTM1ODJhNWZhZjE0ZGUxOTcxOGIyYzkxNDljNmNiMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VVBQRVIgTU9OVEFHVSBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RjYmNhMjEwNmQyMzQ4NzRiNWFjMmYxYzJiYjljNGVlLnNldENvbnRlbnQoaHRtbF9mZTM1ODJhNWZhZjE0ZGUxOTcxOGIyYzkxNDljNmNiMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kZTNmNDc5MzAyYjA0MzZhOTQxNzI1ODBjYjNmYzhlMC5iaW5kUG9wdXAocG9wdXBfZGNiY2EyMTA2ZDIzNDg3NGI1YWMyZjFjMmJiOWM0ZWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjJmMmZjOGJlMmJkNDQxNWFmYTQ1ZGM2YWVlODExMjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43ODM0OTg3LC03Ny44MzMwNjg1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEyODFjZjA5NjIwODRmMGQ4NTIzMjVlYjdkNzdjZmNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUyYWY5ZTA0MjQ2YjQzZWFhNmU5YmExNmEwYzM3ZmZmID0gJCgnPGRpdiBpZD0iaHRtbF81MmFmOWUwNDI0NmI0M2VhYTZlOWJhMTZhMGMzN2ZmZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VklWSUFOIFdBWTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTI4MWNmMDk2MjA4NGYwZDg1MjMyNWViN2Q3N2NmY2Yuc2V0Q29udGVudChodG1sXzUyYWY5ZTA0MjQ2YjQzZWFhNmU5YmExNmEwYzM3ZmZmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYyZjJmYzhiZTJiZDQ0MTVhZmE0NWRjNmFlZTgxMTIyLmJpbmRQb3B1cChwb3B1cF8xMjgxY2YwOTYyMDg0ZjBkODUyMzI1ZWI3ZDc3Y2ZjZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82ZjU4ZmEzNjk2Yjk0NmNkOGQ5YjdlZDk5MTE1MGU4YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ4NDk5NjUsLTAuMTcyNTgyOV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81YzA1NTNjMmYxZTA0YmFhYTg4MzYyYTJjNWU1NzdjYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hMWY2ZDcwZTBjY2M0NTg2YTJmYTBjZDViZmFlNDkwYiA9ICQoJzxkaXYgaWQ9Imh0bWxfYTFmNmQ3MGUwY2NjNDU4NmEyZmEwY2Q1YmZhZTQ5MGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldBTERST04gTUVXUzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWMwNTUzYzJmMWUwNGJhYWE4ODM2MmEyYzVlNTc3Y2Muc2V0Q29udGVudChodG1sX2ExZjZkNzBlMGNjYzQ1ODZhMmZhMGNkNWJmYWU0OTBiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZmNThmYTM2OTZiOTQ2Y2Q4ZDliN2VkOTkxMTUwZThjLmJpbmRQb3B1cChwb3B1cF81YzA1NTNjMmYxZTA0YmFhYTg4MzYyYTJjNWU1NzdjYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYWNiNDM5NTQ5YzA0MjFjOGU1MWQzYzc0ZTM2MjQ3YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM2LjEyOTkwMywtODYuODIxNjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2Y2MjU5MjI1NTRiNGNlNWI4NWVjZWZmYWY1Yzk3M2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjRjNjZmNWI2MDExNGI1Yjg3ZjhlNjYxY2VlYThhY2YgPSAkKCc8ZGl2IGlkPSJodG1sXzI0YzY2ZjViNjAxMTRiNWI4N2Y4ZTY2MWNlZWE4YWNmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XQVJXSUNLIExBTkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNmNjI1OTIyNTU0YjRjZTViODVlY2VmZmFmNWM5NzNjLnNldENvbnRlbnQoaHRtbF8yNGM2NmY1YjYwMTE0YjViODdmOGU2NjFjZWVhOGFjZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iYWNiNDM5NTQ5YzA0MjFjOGU1MWQzYzc0ZTM2MjQ3YS5iaW5kUG9wdXAocG9wdXBfM2Y2MjU5MjI1NTRiNGNlNWI4NWVjZWZmYWY1Yzk3M2MpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2FmNDZkZjM1NjkxNDIxYTkzMDgzMjI5NjY1ZDBhNzcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFsxMy4zMDc4OTQ4LC01OS41ODk4NTc3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y5OTA2YmMyOGQ5ZjQ1YmRhYzUzNjVjM2QxZjQ0MTViID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI4NGNiYjEyNTA2ODQzNjNiMTg1NzZmZjBkMDNmZTVjID0gJCgnPGRpdiBpZD0iaHRtbF8yODRjYmIxMjUwNjg0MzYzYjE4NTc2ZmYwZDAzZmU1YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V0VMTCBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mOTkwNmJjMjhkOWY0NWJkYWM1MzY1YzNkMWY0NDE1Yi5zZXRDb250ZW50KGh0bWxfMjg0Y2JiMTI1MDY4NDM2M2IxODU3NmZmMGQwM2ZlNWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2FmNDZkZjM1NjkxNDIxYTkzMDgzMjI5NjY1ZDBhNzcuYmluZFBvcHVwKHBvcHVwX2Y5OTA2YmMyOGQ5ZjQ1YmRhYzUzNjVjM2QxZjQ0MTViKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZiYjIxYzkzMjM2NDQ2ZmNiNGM2NTgxNmQzYTYyMWUwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuMzM0Njg0NCwtMC4yNzY4MDc0OTMwNDI0MTg1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xNmQ3YjM5Yzc2OGQ0YjViYmQ4ZTlhYTI4MDY2NTUxMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jYjI4NmQyM2QzYjE0ODMzOTdiMzVmOTYwODViMTU0MyA9ICQoJzxkaXYgaWQ9Imh0bWxfY2IyODZkMjNkM2IxNDgzMzk3YjM1Zjk2MDg1YjE1NDMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldFU1QgSElMTCBQQVJLPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xNmQ3YjM5Yzc2OGQ0YjViYmQ4ZTlhYTI4MDY2NTUxMC5zZXRDb250ZW50KGh0bWxfY2IyODZkMjNkM2IxNDgzMzk3YjM1Zjk2MDg1YjE1NDMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmJiMjFjOTMyMzY0NDZmY2I0YzY1ODE2ZDNhNjIxZTAuYmluZFBvcHVwKHBvcHVwXzE2ZDdiMzljNzY4ZDRiNWJiZDhlOWFhMjgwNjY1NTEwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2MzY2NkNWQ2ZTc4MjRhNmE5YzM2NDgyNThjMzhkOGUzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzQuNDEwNzYxLC0xMTcuNjQxMTU4Nl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMzFlYzNmZTA1Njg0ZDk4ODM1MzI1OWEzNGM1MzBhYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZmQ2ZTZhYjU4NjI0YjY3YTdjZDNkOWZlMWI3YmQzYSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmZkNmU2YWI1ODYyNGI2N2E3Y2QzZDlmZTFiN2JkM2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldFU1QgSElMTCBST0FEPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMzFlYzNmZTA1Njg0ZDk4ODM1MzI1OWEzNGM1MzBhYS5zZXRDb250ZW50KGh0bWxfMmZkNmU2YWI1ODYyNGI2N2E3Y2QzZDlmZTFiN2JkM2EpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzNjY2Q1ZDZlNzgyNGE2YTljMzY0ODI1OGMzOGQ4ZTMuYmluZFBvcHVwKHBvcHVwX2UzMWVjM2ZlMDU2ODRkOTg4MzUzMjU5YTM0YzUzMGFhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM0MTQ0Nzk3NWNjOTQxOTVhYjU5ZWMxZTk1M2Q5NzU4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNzMxMDk5MSwwLjY3NDYyMTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWY0Njk0NDVkYWNjNDQwNTg1ZGJhNWY2NjhmZTNjN2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmUxNzIxYzE5ZWJkNGJjNDg0NTQzMGZiMGUwMTdlNDggPSAkKCc8ZGl2IGlkPSJodG1sXzJlMTcyMWMxOWViZDRiYzQ4NDU0MzBmYjBlMDE3ZTQ4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XRVNUIFNRVUFSRTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWY0Njk0NDVkYWNjNDQwNTg1ZGJhNWY2NjhmZTNjN2Muc2V0Q29udGVudChodG1sXzJlMTcyMWMxOWViZDRiYzQ4NDU0MzBmYjBlMDE3ZTQ4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM0MTQ0Nzk3NWNjOTQxOTVhYjU5ZWMxZTk1M2Q5NzU4LmJpbmRQb3B1cChwb3B1cF8xZjQ2OTQ0NWRhY2M0NDA1ODVkYmE1ZjY2OGZlM2M3Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84YjI5YzMwZmRiNDQ0OWUxYWIzNWVlYjZmYjczYjhlYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ2MDEzODksLTAuMjc1NDA0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NlNmVkOTUzOTIwNDRlMTBiNGVhZjI3ZDA5YWNmMTJkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI4MzUzMmQ0YzM1ZjQzMjY4Mjc4MmM3NGZkODRkNmMxID0gJCgnPGRpdiBpZD0iaHRtbF8yODM1MzJkNGMzNWY0MzI2ODI3ODJjNzRmZDg0ZDZjMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V0VTVCBURU1QTEUgU0hFRU48L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NlNmVkOTUzOTIwNDRlMTBiNGVhZjI3ZDA5YWNmMTJkLnNldENvbnRlbnQoaHRtbF8yODM1MzJkNGMzNWY0MzI2ODI3ODJjNzRmZDg0ZDZjMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YjI5YzMwZmRiNDQ0OWUxYWIzNWVlYjZmYjczYjhlYy5iaW5kUG9wdXAocG9wdXBfY2U2ZWQ5NTM5MjA0NGUxMGI0ZWFmMjdkMDlhY2YxMmQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzExZTQyYzcwYjYxNGYyOTk1ZjQ0OWM1OTZhMmNmNWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS42MDQ1NzYyLC0wLjA3MDc4NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzJhMGY1ZTEzMWU5NDhmMGE1OTJlMDIzNWJmZTUwNTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTc3N2NlZWM3OTE5NGQyZDkxMTI1MTJhMzJiOTc1ODEgPSAkKCc8ZGl2IGlkPSJodG1sXzk3NzdjZWVjNzkxOTRkMmQ5MTEyNTEyYTMyYjk3NTgxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XSElURSBIQVJUIExBTkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMyYTBmNWUxMzFlOTQ4ZjBhNTkyZTAyMzViZmU1MDU1LnNldENvbnRlbnQoaHRtbF85Nzc3Y2VlYzc5MTk0ZDJkOTExMjUxMmEzMmI5NzU4MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zMTFlNDJjNzBiNjE0ZjI5OTVmNDQ5YzU5NmEyY2Y1ZC5iaW5kUG9wdXAocG9wdXBfMzJhMGY1ZTEzMWU5NDhmMGE1OTJlMDIzNWJmZTUwNTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmFhNGJkNTM2MWU4NDUyMmEzNGI3MDgzNTIzZGRhNDcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zODAxMTI3LDAuNTI4ODg0NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF85NjFmZGM1NGE3NDA0YjAwOTMyMzZjYTExN2M1NjNhYyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zY2FjNzNkOTQ3OTU0N2IwOTE1YzBlMWM0MzFmM2NmZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ODE1YWQzZjBiNjU0NjNkOTRiOWZjMWYxZTM0ZjhhNyA9ICQoJzxkaXYgaWQ9Imh0bWxfNTgxNWFkM2YwYjY1NDYzZDk0YjlmYzFmMWUzNGY4YTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldISVRUQUtFUiBTVFJFRVQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNjYWM3M2Q5NDc5NTQ3YjA5MTVjMGUxYzQzMWYzY2ZmLnNldENvbnRlbnQoaHRtbF81ODE1YWQzZjBiNjU0NjNkOTRiOWZjMWYxZTM0ZjhhNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mYWE0YmQ1MzYxZTg0NTIyYTM0YjcwODM1MjNkZGE0Ny5iaW5kUG9wdXAocG9wdXBfM2NhYzczZDk0Nzk1NDdiMDkxNWMwZTFjNDMxZjNjZmYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzc4ZDc5NjgzNTYxNDMxYTkyZTBkYmVlYWQ0NGQ0YWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi45NTg1MjgzLC0xLjIwOTU2ODFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOTYxZmRjNTRhNzQwNGIwMDkzMjM2Y2ExMTdjNTYzYWMpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODU3YjMyNjlhYmQyNGFlMWFhOGQzMzE1M2JmODYyMGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTAyY2Y5YTJiMTAxNDMzNjk3OWExYjlhZDJlOGIwZTAgPSAkKCc8ZGl2IGlkPSJodG1sX2EwMmNmOWEyYjEwMTQzMzY5NzlhMWI5YWQyZThiMGUwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XT09EWUFSRCBMQU5FPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84NTdiMzI2OWFiZDI0YWUxYWE4ZDMzMTUzYmY4NjIwZC5zZXRDb250ZW50KGh0bWxfYTAyY2Y5YTJiMTAxNDMzNjk3OWExYjlhZDJlOGIwZTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzc4ZDc5NjgzNTYxNDMxYTkyZTBkYmVlYWQ0NGQ0YWIuYmluZFBvcHVwKHBvcHVwXzg1N2IzMjY5YWJkMjRhZTFhYThkMzMxNTNiZjg2MjBkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVmMTA0Yjk4OWE1NDQ5ZGVhMTE3NTYxYTNlNDg2ZThlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzIuNzU1MSwtOTcuNDU1NDg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzk2MWZkYzU0YTc0MDRiMDA5MzIzNmNhMTE3YzU2M2FjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA2OWVlOGY4ODFmNjRhMzk5NGJlOWE2N2E2ZGYxZGJhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUyMmYzZmFiZDM2NTQ3MWI5Y2IwZGU1N2EzNjAyNDkyID0gJCgnPGRpdiBpZD0iaHRtbF81MjJmM2ZhYmQzNjU0NzFiOWNiMGRlNTdhMzYwMjQ5MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V1lBVFQgRFJJVkU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA2OWVlOGY4ODFmNjRhMzk5NGJlOWE2N2E2ZGYxZGJhLnNldENvbnRlbnQoaHRtbF81MjJmM2ZhYmQzNjU0NzFiOWNiMGRlNTdhMzYwMjQ5Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81ZjEwNGI5ODlhNTQ0OWRlYTExNzU2MWEzZTQ4NmU4ZS5iaW5kUG9wdXAocG9wdXBfMDY5ZWU4Zjg4MWY2NGEzOTk0YmU5YTY3YTZkZjFkYmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" mozallowfullscreen="" onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" webkitallowfullscreen=""&gt;&lt;/iframe&gt;</div></div>



## Methodology<a></a>

For the methodology sector, the main goal is to analysis the data and recommend the most valuable venues for the customers based on the surrounding infrastructure and amenities, which retrieved from the Foursquare API.

K-means clustering is used to analyze the venues, given the fact that it is efficient in terms of computational cost and easy to implement when studying larget data set.

### Foursquare Credentials 


```python
CLIENT_ID = 'ZUG035NNNJK3W5UR2CECWBHTBDPYUZRIPRHBJQU31WEKVYLK' # my Foursquare ID
CLIENT_SECRET = 'G4IL2IN232SOLGAKEX2ZFRWWCRTFNH1E1TKRQ30GJ20YUNZS' # my Foursquare Secret
VERSION = '20200604'
LIMIT = 30
print('Your credentails:')
print('CLIENT_ID: '+CLIENT_ID)
print('CLIENT_SECRET: '+CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: ZUG035NNNJK3W5UR2CECWBHTBDPYUZRIPRHBJQU31WEKVYLK
    CLIENT_SECRET: G4IL2IN232SOLGAKEX2ZFRWWCRTFNH1E1TKRQ30GJ20YUNZS


### Nearby Venues


```python
def getNearbyVenues(names, latitudes, longitudes, radius=500, LIMIT=100):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            LIMIT)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Street', 
                  'Street Latitude', 
                  'Street Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```


```python
# get the venues for each street in the dataframe
london_venues = getNearbyVenues(names=df_p['Street'],
                                   latitudes=df_p['Latitude'],
                                   longitudes=df_p['Longitude']
                                  )
```

    ALBERT EMBANKMENT
    ALBION SQUARE
    ALLEYN PARK
    ARGYLE STREET
    ARTESIAN ROAD
    ASHCHURCH GROVE
    AYNHOE ROAD
    BEDALE STREET
    BEECHWOOD AVENUE
    BETTERTON STREET
    BETTRIDGE ROAD
    BILLING ROAD
    BLENHEIM CRESCENT
    BOURDON STREET
    BRADFIELD ROAD
    BROMPTON PLACE
    CAITHNESS ROAD
    CAMBRIDGE TERRACE
    CAMDEN SQUARE
    CAMPDEN HILL ROAD
    CANONBURY PARK SOUTH
    CARLISLE PLACE
    CARLTON GARDENS
    CARLTON ROAD
    CHALCOT SQUARE
    CHELSEA EMBANKMENT
    CHENISTON GARDENS
    CHESHAM MEWS
    CHEVENING ROAD
    COLLINGHAM ROAD
    COULTER ROAD
    COURT LANE GARDENS
    COURTHOPE ROAD
    COURTNELL STREET
    CRAWFORD MEWS
    CREDITON HILL
    DARTMOUTH PARK AVENUE
    DE VERE GARDENS
    DEEPDALE
    DEER PARK ROAD
    DEVEREUX LANE
    DEVONSHIRE MEWS WEST
    DOVER STREET
    DOWNSIDE CRESCENT
    DUDLEY ROAD
    DUKES LANE
    EATON TERRACE MEWS
    ECCLESTON MEWS
    ECCLESTON SQUARE
    ELLERBY STREET
    ELVASTON PLACE
    ENNISMORE GARDENS MEWS
    ESSEX STREET
    EVERSLEY CRESCENT
    EYNELLA ROAD
    FENCHURCH STREET
    FLORAL STREET
    FOUNTAYNE ROAD
    FOURNIER STREET
    FRISTON STREET
    GERTRUDE STREET
    GLOUCESTER PLACE MEWS
    GORDON COTTAGES
    GORST ROAD
    GRAHAM TERRACE
    GREAT TITCHFIELD STREET
    HALLAM STREET
    HALSEY STREET
    HARLEY GARDENS
    HARMSWORTH WAY
    HARWOOD ROAD
    HATCHAM ROAD
    HEATH DRIVE
    HEATH HURST ROAD
    HENDERSON ROAD
    HIGHBURY NEW PARK
    HILL STREET
    HILLWAY
    HOLBORN CLOSE
    HOLLAND PARK ROAD
    HYDE VALE
    IRVING ROAD
    JUNCTION PLACE
    KILDARE GARDENS
    LEAMOUTH ROAD
    LEINSTER SQUARE
    LILYVILLE ROAD
    LOWER BELGRAVE STREET
    LYONS PLACE
    MARESFIELD GARDENS
    MARKHAM STREET
    MILKY WAY
    MORELLA ROAD
    MOUNTVIEW CLOSE
    NIGHTINGALE SQUARE
    NORRICE LEA
    OAK HILL PARK MEWS
    OLD KENT ROAD
    ONSLOW CRESCENT
    ORMONDE GATE
    PADDINGTON STREET
    PALACE COURT
    PARKE ROAD
    PARR'S WAY
    PARSONS GATE MEWS
    PAVILION ROAD
    PEMBRIDGE CRESCENT
    PEMBRIDGE MEWS
    PENNINGTON STREET
    PICTON STREET
    PINE GROVE
    PORTLAND PLACE
    PRINCES SQUARE
    QUEEN ANNE STREET
    QUEEN ANNES GROVE
    REDCLIFFE MEWS
    ROSE SQUARE
    RYECROFT STREET
    SALUSBURY ROAD
    SOUTH END ROW
    SOUTH PARADE
    SPRIMONT PLACE
    ST AUBYNS AVENUE
    ST JOHN STREET
    ST STEPHENS GARDENS
    STRADELLA ROAD
    SYDNEY STREET
    TEDWORTH SQUARE
    THE LITTLE BOLTONS
    THE MARLOWES
    TOTTERIDGE COMMON
    UPPER MONTAGU STREET
    VIVIAN WAY
    WALDRON MEWS
    WARWICK LANE
    WELL ROAD
    WEST HILL PARK
    WEST HILL ROAD
    WEST SQUARE
    WEST TEMPLE SHEEN
    WHITE HART LANE
    WHITTAKER STREET
    WOODYARD LANE
    WYATT DRIVE



```python
print(london_venues.shape)
london_venues.head()
```

    (3688, 7)





<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>Street Latitude</th>
      <th>Street Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBERT EMBANKMENT</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>The Garden Cafe, Lambeth Palace</td>
      <td>51.495210</td>
      <td>-0.119962</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ALBERT EMBANKMENT</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>Newport Street Gallery</td>
      <td>51.493466</td>
      <td>-0.117040</td>
      <td>Art Gallery</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALBERT EMBANKMENT</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>Plaza On The River Club And Residence</td>
      <td>51.491529</td>
      <td>-0.121371</td>
      <td>Hotel</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ALBERT EMBANKMENT</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>Tamesis Dock</td>
      <td>51.491928</td>
      <td>-0.121934</td>
      <td>Bar</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ALBERT EMBANKMENT</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>Park Plaza London Riverbank</td>
      <td>51.491478</td>
      <td>-0.122177</td>
      <td>Hotel</td>
    </tr>
  </tbody>
</table>
</div>




```python
# get the List of Unique Categories
print('There are {} uniques categories.'.format(len(london_venues['Venue Category'].unique())))
print(london_venues.shape)
london_venues.groupby('Street').count().head()
```

    There are 318 uniques categories.
    (3688, 7)





<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street Latitude</th>
      <th>Street Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
    <tr>
      <th>Street</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ALBERT EMBANKMENT</th>
      <td>53</td>
      <td>53</td>
      <td>53</td>
      <td>53</td>
      <td>53</td>
      <td>53</td>
    </tr>
    <tr>
      <th>ALBION SQUARE</th>
      <td>26</td>
      <td>26</td>
      <td>26</td>
      <td>26</td>
      <td>26</td>
      <td>26</td>
    </tr>
    <tr>
      <th>ALLEYN PARK</th>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
    </tr>
    <tr>
      <th>ARGYLE STREET</th>
      <td>87</td>
      <td>87</td>
      <td>87</td>
      <td>87</td>
      <td>87</td>
      <td>87</td>
    </tr>
    <tr>
      <th>ARTESIAN ROAD</th>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



### One Hot Encoding of Features


```python
# one hot encoding
london_onehot = pd.get_dummies(london_venues[['Venue Category']], prefix="", prefix_sep="")

# add street column back to dataframe
london_onehot['Street'] = london_venues['Street'] 

# move street column to the first column
fixed_columns = [london_onehot.columns[-1]] + list(london_onehot.columns[:-1])
london_onehot = london_onehot[fixed_columns]
london_onehot.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>ATM</th>
      <th>Accessories Store</th>
      <th>Adult Boutique</th>
      <th>African Restaurant</th>
      <th>Airport</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>Antique Shop</th>
      <th>Argentinian Restaurant</th>
      <th>...</th>
      <th>Vietnamese Restaurant</th>
      <th>Warehouse Store</th>
      <th>Weight Loss Center</th>
      <th>Whisky Bar</th>
      <th>Wine Bar</th>
      <th>Wine Shop</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
      <th>Zoo Exhibit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 319 columns</p>
</div>




```python
london_grouped = london_onehot.groupby('Street').mean().reset_index()
london_grouped.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>ATM</th>
      <th>Accessories Store</th>
      <th>Adult Boutique</th>
      <th>African Restaurant</th>
      <th>Airport</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>Antique Shop</th>
      <th>Argentinian Restaurant</th>
      <th>...</th>
      <th>Vietnamese Restaurant</th>
      <th>Warehouse Store</th>
      <th>Weight Loss Center</th>
      <th>Whisky Bar</th>
      <th>Wine Bar</th>
      <th>Wine Shop</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
      <th>Zoo Exhibit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBERT EMBANKMENT</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ALBION SQUARE</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALLEYN PARK</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ARGYLE STREET</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.011494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ARTESIAN ROAD</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 319 columns</p>
</div>




```python
# top 5 venues valued most
num_top_venues = 5

for hood in london_grouped['Street']:
    print("----"+hood+"----")
    temp = london_grouped[london_grouped['Street'] == hood].T.reset_index()
    temp.columns = ['venue','freq']
    temp = temp.iloc[1:]
    temp['freq'] = temp['freq'].astype(float)
    temp = temp.round({'freq': 2})
    print(temp.sort_values('freq', ascending=False).reset_index(drop=True).head(num_top_venues))
    print('\n')
```

    ----ALBERT EMBANKMENT----
             venue  freq
    0   Restaurant  0.13
    1        Hotel  0.11
    2         Café  0.11
    3          Pub  0.08
    4  Pizza Place  0.04
    
    
    ----ALBION SQUARE----
             venue  freq
    0         Café  0.19
    1          Pub  0.08
    2          Bar  0.08
    3  Coffee Shop  0.08
    4   Restaurant  0.08
    
    
    ----ALLEYN PARK----
                   venue  freq
    0                Pub  0.50
    1  Indian Restaurant  0.17
    2             Lawyer  0.17
    3         Playground  0.17
    4                ATM  0.00
    
    
    ----ARGYLE STREET----
                     venue  freq
    0       Cha Chaan Teng  0.07
    1         Dessert Shop  0.06
    2         Noodle House  0.06
    3                Hotel  0.05
    4  Sporting Goods Shop  0.05
    
    
    ----ARTESIAN ROAD----
                  venue  freq
    0     Jewelry Store  0.12
    1  Sushi Restaurant  0.12
    2       Auto Garage  0.12
    3          Pharmacy  0.12
    4    Pilates Studio  0.12
    
    
    ----ASHCHURCH GROVE----
                          venue  freq
    0                       Pub  0.15
    1             Grocery Store  0.15
    2  Mediterranean Restaurant  0.07
    3               Coffee Shop  0.07
    4                    Bakery  0.07
    
    
    ----AYNHOE ROAD----
                            venue  freq
    0                         Pub  0.33
    1       Performing Arts Venue  0.17
    2                         Bar  0.17
    3                        Park  0.17
    4  Construction & Landscaping  0.17
    
    
    ----BEDALE STREET----
                  venue  freq
    0       Sports Club  0.25
    1    Sandwich Place  0.25
    2       Auto Garage  0.25
    3         Locksmith  0.25
    4  Pedestrian Plaza  0.00
    
    
    ----BEECHWOOD AVENUE----
                               venue  freq
    0          Performing Arts Venue   0.5
    1                     Restaurant   0.5
    2           Other Great Outdoors   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----BETTERTON STREET----
              venue  freq
    0       Theater  0.08
    1   Coffee Shop  0.07
    2  Dessert Shop  0.03
    3        Bakery  0.03
    4     Bookstore  0.03
    
    
    ----BETTRIDGE ROAD----
                               venue  freq
    0             Chinese Restaurant  0.33
    1                           Park  0.33
    2                  Grocery Store  0.33
    3                      Nightclub  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----BOURDON STREET----
                       venue  freq
    0                    Pub  0.17
    1         Sandwich Place  0.17
    2      Fish & Chips Shop  0.17
    3                   Park  0.17
    4  Performing Arts Venue  0.17
    
    
    ----BRADFIELD ROAD----
                 venue  freq
    0              Pub  0.12
    1   Discount Store  0.08
    2   Sandwich Place  0.08
    3  Auto Dealership  0.04
    4             Park  0.04
    
    
    ----BROMPTON PLACE----
                         venue  freq
    0                     Park   0.2
    1           Sandwich Place   0.2
    2        Convenience Store   0.2
    3                   Lounge   0.2
    4  Health & Beauty Service   0.2
    
    
    ----CAITHNESS ROAD----
                     venue  freq
    0        Grocery Store   0.4
    1  Fried Chicken Joint   0.2
    2             Bus Stop   0.2
    3    Fish & Chips Shop   0.2
    4                  ATM   0.0
    
    
    ----CAMBRIDGE TERRACE----
                    venue  freq
    0    Asian Restaurant  0.07
    1         Coffee Shop  0.07
    2  Italian Restaurant  0.05
    3              Lounge  0.05
    4  Chinese Restaurant  0.05
    
    
    ----CAMPDEN HILL ROAD----
             venue  freq
    0          Pub  0.10
    1       Hostel  0.04
    2  Yoga Studio  0.04
    3        Hotel  0.04
    4  Coffee Shop  0.04
    
    
    ----CANONBURY PARK SOUTH----
             venue  freq
    0          Pub  0.17
    1         Café  0.11
    2  Coffee Shop  0.11
    3     Bus Stop  0.09
    4         Park  0.06
    
    
    ----CARLISLE PLACE----
                    venue  freq
    0  Athletics & Sports   0.5
    1      Discount Store   0.5
    2                 ATM   0.0
    3   Outdoor Sculpture   0.0
    4                Park   0.0
    
    
    ----CARLTON GARDENS----
                     venue  freq
    0   Italian Restaurant  0.22
    1   Light Rail Station  0.05
    2                Hotel  0.05
    3         Dessert Shop  0.05
    4  Lebanese Restaurant  0.03
    
    
    ----CHALCOT SQUARE----
                    venue  freq
    0                Café  0.08
    1  Italian Restaurant  0.06
    2         Coffee Shop  0.06
    3                 Bar  0.06
    4                 Pub  0.06
    
    
    ----CHELSEA EMBANKMENT----
                    venue  freq
    0                 Pub  0.08
    1  Italian Restaurant  0.08
    2                Café  0.06
    3           Juice Bar  0.04
    4              Bakery  0.04
    
    
    ----CHENISTON GARDENS----
                    venue  freq
    0                Café  0.08
    1                 Pub  0.06
    2           Juice Bar  0.06
    3  Italian Restaurant  0.06
    4              Bakery  0.06
    
    
    ----CHESHAM MEWS----
                               venue  freq
    0                        Dog Run   1.0
    1                            ATM   0.0
    2          Performing Arts Venue   0.0
    3                           Park   0.0
    4  Paper / Office Supplies Store   0.0
    
    
    ----CHEVENING ROAD----
                               venue  freq
    0                            Pub  0.67
    1                           Lake  0.33
    2                            ATM  0.00
    3           Other Great Outdoors  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----COLLINGHAM ROAD----
                               venue  freq
    0         Furniture / Home Store  0.33
    1           Fast Food Restaurant  0.33
    2              Indian Restaurant  0.33
    3              Outdoor Sculpture  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----COULTER ROAD----
                               venue  freq
    0              Convenience Store   1.0
    1                            ATM   0.0
    2              Outdoor Sculpture   0.0
    3                           Park   0.0
    4  Paper / Office Supplies Store   0.0
    
    
    ----COURT LANE GARDENS----
                    venue  freq
    0                 Pub  0.15
    1       Grocery Store  0.15
    2                Café  0.08
    3  Italian Restaurant  0.08
    4              Bakery  0.08
    
    
    ----COURTHOPE ROAD----
                       venue  freq
    0          Metro Station  0.25
    1     Chinese Restaurant  0.25
    2                   Café  0.25
    3                  Hotel  0.25
    4  Outdoors & Recreation  0.00
    
    
    ----COURTNELL STREET----
                    venue  freq
    0                 Pub  0.07
    1  Italian Restaurant  0.07
    2              Bakery  0.06
    3      Clothing Store  0.04
    4          Restaurant  0.04
    
    
    ----CREDITON HILL----
                           venue  freq
    0                Coffee Shop  0.12
    1                       Café  0.05
    2  Middle Eastern Restaurant  0.05
    3            Thai Restaurant  0.05
    4                     Bakery  0.05
    
    
    ----DARTMOUTH PARK AVENUE----
                  venue  freq
    0               Pub  0.31
    1     Deli / Bodega  0.08
    2         Gastropub  0.08
    3  Tapas Restaurant  0.08
    4       Coffee Shop  0.08
    
    
    ----DE VERE GARDENS----
                    venue  freq
    0               Hotel  0.16
    1   Indian Restaurant  0.08
    2  Italian Restaurant  0.08
    3                 Pub  0.05
    4   French Restaurant  0.05
    
    
    ----DEEPDALE----
               venue  freq
    0   Soccer Field  0.17
    1    Supermarket  0.17
    2            Gym  0.17
    3  Grocery Store  0.17
    4    Pizza Place  0.17
    
    
    ----DEER PARK ROAD----
                   venue  freq
    0                Pub  0.17
    1      Event Service  0.17
    2        Bus Station  0.17
    3  Convenience Store  0.17
    4  Fish & Chips Shop  0.17
    
    
    ----DEVEREUX LANE----
                               venue  freq
    0                            Spa  0.67
    1              Convenience Store  0.33
    2                            ATM  0.00
    3           Other Great Outdoors  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----DEVONSHIRE MEWS WEST----
                   venue  freq
    0              Hotel  0.06
    1  French Restaurant  0.06
    2        Art Gallery  0.06
    3               Park  0.04
    4       Tennis Court  0.04
    
    
    ----DOVER STREET----
                venue  freq
    0     Art Gallery  0.08
    1           Hotel  0.07
    2  Clothing Store  0.06
    3     Men's Store  0.06
    4        Boutique  0.04
    
    
    ----DOWNSIDE CRESCENT----
                      venue  freq
    0           Sports Club  0.17
    1              Bus Stop  0.17
    2     Convenience Store  0.17
    3  Gym / Fitness Center  0.17
    4   Martial Arts School  0.17
    
    
    ----DUDLEY ROAD----
                      venue  freq
    0         Deli / Bodega  0.22
    1                 Diner  0.11
    2  Brazilian Restaurant  0.11
    3          Skating Rink  0.11
    4              Pharmacy  0.11
    
    
    ----DUKES LANE----
                               venue  freq
    0                            Pub   1.0
    1                            ATM   0.0
    2           Other Great Outdoors   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----EATON TERRACE MEWS----
                    venue  freq
    0                Café  0.08
    1  Italian Restaurant  0.08
    2               Hotel  0.07
    3         Coffee Shop  0.05
    4              Bakery  0.05
    
    
    ----ECCLESTON MEWS----
             venue  freq
    0        Hotel  0.08
    1         Café  0.08
    2   Restaurant  0.08
    3  Coffee Shop  0.05
    4    Gastropub  0.05
    
    
    ----ECCLESTON SQUARE----
                    venue  freq
    0               Hotel  0.11
    1                 Pub  0.07
    2  Italian Restaurant  0.07
    3              Bakery  0.05
    4                Café  0.05
    
    
    ----ELLERBY STREET----
                    venue  freq
    0                Café  0.18
    1  Athletics & Sports  0.09
    2                Park  0.09
    3        Tennis Court  0.05
    4         Coffee Shop  0.05
    
    
    ----ELVASTON PLACE----
                          venue  freq
    0                     Hotel  0.14
    1            Science Museum  0.09
    2                   Exhibit  0.08
    3  Mediterranean Restaurant  0.06
    4                      Café  0.05
    
    
    ----ENNISMORE GARDENS MEWS----
                    venue  freq
    0                Café  0.12
    1             Exhibit  0.10
    2      Science Museum  0.08
    3  Italian Restaurant  0.07
    4               Hotel  0.05
    
    
    ----ESSEX STREET----
                    venue  freq
    0      Sandwich Place  0.05
    1      Ice Cream Shop  0.04
    2  Mexican Restaurant  0.04
    3         Coffee Shop  0.04
    4                 Bar  0.03
    
    
    ----EVERSLEY CRESCENT----
                      venue  freq
    0      Asian Restaurant  0.09
    1          Soccer Field  0.09
    2          Burger Joint  0.09
    3  Fast Food Restaurant  0.09
    4        Sandwich Place  0.09
    
    
    ----EYNELLA ROAD----
                               venue  freq
    0                           Park  0.25
    1                  Grocery Store  0.25
    2                            Pub  0.25
    3                         Bakery  0.25
    4  Paper / Office Supplies Store  0.00
    
    
    ----FENCHURCH STREET----
                      venue  freq
    0                 Hotel  0.11
    1  Gym / Fitness Center  0.07
    2           Coffee Shop  0.05
    3            Restaurant  0.05
    4                Garden  0.04
    
    
    ----FLORAL STREET----
                         venue  freq
    0               Donut Shop  0.10
    1                      Pub  0.05
    2            Metro Station  0.05
    3  New American Restaurant  0.05
    4                      Spa  0.05
    
    
    ----FOUNTAYNE ROAD----
                 venue  freq
    0              Pub  0.15
    1             Café  0.12
    2              Bar  0.09
    3      Pizza Place  0.06
    4  Thai Restaurant  0.06
    
    
    ----FOURNIER STREET----
             venue  freq
    0  Coffee Shop  0.14
    1         Café  0.05
    2          Pub  0.03
    3   Food Truck  0.03
    4  Flea Market  0.03
    
    
    ----FRISTON STREET----
                      venue  freq
    0           Coffee Shop  0.16
    1                  Café  0.11
    2  Gym / Fitness Center  0.11
    3                  Park  0.05
    4       Thai Restaurant  0.05
    
    
    ----GERTRUDE STREET----
                            venue  freq
    0                 Pizza Place  0.13
    1                         Bar  0.13
    2        Fast Food Restaurant  0.13
    3                 Video Store  0.13
    4  Construction & Landscaping  0.07
    
    
    ----GLOUCESTER PLACE MEWS----
                venue  freq
    0           Hotel  0.08
    1     Coffee Shop  0.05
    2  Sandwich Place  0.05
    3          Bakery  0.04
    4  Clothing Store  0.04
    
    
    ----GORST ROAD----
                   venue  freq
    0               Café  0.28
    1                Pub  0.11
    2         Steakhouse  0.06
    3        Coffee Shop  0.06
    4  French Restaurant  0.06
    
    
    ----GRAHAM TERRACE----
                      venue  freq
    0           Supermarket  0.33
    1                  Café  0.33
    2     Indian Restaurant  0.33
    3                   ATM  0.00
    4  Other Great Outdoors  0.00
    
    
    ----GREAT TITCHFIELD STREET----
                     venue  freq
    0          Coffee Shop  0.15
    1          Pizza Place  0.05
    2    French Restaurant  0.05
    3         Burger Joint  0.05
    4  Japanese Restaurant  0.05
    
    
    ----HALLAM STREET----
                               venue  freq
    0        Health & Beauty Service  0.33
    1                           Park  0.33
    2     Construction & Landscaping  0.33
    3              Outdoor Sculpture  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----HALSEY STREET----
                     venue  freq
    0        Deli / Bodega  0.10
    1                 Café  0.08
    2          Pizza Place  0.08
    3   Mexican Restaurant  0.06
    4  Fried Chicken Joint  0.06
    
    
    ----HARLEY GARDENS----
               venue  freq
    0            Pub   0.2
    1     Restaurant   0.2
    2  Grocery Store   0.2
    3   Soccer Field   0.2
    4           Park   0.2
    
    
    ----HARMSWORTH WAY----
                venue  freq
    0     Golf Course   0.2
    1  Cricket Ground   0.2
    2             Pub   0.2
    3          Lawyer   0.2
    4            Café   0.2
    
    
    ----HARWOOD ROAD----
                      venue  freq
    0  Fast Food Restaurant  0.13
    1           Golf Course  0.13
    2    Miscellaneous Shop  0.07
    3          Liquor Store  0.07
    4        Clothing Store  0.07
    
    
    ----HATCHAM ROAD----
                    venue  freq
    0                Café  0.18
    1         Gas Station  0.09
    2                Park  0.09
    3  Chinese Restaurant  0.09
    4      Soccer Stadium  0.09
    
    
    ----HEATH DRIVE----
                               venue  freq
    0                          Diner   1.0
    1                            ATM   0.0
    2              Outdoor Sculpture   0.0
    3                           Park   0.0
    4  Paper / Office Supplies Store   0.0
    
    
    ----HEATH HURST ROAD----
                   venue  freq
    0               Café  0.13
    1                Pub  0.10
    2             Bakery  0.08
    3        Coffee Shop  0.08
    4  Indian Restaurant  0.05
    
    
    ----HENDERSON ROAD----
                               venue  freq
    0             Weight Loss Center   1.0
    1                            ATM   0.0
    2           Other Great Outdoors   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----HIGHBURY NEW PARK----
                    venue  freq
    0                Café  0.12
    1  Italian Restaurant  0.10
    2                 Pub  0.10
    3         Coffee Shop  0.07
    4         Music Venue  0.05
    
    
    ----HILLWAY----
                         venue  freq
    0               Sports Bar  0.25
    1                  Airport  0.25
    2                   Resort  0.25
    3                      Bar  0.25
    4  North Indian Restaurant  0.00
    
    
    ----HOLBORN CLOSE----
                   venue  freq
    0                Pub   0.2
    1     Breakfast Spot   0.2
    2  Convenience Store   0.2
    3  Indian Restaurant   0.2
    4     Sandwich Place   0.2
    
    
    ----HOLLAND PARK ROAD----
                            venue  freq
    0                        Café  0.10
    1                         Pub  0.08
    2                 Coffee Shop  0.07
    3                       Hotel  0.05
    4  Modern European Restaurant  0.03
    
    
    ----HYDE VALE----
                        venue  freq
    0                     Pub  0.14
    1                    Café  0.14
    2                  Garden  0.14
    3  Thrift / Vintage Store  0.07
    4           Grocery Store  0.07
    
    
    ----KILDARE GARDENS----
             venue  freq
    0        Hotel  0.09
    1          Pub  0.08
    2         Café  0.05
    3  Pizza Place  0.05
    4       Garden  0.05
    
    
    ----LEAMOUTH ROAD----
                      venue  freq
    0  Gym / Fitness Center  0.19
    1                  Park  0.12
    2           Coffee Shop  0.12
    3          Dance Studio  0.06
    4                  Café  0.06
    
    
    ----LEINSTER SQUARE----
                venue  freq
    0             Pub  0.12
    1     Coffee Shop  0.10
    2      Restaurant  0.06
    3     Supermarket  0.06
    4  Sandwich Place  0.04
    
    
    ----LILYVILLE ROAD----
                    venue  freq
    0                Café  0.13
    1  Italian Restaurant  0.10
    2              Bakery  0.08
    3                 Pub  0.08
    4   French Restaurant  0.05
    
    
    ----LOWER BELGRAVE STREET----
             venue  freq
    0  Coffee Shop  0.12
    1      Theater  0.06
    2        Hotel  0.06
    3     Bus Stop  0.06
    4       Bakery  0.06
    
    
    ----MARESFIELD GARDENS----
                      venue  freq
    0                  Café  0.13
    1                Bakery  0.06
    2  Gym / Fitness Center  0.06
    3    Italian Restaurant  0.06
    4         Burrito Place  0.03
    
    
    ----MARKHAM STREET----
                               venue  freq
    0                   Home Service   1.0
    1                            ATM   0.0
    2               Pedestrian Plaza   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----MORELLA ROAD----
               venue  freq
    0            Pub  0.25
    1         Bakery  0.12
    2      Wine Shop  0.12
    3  Train Station  0.12
    4           Café  0.12
    
    
    ----MOUNTVIEW CLOSE----
                               venue  freq
    0                            Pub  0.67
    1               Botanical Garden  0.33
    2                            ATM  0.00
    3           Other Great Outdoors  0.00
    4  Paper / Office Supplies Store  0.00
    
    
    ----NIGHTINGALE SQUARE----
                venue  freq
    0             Pub  0.36
    1  Farmers Market  0.09
    2      Steakhouse  0.09
    3         Brewery  0.09
    4     Supermarket  0.09
    
    
    ----NORRICE LEA----
                       venue  freq
    0                   Park  0.50
    1  Outdoors & Recreation  0.25
    2    Arts & Crafts Store  0.25
    3                    ATM  0.00
    4  Performing Arts Venue  0.00
    
    
    ----OAK HILL PARK MEWS----
                    venue  freq
    0                 Pub  0.12
    1                Café  0.10
    2              Bakery  0.10
    3  Italian Restaurant  0.08
    4      Ice Cream Shop  0.05
    
    
    ----OLD KENT ROAD----
                venue  freq
    0             Bar  0.12
    1           Hotel  0.12
    2      Public Art  0.12
    3            Café  0.12
    4  Breakfast Spot  0.12
    
    
    ----ONSLOW CRESCENT----
                     venue  freq
    0                Trail  0.25
    1  Monument / Landmark  0.25
    2       Baseball Field  0.25
    3                 Park  0.25
    4               Office  0.00
    
    
    ----ORMONDE GATE----
                     venue  freq
    0           Restaurant  0.05
    1                  Pub  0.05
    2            Bookstore  0.05
    3   Italian Restaurant  0.05
    4  Japanese Restaurant  0.05
    
    
    ----PADDINGTON STREET----
                    venue  freq
    0  Italian Restaurant  0.25
    1     Thai Restaurant  0.25
    2            Pharmacy  0.25
    3          Playground  0.25
    4                 ATM  0.00
    
    
    ----PALACE COURT----
                      venue  freq
    0                 Trail  0.33
    1         Design Studio  0.33
    2                Bakery  0.33
    3  Outdoor Supply Store  0.00
    4      Pedestrian Plaza  0.00
    
    
    ----PARKE ROAD----
                venue  freq
    0             Pub  0.25
    1            Lake  0.25
    2  Breakfast Spot  0.25
    3        Gym Pool  0.25
    4             ATM  0.00
    
    
    ----PARR'S WAY----
                       venue  freq
    0  General Entertainment  0.25
    1             Playground  0.25
    2         Discount Store  0.25
    3           Cocktail Bar  0.25
    4                    ATM  0.00
    
    
    ----PARSONS GATE MEWS----
                    venue  freq
    0                 Pub  0.08
    1  Italian Restaurant  0.08
    2                Park  0.08
    3       Grocery Store  0.08
    4         Coffee Shop  0.08
    
    
    ----PAVILION ROAD----
                               venue  freq
    0                  Grocery Store   0.5
    1                           Café   0.5
    2                            ATM   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----PEMBRIDGE CRESCENT----
                    venue  freq
    0                 Pub  0.08
    1      Clothing Store  0.04
    2  Italian Restaurant  0.04
    3  Persian Restaurant  0.03
    4            Boutique  0.03
    
    
    ----PEMBRIDGE MEWS----
                    venue  freq
    0                 Pub  0.05
    1  Italian Restaurant  0.05
    2          Restaurant  0.04
    3      Clothing Store  0.04
    4   Indian Restaurant  0.03
    
    
    ----PENNINGTON STREET----
                    venue  freq
    0           Nightclub  0.25
    1  Italian Restaurant  0.25
    2      Breakfast Spot  0.25
    3     Auto Dealership  0.25
    4   Outdoor Sculpture  0.00
    
    
    ----PICTON STREET----
                               venue  freq
    0                     Playground   1.0
    1                            ATM   0.0
    2           Other Great Outdoors   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----PORTLAND PLACE----
                    venue  freq
    0                 ATM  0.07
    1      Ice Cream Shop  0.07
    2        Noodle House  0.07
    3       Grocery Store  0.07
    4  Chinese Restaurant  0.07
    
    
    ----PRINCES SQUARE----
                    venue  freq
    0                 Pub  0.12
    1         Coffee Shop  0.08
    2    Sushi Restaurant  0.06
    3  Italian Restaurant  0.05
    4      Sandwich Place  0.05
    
    
    ----QUEEN ANNE STREET----
                               venue  freq
    0      Middle Eastern Restaurant  0.25
    1                           Park  0.25
    2           Fast Food Restaurant  0.25
    3                  Grocery Store  0.25
    4  Paper / Office Supplies Store  0.00
    
    
    ----QUEEN ANNES GROVE----
                    venue  freq
    0  Mexican Restaurant  0.12
    1                 Pub  0.12
    2     Bed & Breakfast  0.12
    3                Café  0.12
    4                Farm  0.12
    
    
    ----REDCLIFFE MEWS----
                    venue  freq
    0                 Pub  0.11
    1                Café  0.08
    2  Italian Restaurant  0.08
    3       Grocery Store  0.05
    4         Coffee Shop  0.05
    
    
    ----ROSE SQUARE----
                         venue  freq
    0                     Park  0.50
    1           Scenic Lookout  0.25
    2              Zoo Exhibit  0.25
    3  North Indian Restaurant  0.00
    4     Outdoor Supply Store  0.00
    
    
    ----RYECROFT STREET----
                      venue  freq
    0       Paintball Field  0.25
    1  Gym / Fitness Center  0.25
    2    Athletics & Sports  0.25
    3                   Gym  0.25
    4                   ATM  0.00
    
    
    ----SALUSBURY ROAD----
                            venue  freq
    0                 Coffee Shop  0.14
    1                         Bar  0.09
    2                         Gym  0.09
    3                  Restaurant  0.05
    4  Modern European Restaurant  0.05
    
    
    ----SOUTH END ROW----
                      venue  freq
    0             Juice Bar  0.08
    1                 Hotel  0.08
    2            Restaurant  0.06
    3     Indian Restaurant  0.06
    4  Gym / Fitness Center  0.04
    
    
    ----SOUTH PARADE----
                venue  freq
    0            Café  0.38
    1          Bakery  0.12
    2            Lake  0.12
    3  Farmers Market  0.12
    4           Hotel  0.12
    
    
    ----SPRIMONT PLACE----
             venue  freq
    0   Restaurant  0.06
    1  Coffee Shop  0.04
    2          Pub  0.04
    3        Hotel  0.04
    4    Bookstore  0.04
    
    
    ----ST AUBYNS AVENUE----
                        venue  freq
    0          Scenic Lookout  0.25
    1          Clothing Store  0.25
    2              Food Truck  0.25
    3  Argentinian Restaurant  0.25
    4                     ATM  0.00
    
    
    ----ST JOHN STREET----
                venue  freq
    0             Pub  0.09
    1      Restaurant  0.07
    2     Coffee Shop  0.07
    3  Sandwich Place  0.07
    4            Café  0.05
    
    
    ----ST STEPHENS GARDENS----
                               venue  freq
    0                  Grocery Store   1.0
    1                            ATM   0.0
    2        New American Restaurant   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----STRADELLA ROAD----
                               venue  freq
    0                       Dive Bar   0.5
    1                     Campground   0.5
    2           Other Great Outdoors   0.0
    3  Paper / Office Supplies Store   0.0
    4                         Palace   0.0
    
    
    ----SYDNEY STREET----
                    venue  freq
    0         Men's Store  0.33
    1  Italian Restaurant  0.33
    2                 Gym  0.33
    3   Outdoor Sculpture  0.00
    4                Park  0.00
    
    
    ----TEDWORTH SQUARE----
                     venue  freq
    0                 Café  0.06
    1                Plaza  0.05
    2                  Pub  0.05
    3               Bakery  0.05
    4  Japanese Restaurant  0.05
    
    
    ----THE LITTLE BOLTONS----
                    venue  freq
    0                 Pub  0.12
    1  Italian Restaurant  0.07
    2              Garden  0.07
    3               Hotel  0.07
    4                Café  0.07
    
    
    ----THE MARLOWES----
                           venue  freq
    0                        Pub  0.17
    1              Grocery Store  0.17
    2                Supermarket  0.06
    3             Clothing Store  0.06
    4  Middle Eastern Restaurant  0.06
    
    
    ----UPPER MONTAGU STREET----
             venue  freq
    0         Café  0.06
    1          Pub  0.06
    2  Coffee Shop  0.05
    3    Gastropub  0.05
    4        Hotel  0.05
    
    
    ----VIVIAN WAY----
                         venue  freq
    0                 Pharmacy  0.07
    1                    Hotel  0.07
    2            Grocery Store  0.04
    3  Health & Beauty Service  0.04
    4                    Motel  0.04
    
    
    ----WALDRON MEWS----
                    venue  freq
    0                Café  0.09
    1              Bakery  0.07
    2  Italian Restaurant  0.07
    3           Juice Bar  0.07
    4          Restaurant  0.05
    
    
    ----WARWICK LANE----
                       venue  freq
    0  Street Food Gathering  0.25
    1                   Pool  0.25
    2                    Gym  0.25
    3                   Park  0.25
    4                    ATM  0.00
    
    
    ----WELL ROAD----
                               venue  freq
    0              Convenience Store   1.0
    1                            ATM   0.0
    2              Outdoor Sculpture   0.0
    3                           Park   0.0
    4  Paper / Office Supplies Store   0.0
    
    
    ----WEST HILL PARK----
                               venue  freq
    0                            Pub  0.50
    1             English Restaurant  0.25
    2                    Pizza Place  0.25
    3  Paper / Office Supplies Store  0.00
    4                         Palace  0.00
    
    
    ----WEST SQUARE----
                    venue  freq
    0                 Pub  0.15
    1         Coffee Shop  0.15
    2    Stationery Store  0.08
    3  Mexican Restaurant  0.08
    4      Sandwich Place  0.08
    
    
    ----WEST TEMPLE SHEEN----
                           venue  freq
    0                        Pub   0.4
    1  Middle Eastern Restaurant   0.2
    2               Tennis Court   0.2
    3                       Park   0.2
    4                        ATM   0.0
    
    
    ----WHITE HART LANE----
                     venue  freq
    0                  Pub  0.21
    1                  Bar  0.14
    2               Lounge  0.07
    3  Sporting Goods Shop  0.07
    4          Supermarket  0.07
    
    
    ----WHITTAKER STREET----
                      venue  freq
    0        Clothing Store  0.13
    1                   Pub  0.09
    2             Bookstore  0.09
    3  Fast Food Restaurant  0.09
    4        Sandwich Place  0.09
    
    
    ----WOODYARD LANE----
                      venue  freq
    0                   Pub  0.50
    1                  Lake  0.25
    2  Fast Food Restaurant  0.25
    3                   ATM  0.00
    4     Outdoor Sculpture  0.00
    
    
    ----WYATT DRIVE----
                        venue  freq
    0              Donut Shop  0.29
    1      Mexican Restaurant  0.14
    2        Airport Terminal  0.14
    3          Discount Store  0.14
    4  Thrift / Vintage Store  0.14
    
    


### Most Common Venues on the Street


```python
# definde the function that returns the most common venue
def most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```


```python
num_top_venues = 10
indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Street']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create dataframe
common_venues_sorted = pd.DataFrame(columns=columns)
common_venues_sorted['Street'] = london_grouped['Street']

for ind in np.arange(london_grouped.shape[0]):
    common_venues_sorted.iloc[ind, 1:] = most_common_venues(london_grouped.iloc[ind, :], num_top_venues)
```


```python
print(common_venues_sorted.shape)
common_venues_sorted.head()
```

    (130, 11)





<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBERT EMBANKMENT</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Pub</td>
      <td>Park</td>
      <td>Art Gallery</td>
      <td>Plaza</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ALBION SQUARE</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bar</td>
      <td>Indian Restaurant</td>
      <td>Restaurant</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Seafood Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>New American Restaurant</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALLEYN PARK</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Playground</td>
      <td>Lawyer</td>
      <td>Farmers Market</td>
      <td>English Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ARGYLE STREET</td>
      <td>Cha Chaan Teng</td>
      <td>Noodle House</td>
      <td>Dessert Shop</td>
      <td>Hotel</td>
      <td>Sporting Goods Shop</td>
      <td>Hong Kong Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Cantonese Restaurant</td>
      <td>Bakery</td>
      <td>Toy / Game Store</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ARTESIAN ROAD</td>
      <td>Italian Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Indian Restaurant</td>
      <td>Jewelry Store</td>
      <td>Auto Garage</td>
      <td>Pizza Place</td>
      <td>Pilates Studio</td>
      <td>Pharmacy</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
  </tbody>
</table>
</div>



### K-Means Clustering
After inspecting the data we obtained, we then apply k-means clustering approach with 5 clusters to study the data.
#### 1. Create clustering dataframe


```python
k = 5
london_grouped_clustering = df_p.drop('Street', 1)
kmeans = KMeans(n_clusters=k, random_state=0).fit(london_grouped_clustering)
kmeans.labels_
```




    array([3, 3, 3, 3, 1, 4, 1, 2, 1, 4, 4, 4, 2, 0, 0, 4, 0, 4, 4, 3, 2, 3,
           1, 3, 2, 0, 3, 3, 1, 1, 4, 4, 0, 4, 1, 3, 4, 0, 1, 4, 3, 4, 1, 2,
           3, 2, 4, 0, 1, 4, 2, 0, 1, 0, 3, 1, 4, 1, 4, 4, 3, 1, 0, 3, 0, 4,
           0, 4, 3, 0, 0, 4, 4, 0, 4, 1, 1, 0, 0, 2, 3, 1, 0, 3, 2, 3, 3, 4,
           2, 0, 3, 3, 1, 0, 1, 2, 4, 0, 4, 2, 4, 3, 0, 2, 0, 3, 3, 3, 0, 2,
           4, 3, 0, 4, 2, 4, 3, 0, 4, 2, 4, 0, 1, 3, 0, 2, 2, 0, 0, 1, 0, 0,
           4, 0, 3, 4, 4, 4, 3, 1, 3, 0, 3, 4], dtype=int32)




```python
# check if using the correct dataframe
london_grouped_clustering=df_p
london_grouped_clustering.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>Avg_Price</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>ALBERT EMBANKMENT</td>
      <td>2.283829e+06</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>3</td>
    </tr>
    <tr>
      <th>186</th>
      <td>ALBION SQUARE</td>
      <td>2.292500e+06</td>
      <td>-41.273758</td>
      <td>173.289393</td>
      <td>3</td>
    </tr>
    <tr>
      <th>258</th>
      <td>ALLEYN PARK</td>
      <td>2.283095e+06</td>
      <td>51.492742</td>
      <td>-0.371967</td>
      <td>3</td>
    </tr>
    <tr>
      <th>455</th>
      <td>ARGYLE STREET</td>
      <td>2.300000e+06</td>
      <td>22.319036</td>
      <td>114.167841</td>
      <td>3</td>
    </tr>
    <tr>
      <th>499</th>
      <td>ARTESIAN ROAD</td>
      <td>2.462500e+06</td>
      <td>41.725304</td>
      <td>-88.205529</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
common_venues_sorted.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Cluster Labels</th>
      <th>Street</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>ALBERT EMBANKMENT</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Pub</td>
      <td>Park</td>
      <td>Art Gallery</td>
      <td>Plaza</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>ALBION SQUARE</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bar</td>
      <td>Indian Restaurant</td>
      <td>Restaurant</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Seafood Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>New American Restaurant</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>ALLEYN PARK</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Playground</td>
      <td>Lawyer</td>
      <td>Farmers Market</td>
      <td>English Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>ARGYLE STREET</td>
      <td>Cha Chaan Teng</td>
      <td>Noodle House</td>
      <td>Dessert Shop</td>
      <td>Hotel</td>
      <td>Sporting Goods Shop</td>
      <td>Hong Kong Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Cantonese Restaurant</td>
      <td>Bakery</td>
      <td>Toy / Game Store</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>ARTESIAN ROAD</td>
      <td>Italian Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Indian Restaurant</td>
      <td>Jewelry Store</td>
      <td>Auto Garage</td>
      <td>Pizza Place</td>
      <td>Pilates Studio</td>
      <td>Pharmacy</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
  </tbody>
</table>
</div>




```python
# merge toronto_grouped with toronto_data to add latitude/longitude for each neighborhood
common_venues_sorted = common_venues_sorted.drop(columns = 'Cluster Labels')
```


```python
london_grouped_clustering = london_grouped_clustering.join(common_venues_sorted.set_index('Street'), on='Street')
```


```python
london_grouped_clustering.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Street</th>
      <th>Avg_Price</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>ALBERT EMBANKMENT</td>
      <td>2.283829e+06</td>
      <td>51.493213</td>
      <td>-0.121361</td>
      <td>3</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Pub</td>
      <td>Park</td>
      <td>Art Gallery</td>
      <td>Plaza</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <th>186</th>
      <td>ALBION SQUARE</td>
      <td>2.292500e+06</td>
      <td>-41.273758</td>
      <td>173.289393</td>
      <td>3</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bar</td>
      <td>Indian Restaurant</td>
      <td>Restaurant</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Seafood Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>New American Restaurant</td>
    </tr>
    <tr>
      <th>258</th>
      <td>ALLEYN PARK</td>
      <td>2.283095e+06</td>
      <td>51.492742</td>
      <td>-0.371967</td>
      <td>3</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Playground</td>
      <td>Lawyer</td>
      <td>Farmers Market</td>
      <td>English Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
    </tr>
    <tr>
      <th>455</th>
      <td>ARGYLE STREET</td>
      <td>2.300000e+06</td>
      <td>22.319036</td>
      <td>114.167841</td>
      <td>3</td>
      <td>Cha Chaan Teng</td>
      <td>Noodle House</td>
      <td>Dessert Shop</td>
      <td>Hotel</td>
      <td>Sporting Goods Shop</td>
      <td>Hong Kong Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Cantonese Restaurant</td>
      <td>Bakery</td>
      <td>Toy / Game Store</td>
    </tr>
    <tr>
      <th>499</th>
      <td>ARTESIAN ROAD</td>
      <td>2.462500e+06</td>
      <td>41.725304</td>
      <td>-88.205529</td>
      <td>1</td>
      <td>Italian Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Indian Restaurant</td>
      <td>Jewelry Store</td>
      <td>Auto Garage</td>
      <td>Pizza Place</td>
      <td>Pilates Studio</td>
      <td>Pharmacy</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
  </tbody>
</table>
</div>



#### 2. Create map of clustering


```python
map_clusters = folium.Map(location=[latitude, longitude], zoom_start=11)

# set color scheme for the clusters
x = np.arange(k)
ys = [i+x+(i*x)**2 for i in range(k)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(london_grouped_clustering['Latitude'], 
                                  london_grouped_clustering['Longitude'], 
                                  london_grouped_clustering['Street'], 
                                  london_grouped_clustering['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width: 100%;"><div style="position: relative; width: 100%; height: 0; padding-bottom: 60%;"><span style="color: #565656;">Make this Notebook Trusted to load map: File -&gt; Trust Notebook</span>&lt;iframe allowfullscreen="" data-html="PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NScsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNTEuNTA3MzIxOSwtMC4xMjc2NDc0XSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHpvb206IDExLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbWF4Qm91bmRzOiBib3VuZHMsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBsYXllcnM6IFtdLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgd29ybGRDb3B5SnVtcDogZmFsc2UsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBjcnM6IEwuQ1JTLkVQU0czODU3CiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgdGlsZV9sYXllcl8xODk5YTUwOTliZjQ0ZjVjYWU2OTAwNjQ3MTY4MjdiZCA9IEwudGlsZUxheWVyKAogICAgICAgICAgICAgICAgJ2h0dHBzOi8ve3N9LnRpbGUub3BlbnN0cmVldG1hcC5vcmcve3p9L3t4fS97eX0ucG5nJywKICAgICAgICAgICAgICAgIHsKICAiYXR0cmlidXRpb24iOiBudWxsLAogICJkZXRlY3RSZXRpbmEiOiBmYWxzZSwKICAibWF4Wm9vbSI6IDE4LAogICJtaW5ab29tIjogMSwKICAibm9XcmFwIjogZmFsc2UsCiAgInN1YmRvbWFpbnMiOiAiYWJjIgp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTkxODIwN2UwZTVhNDAxMTgwOTE3YmI4YWFiMTVkYmEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTMyMTI2LC0wLjEyMTM2MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85Yjk5NWU1ZDg4NmQ0MDFjYWY5OGM4ZjkyODAzZTc1NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNzhjYjE1MzllNzA0MmY1OWViYmJiNDg0NmZlN2UzMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMDc4Y2IxNTM5ZTcwNDJmNTllYmJiYjQ4NDZmZTdlMzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFMQkVSVCBFTUJBTktNRU5UIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWI5OTVlNWQ4ODZkNDAxY2FmOThjOGY5MjgwM2U3NTQuc2V0Q29udGVudChodG1sXzA3OGNiMTUzOWU3MDQyZjU5ZWJiYmI0ODQ2ZmU3ZTMzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzk5MTgyMDdlMGU1YTQwMTE4MDkxN2JiOGFhYjE1ZGJhLmJpbmRQb3B1cChwb3B1cF85Yjk5NWU1ZDg4NmQ0MDFjYWY5OGM4ZjkyODAzZTc1NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85ZDRhNTlkZjY2N2E0MmVmYjgzMTg2ZjVkZWQxYzM2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy00MS4yNzM3NTc1NSwxNzMuMjg5MzkzMjM5MTAzNTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzY1YmU1OGViMGU1NDE3OGE5NDAxZDQzNDE5NjE5MTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWMwMDFhNDZkNWE1NDViM2JhNGUxZjFkNWFlM2MwZDIgPSAkKCc8ZGl2IGlkPSJodG1sXzljMDAxYTQ2ZDVhNTQ1YjNiYTRlMWYxZDVhZTNjMGQyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BTEJJT04gU1FVQVJFIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzY1YmU1OGViMGU1NDE3OGE5NDAxZDQzNDE5NjE5MTkuc2V0Q29udGVudChodG1sXzljMDAxYTQ2ZDVhNTQ1YjNiYTRlMWYxZDVhZTNjMGQyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlkNGE1OWRmNjY3YTQyZWZiODMxODZmNWRlZDFjMzZjLmJpbmRQb3B1cChwb3B1cF8zNjViZTU4ZWIwZTU0MTc4YTk0MDFkNDM0MTk2MTkxOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YWY5MWMzY2YxYjA0YzMyYWFiZjdlNmU4NzkzY2Y5MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5Mjc0MTYsLTAuMzcxOTY3NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jNDBlMzRlYWJiZWQ0ZThhOTUxNDI0MWM0MDY0NmIwMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NzJiOTNiOGU5Y2Q0N2NhYTg5YWIwZGNkNjNlZjA3ZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDcyYjkzYjhlOWNkNDdjYWE4OWFiMGRjZDYzZWYwN2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFMTEVZTiBQQVJLIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzQwZTM0ZWFiYmVkNGU4YTk1MTQyNDFjNDA2NDZiMDIuc2V0Q29udGVudChodG1sXzQ3MmI5M2I4ZTljZDQ3Y2FhODlhYjBkY2Q2M2VmMDdlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVhZjkxYzNjZjFiMDRjMzJhYWJmN2U2ZTg3OTNjZjkxLmJpbmRQb3B1cChwb3B1cF9jNDBlMzRlYWJiZWQ0ZThhOTUxNDI0MWM0MDY0NmIwMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hNjllZjIwMWU4N2I0YWJiOTIwN2FlZjdiOWJkZjk0ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzIyLjMxOTAzNjIsMTE0LjE2Nzg0MTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWI0Mjc3ZGU1MjBmNDkwOWE0YjBmMDMwMmM1ZjcyMTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmE2NDExZjE2ZjhiNGU5OTk1NjVjZDMzYzY0ODI4ODggPSAkKCc8ZGl2IGlkPSJodG1sXzZhNjQxMWYxNmY4YjRlOTk5NTY1Y2QzM2M2NDgyODg4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BUkdZTEUgU1RSRUVUIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWI0Mjc3ZGU1MjBmNDkwOWE0YjBmMDMwMmM1ZjcyMTQuc2V0Q29udGVudChodG1sXzZhNjQxMWYxNmY4YjRlOTk5NTY1Y2QzM2M2NDgyODg4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E2OWVmMjAxZTg3YjRhYmI5MjA3YWVmN2I5YmRmOTRlLmJpbmRQb3B1cChwb3B1cF9hYjQyNzdkZTUyMGY0OTA5YTRiMGYwMzAyYzVmNzIxNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zODhhYWY2OGMxZjA0YTY0YTZkYmE1MDc2ODY0ZWY0ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjcyNTMwNDEsLTg4LjIwNTUyOTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzYyYjZhM2JkYzI2NDI3NWEwODUyMzUwZmNmODY2YmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWY1NWQzMDdmNDk5NGI4OThiMjZlNjkzZjAxYmE2YTIgPSAkKCc8ZGl2IGlkPSJodG1sXzFmNTVkMzA3ZjQ5OTRiODk4YjI2ZTY5M2YwMWJhNmEyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BUlRFU0lBTiBST0FEIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzYyYjZhM2JkYzI2NDI3NWEwODUyMzUwZmNmODY2YmYuc2V0Q29udGVudChodG1sXzFmNTVkMzA3ZjQ5OTRiODk4YjI2ZTY5M2YwMWJhNmEyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM4OGFhZjY4YzFmMDRhNjRhNmRiYTUwNzY4NjRlZjRmLmJpbmRQb3B1cChwb3B1cF8zNjJiNmEzYmRjMjY0Mjc1YTA4NTIzNTBmY2Y4NjZiZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xNDUzMjdiNjY2ZTk0NzE1YjQ2NmM5OTI1YjM2NjJkYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUwMTEyMDgsLTAuMjQxNDEwOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNTgyNTJiNDRiYjg0ZGQ2YjM5ZjFiYmYyYmE1MzQ5ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MjI4MzNkM2JkZmI0YWQ4YjQ2NjI5YmI5NTA1OWM1OSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDIyODMzZDNiZGZiNGFkOGI0NjYyOWJiOTUwNTljNTkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFTSENIVVJDSCBHUk9WRSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U1ODI1MmI0NGJiODRkZDZiMzlmMWJiZjJiYTUzNDlkLnNldENvbnRlbnQoaHRtbF80MjI4MzNkM2JkZmI0YWQ4YjQ2NjI5YmI5NTA1OWM1OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xNDUzMjdiNjY2ZTk0NzE1YjQ2NmM5OTI1YjM2NjJkYy5iaW5kUG9wdXAocG9wdXBfZTU4MjUyYjQ0YmI4NGRkNmIzOWYxYmJmMmJhNTM0OWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmJkYWQwZTA4NzQxNGZmYjgyNjdhYTNjMTFiMWZjMmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi4wMTg4MDY0LC0xLjMwNTk0NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTBlZmFkZDE0ZjBlNDVkNjk2YjI1YTM5OThhNTQxZWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmY0NDVhYWM0ODM4NDU4OWFiZWMyYTM2NDY5ZTBjMjcgPSAkKCc8ZGl2IGlkPSJodG1sX2ZmNDQ1YWFjNDgzODQ1ODlhYmVjMmEzNjQ2OWUwYzI3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BWU5IT0UgUk9BRCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUwZWZhZGQxNGYwZTQ1ZDY5NmIyNWEzOTk4YTU0MWVhLnNldENvbnRlbnQoaHRtbF9mZjQ0NWFhYzQ4Mzg0NTg5YWJlYzJhMzY0NjllMGMyNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yYmRhZDBlMDg3NDE0ZmZiODI2N2FhM2MxMWIxZmMyZC5iaW5kUG9wdXAocG9wdXBfNTBlZmFkZDE0ZjBlNDVkNjk2YjI1YTM5OThhNTQxZWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmQ1MTBhNjY1Y2JiNDM1ZDhmMzJmYjI1M2YyN2M1MWEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1NC44MTUzODY2LC0xLjQ1NDg5NTFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzAwYjVlYiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDNlZTNkODY1MjMxNDczY2FkOTg2YWFiNjMwNmMwYzggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODNiOWRiZGU0NWY1NGU4ZTk0Yzc5MmVhMGY1MTY4NjggPSAkKCc8ZGl2IGlkPSJodG1sXzgzYjlkYmRlNDVmNTRlOGU5NGM3OTJlYTBmNTE2ODY4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CRURBTEUgU1RSRUVUIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDNlZTNkODY1MjMxNDczY2FkOTg2YWFiNjMwNmMwYzguc2V0Q29udGVudChodG1sXzgzYjlkYmRlNDVmNTRlOGU5NGM3OTJlYTBmNTE2ODY4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JkNTEwYTY2NWNiYjQzNWQ4ZjMyZmIyNTNmMjdjNTFhLmJpbmRQb3B1cChwb3B1cF9kM2VlM2Q4NjUyMzE0NzNjYWQ5ODZhYWI2MzA2YzBjOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mOTRmZTA5OGY0N2I0YzdmYTRmOGEwYWZmNmYwOWM0YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzU0Ljg1NTUyNzg1LC02LjI5MDU4MDY5MDExOTA1Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMjBkM2ExOTlkY2M0MGVmOTljNmUzMmJhMDM0NmI3YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNzU1OTE4NmE1Y2I0ZDViOWEyZDMxYmRjZWE0NjI2MSA9ICQoJzxkaXYgaWQ9Imh0bWxfYzc1NTkxODZhNWNiNGQ1YjlhMmQzMWJkY2VhNDYyNjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJFRUNIV09PRCBBVkVOVUUgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMjBkM2ExOTlkY2M0MGVmOTljNmUzMmJhMDM0NmI3YS5zZXRDb250ZW50KGh0bWxfYzc1NTkxODZhNWNiNGQ1YjlhMmQzMWJkY2VhNDYyNjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjk0ZmUwOThmNDdiNGM3ZmE0ZjhhMGFmZjZmMDljNGEuYmluZFBvcHVwKHBvcHVwX2UyMGQzYTE5OWRjYzQwZWY5OWM2ZTMyYmEwMzQ2YjdhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I1MDU0M2I4ZDJjNjQzMmZhMjZmMDAxYmMzN2Q2MTQyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTE0OTU3NiwtMC4xMjM4OTQ2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzFmMjY1YjczNzMwMTRjNzNhODE2MDZhY2ZmODIyZThiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2IzOGRiODZjZGIxOTQyMGJiNzZmNDgwN2FlNzNlYTc3ID0gJCgnPGRpdiBpZD0iaHRtbF9iMzhkYjg2Y2RiMTk0MjBiYjc2ZjQ4MDdhZTczZWE3NyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QkVUVEVSVE9OIFNUUkVFVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFmMjY1YjczNzMwMTRjNzNhODE2MDZhY2ZmODIyZThiLnNldENvbnRlbnQoaHRtbF9iMzhkYjg2Y2RiMTk0MjBiYjc2ZjQ4MDdhZTczZWE3Nyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iNTA1NDNiOGQyYzY0MzJmYTI2ZjAwMWJjMzdkNjE0Mi5iaW5kUG9wdXAocG9wdXBfMWYyNjViNzM3MzAxNGM3M2E4MTYwNmFjZmY4MjJlOGIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDYwMWUyNmE2OTVjNDAwYTk2Nzg4YzU1NDFkMTU5MDUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Ny4wMzIwMzc2LC0yLjE0NzY5ODVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTEzNWZlOTc4ZWQyNDVlYjgwYjY3YjZjOTRkOTQxMWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTBhNWVkNjEyMDg2NDNmOTgxYzg2ZGFlMjc2MjgxNzggPSAkKCc8ZGl2IGlkPSJodG1sXzEwYTVlZDYxMjA4NjQzZjk4MWM4NmRhZTI3NjI4MTc4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CRVRUUklER0UgUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkxMzVmZTk3OGVkMjQ1ZWI4MGI2N2I2Yzk0ZDk0MTFhLnNldENvbnRlbnQoaHRtbF8xMGE1ZWQ2MTIwODY0M2Y5ODFjODZkYWUyNzYyODE3OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kNjAxZTI2YTY5NWM0MDBhOTY3ODhjNTU0MWQxNTkwNS5iaW5kUG9wdXAocG9wdXBfOTEzNWZlOTc4ZWQyNDVlYjgwYjY3YjZjOTRkOTQxMWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTk0OGE5ZTdjNTYyNDUwOWFhNTM4MTE4ZmY1N2NiNzcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0NC45MDIwNDYsLTY5LjA3NjMzM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wYjA0MWE3M2UwMTk0YzZlYmE3ZTZiZDhlYzUxYTA5MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNjQ3MGQ0NmZhNzc0M2QwYmQxYTIyY2MzYTY3ODI0NiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTY0NzBkNDZmYTc3NDNkMGJkMWEyMmNjM2E2NzgyNDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJJTExJTkcgUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBiMDQxYTczZTAxOTRjNmViYTdlNmJkOGVjNTFhMDkwLnNldENvbnRlbnQoaHRtbF8xNjQ3MGQ0NmZhNzc0M2QwYmQxYTIyY2MzYTY3ODI0Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hOTQ4YTllN2M1NjI0NTA5YWE1MzgxMThmZjU3Y2I3Ny5iaW5kUG9wdXAocG9wdXBfMGIwNDFhNzNlMDE5NGM2ZWJhN2U2YmQ4ZWM1MWEwOTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTFmMzJiMzExNTJjNDA2ZWE0YmU4NTU5NDUzMTZlMTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1OC4zOTE4MjUzLC0xMjQuODI1Njc4OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMjNiNzIwNjEzM2E0MmE1Yjc2NjA1NGIwYzM5YTI5MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMDBkYjIwZWNlNzI0MzMzOWYxM2QyZDgwMWFkNzdjNSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTAwZGIyMGVjZTcyNDMzMzlmMTNkMmQ4MDFhZDc3YzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJMRU5IRUlNIENSRVNDRU5UIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjIzYjcyMDYxMzNhNDJhNWI3NjYwNTRiMGMzOWEyOTEuc2V0Q29udGVudChodG1sX2UwMGRiMjBlY2U3MjQzMzM5ZjEzZDJkODAxYWQ3N2M1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzExZjMyYjMxMTUyYzQwNmVhNGJlODU1OTQ1MzE2ZTE4LmJpbmRQb3B1cChwb3B1cF8yMjNiNzIwNjEzM2E0MmE1Yjc2NjA1NGIwYzM5YTI5MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mZTJiYzM4MjEyYTI0ZTQ0OGZjNmM5MzZhNjcwY2I2MCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjQ4Nzk0MjIsLTIuMjE1MTg0N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jZDE5NmMxODM4ZTM0NDk2YTAyNzM5YWNjZGU3M2ZkMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80YTY3YTY3NmNhZDI0ZDA1OTY2ZjIzMTY0Yzg1YzRjOSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGE2N2E2NzZjYWQyNGQwNTk2NmYyMzE2NGM4NWM0YzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJPVVJET04gU1RSRUVUIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfY2QxOTZjMTgzOGUzNDQ5NmEwMjczOWFjY2RlNzNmZDEuc2V0Q29udGVudChodG1sXzRhNjdhNjc2Y2FkMjRkMDU5NjZmMjMxNjRjODVjNGM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZlMmJjMzgyMTJhMjRlNDQ4ZmM2YzkzNmE2NzBjYjYwLmJpbmRQb3B1cChwb3B1cF9jZDE5NmMxODM4ZTM0NDk2YTAyNzM5YWNjZGU3M2ZkMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mYjk2NTQyM2IxMmU0MjgxOTRiYjdiNDFlZjE5Y2Q0MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjQwMzg5NywtMS40OTg0MjA2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdhYjE5OGFkMjZhNzQ0OGU5NDhhY2Y2OTdmNWY3Y2JjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA2NzUzNTI5NjdlYTQyYjBhNzVmNjM4YjM1MjBkYjA4ID0gJCgnPGRpdiBpZD0iaHRtbF8wNjc1MzUyOTY3ZWE0MmIwYTc1ZjYzOGIzNTIwZGIwOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QlJBREZJRUxEIFJPQUQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YWIxOThhZDI2YTc0NDhlOTQ4YWNmNjk3ZjVmN2NiYy5zZXRDb250ZW50KGh0bWxfMDY3NTM1Mjk2N2VhNDJiMGE3NWY2MzhiMzUyMGRiMDgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmI5NjU0MjNiMTJlNDI4MTk0YmI3YjQxZWYxOWNkNDMuYmluZFBvcHVwKHBvcHVwXzdhYjE5OGFkMjZhNzQ0OGU5NDhhY2Y2OTdmNWY3Y2JjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U3MGY3ZmM5MzllNDQwNTliYjhiNmI2NmIyMTkxNWNlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuODM5MDY0LC03My4zOTU5NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDgzMmFmNmEwZjE3NDQ5MWJjMWJhZDJjZTdlY2QzNjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmRjMWYyYjEzMzUxNDAxNTgzOTJmMWNmNzA2Yzk5ZmYgPSAkKCc8ZGl2IGlkPSJodG1sXzZkYzFmMmIxMzM1MTQwMTU4MzkyZjFjZjcwNmM5OWZmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CUk9NUFRPTiBQTEFDRSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ4MzJhZjZhMGYxNzQ0OTFiYzFiYWQyY2U3ZWNkMzY2LnNldENvbnRlbnQoaHRtbF82ZGMxZjJiMTMzNTE0MDE1ODM5MmYxY2Y3MDZjOTlmZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNzBmN2ZjOTM5ZTQ0MDU5YmI4YjZiNjZiMjE5MTVjZS5iaW5kUG9wdXAocG9wdXBfNDgzMmFmNmEwZjE3NDQ5MWJjMWJhZDJjZTdlY2QzNjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDAzNDBiZjAwYzU4NDRmZmFjZDA0YjY4OGU0OTBiZTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40MTcwMjI3LC0wLjE1MDkyODZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTQzNGM2NjQ3MTcyNGNlMjgxMDAyOWVkNGUxNjc0ZmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTdlM2NiZjA2MzY4NDNlMmE0NGY4MmViNzJlYTM1ODIgPSAkKCc8ZGl2IGlkPSJodG1sXzk3ZTNjYmYwNjM2ODQzZTJhNDRmODJlYjcyZWEzNTgyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQUlUSE5FU1MgUk9BRCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE0MzRjNjY0NzE3MjRjZTI4MTAwMjllZDRlMTY3NGZhLnNldENvbnRlbnQoaHRtbF85N2UzY2JmMDYzNjg0M2UyYTQ0ZjgyZWI3MmVhMzU4Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMDM0MGJmMDBjNTg0NGZmYWNkMDRiNjg4ZTQ5MGJlMy5iaW5kUG9wdXAocG9wdXBfMTQzNGM2NjQ3MTcyNGNlMjgxMDAyOWVkNGUxNjc0ZmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjk3Mzg2ZGFhNjA1NGVhNTliNjk5NjA4Y2I4ZDQzYzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFstNDEuMjk3MzE1MiwxNzQuNzgxODY1Nl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OWRiYWZmYjVlNzk0MjgzYWE3NzljY2I1Njg4ZTFiZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83MjFmNTM2ODIwZjE0OThlOGNmYzUzY2NmMTgzMTA0MSA9ICQoJzxkaXYgaWQ9Imh0bWxfNzIxZjUzNjgyMGYxNDk4ZThjZmM1M2NjZjE4MzEwNDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBTUJSSURHRSBURVJSQUNFIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTlkYmFmZmI1ZTc5NDI4M2FhNzc5Y2NiNTY4OGUxYmUuc2V0Q29udGVudChodG1sXzcyMWY1MzY4MjBmMTQ5OGU4Y2ZjNTNjY2YxODMxMDQxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY5NzM4NmRhYTYwNTRlYTU5YjY5OTYwOGNiOGQ0M2MyLmJpbmRQb3B1cChwb3B1cF85OWRiYWZmYjVlNzk0MjgzYWE3NzljY2I1Njg4ZTFiZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83ZTE0Nzg4NTQ1NWI0NmVmOTAxMWExOTBmOTgxOTA3MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjkwMTM1ODY1MDAwMDAwNiwtODAuNjMzNDc2NzYwMDU4MjJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWRhNGEwZWM3NTg2NGYzNzhiNzQ5YzAyOGM4MzUxMmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzBkZTIwYWRjYjUyNDlkMzk5ZDE4NGI3ODhmNWQyYmUgPSAkKCc8ZGl2IGlkPSJodG1sXzcwZGUyMGFkY2I1MjQ5ZDM5OWQxODRiNzg4ZjVkMmJlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DQU1ERU4gU1FVQVJFIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWRhNGEwZWM3NTg2NGYzNzhiNzQ5YzAyOGM4MzUxMmYuc2V0Q29udGVudChodG1sXzcwZGUyMGFkY2I1MjQ5ZDM5OWQxODRiNzg4ZjVkMmJlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdlMTQ3ODg1NDU1YjQ2ZWY5MDExYTE5MGY5ODE5MDczLmJpbmRQb3B1cChwb3B1cF85ZGE0YTBlYzc1ODY0ZjM3OGI3NDljMDI4YzgzNTEyZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNjA5MzgzMTBkNTQ0MTUxYTY5MmQ5NzljZmFjNjg5MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUwNjM1MTUsLTAuMTk4ODUzMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YzkzZTk3MThhNDk0ZTBmYTUzNGJiNjI2ODFlMmMyNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iYzhhNGRiODQ2ZmY0NDU4OTY1M2NlOTFjZDAzOGJkMyA9ICQoJzxkaXYgaWQ9Imh0bWxfYmM4YTRkYjg0NmZmNDQ1ODk2NTNjZTkxY2QwMzhiZDMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBTVBERU4gSElMTCBST0FEIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2M5M2U5NzE4YTQ5NGUwZmE1MzRiYjYyNjgxZTJjMjUuc2V0Q29udGVudChodG1sX2JjOGE0ZGI4NDZmZjQ0NTg5NjUzY2U5MWNkMDM4YmQzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M2MDkzODMxMGQ1NDQxNTFhNjkyZDk3OWNmYWM2ODkxLmJpbmRQb3B1cChwb3B1cF83YzkzZTk3MThhNDk0ZTBmYTUzNGJiNjI2ODFlMmMyNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kYTY0NTg3YWZmNzU0NDRmOGQ0MzU2ZTAyNWMwMWRjOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjU0NTI5NzMsLTAuMDk0NDM0OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83OWE5OWMzYWUwZDQ0OTcxYmQ5NjViYWU5ODQ5ZjQ1NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82OWYxZTE3ZmFjMDM0ZTk3YTdiYTc4MWJhNmY5MjIzOSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjlmMWUxN2ZhYzAzNGU5N2E3YmE3ODFiYTZmOTIyMzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBTk9OQlVSWSBQQVJLIFNPVVRIIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzlhOTljM2FlMGQ0NDk3MWJkOTY1YmFlOTg0OWY0NTQuc2V0Q29udGVudChodG1sXzY5ZjFlMTdmYWMwMzRlOTdhN2JhNzgxYmE2ZjkyMjM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RhNjQ1ODdhZmY3NTQ0NGY4ZDQzNTZlMDI1YzAxZGM5LmJpbmRQb3B1cChwb3B1cF83OWE5OWMzYWUwZDQ0OTcxYmQ5NjViYWU5ODQ5ZjQ1NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lNWU2MWQ5Yjg1MWI0YTE0OWY2YjE3NjU5MzEwY2RiNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM1LjA1MzEyNDg1LC04MS45NDE1Njk4MDE4MDA1MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83OTcwYWVjOWYzMGM0OTNhYWVhNTdmMTI5NzY3N2Y3YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMGMxMWI5N2MwYWI0ZjYxYjkwNGJlOWNhNjEyZjhmMSA9ICQoJzxkaXYgaWQ9Imh0bWxfMzBjMTFiOTdjMGFiNGY2MWI5MDRiZTljYTYxMmY4ZjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBUkxJU0xFIFBMQUNFIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzk3MGFlYzlmMzBjNDkzYWFlYTU3ZjEyOTc2NzdmN2Muc2V0Q29udGVudChodG1sXzMwYzExYjk3YzBhYjRmNjFiOTA0YmU5Y2E2MTJmOGYxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U1ZTYxZDliODUxYjRhMTQ5ZjZiMTc2NTkzMTBjZGI1LmJpbmRQb3B1cChwb3B1cF83OTcwYWVjOWYzMGM0OTNhYWVhNTdmMTI5NzY3N2Y3Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMzk0YWQzZjA0Yjg0MGZmODYwNWJlM2JjYjMwZDA0YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy0zNy44MDE5NDMzNSwxNDQuOTcxOTcwMTcxMDE3Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kMjYwYmJhZWM1ZDU0YWVkYmFjNGUwODc0MDYzYTY0MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yMTUyM2NjZmI3NTQ0OGFjODgwZjA2N2I4MTliNDUyNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMjE1MjNjY2ZiNzU0NDhhYzg4MGYwNjdiODE5YjQ1MjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBUkxUT04gR0FSREVOUyBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QyNjBiYmFlYzVkNTRhZWRiYWM0ZTA4NzQwNjNhNjQwLnNldENvbnRlbnQoaHRtbF8yMTUyM2NjZmI3NTQ0OGFjODgwZjA2N2I4MTliNDUyNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMzk0YWQzZjA0Yjg0MGZmODYwNWJlM2JjYjMwZDA0Yy5iaW5kUG9wdXAocG9wdXBfZDI2MGJiYWVjNWQ1NGFlZGJhYzRlMDg3NDA2M2E2NDApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjYxOGQyYzhiOGUzNDZiZDg3ZGUwYmJjZDQxNTMwYjAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszMS4zNjYyNjYsLTgzLjkzNTQ4OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xODMxNjcxYzk4YzY0OTYxODdhMzMyZmFjNmE5ZGYzMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZjMxMmI1MzNmM2Q0ZGZlYWZlMGU1YzI1ODU0NTViMiA9ICQoJzxkaXYgaWQ9Imh0bWxfN2YzMTJiNTMzZjNkNGRmZWFmZTBlNWMyNTg1NDU1YjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNBUkxUT04gUk9BRCBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE4MzE2NzFjOThjNjQ5NjE4N2EzMzJmYWM2YTlkZjMwLnNldENvbnRlbnQoaHRtbF83ZjMxMmI1MzNmM2Q0ZGZlYWZlMGU1YzI1ODU0NTViMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNjE4ZDJjOGI4ZTM0NmJkODdkZTBiYmNkNDE1MzBiMC5iaW5kUG9wdXAocG9wdXBfMTgzMTY3MWM5OGM2NDk2MTg3YTMzMmZhYzZhOWRmMzApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzMwMDJkNjJhMzcwNGUzYjlkMjk3M2I5NDQzNmVlOTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NDEwODc2LC0wLjE1NTkyNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzAwYjVlYiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjdlMDVjY2RkODhkNGMyMmE2YjlkYjkzMGY0ODgyYzkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDVkYWZiZDk3OWI1NDNhNThkNzlkMzgxZjMzMDIyZTUgPSAkKCc8ZGl2IGlkPSJodG1sX2Q1ZGFmYmQ5NzliNTQzYTU4ZDc5ZDM4MWYzMzAyMmU1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DSEFMQ09UIFNRVUFSRSBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y3ZTA1Y2NkZDg4ZDRjMjJhNmI5ZGI5MzBmNDg4MmM5LnNldENvbnRlbnQoaHRtbF9kNWRhZmJkOTc5YjU0M2E1OGQ3OWQzODFmMzMwMjJlNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MzAwMmQ2MmEzNzA0ZTNiOWQyOTczYjk0NDM2ZWU5NC5iaW5kUG9wdXAocG9wdXBfZjdlMDVjY2RkODhkNGMyMmE2YjlkYjkzMGY0ODgyYzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2EyMzEzMmQ5ODcyNDk4NWEyNzMzYWRjYmIzZjNmMzggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODI3ODc1LC0wLjE3MDQ5MTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmYwMmNiZDI3MTg2NGZiMmIyNjI5MTg1YzhiNDliNGUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjk2NjQyOTY4NGYwNDE0Y2JhMTQ2NmZiNjI0Njc5NzIgPSAkKCc8ZGl2IGlkPSJodG1sX2Y5NjY0Mjk2ODRmMDQxNGNiYTE0NjZmYjYyNDY3OTcyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DSEVMU0VBIEVNQkFOS01FTlQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yZjAyY2JkMjcxODY0ZmIyYjI2MjkxODVjOGI0OWI0ZS5zZXRDb250ZW50KGh0bWxfZjk2NjQyOTY4NGYwNDE0Y2JhMTQ2NmZiNjI0Njc5NzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2EyMzEzMmQ5ODcyNDk4NWEyNzMzYWRjYmIzZjNmMzguYmluZFBvcHVwKHBvcHVwXzJmMDJjYmQyNzE4NjRmYjJiMjYyOTE4NWM4YjQ5YjRlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk2NjBkOTg3Y2Q2OTQxZGZiYjgxZmY5OTQ1NjVlMzM5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDk4NzI1NywtMC4xOTI4MTQ5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RjNDFjZjNkNzg0YzQ4ZmE5YjkzNmJlODgwYzE0NTUxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhmODNlOTVhZTU1ZjQyNjJhM2MwNDU4YjE2MTE0MGY3ID0gJCgnPGRpdiBpZD0iaHRtbF84ZjgzZTk1YWU1NWY0MjYyYTNjMDQ1OGIxNjExNDBmNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q0hFTklTVE9OIEdBUkRFTlMgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kYzQxY2YzZDc4NGM0OGZhOWI5MzZiZTg4MGMxNDU1MS5zZXRDb250ZW50KGh0bWxfOGY4M2U5NWFlNTVmNDI2MmEzYzA0NThiMTYxMTQwZjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTY2MGQ5ODdjZDY5NDFkZmJiODFmZjk5NDU2NWUzMzkuYmluZFBvcHVwKHBvcHVwX2RjNDFjZjNkNzg0YzQ4ZmE5YjkzNmJlODgwYzE0NTUxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZmYmIyZTExMzUwODQ2Y2E4NjgwMzM5MWVkM2Q2NWI0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTMyLjA5MTQ4MywxMTUuOTA0MjY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ZWYxMWQxMDk4NDE0MjM0OGQxMjQ4OWM5YzZmZmM1OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hOTQzNzZjNzg1NTk0MGJhODg3ZmY3MTNiMGU4MTJhNCA9ICQoJzxkaXYgaWQ9Imh0bWxfYTk0Mzc2Yzc4NTU5NDBiYTg4N2ZmNzEzYjBlODEyYTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNIRVNIQU0gTUVXUyBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRlZjExZDEwOTg0MTQyMzQ4ZDEyNDg5YzljNmZmYzU4LnNldENvbnRlbnQoaHRtbF9hOTQzNzZjNzg1NTk0MGJhODg3ZmY3MTNiMGU4MTJhNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mZmJiMmUxMTM1MDg0NmNhODY4MDMzOTFlZDNkNjViNC5iaW5kUG9wdXAocG9wdXBfNGVmMTFkMTA5ODQxNDIzNDhkMTI0ODljOWM2ZmZjNTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzA4NTNkZjUxOThmNDU1MzlmMzI3NGQyMzgxZjEyZDUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4yODQzMTY0LDAuMTUwMzM5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85NzQxNDY4ZjBiZDM0M2M4OWI0NmIyMWVlZGUxOGE1MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84MzdhYzY0ODZiZmI0YjRiODc2N2FmYWI0MjA0OGRlYiA9ICQoJzxkaXYgaWQ9Imh0bWxfODM3YWM2NDg2YmZiNGI0Yjg3NjdhZmFiNDIwNDhkZWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNIRVZFTklORyBST0FEIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTc0MTQ2OGYwYmQzNDNjODliNDZiMjFlZWRlMThhNTIuc2V0Q29udGVudChodG1sXzgzN2FjNjQ4NmJmYjRiNGI4NzY3YWZhYjQyMDQ4ZGViKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMwODUzZGY1MTk4ZjQ1NTM5ZjMyNzRkMjM4MWYxMmQ1LmJpbmRQb3B1cChwb3B1cF85NzQxNDY4ZjBiZDM0M2M4OWI0NmIyMWVlZGUxOGE1Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNTUzOWEwOGUxMDQ0YzBkOTAxNzEyOWM4MWQ0ZjEzNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUyLjYxNzQwNTQsLTEuMTU3MjQwOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMzIzNWIwM2ExY2U0NGM2YmFjMjc5MWNhOGFiMGQ1MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iN2M0NDFlOGM2NTg0NDY4YmFmYjQ1ZDMyMTc5OGU4MSA9ICQoJzxkaXYgaWQ9Imh0bWxfYjdjNDQxZThjNjU4NDQ2OGJhZmI0NWQzMjE3OThlODEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNPTExJTkdIQU0gUk9BRCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MzMjM1YjAzYTFjZTQ0YzZiYWMyNzkxY2E4YWIwZDUwLnNldENvbnRlbnQoaHRtbF9iN2M0NDFlOGM2NTg0NDY4YmFmYjQ1ZDMyMTc5OGU4MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNTUzOWEwOGUxMDQ0YzBkOTAxNzEyOWM4MWQ0ZjEzNC5iaW5kUG9wdXAocG9wdXBfYzMyMzViMDNhMWNlNDRjNmJhYzI3OTFjYThhYjBkNTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmVlNjhhZDZhZDI1NDEzMTgzNWNiYWM0MTdiZjk3YzggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zNTk2ODYxLDEuMDk3OTMwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNWRlYmM3MzZmMDc0OTIyYmEzNDczNzRhYTQzM2UxMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MTAwZTkwYzEwYTI0ZWRmODJiNDQ2ZTFiOTFjMjkyNiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTEwMGU5MGMxMGEyNGVkZjgyYjQ0NmUxYjkxYzI5MjYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNPVUxURVIgUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI1ZGViYzczNmYwNzQ5MjJiYTM0NzM3NGFhNDMzZTEwLnNldENvbnRlbnQoaHRtbF85MTAwZTkwYzEwYTI0ZWRmODJiNDQ2ZTFiOTFjMjkyNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iZWU2OGFkNmFkMjU0MTMxODM1Y2JhYzQxN2JmOTdjOC5iaW5kUG9wdXAocG9wdXBfMjVkZWJjNzM2ZjA3NDkyMmJhMzQ3Mzc0YWE0MzNlMTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWE0Yzg5YzgyM2MxNDY5NDk2YjhiNjE2MjM2MDBhYTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NDg1MDAyLC0wLjA4MDEwMTZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGE5OTIxMjE1NTA0NDRjMWFlNDEzNmFmNWIwYmJkODMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTdiN2UzM2VmNzg0NDQ2ZDkyN2RmNzY5NGIxMWVhMDYgPSAkKCc8ZGl2IGlkPSJodG1sX2U3YjdlMzNlZjc4NDQ0NmQ5MjdkZjc2OTRiMTFlYTA2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DT1VSVCBMQU5FIEdBUkRFTlMgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80YTk5MjEyMTU1MDQ0NGMxYWU0MTM2YWY1YjBiYmQ4My5zZXRDb250ZW50KGh0bWxfZTdiN2UzM2VmNzg0NDQ2ZDkyN2RmNzY5NGIxMWVhMDYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWE0Yzg5YzgyM2MxNDY5NDk2YjhiNjE2MjM2MDBhYTguYmluZFBvcHVwKHBvcHVwXzRhOTkyMTIxNTUwNDQ0YzFhZTQxMzZhZjViMGJiZDgzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcxMDdlMjQ2ZTRlYTQ4MzU5NjhiNDI4N2EwZWE0YzhiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTM4NDM5MywtMC4zNDc4ODY4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdiYmNjNDcwZmJiYTQxMmU5N2U3OGNlM2U5ZDIzM2UzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RkOGI5OTNhNWI2ZDRmMGY5NDFmYzkxNTkzZjE3YTcyID0gJCgnPGRpdiBpZD0iaHRtbF9kZDhiOTkzYTViNmQ0ZjBmOTQxZmM5MTU5M2YxN2E3MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q09VUlRIT1BFIFJPQUQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YmJjYzQ3MGZiYmE0MTJlOTdlNzhjZTNlOWQyMzNlMy5zZXRDb250ZW50KGh0bWxfZGQ4Yjk5M2E1YjZkNGYwZjk0MWZjOTE1OTNmMTdhNzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzEwN2UyNDZlNGVhNDgzNTk2OGI0Mjg3YTBlYTRjOGIuYmluZFBvcHVwKHBvcHVwXzdiYmNjNDcwZmJiYTQxMmU5N2U3OGNlM2U5ZDIzM2UzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I2ZDQ4NGZiM2UwNjQ3NTdhYjdmYzkxZGViM2NiYTA0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTE2MjMyMSwtMC4xOTg4MjExXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY5Mjg3YzAzYTVjNDRmMGQ5MjRiMmZmYjE4NjY3ODgzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI3ZTJlNWQ2YjFjYjRjYjVhY2JhMzU1Yjg4Zjg0MzE2ID0gJCgnPGRpdiBpZD0iaHRtbF8yN2UyZTVkNmIxY2I0Y2I1YWNiYTM1NWI4OGY4NDMxNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q09VUlRORUxMIFNUUkVFVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY5Mjg3YzAzYTVjNDRmMGQ5MjRiMmZmYjE4NjY3ODgzLnNldENvbnRlbnQoaHRtbF8yN2UyZTVkNmIxY2I0Y2I1YWNiYTM1NWI4OGY4NDMxNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iNmQ0ODRmYjNlMDY0NzU3YWI3ZmM5MWRlYjNjYmEwNC5iaW5kUG9wdXAocG9wdXBfNjkyODdjMDNhNWM0NGYwZDkyNGIyZmZiMTg2Njc4ODMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYWE1ZjEwY2U3YjljNDYwZmI4NDE4YTE2OGFiZTZlZjQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40MTgxODk4LC0wLjIzNzM4NDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDMxMjg5ZGNiYzk3NGE4MDllNGY0YjAyMjAxNzE4N2YgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTg3OGRmNmI1ZDI5NDhkYTgyNWRjYzY1MmJjZjI1NWYgPSAkKCc8ZGl2IGlkPSJodG1sXzk4NzhkZjZiNWQyOTQ4ZGE4MjVkY2M2NTJiY2YyNTVmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DUkFXRk9SRCBNRVdTIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDMxMjg5ZGNiYzk3NGE4MDllNGY0YjAyMjAxNzE4N2Yuc2V0Q29udGVudChodG1sXzk4NzhkZjZiNWQyOTQ4ZGE4MjVkY2M2NTJiY2YyNTVmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FhNWYxMGNlN2I5YzQ2MGZiODQxOGExNjhhYmU2ZWY0LmJpbmRQb3B1cChwb3B1cF80MzEyODlkY2JjOTc0YTgwOWU0ZjRiMDIyMDE3MTg3Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yMDMyYzJlOTY5MzY0MDc5OGQxYTdkNTczMGVhZWM0NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjU1MTEyODEsLTAuMTg5MDI2Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85YjAxZGE2ZGJjNmU0OTY2YjE0NjRlNjQwZmQwNTVkNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mOTM3MjIyMzhmMWM0MGQ3YTM3ZmMwZGU2ZjgyODY1NSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjkzNzIyMjM4ZjFjNDBkN2EzN2ZjMGRlNmY4Mjg2NTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNSRURJVE9OIEhJTEwgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85YjAxZGE2ZGJjNmU0OTY2YjE0NjRlNjQwZmQwNTVkNS5zZXRDb250ZW50KGh0bWxfZjkzNzIyMjM4ZjFjNDBkN2EzN2ZjMGRlNmY4Mjg2NTUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjAzMmMyZTk2OTM2NDA3OThkMWE3ZDU3MzBlYWVjNDQuYmluZFBvcHVwKHBvcHVwXzliMDFkYTZkYmM2ZTQ5NjZiMTQ2NGU2NDBmZDA1NWQ1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JiYjJlNzJkOGEwNTQ1MjA4NmJhMzNiYWJhZmZkYzA4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTYxNDIwMiwtMC4xNDIyNDk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdmYjE3OTUyMDBkNDRlYzFhMGJjNzI0ZTE4NmI4ZjczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQxM2U0ODU3OWFlMjRhOThhNzEzMTZhMTBkOTNjZDJiID0gJCgnPGRpdiBpZD0iaHRtbF80MTNlNDg1NzlhZTI0YTk4YTcxMzE2YTEwZDkzY2QyYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+REFSVE1PVVRIIFBBUksgQVZFTlVFIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2ZiMTc5NTIwMGQ0NGVjMWEwYmM3MjRlMTg2YjhmNzMuc2V0Q29udGVudChodG1sXzQxM2U0ODU3OWFlMjRhOThhNzEzMTZhMTBkOTNjZDJiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JiYjJlNzJkOGEwNTQ1MjA4NmJhMzNiYWJhZmZkYzA4LmJpbmRQb3B1cChwb3B1cF83ZmIxNzk1MjAwZDQ0ZWMxYTBiYzcyNGUxODZiOGY3Myk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82YmEzYjA0MTQ5ZjE0NGMyYmRmODcxZDg5MWExZTMxZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5OTU1MjMsLTAuMTg0ODddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDNjMTgyMmUzYjczNDhkNWEyMTFkYmNhOGM0OWIzYmQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGY5NWRhZDc5NmIwNDgyOTk1MjU4NzRjNzYxMzZlMjQgPSAkKCc8ZGl2IGlkPSJodG1sXzBmOTVkYWQ3OTZiMDQ4Mjk5NTI1ODc0Yzc2MTM2ZTI0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERSBWRVJFIEdBUkRFTlMgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wM2MxODIyZTNiNzM0OGQ1YTIxMWRiY2E4YzQ5YjNiZC5zZXRDb250ZW50KGh0bWxfMGY5NWRhZDc5NmIwNDgyOTk1MjU4NzRjNzYxMzZlMjQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmJhM2IwNDE0OWYxNDRjMmJkZjg3MWQ4OTFhMWUzMWYuYmluZFBvcHVwKHBvcHVwXzAzYzE4MjJlM2I3MzQ4ZDVhMjExZGJjYThjNDliM2JkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMwYWI5ZGM0OTk1YzRhNjA4YzRhNTg3YThmODIzYjk0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuNzcyMjE0MDUsLTIuNjg4NTYwNjE2NTg2ODc4M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wN2RiZTBhZDcyZDQ0OTY0YTM1OTMyZmUyODY3ZmE2YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZWM4ZGZlNTBiNzg0Mjg5YThiYjdjOTJhZGYwZTEzMSA9ICQoJzxkaXYgaWQ9Imh0bWxfM2VjOGRmZTUwYjc4NDI4OWE4YmI3YzkyYWRmMGUxMzEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRFRVBEQUxFIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDdkYmUwYWQ3MmQ0NDk2NGEzNTkzMmZlMjg2N2ZhNmIuc2V0Q29udGVudChodG1sXzNlYzhkZmU1MGI3ODQyODlhOGJiN2M5MmFkZjBlMTMxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMwYWI5ZGM0OTk1YzRhNjA4YzRhNTg3YThmODIzYjk0LmJpbmRQb3B1cChwb3B1cF8wN2RiZTBhZDcyZDQ0OTY0YTM1OTMyZmUyODY3ZmE2Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85YjBlMTJhNTMyMDA0MTg1OTViMzNjNDJlNWZhMjc0MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUyLjcwNzQ3NjE1MDAwMDAwNSwtMi41MjY3OTQ1NTcxNjgxNDhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDA1NjBjMzYxZDY2NDA2MmE2OTA5YTcyODQzNzc0MTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjdhNDdhYWEyMzU2NDkwNTkyYjlmODBiY2U4OWUzMWMgPSAkKCc8ZGl2IGlkPSJodG1sXzY3YTQ3YWFhMjM1NjQ5MDU5MmI5ZjgwYmNlODllMzFjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERUVSIFBBUksgUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQwNTYwYzM2MWQ2NjQwNjJhNjkwOWE3Mjg0Mzc3NDEzLnNldENvbnRlbnQoaHRtbF82N2E0N2FhYTIzNTY0OTA1OTJiOWY4MGJjZTg5ZTMxYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YjBlMTJhNTMyMDA0MTg1OTViMzNjNDJlNWZhMjc0My5iaW5kUG9wdXAocG9wdXBfNDA1NjBjMzYxZDY2NDA2MmE2OTA5YTcyODQzNzc0MTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2IwYWNhMzg1OTM4NGFhZWE5MDI1YzliNjhmZDY0MDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszNS41NTUwOTY2LC04Mi40Nzg4MDU2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzExMDE1ZjNlOWI5NTQxMWJiMjVjYzg1NGRkZjIwNmZhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ5YWNjMTFhMWYwZTRiYzQ5MGVmN2I4NmU5ODY0NjJiID0gJCgnPGRpdiBpZD0iaHRtbF80OWFjYzExYTFmMGU0YmM0OTBlZjdiODZlOTg2NDYyYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+REVWRVJFVVggTEFORSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzExMDE1ZjNlOWI5NTQxMWJiMjVjYzg1NGRkZjIwNmZhLnNldENvbnRlbnQoaHRtbF80OWFjYzExYTFmMGU0YmM0OTBlZjdiODZlOTg2NDYyYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jYjBhY2EzODU5Mzg0YWFlYTkwMjVjOWI2OGZkNjQwMy5iaW5kUG9wdXAocG9wdXBfMTEwMTVmM2U5Yjk1NDExYmIyNWNjODU0ZGRmMjA2ZmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTRkNTNiZWE5ZDFlNDkxNjg5Yjk5YWI3MjQyYWIzOTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MjIyMzA2LC0wLjE0OTE2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2U1MDgyYTM2N2JmNDA0ZTg5ZDQzYjY2MWZjYWFmZjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTAwMmFiMWVjMTgzNDI3Mjg2OGUwYmVlODAyOTE2MmQgPSAkKCc8ZGl2IGlkPSJodG1sXzEwMDJhYjFlYzE4MzQyNzI4NjhlMGJlZTgwMjkxNjJkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ERVZPTlNISVJFIE1FV1MgV0VTVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNlNTA4MmEzNjdiZjQwNGU4OWQ0M2I2NjFmY2FhZmY1LnNldENvbnRlbnQoaHRtbF8xMDAyYWIxZWMxODM0MjcyODY4ZTBiZWU4MDI5MTYyZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85NGQ1M2JlYTlkMWU0OTE2ODliOTlhYjcyNDJhYjM5MC5iaW5kUG9wdXAocG9wdXBfM2U1MDgyYTM2N2JmNDA0ZTg5ZDQzYjY2MWZjYWFmZjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzJlY2NhMTU5OGU0NDE3ZmEwNWI0YmFkNjVjNzk4OWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MDgzOTM0LC0wLjE0MjE2MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDY4ODVmZGRiMmQzNDRiNTllMGY5ZWU3OTNmM2YyYzYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTI0MWU3ZmMzMDJkNDA5YThhYjBjODhiOWY1OWM0NTAgPSAkKCc8ZGl2IGlkPSJodG1sXzUyNDFlN2ZjMzAyZDQwOWE4YWIwYzg4YjlmNTljNDUwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ET1ZFUiBTVFJFRVQgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNjg4NWZkZGIyZDM0NGI1OWUwZjllZTc5M2YzZjJjNi5zZXRDb250ZW50KGh0bWxfNTI0MWU3ZmMzMDJkNDA5YThhYjBjODhiOWY1OWM0NTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzJlY2NhMTU5OGU0NDE3ZmEwNWI0YmFkNjVjNzk4OWUuYmluZFBvcHVwKHBvcHVwX2Q2ODg1ZmRkYjJkMzQ0YjU5ZTBmOWVlNzkzZjNmMmM2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NkNGIxZjIxNTgwODQ1NGRiZWJkMDVlYTFiMmYxNGYzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTI2MDg3MywtMC4zMjc4NjU2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiMwMGI1ZWIiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzllN2YzYjExOWRlMTQ1NGVhMDJhMjE2MzZiMGIxYjhhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgzZGUwOTNhZjVjNjRhM2Y4Yzg4NmM5MmNlODI5ZTliID0gJCgnPGRpdiBpZD0iaHRtbF84M2RlMDkzYWY1YzY0YTNmOGM4ODZjOTJjZTgyOWU5YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RE9XTlNJREUgQ1JFU0NFTlQgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZTdmM2IxMTlkZTE0NTRlYTAyYTIxNjM2YjBiMWI4YS5zZXRDb250ZW50KGh0bWxfODNkZTA5M2FmNWM2NGEzZjhjODg2YzkyY2U4MjllOWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2Q0YjFmMjE1ODA4NDU0ZGJlYmQwNWVhMWIyZjE0ZjMuYmluZFBvcHVwKHBvcHVwXzllN2YzYjExOWRlMTQ1NGVhMDJhMjE2MzZiMGIxYjhhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NkNzI3ZWFjZWIxNTRjMzk5YzQ5ZWI0YmNlNDAzYTkxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMjc2MDE1OCwtNzEuNDI4NTUwNF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNjg4ZGJiZGVlMmU0MTg0YjUzMzc5N2JhMmY4NWMxYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNTMxODAwOWZlY2I0MzVhYjBiYTNiNDA0ZTE1Zjg5MiA9ICQoJzxkaXYgaWQ9Imh0bWxfMzUzMTgwMDlmZWNiNDM1YWIwYmEzYjQwNGUxNWY4OTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRVRExFWSBST0FEIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjY4OGRiYmRlZTJlNDE4NGI1MzM3OTdiYTJmODVjMWMuc2V0Q29udGVudChodG1sXzM1MzE4MDA5ZmVjYjQzNWFiMGJhM2I0MDRlMTVmODkyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NkNzI3ZWFjZWIxNTRjMzk5YzQ5ZWI0YmNlNDAzYTkxLmJpbmRQb3B1cChwb3B1cF8yNjg4ZGJiZGVlMmU0MTg0YjUzMzc5N2JhMmY4NWMxYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YTExYzk2NTk2YmM0MmFjYTM4MDhlYWFiNjE5YmI1OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzU0LjExNjQwMzksLTAuMzk1Nzg0Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84MzFjZDA2YjdmNTc0ODg5YmZlMGNjYzNjZmYyZDkwZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lOTg2OWU4MjgyYTE0Y2Q5OTRiOGMwZmNmOGMyM2ZmMiA9ICQoJzxkaXYgaWQ9Imh0bWxfZTk4NjllODI4MmExNGNkOTk0YjhjMGZjZjhjMjNmZjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRVS0VTIExBTkUgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84MzFjZDA2YjdmNTc0ODg5YmZlMGNjYzNjZmYyZDkwZC5zZXRDb250ZW50KGh0bWxfZTk4NjllODI4MmExNGNkOTk0YjhjMGZjZjhjMjNmZjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWExMWM5NjU5NmJjNDJhY2EzODA4ZWFhYjYxOWJiNTkuYmluZFBvcHVwKHBvcHVwXzgzMWNkMDZiN2Y1NzQ4ODliZmUwY2NjM2NmZjJkOTBkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJkNDhjZmQ0YjMzZTQ2MDNhNTc1MWVhMGVjMjJkYzViID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDk0NDA0MSwtMC4xNTUyOTE0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzIyYTFlZDdjZDA3ZjQzMmY5M2FiNjcxYzgyNzVkMTcwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI2M2M3YzNlOTA1MzQ4Y2Y5YTZiMjNlYzAzZWZmMTQ5ID0gJCgnPGRpdiBpZD0iaHRtbF8yNjNjN2MzZTkwNTM0OGNmOWE2YjIzZWMwM2VmZjE0OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RUFUT04gVEVSUkFDRSBNRVdTIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjJhMWVkN2NkMDdmNDMyZjkzYWI2NzFjODI3NWQxNzAuc2V0Q29udGVudChodG1sXzI2M2M3YzNlOTA1MzQ4Y2Y5YTZiMjNlYzAzZWZmMTQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzJkNDhjZmQ0YjMzZTQ2MDNhNTc1MWVhMGVjMjJkYzViLmJpbmRQb3B1cChwb3B1cF8yMmExZWQ3Y2QwN2Y0MzJmOTNhYjY3MWM4Mjc1ZDE3MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hNzBkNDYzNjBmZjc0NTQwOTZiNDE5MDJiNDRlZTM0OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5NzkwMDcsLTAuMTUwNDMzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MyYzljMDNjZDBjZDRlYmJiM2IwZTgzZWEwMmQyM2ZjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU0NDZmMDhkMzIxOTQ2NTdhZGQyOGI2ZGVkNWQ4NzBmID0gJCgnPGRpdiBpZD0iaHRtbF81NDQ2ZjA4ZDMyMTk0NjU3YWRkMjhiNmRlZDVkODcwZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RUNDTEVTVE9OIE1FV1MgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMmM5YzAzY2QwY2Q0ZWJiYjNiMGU4M2VhMDJkMjNmYy5zZXRDb250ZW50KGh0bWxfNTQ0NmYwOGQzMjE5NDY1N2FkZDI4YjZkZWQ1ZDg3MGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTcwZDQ2MzYwZmY3NDU0MDk2YjQxOTAyYjQ0ZWUzNDkuYmluZFBvcHVwKHBvcHVwX2MyYzljMDNjZDBjZDRlYmJiM2IwZTgzZWEwMmQyM2ZjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E2NjVkN2E0MmZjNjRlZTk4N2FlNzZjNzQwZGZmZjAwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDkxNjA1NywtMC4xNDUzNDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmZhZWU1OTc2NmI3NDJmNjkxMjA0OTQyYjEwNTk0NWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTFlN2Y0ZWRmOTg4NDJiNjg1OTUxNjI5ZmVhYTQ1MjQgPSAkKCc8ZGl2IGlkPSJodG1sX2UxZTdmNGVkZjk4ODQyYjY4NTk1MTYyOWZlYWE0NTI0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FQ0NMRVNUT04gU1FVQVJFIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmZhZWU1OTc2NmI3NDJmNjkxMjA0OTQyYjEwNTk0NWEuc2V0Q29udGVudChodG1sX2UxZTdmNGVkZjk4ODQyYjY4NTk1MTYyOWZlYWE0NTI0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E2NjVkN2E0MmZjNjRlZTk4N2FlNzZjNzQwZGZmZjAwLmJpbmRQb3B1cChwb3B1cF9iZmFlZTU5NzY2Yjc0MmY2OTEyMDQ5NDJiMTA1OTQ1YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMzQ4NTM1NDg4ZTM0ZWExYWE4ZGE4MTVhYTMwNDc1YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ3NDk4MDMsLTAuMjE3NjAwNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mYmNiNGFlNmI1ZTU0YWMxYjliNzliNmNlNDg4MTg0NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZWJhY2I1ZWQyZTg0ZGIyOWM0MjRkODNhM2NmY2ZmYiA9ICQoJzxkaXYgaWQ9Imh0bWxfY2ViYWNiNWVkMmU4NGRiMjljNDI0ZDgzYTNjZmNmZmIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVMTEVSQlkgU1RSRUVUIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmJjYjRhZTZiNWU1NGFjMWI5Yjc5YjZjZTQ4ODE4NDQuc2V0Q29udGVudChodG1sX2NlYmFjYjVlZDJlODRkYjI5YzQyNGQ4M2EzY2ZjZmZiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2UzNDg1MzU0ODhlMzRlYTFhYThkYTgxNWFhMzA0NzVhLmJpbmRQb3B1cChwb3B1cF9mYmNiNGFlNmI1ZTU0YWMxYjliNzliNmNlNDg4MTg0NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNjZjNjJlZDc1NTM0YTAxYjI4MzdiOTVlYWNjZGI5MCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5Nzc5MzgsLTAuMTgxNTQ1NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85ZDU1NjEwOWI2YjI0NzFlOWU5NmIzMGVhMTFiNDM0YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wOGFkOTM4ZjQ0MzI0OTZiYTdmMTJkNDUyNWUzZmU5NSA9ICQoJzxkaXYgaWQ9Imh0bWxfMDhhZDkzOGY0NDMyNDk2YmE3ZjEyZDQ1MjVlM2ZlOTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVMVkFTVE9OIFBMQUNFIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWQ1NTYxMDliNmIyNDcxZTllOTZiMzBlYTExYjQzNGMuc2V0Q29udGVudChodG1sXzA4YWQ5MzhmNDQzMjQ5NmJhN2YxMmQ0NTI1ZTNmZTk1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM2NmM2MmVkNzU1MzRhMDFiMjgzN2I5NWVhY2NkYjkwLmJpbmRQb3B1cChwb3B1cF85ZDU1NjEwOWI2YjI0NzFlOWU5NmIzMGVhMTFiNDM0Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYWE2MGZkZmJlNTE0MDk3ODQwNzkzYjAzNTA0MzAxZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5ODc3MTksLTAuMTcwMzk2Nl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YmJhMDRmOGRiOWI0YzI0YTUzNTk4MTQ4YzE4Yjg0ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZGU5MDJlMmE5MWM0Y2RjODFmNWFhM2Y1N2FmNDM2MiA9ICQoJzxkaXYgaWQ9Imh0bWxfNGRlOTAyZTJhOTFjNGNkYzgxZjVhYTNmNTdhZjQzNjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVOTklTTU9SRSBHQVJERU5TIE1FV1MgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YmJhMDRmOGRiOWI0YzI0YTUzNTk4MTQ4YzE4Yjg0ZS5zZXRDb250ZW50KGh0bWxfNGRlOTAyZTJhOTFjNGNkYzgxZjVhYTNmNTdhZjQzNjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmFhNjBmZGZiZTUxNDA5Nzg0MDc5M2IwMzUwNDMwMWYuYmluZFBvcHVwKHBvcHVwXzdiYmEwNGY4ZGI5YjRjMjRhNTM1OTgxNDhjMThiODRlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdiYzkyYzUwNmVlMTQzNTY5Mjc3YjQyNTY0N2NlMWZkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuNzE4Mzk4MiwtNzMuOTg3NDIzNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83N2YyMGRiNTc2ZjM0OGFkYjA2MGY3OWI3NDIwNDdjOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZTRlYjFiYmU4MmE0MjU0YWMwMjc0MWU3ZjQzOWJmNSA9ICQoJzxkaXYgaWQ9Imh0bWxfM2U0ZWIxYmJlODJhNDI1NGFjMDI3NDFlN2Y0MzliZjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVTU0VYIFNUUkVFVCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc3ZjIwZGI1NzZmMzQ4YWRiMDYwZjc5Yjc0MjA0N2M5LnNldENvbnRlbnQoaHRtbF8zZTRlYjFiYmU4MmE0MjU0YWMwMjc0MWU3ZjQzOWJmNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83YmM5MmM1MDZlZTE0MzU2OTI3N2I0MjU2NDdjZTFmZC5iaW5kUG9wdXAocG9wdXBfNzdmMjBkYjU3NmYzNDhhZGIwNjBmNzliNzQyMDQ3YzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGIyOGZmY2MwZTJmNDNmYmIxOTc5MTk1NDNmMmEwZmEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NjkxODkzLC0wLjQyMTIwNTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGRhZWM2NzI0NmMzNDA1OThmMDJiYmRlZTc0ZTRmMzMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWNhY2UwZjc5ZWI1NGYyNzk2NTM0MWM5NzM1YmU3NzYgPSAkKCc8ZGl2IGlkPSJodG1sXzVjYWNlMGY3OWViNTRmMjc5NjUzNDFjOTczNWJlNzc2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FVkVSU0xFWSBDUkVTQ0VOVCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBkYWVjNjcyNDZjMzQwNTk4ZjAyYmJkZWU3NGU0ZjMzLnNldENvbnRlbnQoaHRtbF81Y2FjZTBmNzllYjU0ZjI3OTY1MzQxYzk3MzViZTc3Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kYjI4ZmZjYzBlMmY0M2ZiYjE5NzkxOTU0M2YyYTBmYS5iaW5kUG9wdXAocG9wdXBfMGRhZWM2NzI0NmMzNDA1OThmMDJiYmRlZTc0ZTRmMzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDE1ZWRiNTYyMmU2NGU4ZWJjMzc2MzBiOWI2NzAzZWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NDg3MjQ3LC0wLjA3NjY0MDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2NmMzE5NmM3ZGU1NGE5Zjk0NGQ3MWYyNTgyN2ZhMjEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjc0NDFhMjUwNTg2NDQ1ZWE2MTE5YWIxNDZkMGU5ZTAgPSAkKCc8ZGl2IGlkPSJodG1sXzY3NDQxYTI1MDU4NjQ0NWVhNjExOWFiMTQ2ZDBlOWUwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FWU5FTExBIFJPQUQgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zY2YzMTk2YzdkZTU0YTlmOTQ0ZDcxZjI1ODI3ZmEyMS5zZXRDb250ZW50KGh0bWxfNjc0NDFhMjUwNTg2NDQ1ZWE2MTE5YWIxNDZkMGU5ZTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDE1ZWRiNTYyMmU2NGU4ZWJjMzc2MzBiOWI2NzAzZWMuYmluZFBvcHVwKHBvcHVwXzNjZjMxOTZjN2RlNTRhOWY5NDRkNzFmMjU4MjdmYTIxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M1ZWFlNGZlODhhODQxMzI4MGYxYTI0NzY4NjBmN2Y2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTEyMDUzNywtMC4wODAxODQ0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MDAwZmYiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODAwMGZmIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEzYTIzNjE1MTUyYTRmMmE4NmIzYTE3YjRiZTNlMjU5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M0ZTA3YWI3ODJiMjRiMGQ5ZTQzYzNjNmE5NGFiM2YwID0gJCgnPGRpdiBpZD0iaHRtbF9jNGUwN2FiNzgyYjI0YjBkOWU0M2MzYzZhOTRhYjNmMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RkVOQ0hVUkNIIFNUUkVFVCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzEzYTIzNjE1MTUyYTRmMmE4NmIzYTE3YjRiZTNlMjU5LnNldENvbnRlbnQoaHRtbF9jNGUwN2FiNzgyYjI0YjBkOWU0M2MzYzZhOTRhYjNmMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNWVhZTRmZTg4YTg0MTMyODBmMWEyNDc2ODYwZjdmNi5iaW5kUG9wdXAocG9wdXBfMTNhMjM2MTUxNTJhNGYyYTg2YjNhMTdiNGJlM2UyNTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzAyYTRjNTgxYTdkNDYzNjgxODJkOTRhZmE1NTJmYWEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0Mi4zMjExMzA0LC03MS4yMDYwNzg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk0YTRmYWRlMzNiNTQ3MDU4MmY4NDlkM2MxNjBmMWMxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U2ZWFlZTVmOTAzYTRlN2U4ZjVhMDBjYjcyMDJkYzdlID0gJCgnPGRpdiBpZD0iaHRtbF9lNmVhZWU1ZjkwM2E0ZTdlOGY1YTAwY2I3MjAyZGM3ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RkxPUkFMIFNUUkVFVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk0YTRmYWRlMzNiNTQ3MDU4MmY4NDlkM2MxNjBmMWMxLnNldENvbnRlbnQoaHRtbF9lNmVhZWU1ZjkwM2E0ZTdlOGY1YTAwY2I3MjAyZGM3ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MDJhNGM1ODFhN2Q0NjM2ODE4MmQ5NGFmYTU1MmZhYS5iaW5kUG9wdXAocG9wdXBfOTRhNGZhZGUzM2I1NDcwNTgyZjg0OWQzYzE2MGYxYzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2I3OTY0OTc5MmE0NDhkZWEwNmNlOWFlOThmN2M4MTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NjI0MTIzLC0wLjA2NzEyMDldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWI5MjYwNmU0MDY4NGY3NWE3MjI0YTBkYTViZmQ4MmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTdhM2YxN2QzOWYxNGM1OThhODhmNDdhM2JmZjBlZjkgPSAkKCc8ZGl2IGlkPSJodG1sXzE3YTNmMTdkMzlmMTRjNTk4YTg4ZjQ3YTNiZmYwZWY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GT1VOVEFZTkUgUk9BRCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzliOTI2MDZlNDA2ODRmNzVhNzIyNGEwZGE1YmZkODJlLnNldENvbnRlbnQoaHRtbF8xN2EzZjE3ZDM5ZjE0YzU5OGE4OGY0N2EzYmZmMGVmOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zYjc5NjQ5NzkyYTQ0OGRlYTA2Y2U5YWU5OGY3YzgxOC5iaW5kUG9wdXAocG9wdXBfOWI5MjYwNmU0MDY4NGY3NWE3MjI0YTBkYTViZmQ4MmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzEwYmEwMTcxZGYwNDY0OTg5NjVkMzgyZjdlNjMxODIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MTkyNDMzLC0wLjA3MjUyMDNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTEzMGZlZTE3YmJkNDk5NzkyNGZiNmFkMWUyZDlmZjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTRjNzkyMzJjZWE3NDUxNDg2MGJkYTVkYjhhYTE4ODUgPSAkKCc8ZGl2IGlkPSJodG1sXzU0Yzc5MjMyY2VhNzQ1MTQ4NjBiZGE1ZGI4YWExODg1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GT1VSTklFUiBTVFJFRVQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MTMwZmVlMTdiYmQ0OTk3OTI0ZmI2YWQxZTJkOWZmNi5zZXRDb250ZW50KGh0bWxfNTRjNzkyMzJjZWE3NDUxNDg2MGJkYTVkYjhhYTE4ODUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzEwYmEwMTcxZGYwNDY0OTg5NjVkMzgyZjdlNjMxODIuYmluZFBvcHVwKHBvcHVwXzkxMzBmZWUxN2JiZDQ5OTc5MjRmYjZhZDFlMmQ5ZmY2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY5MDRjNTQzNDdjZTRjMzU4ZGVlNDE2MTc0NDE5ZDg4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDY5NjUzOSwtMC4xOTE0NDM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJiNTc0NDEwMWRjMDQ4MjQ4NTM1ZGZkNGU1ODc3MzRjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2EzZWMzZGI3NTMzMjQ5YWVhZjVkZjNhYzI3ODdhMmYyID0gJCgnPGRpdiBpZD0iaHRtbF9hM2VjM2RiNzUzMzI0OWFlYWY1ZGYzYWMyNzg3YTJmMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RlJJU1RPTiBTVFJFRVQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yYjU3NDQxMDFkYzA0ODI0ODUzNWRmZDRlNTg3NzM0Yy5zZXRDb250ZW50KGh0bWxfYTNlYzNkYjc1MzMyNDlhZWFmNWRmM2FjMjc4N2EyZjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjkwNGM1NDM0N2NlNGMzNThkZWU0MTYxNzQ0MTlkODguYmluZFBvcHVwKHBvcHVwXzJiNTc0NDEwMWRjMDQ4MjQ4NTM1ZGZkNGU1ODc3MzRjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJjNDFmYWIwNWJkNjQ2NjA4OTE2NDBlZjgzOGZjZjVhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuMTg4NTI1LC05Ni4xNzUwMDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjI2ZTBmZGU5YzljNDM3YzgwODViNjU5NzgyZTUzN2QgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjc5NDZkOWNhYmFmNDU2ZTgwZTViNzBmODFjOWZlYWIgPSAkKCc8ZGl2IGlkPSJodG1sXzY3OTQ2ZDljYWJhZjQ1NmU4MGU1YjcwZjgxYzlmZWFiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HRVJUUlVERSBTVFJFRVQgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82MjZlMGZkZTljOWM0MzdjODA4NWI2NTk3ODJlNTM3ZC5zZXRDb250ZW50KGh0bWxfNjc5NDZkOWNhYmFmNDU2ZTgwZTViNzBmODFjOWZlYWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmM0MWZhYjA1YmQ2NDY2MDg5MTY0MGVmODM4ZmNmNWEuYmluZFBvcHVwKHBvcHVwXzYyNmUwZmRlOWM5YzQzN2M4MDg1YjY1OTc4MmU1MzdkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NhZjY0M2U0NDk5ZTRkZjdiMmY2NmI5ZWVjMDYwZGY2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTE3MzkwMywtMC4xNTgyNzEyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MDAwZmYiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODAwMGZmIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY1ZWM4NjlhMDYzODRkMWU5MjFmNTJkNDY0M2FhNGRiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEzYjBjNGViNTIzMjRiYjRhOGY2OGY0MjUxNzNlYmYyID0gJCgnPGRpdiBpZD0iaHRtbF8xM2IwYzRlYjUyMzI0YmI0YThmNjhmNDI1MTczZWJmMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R0xPVUNFU1RFUiBQTEFDRSBNRVdTIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjVlYzg2OWEwNjM4NGQxZTkyMWY1MmQ0NjQzYWE0ZGIuc2V0Q29udGVudChodG1sXzEzYjBjNGViNTIzMjRiYjRhOGY2OGY0MjUxNzNlYmYyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NhZjY0M2U0NDk5ZTRkZjdiMmY2NmI5ZWVjMDYwZGY2LmJpbmRQb3B1cChwb3B1cF82NWVjODY5YTA2Mzg0ZDFlOTIxZjUyZDQ2NDNhYTRkYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNDE5MTVlODIyY2E0NjY3YWJmMTc0ZmY0OGFjNzA2MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjMxNjUyMjgsMC43MDQ2Mzc3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZiNDUzMDg1NzY4NjQwNTQ4ZjlkYjVjN2M0OWIxNTcwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FhNjUyYjM0YWY1NjQzYTBhMmRkOTQxZjQyMjUwZGIyID0gJCgnPGRpdiBpZD0iaHRtbF9hYTY1MmIzNGFmNTY0M2EwYTJkZDk0MWY0MjI1MGRiMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R09SRE9OIENPVFRBR0VTIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmI0NTMwODU3Njg2NDA1NDhmOWRiNWM3YzQ5YjE1NzAuc2V0Q29udGVudChodG1sX2FhNjUyYjM0YWY1NjQzYTBhMmRkOTQxZjQyMjUwZGIyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q0MTkxNWU4MjJjYTQ2NjdhYmYxNzRmZjQ4YWM3MDYzLmJpbmRQb3B1cChwb3B1cF9mYjQ1MzA4NTc2ODY0MDU0OGY5ZGI1YzdjNDliMTU3MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lY2MwZWJhZThkNmU0MmFlOGM2NGY5MjQzZGIzMmU0ZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ1Mjg5NTUsLTAuMTYzODE5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzMwNDI3NTQwNTU4ODQwYThhOWJhNGNjMzEwZDk1YTFjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzllYzhkMjllNGM5MzQ1NWY4NjI5YzFjZTNjYjAyOGM5ID0gJCgnPGRpdiBpZD0iaHRtbF85ZWM4ZDI5ZTRjOTM0NTVmODYyOWMxY2UzY2IwMjhjOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R09SU1QgUk9BRCBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMwNDI3NTQwNTU4ODQwYThhOWJhNGNjMzEwZDk1YTFjLnNldENvbnRlbnQoaHRtbF85ZWM4ZDI5ZTRjOTM0NTVmODYyOWMxY2UzY2IwMjhjOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lY2MwZWJhZThkNmU0MmFlOGM2NGY5MjQzZGIzMmU0ZC5iaW5kUG9wdXAocG9wdXBfMzA0Mjc1NDA1NTg4NDBhOGE5YmE0Y2MzMTBkOTVhMWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDhhNGQ2MDBkYjUzNDE4ZTgwYThlOTJiMjJiNjQwYTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NDk1NzQ0LDAuMTE0MTIyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNDNlODQwZWM1ZDE0MTIxOTdiOTU1OGQyMTc5YWYwMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lOWY3NzJkNjk2ZDI0YzE3ODVkMmNhYTk1ZDBmZWM2OCA9ICQoJzxkaXYgaWQ9Imh0bWxfZTlmNzcyZDY5NmQyNGMxNzg1ZDJjYWE5NWQwZmVjNjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdSQUhBTSBURVJSQUNFIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTQzZTg0MGVjNWQxNDEyMTk3Yjk1NThkMjE3OWFmMDIuc2V0Q29udGVudChodG1sX2U5Zjc3MmQ2OTZkMjRjMTc4NWQyY2FhOTVkMGZlYzY4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA4YTRkNjAwZGI1MzQxOGU4MGE4ZTkyYjIyYjY0MGE1LmJpbmRQb3B1cChwb3B1cF9hNDNlODQwZWM1ZDE0MTIxOTdiOTU1OGQyMTc5YWYwMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84M2E0YWE3MGU4YTY0MjMwOTViZTQzYWU4NjdhOWJkNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUyMDQxMjgsLTAuMTQxNDAyMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wYmNkODdkZDlkZjY0MTQ0YTQwNmMzMTdkMzBlMzM3YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iYzM3MjE0YmUxNTk0OTEwYjg3ODkxM2EyOTkwMGUxZSA9ICQoJzxkaXYgaWQ9Imh0bWxfYmMzNzIxNGJlMTU5NDkxMGI4Nzg5MTNhMjk5MDBlMWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdSRUFUIFRJVENIRklFTEQgU1RSRUVUIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGJjZDg3ZGQ5ZGY2NDE0NGE0MDZjMzE3ZDMwZTMzN2Iuc2V0Q29udGVudChodG1sX2JjMzcyMTRiZTE1OTQ5MTBiODc4OTEzYTI5OTAwZTFlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgzYTRhYTcwZThhNjQyMzA5NWJlNDNhZTg2N2E5YmQ2LmJpbmRQb3B1cChwb3B1cF8wYmNkODdkZDlkZjY0MTQ0YTQwNmMzMTdkMzBlMzM3Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ZDIwNzc5ZWFiZjc0MDBkOTZhYjVhY2U2MGU4M2UyMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjU2NzcyMjQsLTIuMzA3Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM4NWJmZTY2NTc0NDRhMzJiOTFlNWM3MzVhODNkYTliID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Q4ZDBlOTQ1OGM1ZjRmOTA5OTgyNGIyNjM3Y2MxMzgxID0gJCgnPGRpdiBpZD0iaHRtbF9kOGQwZTk0NThjNWY0ZjkwOTk4MjRiMjYzN2NjMTM4MSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFMTEFNIFNUUkVFVCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM4NWJmZTY2NTc0NDRhMzJiOTFlNWM3MzVhODNkYTliLnNldENvbnRlbnQoaHRtbF9kOGQwZTk0NThjNWY0ZjkwOTk4MjRiMjYzN2NjMTM4MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81ZDIwNzc5ZWFiZjc0MDBkOTZhYjVhY2U2MGU4M2UyMi5iaW5kUG9wdXAocG9wdXBfMzg1YmZlNjY1NzQ0NGEzMmI5MWU1YzczNWE4M2RhOWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2FlNTk2YTE5MzIwNDQ2ZThhMWM2M2JmNDUzOTVhZjAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC42ODU5MTkzLC03My45MTU4NTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWNiYTlmOTVmODMxNGU1Yzk0Y2NiZDUzOTQzN2ViYmIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMThhYzIzMzgzMmI0NDk0MWFmNzNlZjBkNzFiMDU2YzMgPSAkKCc8ZGl2IGlkPSJodG1sXzE4YWMyMzM4MzJiNDQ5NDFhZjczZWYwZDcxYjA1NmMzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IQUxTRVkgU1RSRUVUIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWNiYTlmOTVmODMxNGU1Yzk0Y2NiZDUzOTQzN2ViYmIuc2V0Q29udGVudChodG1sXzE4YWMyMzM4MzJiNDQ5NDFhZjczZWYwZDcxYjA1NmMzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdhZTU5NmExOTMyMDQ0NmU4YTFjNjNiZjQ1Mzk1YWYwLmJpbmRQb3B1cChwb3B1cF9hY2JhOWY5NWY4MzE0ZTVjOTRjY2JkNTM5NDM3ZWJiYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MjI2OTE1NmM2M2M0NDFjOWQyMzY4YmM3OTBiZGEzMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjM2MjY5NzUsMC4wODQwNzM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY1YWNjMTY4ZGMxNDRlMWFiOWRkYmYzOGY3ZjZiMTFhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QyYTM0ZjhlN2FiMjQ3ODJiZDMwOTZmMGM2OTZlZTNkID0gJCgnPGRpdiBpZD0iaHRtbF9kMmEzNGY4ZTdhYjI0NzgyYmQzMDk2ZjBjNjk2ZWUzZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFSTEVZIEdBUkRFTlMgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82NWFjYzE2OGRjMTQ0ZTFhYjlkZGJmMzhmN2Y2YjExYS5zZXRDb250ZW50KGh0bWxfZDJhMzRmOGU3YWIyNDc4MmJkMzA5NmYwYzY5NmVlM2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODIyNjkxNTZjNjNjNDQxYzlkMjM2OGJjNzkwYmRhMzMuYmluZFBvcHVwKHBvcHVwXzY1YWNjMTY4ZGMxNDRlMWFiOWRkYmYzOGY3ZjZiMTFhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNiNzZhZDAyNWM0MjQzZjQ5OWFkMzlmNjBhMzJlOGNiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNjMxNTYyMiwtMC4xOTUwMjU4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2YzMTc3YWI1YzE1YzQ3MWM5MjhiNDc1N2RhMzFlNmVjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNlYTNhYWUyYzliYzRkMzVhOTNhZmJkZTZiY2JlMDNjID0gJCgnPGRpdiBpZD0iaHRtbF8zZWEzYWFlMmM5YmM0ZDM1YTkzYWZiZGU2YmNiZTAzYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFSTVNXT1JUSCBXQVkgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMzE3N2FiNWMxNWM0NzFjOTI4YjQ3NTdkYTMxZTZlYy5zZXRDb250ZW50KGh0bWxfM2VhM2FhZTJjOWJjNGQzNWE5M2FmYmRlNmJjYmUwM2MpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2I3NmFkMDI1YzQyNDNmNDk5YWQzOWY2MGEzMmU4Y2IuYmluZFBvcHVwKHBvcHVwX2YzMTc3YWI1YzE1YzQ3MWM5MjhiNDc1N2RhMzFlNmVjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU4MzgyOTBhMDE1YjRjODU5NGUwZDE4NTFjYWY3OTZkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMzA1Mzk1LC03MS4zMzgwNTczXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZlODlhZDMxN2ViNTQzM2M5YmE3NzI1ZWMwZWE3NmJmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZiNTE0ZmYxOTU1NTRiOWViYWNkMjQyNzc4ZTQ2MzBmID0gJCgnPGRpdiBpZD0iaHRtbF9mYjUxNGZmMTk1NTU0YjllYmFjZDI0Mjc3OGU0NjMwZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEFSV09PRCBST0FEIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmU4OWFkMzE3ZWI1NDMzYzliYTc3MjVlYzBlYTc2YmYuc2V0Q29udGVudChodG1sX2ZiNTE0ZmYxOTU1NTRiOWViYWNkMjQyNzc4ZTQ2MzBmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU4MzgyOTBhMDE1YjRjODU5NGUwZDE4NTFjYWY3OTZkLmJpbmRQb3B1cChwb3B1cF82ZTg5YWQzMTdlYjU0MzNjOWJhNzcyNWVjMGVhNzZiZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80ZmZmODM5MzE4MDQ0ZWFlYjY0Nzc1YTJmMjZhNzgzMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ4MzQ4NDgsLTAuMDU0NzA3M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wZTEzOTM1MjcwMzQ0NjBhYTc4MmU5NWUxN2E5OTRiNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MDZmN2NhZDc3YzQ0MjgxODE2M2M5ZWJhN2E5MzY1MyA9ICQoJzxkaXYgaWQ9Imh0bWxfNDA2ZjdjYWQ3N2M0NDI4MTgxNjNjOWViYTdhOTM2NTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhBVENIQU0gUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBlMTM5MzUyNzAzNDQ2MGFhNzgyZTk1ZTE3YTk5NGI1LnNldENvbnRlbnQoaHRtbF80MDZmN2NhZDc3YzQ0MjgxODE2M2M5ZWJhN2E5MzY1Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80ZmZmODM5MzE4MDQ0ZWFlYjY0Nzc1YTJmMjZhNzgzMi5iaW5kUG9wdXAocG9wdXBfMGUxMzkzNTI3MDM0NDYwYWE3ODJlOTVlMTdhOTk0YjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2Y1MTc3NzkxOTk2NDY2OGIxZWVkOTllNDNjZjQ2NjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1Mi43MDAzMDcxLC0yLjk2NDkxODJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWIxMjRlZTI1NTU4NGUxMGFiODZmMjg4ODQ1ODExMmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWM2MzM0MzRkYWM4NDQyNmI4MWFiZDc5ZGI5ZDQ5ZTcgPSAkKCc8ZGl2IGlkPSJodG1sXzFjNjMzNDM0ZGFjODQ0MjZiODFhYmQ3OWRiOWQ0OWU3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IRUFUSCBEUklWRSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ViMTI0ZWUyNTU1ODRlMTBhYjg2ZjI4ODg0NTgxMTJlLnNldENvbnRlbnQoaHRtbF8xYzYzMzQzNGRhYzg0NDI2YjgxYWJkNzlkYjlkNDllNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZjUxNzc3OTE5OTY0NjY4YjFlZWQ5OWU0M2NmNDY2OS5iaW5kUG9wdXAocG9wdXBfZWIxMjRlZTI1NTU4NGUxMGFiODZmMjg4ODQ1ODExMmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmFjYzdmNjRjZDVlNDMzZGIyMjQxZjhiNjk5MGYwNjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41NTQ5ODg4LC0wLjE2NzUxMTFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWYxMWE1NTM5NmQ2NDE0YmFhOWVhYjQ3MWZmZjdiNzQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjRkNDdmZGRlMWRkNDRmYTljMjI4NWFlYTgwMDdiM2UgPSAkKCc8ZGl2IGlkPSJodG1sXzY0ZDQ3ZmRkZTFkZDQ0ZmE5YzIyODVhZWE4MDA3YjNlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IRUFUSCBIVVJTVCBST0FEIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWYxMWE1NTM5NmQ2NDE0YmFhOWVhYjQ3MWZmZjdiNzQuc2V0Q29udGVudChodG1sXzY0ZDQ3ZmRkZTFkZDQ0ZmE5YzIyODVhZWE4MDA3YjNlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZhY2M3ZjY0Y2Q1ZTQzM2RiMjI0MWY4YjY5OTBmMDY1LmJpbmRQb3B1cChwb3B1cF85ZjExYTU1Mzk2ZDY0MTRiYWE5ZWFiNDcxZmZmN2I3NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82OGY3NDkxOWVjY2Q0OTk3YWM5ZjgxNjVkNDAyZmM5ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM0LjI4Mjc0MzMsLTExOS4xNjczMDIyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E0NzVmYmJkYTY4NjRkZjJiN2I3ZmNkMGFiMzIyZTczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZiNjkxYmUwNDQzOTRhMjRiNzZkODZkZWQzMGYwY2YwID0gJCgnPGRpdiBpZD0iaHRtbF82YjY5MWJlMDQ0Mzk0YTI0Yjc2ZDg2ZGVkMzBmMGNmMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SEVOREVSU09OIFJPQUQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNDc1ZmJiZGE2ODY0ZGYyYjdiN2ZjZDBhYjMyMmU3My5zZXRDb250ZW50KGh0bWxfNmI2OTFiZTA0NDM5NGEyNGI3NmQ4NmRlZDMwZjBjZjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjhmNzQ5MTllY2NkNDk5N2FjOWY4MTY1ZDQwMmZjOWUuYmluZFBvcHVwKHBvcHVwX2E0NzVmYmJkYTY4NjRkZjJiN2I3ZmNkMGFiMzIyZTczKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJkZWM1YjY0MmVlZTQyYjZhNzMzY2VmNTgyZDg5MTJjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTQ4MTA2OSwtMC4wOTgwMDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGY1MDAyYjgyNjgwNDZjMDlkZDQwZGJmNGMzZWM2ZjAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmQyZmQ1ZDY5YzA0NDUyM2IwMzFhZWYzZjI1MjM5OTAgPSAkKCc8ZGl2IGlkPSJodG1sXzZkMmZkNWQ2OWMwNDQ1MjNiMDMxYWVmM2YyNTIzOTkwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ISUdIQlVSWSBORVcgUEFSSyBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBmNTAwMmI4MjY4MDQ2YzA5ZGQ0MGRiZjRjM2VjNmYwLnNldENvbnRlbnQoaHRtbF82ZDJmZDVkNjljMDQ0NTIzYjAzMWFlZjNmMjUyMzk5MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZGVjNWI2NDJlZWU0MmI2YTczM2NlZjU4MmQ4OTEyYy5iaW5kUG9wdXAocG9wdXBfMGY1MDAyYjgyNjgwNDZjMDlkZDQwZGJmNGMzZWM2ZjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmVjMWI3MDgyM2MyNGJjOThmMTkyNTE1ZjQ1NGQxZjAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1My44ODI2NzQ4LC04LjA4Mjc3MDVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZmE0NzZjOTIyZTc4NDE4NGIzYjk3ZjYwYzliZjAwZDggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfY2NhMjYzMmYxMDU3NDExN2JkZDJiMjEyMzQ5ZTc2ZjIgPSAkKCc8ZGl2IGlkPSJodG1sX2NjYTI2MzJmMTA1NzQxMTdiZGQyYjIxMjM0OWU3NmYyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ISUxMIFNUUkVFVCBDbHVzdGVyIDE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ZhNDc2YzkyMmU3ODQxODRiM2I5N2Y2MGM5YmYwMGQ4LnNldENvbnRlbnQoaHRtbF9jY2EyNjMyZjEwNTc0MTE3YmRkMmIyMTIzNDllNzZmMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iZWMxYjcwODIzYzI0YmM5OGYxOTI1MTVmNDU0ZDFmMC5iaW5kUG9wdXAocG9wdXBfZmE0NzZjOTIyZTc4NDE4NGIzYjk3ZjYwYzliZjAwZDgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzNmNjg5NzhkNGIyNGExYjhmNzMzYWQ5MzQxODY3YjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MC42NzU3MDUsLTEuMDk5NTg3N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yODY2YThhZGJlY2U0YjYxYWRhM2FkMjg0YjU1NWFlMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yNjljMzIxODI2MWM0ZWI5YTA0OTgwMTE3MjA5NmU3MyA9ICQoJzxkaXYgaWQ9Imh0bWxfMjY5YzMyMTgyNjFjNGViOWEwNDk4MDExNzIwOTZlNzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhJTExXQVkgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yODY2YThhZGJlY2U0YjYxYWRhM2FkMjg0YjU1NWFlMy5zZXRDb250ZW50KGh0bWxfMjY5YzMyMTgyNjFjNGViOWEwNDk4MDExNzIwOTZlNzMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzNmNjg5NzhkNGIyNGExYjhmNzMzYWQ5MzQxODY3YjEuYmluZFBvcHVwKHBvcHVwXzI4NjZhOGFkYmVjZTRiNjFhZGEzYWQyODRiNTU1YWUzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzgzYzFkYWU0YzIwNTRiMzJhZDc4OWQzMzkxYjA2ZjhmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTIuOTg1NjEzNywtMS4yMzA4MjM5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhiMzZlODJmMmE3ZDQ2ZTE4YjkxMzIyMjUyMDMyYTJjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZiYTg0NjlhMWE0NTQyN2Y5YzJkNmRlYTZhNTk4ZjlkID0gJCgnPGRpdiBpZD0iaHRtbF9mYmE4NDY5YTFhNDU0MjdmOWMyZDZkZWE2YTU5OGY5ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SE9MQk9STiBDTE9TRSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhiMzZlODJmMmE3ZDQ2ZTE4YjkxMzIyMjUyMDMyYTJjLnNldENvbnRlbnQoaHRtbF9mYmE4NDY5YTFhNDU0MjdmOWMyZDZkZWE2YTU5OGY5ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84M2MxZGFlNGMyMDU0YjMyYWQ3ODlkMzM5MWIwNmY4Zi5iaW5kUG9wdXAocG9wdXBfOGIzNmU4MmYyYTdkNDZlMThiOTEzMjIyNTIwMzJhMmMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWM3ODhhZjJkNDE1NDQ4NmI1MzY1Y2QzNjhiYTM5YWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTg3NTI1LC0wLjIwMjEyOTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzAwYjVlYiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDQyNmE5NzJjYzlmNGRhMzhjNWFjY2JlYTEzYmRkODYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjQxMDAyYWNmMzMzNGNhODhlMjg0YmMyMDU2MTAzNzUgPSAkKCc8ZGl2IGlkPSJodG1sX2Y0MTAwMmFjZjMzMzRjYTg4ZTI4NGJjMjA1NjEwMzc1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IT0xMQU5EIFBBUksgUk9BRCBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ0MjZhOTcyY2M5ZjRkYTM4YzVhY2NiZWExM2JkZDg2LnNldENvbnRlbnQoaHRtbF9mNDEwMDJhY2YzMzM0Y2E4OGUyODRiYzIwNTYxMDM3NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85Yzc4OGFmMmQ0MTU0NDg2YjUzNjVjZDM2OGJhMzlhYi5iaW5kUG9wdXAocG9wdXBfNDQyNmE5NzJjYzlmNGRhMzhjNWFjY2JlYTEzYmRkODYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2VhMjZhM2UyYmJlNDRhODkxYjc1YWRhMWRmN2RkZmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NzUwMDUxLC0wLjAwNjYwNDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzBlMzNhMmRhOWM3NDY0MWIzZmNhZjgxOWRhMTVhYzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDQ4YzFlMzliOGNjNDhmMzk4ZWEyZTViNmJlMzVhODkgPSAkKCc8ZGl2IGlkPSJodG1sXzA0OGMxZTM5YjhjYzQ4ZjM5OGVhMmU1YjZiZTM1YTg5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IWURFIFZBTEUgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MGUzM2EyZGE5Yzc0NjQxYjNmY2FmODE5ZGExNWFjMS5zZXRDb250ZW50KGh0bWxfMDQ4YzFlMzliOGNjNDhmMzk4ZWEyZTViNmJlMzVhODkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2VhMjZhM2UyYmJlNDRhODkxYjc1YWRhMWRmN2RkZmYuYmluZFBvcHVwKHBvcHVwXzcwZTMzYTJkYTljNzQ2NDFiM2ZjYWY4MTlkYTE1YWMxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRhM2Q3YWY2M2Q1ZTQxNDA5NWQ3ZmViZWMzY2M1ZGZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMzA5MzY5OCwtNzEuMzQ5MDIwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mZjg1YTM2M2FmOWU0N2RkYjY4NmE3ZmU1MjBkZmZmMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kZmZiZDg4ZjZlOTY0M2U0YmRlYzIyNmJkMTYwNWUxZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZGZmYmQ4OGY2ZTk2NDNlNGJkZWMyMjZiZDE2MDVlMWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPklSVklORyBST0FEIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmY4NWEzNjNhZjllNDdkZGI2ODZhN2ZlNTIwZGZmZjIuc2V0Q29udGVudChodG1sX2RmZmJkODhmNmU5NjQzZTRiZGVjMjI2YmQxNjA1ZTFmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRhM2Q3YWY2M2Q1ZTQxNDA5NWQ3ZmViZWMzY2M1ZGZhLmJpbmRQb3B1cChwb3B1cF9mZjg1YTM2M2FmOWU0N2RkYjY4NmE3ZmU1MjBkZmZmMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lYWM4NDI5Y2ZhYmE0YWE1OTRhZWY5OTE5ZWU2M2QzYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzE0LjY4MjEwOTU1LDEyMS4wMjU3ODc1NDE4NTg2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMjMxNmNlZjcwN2I0ZWIxYmMyNTVmODU5YTNkM2IwOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNTE3ZWVkNmM4MDY0MTBkOTMyNjJiMGQzZjY1MGQ3MCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzUxN2VlZDZjODA2NDEwZDkzMjYyYjBkM2Y2NTBkNzAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkpVTkNUSU9OIFBMQUNFIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTIzMTZjZWY3MDdiNGViMWJjMjU1Zjg1OWEzZDNiMDguc2V0Q29udGVudChodG1sXzM1MTdlZWQ2YzgwNjQxMGQ5MzI2MmIwZDNmNjUwZDcwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VhYzg0MjljZmFiYTRhYTU5NGFlZjk5MTllZTYzZDNjLmJpbmRQb3B1cChwb3B1cF8xMjMxNmNlZjcwN2I0ZWIxYmMyNTVmODU5YTNkM2IwOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84ZjJjNTk0ZDhhZGY0YmQ2ODc0NGFiY2NlMDNlODI1MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUxNjI5MDUsLTAuMTkzMjI0NDg0NDk2MjA3NTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjllNDA4Yjk4N2I1NGY4NWEwNDk3N2JhMmFhNjc2YzYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTE0MzljMTgwOTIwNDY2MmJhNTI4NGVhNDVkZmYwNGMgPSAkKCc8ZGl2IGlkPSJodG1sX2UxNDM5YzE4MDkyMDQ2NjJiYTUyODRlYTQ1ZGZmMDRjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LSUxEQVJFIEdBUkRFTlMgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yOWU0MDhiOTg3YjU0Zjg1YTA0OTc3YmEyYWE2NzZjNi5zZXRDb250ZW50KGh0bWxfZTE0MzljMTgwOTIwNDY2MmJhNTI4NGVhNDVkZmYwNGMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGYyYzU5NGQ4YWRmNGJkNjg3NDRhYmNjZTAzZTgyNTEuYmluZFBvcHVwKHBvcHVwXzI5ZTQwOGI5ODdiNTRmODVhMDQ5NzdiYTJhYTY3NmM2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RiM2UwNDRmYzVjOTRmMWQ5MmFkYmFhNmIwOWE2NDNmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTEyNTcxNCwyLjk2ZS0wNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iZGNjNzhhZTYxMGY0NGJiYjQ4ODg4NzBjOTc1OTM4MyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNzgzNjE2ZTk3YTU0OTc1ODBkOGZlZjZmYTVjM2IxMCA9ICQoJzxkaXYgaWQ9Imh0bWxfYTc4MzYxNmU5N2E1NDk3NTgwZDhmZWY2ZmE1YzNiMTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxFQU1PVVRIIFJPQUQgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iZGNjNzhhZTYxMGY0NGJiYjQ4ODg4NzBjOTc1OTM4My5zZXRDb250ZW50KGh0bWxfYTc4MzYxNmU5N2E1NDk3NTgwZDhmZWY2ZmE1YzNiMTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGIzZTA0NGZjNWM5NGYxZDkyYWRiYWE2YjA5YTY0M2YuYmluZFBvcHVwKHBvcHVwX2JkY2M3OGFlNjEwZjQ0YmJiNDg4ODg3MGM5NzU5MzgzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NkYjJmOWUyYmY2ODQ4YmJhMDI2Njc4MjU4Njg5YTk4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuMzIzMDYzOCwtNi4yNjczNjQ5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEwY2VjMDFlZmRlMzQ4N2I5NWRmMDIzOTgyZjk3NmJlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2VlYWYwODc0ZTQ4NTQwOWE5MmM3MGM4OTZhYzA1ZjliID0gJCgnPGRpdiBpZD0iaHRtbF9lZWFmMDg3NGU0ODU0MDlhOTJjNzBjODk2YWMwNWY5YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TEVJTlNURVIgU1FVQVJFIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTBjZWMwMWVmZGUzNDg3Yjk1ZGYwMjM5ODJmOTc2YmUuc2V0Q29udGVudChodG1sX2VlYWYwODc0ZTQ4NTQwOWE5MmM3MGM4OTZhYzA1ZjliKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NkYjJmOWUyYmY2ODQ4YmJhMDI2Njc4MjU4Njg5YTk4LmJpbmRQb3B1cChwb3B1cF8xMGNlYzAxZWZkZTM0ODdiOTVkZjAyMzk4MmY5NzZiZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hM2M3ZWM2NmFmYzY0YzA5OGEwMzYxMzI2Y2UyY2UxOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ3Njg0MDcsLTAuMjA0NjU3OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ZTdjNzRjMGFmYTA0YmUyOTFlNGZmM2ZlMjFiZWU2ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kMGExNTFhNjlmMWI0ZjZmYWNmZWE4Njc1NjQzYWVkYyA9ICQoJzxkaXYgaWQ9Imh0bWxfZDBhMTUxYTY5ZjFiNGY2ZmFjZmVhODY3NTY0M2FlZGMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxJTFlWSUxMRSBST0FEIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmU3Yzc0YzBhZmEwNGJlMjkxZTRmZjNmZTIxYmVlNmUuc2V0Q29udGVudChodG1sX2QwYTE1MWE2OWYxYjRmNmZhY2ZlYTg2NzU2NDNhZWRjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2EzYzdlYzY2YWZjNjRjMDk4YTAzNjEzMjZjZTJjZTE4LmJpbmRQb3B1cChwb3B1cF82ZTdjNzRjMGFmYTA0YmUyOTFlNGZmM2ZlMjFiZWU2ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MzcwN2RkMzA0MjU0YTZhOWE1YzYxZjg1YzA1YjE4ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5NjYxNjcsLTAuMTQ4MDI2Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84N2JmYjdjYTFjYzI0MmRhOGJkNzUyY2Y4NzkzNjJmNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NThjNjkzODVlZmE0NjJjYWM0NjU1YTA2ZDY3NTkwYSA9ICQoJzxkaXYgaWQ9Imh0bWxfNzU4YzY5Mzg1ZWZhNDYyY2FjNDY1NWEwNmQ2NzU5MGEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxPV0VSIEJFTEdSQVZFIFNUUkVFVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg3YmZiN2NhMWNjMjQyZGE4YmQ3NTJjZjg3OTM2MmY2LnNldENvbnRlbnQoaHRtbF83NThjNjkzODVlZmE0NjJjYWM0NjU1YTA2ZDY3NTkwYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84MzcwN2RkMzA0MjU0YTZhOWE1YzYxZjg1YzA1YjE4ZS5iaW5kUG9wdXAocG9wdXBfODdiZmI3Y2ExY2MyNDJkYThiZDc1MmNmODc5MzYyZjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTBiNWM1YWQyNjYyNDU1NmJmNzI1NDYzNGFmNmQzOTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS41NTEzNzcxLC0xMDEuNjA3OTUyMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMzI1YTIwNDdiZmU0OGU2YjA1MDhhMTJiYTRkOWUzYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iOGI3M2Q5ZjQ0Yzk0ZGZjYjg0N2VlMjQ4ZmVlMDNkNCA9ICQoJzxkaXYgaWQ9Imh0bWxfYjhiNzNkOWY0NGM5NGRmY2I4NDdlZTI0OGZlZTAzZDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxZT05TIFBMQUNFIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTMyNWEyMDQ3YmZlNDhlNmIwNTA4YTEyYmE0ZDllM2Muc2V0Q29udGVudChodG1sX2I4YjczZDlmNDRjOTRkZmNiODQ3ZWUyNDhmZWUwM2Q0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2EwYjVjNWFkMjY2MjQ1NTZiZjcyNTQ2MzRhZjZkMzkxLmJpbmRQb3B1cChwb3B1cF8xMzI1YTIwNDdiZmU0OGU2YjA1MDhhMTJiYTRkOWUzYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMjU2OGMyZjcwYTc0YTY2ODc3NTVjYzdhOTZkOGViMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjU1MDgyMjMsLTAuMTc3NzM5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lODAyZDRlN2ZiMjU0MGY2YTRlN2ZlNDNhOTI1ZjdkZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYWFhODg0OTA5MzM0NmQxODM3MjkxODJhMzBiZmIzZiA9ICQoJzxkaXYgaWQ9Imh0bWxfM2FhYTg4NDkwOTMzNDZkMTgzNzI5MTgyYTMwYmZiM2YiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1BUkVTRklFTEQgR0FSREVOUyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U4MDJkNGU3ZmIyNTQwZjZhNGU3ZmU0M2E5MjVmN2RlLnNldENvbnRlbnQoaHRtbF8zYWFhODg0OTA5MzM0NmQxODM3MjkxODJhMzBiZmIzZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMjU2OGMyZjcwYTc0YTY2ODc3NTVjYzdhOTZkOGViMC5iaW5kUG9wdXAocG9wdXBfZTgwMmQ0ZTdmYjI1NDBmNmE0ZTdmZTQzYTkyNWY3ZGUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWRiOTcyZTUwY2MwNGQxOGEyMDI0ZmYwYjNlY2E4NjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszMy44NTE4MzE3LC0xMTcuMjMyMzMxOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNDk1ZmJjZjc2MDI0MjczOTY3MWIwNGI5OTg0NGUxNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82N2Q5MGYzZTExYTc0YmVmYmU1MzA2ODc5M2FkNjY4NyA9ICQoJzxkaXYgaWQ9Imh0bWxfNjdkOTBmM2UxMWE3NGJlZmJlNTMwNjg3OTNhZDY2ODciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1BUktIQU0gU1RSRUVUIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTQ5NWZiY2Y3NjAyNDI3Mzk2NzFiMDRiOTk4NDRlMTcuc2V0Q29udGVudChodG1sXzY3ZDkwZjNlMTFhNzRiZWZiZTUzMDY4NzkzYWQ2Njg3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFkYjk3MmU1MGNjMDRkMThhMjAyNGZmMGIzZWNhODY5LmJpbmRQb3B1cChwb3B1cF9hNDk1ZmJjZjc2MDI0MjczOTY3MWIwNGI5OTg0NGUxNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YzZhZDU3NTUyZTY0NGRkYTUzMTk5ZjJkZjFmNWVmNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy03LjEyODAzODgsNTIuNzQ0ODM5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNzU2NzNiNjExYjc0ZmZiYmM4NzIzYWE4ZDFlODAxMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yYjgzYmM2MjI1YzA0OWFlODg4MDc2YTk0ZDNjMWEyNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmI4M2JjNjIyNWMwNDlhZTg4ODA3NmE5NGQzYzFhMjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1JTEtZIFdBWSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y3NTY3M2I2MTFiNzRmZmJiYzg3MjNhYThkMWU4MDEwLnNldENvbnRlbnQoaHRtbF8yYjgzYmM2MjI1YzA0OWFlODg4MDc2YTk0ZDNjMWEyNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83YzZhZDU3NTUyZTY0NGRkYTUzMTk5ZjJkZjFmNWVmNS5iaW5kUG9wdXAocG9wdXBfZjc1NjczYjYxMWI3NGZmYmJjODcyM2FhOGQxZTgwMTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjE4YTg3YWJkZTQzNDg5YmI0N2RiM2FjM2U3NTU2NWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NTAzMzU0LC0wLjE2MTg1MTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmNlMDkxMGE3ODEzNDQ1MzhmYWYyZGUzODk5MmVmZmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTI5MzEzNzg4M2NjNDdiNDhkZTFhODRkM2E0ZTdkMTMgPSAkKCc8ZGl2IGlkPSJodG1sX2EyOTMxMzc4ODNjYzQ3YjQ4ZGUxYTg0ZDNhNGU3ZDEzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NT1JFTExBIFJPQUQgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82Y2UwOTEwYTc4MTM0NDUzOGZhZjJkZTM4OTkyZWZmYy5zZXRDb250ZW50KGh0bWxfYTI5MzEzNzg4M2NjNDdiNDhkZTFhODRkM2E0ZTdkMTMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjE4YTg3YWJkZTQzNDg5YmI0N2RiM2FjM2U3NTU2NWIuYmluZFBvcHVwKHBvcHVwXzZjZTA5MTBhNzgxMzQ0NTM4ZmFmMmRlMzg5OTJlZmZjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JkMzMzZDEwOGE0NTRmODI5YThmZGMwZTk1MzZhMTE4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuMTQwMDA0NSwtMS4xODY0NDE2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2U4ZTM5MTVjM2YxZDQ5NDU5MTVlZjNiNDY0YjFjMmU2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUxOGFiZTdiYTUzNzRkZDBhN2I0YjY3Y2Q2Y2RmZGY1ID0gJCgnPGRpdiBpZD0iaHRtbF81MThhYmU3YmE1Mzc0ZGQwYTdiNGI2N2NkNmNkZmRmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TU9VTlRWSUVXIENMT1NFIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZThlMzkxNWMzZjFkNDk0NTkxNWVmM2I0NjRiMWMyZTYuc2V0Q29udGVudChodG1sXzUxOGFiZTdiYTUzNzRkZDBhN2I0YjY3Y2Q2Y2RmZGY1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JkMzMzZDEwOGE0NTRmODI5YThmZGMwZTk1MzZhMTE4LmJpbmRQb3B1cChwb3B1cF9lOGUzOTE1YzNmMWQ0OTQ1OTE1ZWYzYjQ2NGIxYzJlNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yMjk1NjVmYzQ5NDg0OTk5OTA5Y2JhYmU2OTE2NzQ2YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ0ODE5ODMsLTAuMTU2NTAzNjg2ODIyMDYwNDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjZiNWRlNjliMDZkNGJkZmFlOWY5M2RlZWU2ODY0ZDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTE1ZGM5ZDVhMTVkNDYxY2FmZGRmNjE4ZjcyNGZjYWUgPSAkKCc8ZGl2IGlkPSJodG1sXzUxNWRjOWQ1YTE1ZDQ2MWNhZmRkZjYxOGY3MjRmY2FlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5OSUdIVElOR0FMRSBTUVVBUkUgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNmI1ZGU2OWIwNmQ0YmRmYWU5ZjkzZGVlZTY4NjRkNC5zZXRDb250ZW50KGh0bWxfNTE1ZGM5ZDVhMTVkNDYxY2FmZGRmNjE4ZjcyNGZjYWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjI5NTY1ZmM0OTQ4NDk5OTkwOWNiYWJlNjkxNjc0NmEuYmluZFBvcHVwKHBvcHVwX2I2YjVkZTY5YjA2ZDRiZGZhZTlmOTNkZWVlNjg2NGQ0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NkNjVjYmRkOTE4YjRjNGJhZTMxNjMzYzZkNzA4MDcxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTgxMzU5OCwtMC4xNzM2NzYyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiMwMGI1ZWIiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y2OTlmY2E4YTI0NjQ1Njg5YTdjOTk4YmI3OGVkYTIyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZjMTg4NDhkOTcyMTQ1NjdiMWE1MzI1NTgyNTEzNDcyID0gJCgnPGRpdiBpZD0iaHRtbF9mYzE4ODQ4ZDk3MjE0NTY3YjFhNTMyNTU4MjUxMzQ3MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Tk9SUklDRSBMRUEgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mNjk5ZmNhOGEyNDY0NTY4OWE3Yzk5OGJiNzhlZGEyMi5zZXRDb250ZW50KGh0bWxfZmMxODg0OGQ5NzIxNDU2N2IxYTUzMjU1ODI1MTM0NzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2Q2NWNiZGQ5MThiNGM0YmFlMzE2MzNjNmQ3MDgwNzEuYmluZFBvcHVwKHBvcHVwX2Y2OTlmY2E4YTI0NjQ1Njg5YTdjOTk4YmI3OGVkYTIyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U0ZWI1MTJhOGJiZjQ2YmNhZWM0MDViZDk5YTVlOWRiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTU3MTE3NCwtMC4xODM2MDQ2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M5ZTkwMWJhMjYzMzRlNTRiNmVhM2Y3NjY0NzMxNjY5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y0YjUyNzk1MWZlYjQzYWM4NGM1OWEyMjdlMjkxYmUwID0gJCgnPGRpdiBpZD0iaHRtbF9mNGI1Mjc5NTFmZWI0M2FjODRjNTlhMjI3ZTI5MWJlMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+T0FLIEhJTEwgUEFSSyBNRVdTIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzllOTAxYmEyNjMzNGU1NGI2ZWEzZjc2NjQ3MzE2Njkuc2V0Q29udGVudChodG1sX2Y0YjUyNzk1MWZlYjQzYWM4NGM1OWEyMjdlMjkxYmUwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U0ZWI1MTJhOGJiZjQ2YmNhZWM0MDViZDk5YTVlOWRiLmJpbmRQb3B1cChwb3B1cF9jOWU5MDFiYTI2MzM0ZTU0YjZlYTNmNzY2NDczMTY2OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZDZmNzdjODNhYWM0YzVmYmFhMDFjZDUxY2M5ZjhmMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ5MDA2OTQsLTAuMDgwMTM2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NDRlNzE0YTM5YjI0ODcwYWRmNTc5YmM0YmJmOWYzNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZDg5Mjg0YjdkMTI0YWI5YjZhNzYwMjgyY2JjMWMxZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGQ4OTI4NGI3ZDEyNGFiOWI2YTc2MDI4MmNiYzFjMWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9MRCBLRU5UIFJPQUQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81NDRlNzE0YTM5YjI0ODcwYWRmNTc5YmM0YmJmOWYzNC5zZXRDb250ZW50KGh0bWxfNGQ4OTI4NGI3ZDEyNGFiOWI2YTc2MDI4MmNiYzFjMWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmQ2Zjc3YzgzYWFjNGM1ZmJhYTAxY2Q1MWNjOWY4ZjMuYmluZFBvcHVwKHBvcHVwXzU0NGU3MTRhMzliMjQ4NzBhZGY1NzliYzRiYmY5ZjM0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzUxNmZhNzg3NjgyNDQ0ZTE4MzIzYjE1OWEwZTcwMmY5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDUuNDA1MTYyNCwtNzUuNjcxMzE3M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xNzQ3NzQzMjExNTk0MjBhYTJiOTMzYTVlNzNkNGJlMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iYzYwNGY4NjI2OTE0ZjkwOTEyZjgxNTExYWVlODFiNSA9ICQoJzxkaXYgaWQ9Imh0bWxfYmM2MDRmODYyNjkxNGY5MDkxMmY4MTUxMWFlZTgxYjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9OU0xPVyBDUkVTQ0VOVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE3NDc3NDMyMTE1OTQyMGFhMmI5MzNhNWU3M2Q0YmUwLnNldENvbnRlbnQoaHRtbF9iYzYwNGY4NjI2OTE0ZjkwOTEyZjgxNTExYWVlODFiNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81MTZmYTc4NzY4MjQ0NGUxODMyM2IxNTlhMGU3MDJmOS5iaW5kUG9wdXAocG9wdXBfMTc0Nzc0MzIxMTU5NDIwYWEyYjkzM2E1ZTczZDRiZTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTcyNjAzODM4YjFjNGUwMzkyNmM0MWE3NDFmNWM5ZjMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODc2Mzc1LC0wLjE2MTM4MThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzAwYjVlYiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTUxNjA0YmM3YzhmNGNjYThjMzg0N2ZmYmY2ZjUyYWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2UxMTVjZDYyMDUwNDM4ZmE2OTExZDMwYjJkODM2YzcgPSAkKCc8ZGl2IGlkPSJodG1sXzNlMTE1Y2Q2MjA1MDQzOGZhNjkxMWQzMGIyZDgzNmM3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5PUk1PTkRFIEdBVEUgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85NTE2MDRiYzdjOGY0Y2NhOGMzODQ3ZmZiZjZmNTJhYS5zZXRDb250ZW50KGh0bWxfM2UxMTVjZDYyMDUwNDM4ZmE2OTExZDMwYjJkODM2YzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTcyNjAzODM4YjFjNGUwMzkyNmM0MWE3NDFmNWM5ZjMuYmluZFBvcHVwKHBvcHVwXzk1MTYwNGJjN2M4ZjRjY2E4YzM4NDdmZmJmNmY1MmFhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVlODFjMzI3NWQyZjRlZDg5N2ZhZTRiNTE1MTdhNTc1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbLTM3LjU4MDA2MDIsMTQ0LjkyMDczMjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODEyNDY2MjAzYWQ3NGYzN2IxODRiZjFkNDM2NjliMzggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODkzYWQ3MzMxNDAzNGQ4Yjk1Mjk4ODE4MDAwMmVjMGQgPSAkKCc8ZGl2IGlkPSJodG1sXzg5M2FkNzMzMTQwMzRkOGI5NTI5ODgxODAwMDJlYzBkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QQURESU5HVE9OIFNUUkVFVCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgxMjQ2NjIwM2FkNzRmMzdiMTg0YmYxZDQzNjY5YjM4LnNldENvbnRlbnQoaHRtbF84OTNhZDczMzE0MDM0ZDhiOTUyOTg4MTgwMDAyZWMwZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81ZTgxYzMyNzVkMmY0ZWQ4OTdmYWU0YjUxNTE3YTU3NS5iaW5kUG9wdXAocG9wdXBfODEyNDY2MjAzYWQ3NGYzN2IxODRiZjFkNDM2NjliMzgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2M0NGMyMzZhYzk4NDUyZTk2NDE2ZmJjYzhjODdmMWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszNS43MzcxNTgyLC03OC44MjY1NjM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg4Nzk3ZjdiMjVhYjRlMjE4YWQ4NWEyOTJkOWQ3NGY2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA0OTU3MTI1YzcxNTRiYzg5OWFlZGRkZGNhMjZhMjlhID0gJCgnPGRpdiBpZD0iaHRtbF8wNDk1NzEyNWM3MTU0YmM4OTlhZWRkZGRjYTI2YTI5YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEFMQUNFIENPVVJUIENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODg3OTdmN2IyNWFiNGUyMThhZDg1YTI5MmQ5ZDc0ZjYuc2V0Q29udGVudChodG1sXzA0OTU3MTI1YzcxNTRiYzg5OWFlZGRkZGNhMjZhMjlhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNjNDRjMjM2YWM5ODQ1MmU5NjQxNmZiY2M4Yzg3ZjFjLmJpbmRQb3B1cChwb3B1cF84ODc5N2Y3YjI1YWI0ZTIxOGFkODVhMjkyZDlkNzRmNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMmUxYzYzNjdkNTk0NTJjYWM0MTE4ZGQ3OWUwYjZkMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUzLjY5MDI4OTQsLTIuNTY4NTI5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMmJkY2MyOTg0OGM0YzY1YTkzZTAwZjA0NDc0OWJhZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lM2NiZjc1YWUzZjQ0YTU1OWVmODBjYWY3ZWQzOGFiOSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTNjYmY3NWFlM2Y0NGE1NTllZjgwY2FmN2VkMzhhYjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBUktFIFJPQUQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wMmJkY2MyOTg0OGM0YzY1YTkzZTAwZjA0NDc0OWJhZC5zZXRDb250ZW50KGh0bWxfZTNjYmY3NWFlM2Y0NGE1NTllZjgwY2FmN2VkMzhhYjkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDJlMWM2MzY3ZDU5NDUyY2FjNDExOGRkNzllMGI2ZDIuYmluZFBvcHVwKHBvcHVwXzAyYmRjYzI5ODQ4YzRjNjVhOTNlMDBmMDQ0NzQ5YmFkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdjMzk5MDBjYmI0ODRjYWE4ODAwMmE5M2NiNWFlN2MwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTIuOTIzMzIsLTEuMTk5MTcwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNmNlZGFjYTIwNTg0MGM3YmFlNGY4N2VjYzRjOGYzMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYzg5ZjkxMWJjN2I0NjMzYTBkNTM1MDlkNjQ3ZGRhMyA9ICQoJzxkaXYgaWQ9Imh0bWxfZWM4OWY5MTFiYzdiNDYzM2EwZDUzNTA5ZDY0N2RkYTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBUlImIzM5O1MgV0FZIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjZjZWRhY2EyMDU4NDBjN2JhZTRmODdlY2M0YzhmMzEuc2V0Q29udGVudChodG1sX2VjODlmOTExYmM3YjQ2MzNhMGQ1MzUwOWQ2NDdkZGEzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdjMzk5MDBjYmI0ODRjYWE4ODAwMmE5M2NiNWFlN2MwLmJpbmRQb3B1cChwb3B1cF9iNmNlZGFjYTIwNTg0MGM3YmFlNGY4N2VjYzRjOGYzMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iNDVkOWU2NGE1ZDU0Mjg1ODA4NDAzNjA1OTVlODA0YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ3MTg2ODQsLTAuMTk5ODE3MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NDc1N2IzZTA3MDQ0YjBiOGY0MGViYzhlMmI0MTM5YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MmY1NDkzZWNmMDQ0MTVjYjI1NTExNTk4OTFlNjMxNyA9ICQoJzxkaXYgaWQ9Imh0bWxfNDJmNTQ5M2VjZjA0NDE1Y2IyNTUxMTU5ODkxZTYzMTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBBUlNPTlMgR0FURSBNRVdTIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzQ3NTdiM2UwNzA0NGIwYjhmNDBlYmM4ZTJiNDEzOWMuc2V0Q29udGVudChodG1sXzQyZjU0OTNlY2YwNDQxNWNiMjU1MTE1OTg5MWU2MzE3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I0NWQ5ZTY0YTVkNTQyODU4MDg0MDM2MDU5NWU4MDRjLmJpbmRQb3B1cChwb3B1cF83NDc1N2IzZTA3MDQ0YjBiOGY0MGViYzhlMmI0MTM5Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yZmE0ZmU3YzM3Mjg0ZDIxYWU0ZjM0MmM3NmFlOGRiMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjA4NjU5ODQsMS4xNzY0ODg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM0NDUzNjhmM2RjMDRmN2NiZjE0NzdkMTE4M2JmY2I4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FjOTQ1YjBjMjgzMTQ1ZmFiNmY4MWU0OGQ4NGJiMWViID0gJCgnPGRpdiBpZD0iaHRtbF9hYzk0NWIwYzI4MzE0NWZhYjZmODFlNDhkODRiYjFlYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEFWSUxJT04gUk9BRCBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM0NDUzNjhmM2RjMDRmN2NiZjE0NzdkMTE4M2JmY2I4LnNldENvbnRlbnQoaHRtbF9hYzk0NWIwYzI4MzE0NWZhYjZmODFlNDhkODRiYjFlYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZmE0ZmU3YzM3Mjg0ZDIxYWU0ZjM0MmM3NmFlOGRiMS5iaW5kUG9wdXAocG9wdXBfMzQ0NTM2OGYzZGMwNGY3Y2JmMTQ3N2QxMTgzYmZjYjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmMxMDk2NmY5MmQwNDBiYmEzNmZhYTExOGU1YjFkMjYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MTE2NDg2LC0wLjE5Nzc5MjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjAzZjQ0ZjJmNmE5NGI1ZDhlMDAxOTY3ZGQ1NWQ5NGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjVkNDg2MGY1NWU5NGVkMTg3YzFjNmM0NDQ3OWE0YzkgPSAkKCc8ZGl2IGlkPSJodG1sXzI1ZDQ4NjBmNTVlOTRlZDE4N2MxYzZjNDQ0NzlhNGM5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QRU1CUklER0UgQ1JFU0NFTlQgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82MDNmNDRmMmY2YTk0YjVkOGUwMDE5NjdkZDU1ZDk0Yi5zZXRDb250ZW50KGh0bWxfMjVkNDg2MGY1NWU5NGVkMTg3YzFjNmM0NDQ3OWE0YzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmMxMDk2NmY5MmQwNDBiYmEzNmZhYTExOGU1YjFkMjYuYmluZFBvcHVwKHBvcHVwXzYwM2Y0NGYyZjZhOTRiNWQ4ZTAwMTk2N2RkNTVkOTRiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZiYTRjNzUzZDg5ZTQxN2Q4M2RiY2I4Nzc0MmZhOTJmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTEyMzYxOSwtMC4xOTgyOTQ3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZlNTFkODQ3NGNlYzQ0Y2Q4OTQ0ZWFjMTZkYTlhNTFiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzFkNzdiOTQ5MmI0NjQzOTA4NDQzZDEwMTRkNjgyMjliID0gJCgnPGRpdiBpZD0iaHRtbF8xZDc3Yjk0OTJiNDY0MzkwODQ0M2QxMDE0ZDY4MjI5YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEVNQlJJREdFIE1FV1MgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mZTUxZDg0NzRjZWM0NGNkODk0NGVhYzE2ZGE5YTUxYi5zZXRDb250ZW50KGh0bWxfMWQ3N2I5NDkyYjQ2NDM5MDg0NDNkMTAxNGQ2ODIyOWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmJhNGM3NTNkODllNDE3ZDgzZGJjYjg3NzQyZmE5MmYuYmluZFBvcHVwKHBvcHVwX2ZlNTFkODQ3NGNlYzQ0Y2Q4OTQ0ZWFjMTZkYTlhNTFiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJmZGU5OTVkYzkyZDRkZTVhNjQ4MGI4NjAyNDlmMjhmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTMuNzUwMDgyNSwtMC4zMjM0MzMzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkxNTM2MjYwZTM3ODQyOWZhZWNiMTczYWNkNjFjN2VjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUwYWQ5MzU0OTA2YTRmNTA4Y2JiNjM3ZGNmNzlkY2M5ID0gJCgnPGRpdiBpZD0iaHRtbF81MGFkOTM1NDkwNmE0ZjUwOGNiYjYzN2RjZjc5ZGNjOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UEVOTklOR1RPTiBTVFJFRVQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MTUzNjI2MGUzNzg0MjlmYWVjYjE3M2FjZDYxYzdlYy5zZXRDb250ZW50KGh0bWxfNTBhZDkzNTQ5MDZhNGY1MDhjYmI2MzdkY2Y3OWRjYzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmZkZTk5NWRjOTJkNGRlNWE2NDgwYjg2MDI0OWYyOGYuYmluZFBvcHVwKHBvcHVwXzkxNTM2MjYwZTM3ODQyOWZhZWNiMTczYWNkNjFjN2VjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc4NWU5NGFkMGRjYTQ2ZjdhNjY5OWFiMWEzZjg4NzYyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTIuMDAxMzYwNCwtMC44MDQyNzc4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiMwMGI1ZWIiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M3OWUyNjYzZDIyMDQ0MDM4OTdiYmM1ZjJhMTUzNzNiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M2MGQ0NTU0MzcyOTRmYzNiNmM0NDM5MGNhMjY4NjE5ID0gJCgnPGRpdiBpZD0iaHRtbF9jNjBkNDU1NDM3Mjk0ZmMzYjZjNDQzOTBjYTI2ODYxOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UElDVE9OIFNUUkVFVCBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M3OWUyNjYzZDIyMDQ0MDM4OTdiYmM1ZjJhMTUzNzNiLnNldENvbnRlbnQoaHRtbF9jNjBkNDU1NDM3Mjk0ZmMzYjZjNDQzOTBjYTI2ODYxOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ODVlOTRhZDBkY2E0NmY3YTY2OTlhYjFhM2Y4ODc2Mi5iaW5kUG9wdXAocG9wdXBfYzc5ZTI2NjNkMjIwNDQwMzg5N2JiYzVmMmExNTM3M2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGYxNWQ5NDQyOTk2NDcwZDlmNGM5MTIwYzUxMDliMDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszOS4zNDkzMzg5LC0xMjMuODE0MTgyMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNDlmNGQ1NDExNWE0YmJjOGI1YzZmZTFmMTFlNzlhMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82ZTNiZTJkMzY3ZDk0NDc3YjlkN2FhY2UzNDQyMjFlZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNmUzYmUyZDM2N2Q5NDQ3N2I5ZDdhYWNlMzQ0MjIxZWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBJTkUgR1JPVkUgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNDlmNGQ1NDExNWE0YmJjOGI1YzZmZTFmMTFlNzlhMy5zZXRDb250ZW50KGh0bWxfNmUzYmUyZDM2N2Q5NDQ3N2I5ZDdhYWNlMzQ0MjIxZWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGYxNWQ5NDQyOTk2NDcwZDlmNGM5MTIwYzUxMDliMDguYmluZFBvcHVwKHBvcHVwX2I0OWY0ZDU0MTE1YTRiYmM4YjVjNmZlMWYxMWU3OWEzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI0NzU4ZDg1NjIzNzRhZDdiY2Y2MjY3NjJkOGMxOGNlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDAuMDIzMjc1NywtMTA1LjI4MzEwOTRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODM1NWEzY2E4NjM4NDcyZWIxODNlZDQwYzQ1ZDE1ZWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzA2YWUyODYwY2M2NDg2YTg3MDY3YzIyZDA5YjEyMmMgPSAkKCc8ZGl2IGlkPSJodG1sXzMwNmFlMjg2MGNjNjQ4NmE4NzA2N2MyMmQwOWIxMjJjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QT1JUTEFORCBQTEFDRSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgzNTVhM2NhODYzODQ3MmViMTgzZWQ0MGM0NWQxNWViLnNldENvbnRlbnQoaHRtbF8zMDZhZTI4NjBjYzY0ODZhODcwNjdjMjJkMDliMTIyYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yNDc1OGQ4NTYyMzc0YWQ3YmNmNjI2NzYyZDhjMThjZS5iaW5kUG9wdXAocG9wdXBfODM1NWEzY2E4NjM4NDcyZWIxODNlZDQwYzQ1ZDE1ZWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTIxZmZjNDNiOTFmNDIzYmJhZTk4M2Y3Y2E4YmUzYTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My4yNTQ2OTY2LC03OS44Njc3OTQ5NzI5NzI5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZWIzODIwZTk0OGE0YmNlOWY1ZmFjMjFhNWM1OTA3OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xZjRiOWY3YmJmYTg0MjEwYjY0NjlkOWQ0NzdjODUyNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMWY0YjlmN2JiZmE4NDIxMGI2NDY5ZDlkNDc3Yzg1MjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBSSU5DRVMgU1FVQVJFIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2ViMzgyMGU5NDhhNGJjZTlmNWZhYzIxYTVjNTkwNzkuc2V0Q29udGVudChodG1sXzFmNGI5ZjdiYmZhODQyMTBiNjQ2OWQ5ZDQ3N2M4NTI1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzkyMWZmYzQzYjkxZjQyM2JiYWU5ODNmN2NhOGJlM2E3LmJpbmRQb3B1cChwb3B1cF8zZWIzODIwZTk0OGE0YmNlOWY1ZmFjMjFhNWM1OTA3OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wYjk5YTBkYjI5NTQ0NDYyODIxNDY2MGYyZTcyOWQwMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUyLjA2NDEzMTEsLTAuNzk2MjY2N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84MDQ5Nzk1MDQwOGQ0YjEwODc1ZDYyOTc0YWQwNzQzOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMTk5MWM3ODg3YTk0NTRjYmZiODBjNzE3ZTNkOGY0MSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTE5OTFjNzg4N2E5NDU0Y2JmYjgwYzcxN2UzZDhmNDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlFVRUVOIEFOTkUgU1RSRUVUIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODA0OTc5NTA0MDhkNGIxMDg3NWQ2Mjk3NGFkMDc0Mzguc2V0Q29udGVudChodG1sX2UxOTkxYzc4ODdhOTQ1NGNiZmI4MGM3MTdlM2Q4ZjQxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzBiOTlhMGRiMjk1NDQ0NjI4MjE0NjYwZjJlNzI5ZDAxLmJpbmRQb3B1cChwb3B1cF84MDQ5Nzk1MDQwOGQ0YjEwODc1ZDYyOTc0YWQwNzQzOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YjVjYTRlNThiOWE0MDM3OTBiNjlkMmE3OTc4OWIzYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjUwNDQ4OTYsLTAuMjk4MTQwNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82MzlhMGZjMTkzYTk0NzdmODE4YjFmYzk1ZTE1YmE4YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jYmYzZGUyMDczNDI0YzhkOWY4MTM0MzMwMWJhZmExNSA9ICQoJzxkaXYgaWQ9Imh0bWxfY2JmM2RlMjA3MzQyNGM4ZDlmODEzNDMzMDFiYWZhMTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlFVRUVOIEFOTkVTIEdST1ZFIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjM5YTBmYzE5M2E5NDc3ZjgxOGIxZmM5NWUxNWJhOGIuc2V0Q29udGVudChodG1sX2NiZjNkZTIwNzM0MjRjOGQ5ZjgxMzQzMzAxYmFmYTE1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdiNWNhNGU1OGI5YTQwMzc5MGI2OWQyYTc5Nzg5YjNjLmJpbmRQb3B1cChwb3B1cF82MzlhMGZjMTkzYTk0NzdmODE4YjFmYzk1ZTE1YmE4Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xZGUwMWYzYzljZDM0ZjgzOTFkZDlhN2M1NTVmMzNkOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ4Njg5MDEsLTAuMTg2NzI2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kODBkNTgzM2YzOTk0Yjk0YmNmOGQyM2E4MmUyMmYxOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZmNmODgxMGZjMjg0YjNlYWVkOGYzNGRjMGViYWUzYiA9ICQoJzxkaXYgaWQ9Imh0bWxfM2ZjZjg4MTBmYzI4NGIzZWFlZDhmMzRkYzBlYmFlM2IiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJFRENMSUZGRSBNRVdTIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDgwZDU4MzNmMzk5NGI5NGJjZjhkMjNhODJlMjJmMTkuc2V0Q29udGVudChodG1sXzNmY2Y4ODEwZmMyODRiM2VhZWQ4ZjM0ZGMwZWJhZTNiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFkZTAxZjNjOWNkMzRmODM5MWRkOWE3YzU1NWYzM2Q4LmJpbmRQb3B1cChwb3B1cF9kODBkNTgzM2YzOTk0Yjk0YmNmOGQyM2E4MmUyMmYxOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNjNiYWRhNjdlZTQ0NzYzOTExN2IzZjg0YjBlODNlMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzM4LjgyODg4MTcsLTc3LjA0NTU2NzRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDgzODQwMDc5YTdlNDM0NTkxNzM3YTljNWRiZGE5ODYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTI3ZjQzZjM2Yzg1NDJiMDkzODZkZGM3ODA1MTY3MTQgPSAkKCc8ZGl2IGlkPSJodG1sXzUyN2Y0M2YzNmM4NTQyYjA5Mzg2ZGRjNzgwNTE2NzE0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5ST1NFIFNRVUFSRSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q4Mzg0MDA3OWE3ZTQzNDU5MTczN2E5YzVkYmRhOTg2LnNldENvbnRlbnQoaHRtbF81MjdmNDNmMzZjODU0MmIwOTM4NmRkYzc4MDUxNjcxNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zNjNiYWRhNjdlZTQ0NzYzOTExN2IzZjg0YjBlODNlMy5iaW5kUG9wdXAocG9wdXBfZDgzODQwMDc5YTdlNDM0NTkxNzM3YTljNWRiZGE5ODYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWIyMmMwZDNmMmIyNGQwMjkyYjIxOTFmZWU3NmRhOTkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1My40ODE0OTE1LC0yLjExMDI3ODRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZThmNGE4NzMxMDdkNDQ3MDkzYTM3MDdhMjAzZDY4ZTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWIzNjJlYTY3NmE1NGVlY2E4NjBkZGE1YzgwYjM4ZjMgPSAkKCc8ZGl2IGlkPSJodG1sX2ViMzYyZWE2NzZhNTRlZWNhODYwZGRhNWM4MGIzOGYzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SWUVDUk9GVCBTVFJFRVQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lOGY0YTg3MzEwN2Q0NDcwOTNhMzcwN2EyMDNkNjhlNS5zZXRDb250ZW50KGh0bWxfZWIzNjJlYTY3NmE1NGVlY2E4NjBkZGE1YzgwYjM4ZjMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMWIyMmMwZDNmMmIyNGQwMjkyYjIxOTFmZWU3NmRhOTkuYmluZFBvcHVwKHBvcHVwX2U4ZjRhODczMTA3ZDQ0NzA5M2EzNzA3YTIwM2Q2OGU1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIxZWQ4MzNjYjYxNTQ3MTRhNTZlOGUwNDg5ZDdhYWU2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNTMzMjYxLC0wLjIwMzYzNzFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmEzZDRhODM3YmMzNDZiYmI4ZmFiYjk5MjZhMjlhNjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmNhNWQ2NzQwZDYyNDRmNmJhOTI0ZDI5MDQxMmViMzAgPSAkKCc8ZGl2IGlkPSJodG1sX2JjYTVkNjc0MGQ2MjQ0ZjZiYTkyNGQyOTA0MTJlYjMwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TQUxVU0JVUlkgUk9BRCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZhM2Q0YTgzN2JjMzQ2YmJiOGZhYmI5OTI2YTI5YTY1LnNldENvbnRlbnQoaHRtbF9iY2E1ZDY3NDBkNjI0NGY2YmE5MjRkMjkwNDEyZWIzMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMWVkODMzY2I2MTU0NzE0YTU2ZThlMDQ4OWQ3YWFlNi5iaW5kUG9wdXAocG9wdXBfNmEzZDRhODM3YmMzNDZiYmI4ZmFiYjk5MjZhMjlhNjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDQzNTI2ZThkYjU0NGY1YWI0NDlmMGVkNWIwY2MyMWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40OTg3NDYzLC0wLjE4OTA3ODddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzAwYjVlYiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDIyMWI3MTRiMTM0NGViNGFlNTA4YzA4MTQwOTVmYTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGJhMmI2N2E0YTM2NDhjOTg0NmZjMmFjMWUxYzNjZDcgPSAkKCc8ZGl2IGlkPSJodG1sX2RiYTJiNjdhNGEzNjQ4Yzk4NDZmYzJhYzFlMWMzY2Q3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TT1VUSCBFTkQgUk9XIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDIyMWI3MTRiMTM0NGViNGFlNTA4YzA4MTQwOTVmYTQuc2V0Q29udGVudChodG1sX2RiYTJiNjdhNGEzNjQ4Yzk4NDZmYzJhYzFlMWMzY2Q3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ0MzUyNmU4ZGI1NDRmNWFiNDQ5ZjBlZDViMGNjMjFkLmJpbmRQb3B1cChwb3B1cF80MjIxYjcxNGIxMzQ0ZWI0YWU1MDhjMDgxNDA5NWZhNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZTgwOTljODE0NjQ0NTNkYWRjOGY4Y2UyOTVmNzM0OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy00Mi4yOTgzMDQ4LDE0Ny4zNzE2NDRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzQzZDQxNWM0OWMxNGM5ZDk3M2M4OTMzZjcxNGRiNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODllNTljM2E1YzA5NGU5ZmEyYzdhNjUyM2E1NzlhMGUgPSAkKCc8ZGl2IGlkPSJodG1sXzg5ZTU5YzNhNWMwOTRlOWZhMmM3YTY1MjNhNTc5YTBlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TT1VUSCBQQVJBREUgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83NDNkNDE1YzQ5YzE0YzlkOTczYzg5MzNmNzE0ZGI0Yy5zZXRDb250ZW50KGh0bWxfODllNTljM2E1YzA5NGU5ZmEyYzdhNjUyM2E1NzlhMGUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2U4MDk5YzgxNDY0NDUzZGFkYzhmOGNlMjk1ZjczNDkuYmluZFBvcHVwKHBvcHVwXzc0M2Q0MTVjNDljMTRjOWQ5NzNjODkzM2Y3MTRkYjRjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzg4NDI4YTZjOWUyMjRiMTA4YWQ4OWM1YTJmZTc3ZTE1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDkwODU1NCwtMC4xNjQ2MTA1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI5ZDY3YWNiZTI5OTQyZWRiMmNmZWUxYTE0NmQzMjQyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc4MjZhMWIzMTZiMzRjOWY5OWRiMDRhMzZkMjY4MzA5ID0gJCgnPGRpdiBpZD0iaHRtbF83ODI2YTFiMzE2YjM0YzlmOTlkYjA0YTM2ZDI2ODMwOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U1BSSU1PTlQgUExBQ0UgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yOWQ2N2FjYmUyOTk0MmVkYjJjZmVlMWExNDZkMzI0Mi5zZXRDb250ZW50KGh0bWxfNzgyNmExYjMxNmIzNGM5Zjk5ZGIwNGEzNmQyNjgzMDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODg0MjhhNmM5ZTIyNGIxMDhhZDg5YzVhMmZlNzdlMTUuYmluZFBvcHVwKHBvcHVwXzI5ZDY3YWNiZTI5OTQyZWRiMmNmZWUxYTE0NmQzMjQyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU2MDczNDNlZWZhNjRmOGFhMDQyY2E5Zjk0NzkyNTYwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDI3MTE1OCwtMC4yMDg1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZWFiNDQxNmY1ZTA0NjRiODc3YzQyMGFlZGY4ZjAyMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNzBkMTcwYzJiNmI0ODg4YjdhZGRlYmQ0NDE1NWRjMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMDcwZDE3MGMyYjZiNDg4OGI3YWRkZWJkNDQxNTVkYzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNUIEFVQllOUyBBVkVOVUUgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zZWFiNDQxNmY1ZTA0NjRiODc3YzQyMGFlZGY4ZjAyMi5zZXRDb250ZW50KGh0bWxfMDcwZDE3MGMyYjZiNDg4OGI3YWRkZWJkNDQxNTVkYzMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTYwNzM0M2VlZmE2NGY4YWEwNDJjYTlmOTQ3OTI1NjAuYmluZFBvcHVwKHBvcHVwXzNlYWI0NDE2ZjVlMDQ2NGI4NzdjNDIwYWVkZjhmMDIyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk1Y2ZlZDRhNTViYzQ3MjViMDc3Mjc3ODUxZjBlNDk5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNzU2Mjk1OSwtMS4yNjE5MTg0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM1N2IxOTU2ZWIyNTQ4ZjA5ZmYyZTQ0NWRmZGYzMjI2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FhYjk5ZGMwZTU0NzQ4NzQ5ZjBkYzdlN2MyZjQyMDZhID0gJCgnPGRpdiBpZD0iaHRtbF9hYWI5OWRjMGU1NDc0ODc0OWYwZGM3ZTdjMmY0MjA2YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U1QgSk9ITiBTVFJFRVQgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zNTdiMTk1NmViMjU0OGYwOWZmMmU0NDVkZmRmMzIyNi5zZXRDb250ZW50KGh0bWxfYWFiOTlkYzBlNTQ3NDg3NDlmMGRjN2U3YzJmNDIwNmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTVjZmVkNGE1NWJjNDcyNWIwNzcyNzc4NTFmMGU0OTkuYmluZFBvcHVwKHBvcHVwXzM1N2IxOTU2ZWIyNTQ4ZjA5ZmYyZTQ0NWRmZGYzMjI2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U0MTg3ZTMzNjE0YTQyMzg5NDBkNzFhMWYwMDBkNGZmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTQuMzI5MjI3OSwtMS40MjkxMjA3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM5M2JlODY0ZTYzYzQ4ZmE5MTRjZWFiNDdiYzZlN2RkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI3Njk1NjMzZTVjODQ0YWI4MjA1NWE3YWIxZGM0ZjFlID0gJCgnPGRpdiBpZD0iaHRtbF8yNzY5NTYzM2U1Yzg0NGFiODIwNTVhN2FiMWRjNGYxZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U1QgU1RFUEhFTlMgR0FSREVOUyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM5M2JlODY0ZTYzYzQ4ZmE5MTRjZWFiNDdiYzZlN2RkLnNldENvbnRlbnQoaHRtbF8yNzY5NTYzM2U1Yzg0NGFiODIwNTVhN2FiMWRjNGYxZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNDE4N2UzMzYxNGE0MjM4OTQwZDcxYTFmMDAwZDRmZi5iaW5kUG9wdXAocG9wdXBfMzkzYmU4NjRlNjNjNDhmYTkxNGNlYWI0N2JjNmU3ZGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmE1MTQ5Zjg4NmUzNDdlMGE1ZjQ2MTg0MTYyNDJmZGQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszNC4wOTgxMzMyLC0xMTguNDU2NzMzMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zOTA0MGQ4ZmJlNWY0MmRhYTU1M2U5ZTJhMjc1NTcwNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMzRiZWUzYTI4ODk0NmYxODY0NjQxMWI3Y2M2YjBiYiA9ICQoJzxkaXYgaWQ9Imh0bWxfMDM0YmVlM2EyODg5NDZmMTg2NDY0MTFiN2NjNmIwYmIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNUUkFERUxMQSBST0FEIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzkwNDBkOGZiZTVmNDJkYWE1NTNlOWUyYTI3NTU3MDYuc2V0Q29udGVudChodG1sXzAzNGJlZTNhMjg4OTQ2ZjE4NjQ2NDExYjdjYzZiMGJiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JhNTE0OWY4ODZlMzQ3ZTBhNWY0NjE4NDE2MjQyZmRkLmJpbmRQb3B1cChwb3B1cF8zOTA0MGQ4ZmJlNWY0MmRhYTU1M2U5ZTJhMjc1NTcwNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMzUzZWVmZDE1NDI0MWFiOTgzYTcwNDdiZDBiMmFhZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWy0yOS44MjM5NzIsMzAuOTE5MjQ3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NGZlOGM5MjQ4NzQ0ZGJhYjNhYzYxNjQzMTQ0YjJjZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMjRlYzc2N2IwMTc0ZWY3ODRmM2RiODEyYjAwODZjYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTI0ZWM3NjdiMDE3NGVmNzg0ZjNkYjgxMmIwMDg2Y2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNZRE5FWSBTVFJFRVQgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84NGZlOGM5MjQ4NzQ0ZGJhYjNhYzYxNjQzMTQ0YjJjZC5zZXRDb250ZW50KGh0bWxfZTI0ZWM3NjdiMDE3NGVmNzg0ZjNkYjgxMmIwMDg2Y2EpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjM1M2VlZmQxNTQyNDFhYjk4M2E3MDQ3YmQwYjJhYWYuYmluZFBvcHVwKHBvcHVwXzg0ZmU4YzkyNDg3NDRkYmFiM2FjNjE2NDMxNDRiMmNkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2UwZTQyZDA5MmQ0YTQ4M2U5ZDI4NDFiM2RkZDQwYzcyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNDg3MDEyMSwtMC4xNjI3NzIyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzVmOGMyYWRiNDc2NTQyNjk4MmJhMmNmODQ4NDAwYzE2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUwM2M3MDEyNTJmMzQ5ZWViMGNjMzA2M2E5ZDcxNTc5ID0gJCgnPGRpdiBpZD0iaHRtbF81MDNjNzAxMjUyZjM0OWVlYjBjYzMwNjNhOWQ3MTU3OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VEVEV09SVEggU1FVQVJFIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWY4YzJhZGI0NzY1NDI2OTgyYmEyY2Y4NDg0MDBjMTYuc2V0Q29udGVudChodG1sXzUwM2M3MDEyNTJmMzQ5ZWViMGNjMzA2M2E5ZDcxNTc5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2UwZTQyZDA5MmQ0YTQ4M2U5ZDI4NDFiM2RkZDQwYzcyLmJpbmRQb3B1cChwb3B1cF81ZjhjMmFkYjQ3NjU0MjY5ODJiYTJjZjg0ODQwMGMxNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMDI5Zjk5Njk2MjQ0YzIyOTFiMWMwNWRhN2NhNmVmMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjQ4ODY4NzMsLTAuMTg2NDI0Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81N2Y0MzhjMjEwYjI0NzY2YjE1YjJjNzA3OWVkMTQ5MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yMjdlNTczMDllNTU0ZDg5OWUyMzlkNThlZDU5YmZjNCA9ICQoJzxkaXYgaWQ9Imh0bWxfMjI3ZTU3MzA5ZTU1NGQ4OTllMjM5ZDU4ZWQ1OWJmYzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRIRSBMSVRUTEUgQk9MVE9OUyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU3ZjQzOGMyMTBiMjQ3NjZiMTViMmM3MDc5ZWQxNDkxLnNldENvbnRlbnQoaHRtbF8yMjdlNTczMDllNTU0ZDg5OWUyMzlkNThlZDU5YmZjNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lMDI5Zjk5Njk2MjQ0YzIyOTFiMWMwNWRhN2NhNmVmMS5iaW5kUG9wdXAocG9wdXBfNTdmNDM4YzIxMGIyNDc2NmIxNWIyYzcwNzllZDE0OTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmNjNGExMWY1ZWU1NDQwOWE4YmZhMGM1MTQ4OTUyNDAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS43NTQ0MTE2LC0wLjQ3MjYwNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmZlMzVkNzdiMjg4NGQ4OTlhYzk2MjBmMDEwYmQ1NjMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODdlM2RlNDgzY2E4NDFhMGJjYWVhMTRhZTRlM2YyMGUgPSAkKCc8ZGl2IGlkPSJodG1sXzg3ZTNkZTQ4M2NhODQxYTBiY2FlYTE0YWU0ZTNmMjBlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5USEUgTUFSTE9XRVMgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZmUzNWQ3N2IyODg0ZDg5OWFjOTYyMGYwMTBiZDU2My5zZXRDb250ZW50KGh0bWxfODdlM2RlNDgzY2E4NDFhMGJjYWVhMTRhZTRlM2YyMGUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmNjNGExMWY1ZWU1NDQwOWE4YmZhMGM1MTQ4OTUyNDAuYmluZFBvcHVwKHBvcHVwXzZmZTM1ZDc3YjI4ODRkODk5YWM5NjIwZjAxMGJkNTYzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRmNjZlNDQ4YTNmNzRhODA4NGI1YzQ5NDM1Y2E0MWQ4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNjMyMzg2NDUsLTAuMjE4ODI5MTQzNjQ3ODQ5NDddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzk5ZjA2YTVmODU4NGE5Mjk2MDUyZTY2MzBlYjEyMzcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTc2ZTY2YTY3ZjgzNGJhMTg2MzgwMDJhM2U4NDc0YTQgPSAkKCc8ZGl2IGlkPSJodG1sXzE3NmU2NmE2N2Y4MzRiYTE4NjM4MDAyYTNlODQ3NGE0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UT1RURVJJREdFIENPTU1PTiBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM5OWYwNmE1Zjg1ODRhOTI5NjA1MmU2NjMwZWIxMjM3LnNldENvbnRlbnQoaHRtbF8xNzZlNjZhNjdmODM0YmExODYzODAwMmEzZTg0NzRhNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80ZjY2ZTQ0OGEzZjc0YTgwODRiNWM0OTQzNWNhNDFkOC5iaW5kUG9wdXAocG9wdXBfMzk5ZjA2YTVmODU4NGE5Mjk2MDUyZTY2MzBlYjEyMzcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzU1N2M4OGZlM2MxNDU0OGJlZDhmMzkzNmY3NWVmNDkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS41MjAxNzU3LC0wLjE2MDUxNjhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjBlOWQ5MTFlMzJiNDA2NzllY2FjNzNlZjllMmI5N2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGFlMWMxOGVkZDAwNDVmOGI4MzdkMzkzYmNlOTc0NzcgPSAkKCc8ZGl2IGlkPSJodG1sXzBhZTFjMThlZGQwMDQ1ZjhiODM3ZDM5M2JjZTk3NDc3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5VUFBFUiBNT05UQUdVIFNUUkVFVCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2YwZTlkOTExZTMyYjQwNjc5ZWNhYzczZWY5ZTJiOTdhLnNldENvbnRlbnQoaHRtbF8wYWUxYzE4ZWRkMDA0NWY4YjgzN2QzOTNiY2U5NzQ3Nyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNTU3Yzg4ZmUzYzE0NTQ4YmVkOGYzOTM2Zjc1ZWY0OS5iaW5kUG9wdXAocG9wdXBfZjBlOWQ5MTFlMzJiNDA2NzllY2FjNzNlZjllMmI5N2EpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGU1NzNhMDQ4MGZkNGY0NWI4ZTVkMDFjMmVmMGQ5MDcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0MC43ODM0OTg3LC03Ny44MzMwNjg1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhiMTExYjExNTIzNzQ3MmNhY2M1NDhlMTdjZDUwMGFhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzM1OTkxYjJiZGFlZjRhZjFhNWJlOTIyYTA4NGM1YTBmID0gJCgnPGRpdiBpZD0iaHRtbF8zNTk5MWIyYmRhZWY0YWYxYTViZTkyMmEwODRjNWEwZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VklWSUFOIFdBWSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhiMTExYjExNTIzNzQ3MmNhY2M1NDhlMTdjZDUwMGFhLnNldENvbnRlbnQoaHRtbF8zNTk5MWIyYmRhZWY0YWYxYTViZTkyMmEwODRjNWEwZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84ZTU3M2EwNDgwZmQ0ZjQ1YjhlNWQwMWMyZWYwZDkwNy5iaW5kUG9wdXAocG9wdXBfOGIxMTFiMTE1MjM3NDcyY2FjYzU0OGUxN2NkNTAwYWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWMyMGYxZTFmNjhjNDdhYTkwNTJhMGJmM2FkMDUyMTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40ODQ5OTY1LC0wLjE3MjU4MjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTg4MWNlN2FhOGU2NGU0MWE0ZWZmNjNlZGJmMTBhYmQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDcyOTQ2MTM5MGQxNDBjY2FiNWIxYmIxMjQ1Mzk3MDkgPSAkKCc8ZGl2IGlkPSJodG1sXzQ3Mjk0NjEzOTBkMTQwY2NhYjViMWJiMTI0NTM5NzA5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XQUxEUk9OIE1FV1MgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ODgxY2U3YWE4ZTY0ZTQxYTRlZmY2M2VkYmYxMGFiZC5zZXRDb250ZW50KGh0bWxfNDcyOTQ2MTM5MGQxNDBjY2FiNWIxYmIxMjQ1Mzk3MDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMWMyMGYxZTFmNjhjNDdhYTkwNTJhMGJmM2FkMDUyMTguYmluZFBvcHVwKHBvcHVwXzk4ODFjZTdhYThlNjRlNDFhNGVmZjYzZWRiZjEwYWJkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E3NjdhODA2YjRkMzQwNmFhYmZhNzI3MzgxYTA4MGM4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbMzYuMTI5OTAzLC04Ni44MjE2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kN2FhODJkZWQ3YWM0ZjBlYmQ0MTJlMDlkMGNlZDU1ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNDc1NzQ4NWQ3YmM0YWM2YmRmNDg0OWUyM2VkMTU3MCA9ICQoJzxkaXYgaWQ9Imh0bWxfMDQ3NTc0ODVkN2JjNGFjNmJkZjQ4NDllMjNlZDE1NzAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldBUldJQ0sgTEFORSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q3YWE4MmRlZDdhYzRmMGViZDQxMmUwOWQwY2VkNTVkLnNldENvbnRlbnQoaHRtbF8wNDc1NzQ4NWQ3YmM0YWM2YmRmNDg0OWUyM2VkMTU3MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hNzY3YTgwNmI0ZDM0MDZhYWJmYTcyNzM4MWEwODBjOC5iaW5kUG9wdXAocG9wdXBfZDdhYTgyZGVkN2FjNGYwZWJkNDEyZTA5ZDBjZWQ1NWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTk5N2E5ODEwNGViNDhmZTk4NjAxZDgyNzRmZWVkMWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFsxMy4zMDc4OTQ4LC01OS41ODk4NTc3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzAyNWNkYTAyN2U4ZDQ4YjNiMTk4ZGM0ZGI3NWYwOGE5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg0OTZmNDExYjQwNTRjY2ZiZDFjMTJhYzRjMjdmOGYxID0gJCgnPGRpdiBpZD0iaHRtbF84NDk2ZjQxMWI0MDU0Y2NmYmQxYzEyYWM0YzI3ZjhmMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V0VMTCBST0FEIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDI1Y2RhMDI3ZThkNDhiM2IxOThkYzRkYjc1ZjA4YTkuc2V0Q29udGVudChodG1sXzg0OTZmNDExYjQwNTRjY2ZiZDFjMTJhYzRjMjdmOGYxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E5OTdhOTgxMDRlYjQ4ZmU5ODYwMWQ4Mjc0ZmVlZDFkLmJpbmRQb3B1cChwb3B1cF8wMjVjZGEwMjdlOGQ0OGIzYjE5OGRjNGRiNzVmMDhhOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MjlmYmY0ZTI0Y2U0MDJkYWYxYmRhMTM3OTE5ODUwZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjMzNDY4NDQsLTAuMjc2ODA3NDkzMDQyNDE4NTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDA2NDY3ZmM1ZjJiNDhhMTkxN2ZhMjdiMDc4YWIzZTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTE4MmUyYTMwYjQ0NGVkOGFjMzVjYTA1OTQ4OTliZjEgPSAkKCc8ZGl2IGlkPSJodG1sXzExODJlMmEzMGI0NDRlZDhhYzM1Y2EwNTk0ODk5YmYxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XRVNUIEhJTEwgUEFSSyBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAwNjQ2N2ZjNWYyYjQ4YTE5MTdmYTI3YjA3OGFiM2UzLnNldENvbnRlbnQoaHRtbF8xMTgyZTJhMzBiNDQ0ZWQ4YWMzNWNhMDU5NDg5OWJmMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MjlmYmY0ZTI0Y2U0MDJkYWYxYmRhMTM3OTE5ODUwZS5iaW5kUG9wdXAocG9wdXBfMDA2NDY3ZmM1ZjJiNDhhMTkxN2ZhMjdiMDc4YWIzZTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTdhMTZkYTYyZGJlNDAzOWE4YTczNzMwN2E3MjI5YjggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszNC40MTA3NjEsLTExNy42NDExNTg2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E4Yjk4ODdiNWY5YTQ0MjZiZmEwNmQxNmI3OTg5NzI2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUyNWEyNWZkMTE3MzQ2MDZiNWFmZTllNGUxMGFmMmNjID0gJCgnPGRpdiBpZD0iaHRtbF81MjVhMjVmZDExNzM0NjA2YjVhZmU5ZTRlMTBhZjJjYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V0VTVCBISUxMIFJPQUQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOGI5ODg3YjVmOWE0NDI2YmZhMDZkMTZiNzk4OTcyNi5zZXRDb250ZW50KGh0bWxfNTI1YTI1ZmQxMTczNDYwNmI1YWZlOWU0ZTEwYWYyY2MpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTdhMTZkYTYyZGJlNDAzOWE4YTczNzMwN2E3MjI5YjguYmluZFBvcHVwKHBvcHVwX2E4Yjk4ODdiNWY5YTQ0MjZiZmEwNmQxNmI3OTg5NzI2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZlODZmNDEwODcwNDQxYTdiMTJjNGZkZmUwYzVhYTE4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTEuNzMxMDk5MSwwLjY3NDYyMTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDdiMjkxNGM3NGM4NGZiYWI1YjQwZGIyOTk3NmJiNTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzBkYTkwM2E0ZjEwNGIxODg4OTFhMDkyZDU4NTAzMTcgPSAkKCc8ZGl2IGlkPSJodG1sX2MwZGE5MDNhNGYxMDRiMTg4ODkxYTA5MmQ1ODUwMzE3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XRVNUIFNRVUFSRSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA3YjI5MTRjNzRjODRmYmFiNWI0MGRiMjk5NzZiYjUxLnNldENvbnRlbnQoaHRtbF9jMGRhOTAzYTRmMTA0YjE4ODg5MWEwOTJkNTg1MDMxNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82ZTg2ZjQxMDg3MDQ0MWE3YjEyYzRmZGZlMGM1YWExOC5iaW5kUG9wdXAocG9wdXBfMDdiMjkxNGM3NGM4NGZiYWI1YjQwZGIyOTk3NmJiNTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjNlYTIxZmJiNjQ2NDc2ZDkwMDM2MWM1NzQ5YjFhMGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS40NjAxMzg5LC0wLjI3NTQwNF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZDYyZDJmOWJlODA0ZGZlOTE3MzgxODEwOWYxMjhjOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NjlkY2U3OTVjNzc0NmVhYjg0YzU3ZDA1ZDBhYmRkZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDY5ZGNlNzk1Yzc3NDZlYWI4NGM1N2QwNWQwYWJkZGUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldFU1QgVEVNUExFIFNIRUVOIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGQ2MmQyZjliZTgwNGRmZTkxNzM4MTgxMDlmMTI4Yzguc2V0Q29udGVudChodG1sXzQ2OWRjZTc5NWM3NzQ2ZWFiODRjNTdkMDVkMGFiZGRlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYzZWEyMWZiYjY0NjQ3NmQ5MDAzNjFjNTc0OWIxYTBmLmJpbmRQb3B1cChwb3B1cF9kZDYyZDJmOWJlODA0ZGZlOTE3MzgxODEwOWYxMjhjOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81MTQwYjlkMTgwNmY0ZTJjODMzZTQ1M2UxMzk4OTY0YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzUxLjYwNDU3NjIsLTAuMDcwNzg1N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODBmZmI0IiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iMzVmN2M5ZWNkYjg0YzZjYTkzZDc1MDkxNDczMTNmZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NGFjNTYxZTc4MGU0YTM3ODZhNDAwMjU3MTYzYjU4OSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDRhYzU2MWU3ODBlNGEzNzg2YTQwMDI1NzE2M2I1ODkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldISVRFIEhBUlQgTEFORSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IzNWY3YzllY2RiODRjNmNhOTNkNzUwOTE0NzMxM2ZkLnNldENvbnRlbnQoaHRtbF80NGFjNTYxZTc4MGU0YTM3ODZhNDAwMjU3MTYzYjU4OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81MTQwYjlkMTgwNmY0ZTJjODMzZTQ1M2UxMzk4OTY0YS5iaW5kUG9wdXAocG9wdXBfYjM1ZjdjOWVjZGI4NGM2Y2E5M2Q3NTA5MTQ3MzEzZmQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTMxNjQzZmI0MDEyNDNlZThlOTViMTNlMDc3NmY4ZTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs1MS4zODAxMTI3LDAuNTI4ODg0NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jOWViZTk5NjlkY2I0MGMzYmU2YTQ5MzA1MzYwNTI0NSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mZDBhMjgzNDY1Mjk0ZTQ0YjNiOGM1Y2RlOGUwYzI3ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hYTBmNWIxOWUzNTM0MjdmOTMwOWVhOTVlZDI3MTg4YiA9ICQoJzxkaXYgaWQ9Imh0bWxfYWEwZjViMTllMzUzNDI3ZjkzMDllYTk1ZWQyNzE4OGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldISVRUQUtFUiBTVFJFRVQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mZDBhMjgzNDY1Mjk0ZTQ0YjNiOGM1Y2RlOGUwYzI3ZS5zZXRDb250ZW50KGh0bWxfYWEwZjViMTllMzUzNDI3ZjkzMDllYTk1ZWQyNzE4OGIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTMxNjQzZmI0MDEyNDNlZThlOTViMTNlMDc3NmY4ZTUuYmluZFBvcHVwKHBvcHVwX2ZkMGEyODM0NjUyOTRlNDRiM2I4YzVjZGU4ZTBjMjdlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M0MzVkYzVlOTAzMTRmNzdhNjU3YThjMmQzZTk2OTc2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNTIuOTU4NTI4MywtMS4yMDk1NjgxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2M5ZWJlOTk2OWRjYjQwYzNiZTZhNDkzMDUzNjA1MjQ1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RhZDcxMTAxNGFmNjQzYTRhZWQxYmY5NjRiMWU1M2ZlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE5NjU0MTJjOTEyZDQ2NTliZjZiNTM3NzJkNTRmNzBiID0gJCgnPGRpdiBpZD0iaHRtbF8xOTY1NDEyYzkxMmQ0NjU5YmY2YjUzNzcyZDU0ZjcwYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V09PRFlBUkQgTEFORSBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RhZDcxMTAxNGFmNjQzYTRhZWQxYmY5NjRiMWU1M2ZlLnNldENvbnRlbnQoaHRtbF8xOTY1NDEyYzkxMmQ0NjU5YmY2YjUzNzcyZDU0ZjcwYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNDM1ZGM1ZTkwMzE0Zjc3YTY1N2E4YzJkM2U5Njk3Ni5iaW5kUG9wdXAocG9wdXBfZGFkNzExMDE0YWY2NDNhNGFlZDFiZjk2NGIxZTUzZmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjk2Y2NiMzM2YjIwNDE4M2IxZmUyOGYyNjQ4NmJiMjQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFszMi43NTUxLC05Ny40NTU0ODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfYzllYmU5OTY5ZGNiNDBjM2JlNmE0OTMwNTM2MDUyNDUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDBhYjFmOTY0NzAzNGJmN2I2YThmZDFmYTc0ZjQ2Y2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmJjOGUyMWI3MTdjNDY3YjhhMTQxZTQzNmM2MjMxN2YgPSAkKCc8ZGl2IGlkPSJodG1sXzZiYzhlMjFiNzE3YzQ2N2I4YTE0MWU0MzZjNjIzMTdmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XWUFUVCBEUklWRSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QwYWIxZjk2NDcwMzRiZjdiNmE4ZmQxZmE3NGY0NmNjLnNldENvbnRlbnQoaHRtbF82YmM4ZTIxYjcxN2M0NjdiOGExNDFlNDM2YzYyMzE3Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mOTZjY2IzMzZiMjA0MTgzYjFmZTI4ZjI2NDg2YmIyNC5iaW5kUG9wdXAocG9wdXBfZDBhYjFmOTY0NzAzNGJmN2I2YThmZDFmYTc0ZjQ2Y2MpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" mozallowfullscreen="" onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" webkitallowfullscreen=""&gt;&lt;/iframe&gt;</div></div>



## Result


```python
df1 = london_grouped_clustering.loc[london_grouped_clustering['Cluster Labels'] == 0, 
                              london_grouped_clustering.columns[[1] + list(range(5, london_grouped_clustering.shape[1]))]]
df2 = london_grouped_clustering.loc[london_grouped_clustering['Cluster Labels'] == 1, 
                                    london_grouped_clustering.columns[[1] + list(range(5, london_grouped_clustering.shape[1]))]]
df3 = london_grouped_clustering.loc[london_grouped_clustering['Cluster Labels'] == 2, 
                                    london_grouped_clustering.columns[[1] + list(range(5, london_grouped_clustering.shape[1]))]]
df4 = london_grouped_clustering.loc[london_grouped_clustering['Cluster Labels'] == 3, 
                                    london_grouped_clustering.columns[[1] + list(range(5, london_grouped_clustering.shape[1]))]]
df5 = london_grouped_clustering.loc[london_grouped_clustering['Cluster Labels'] == 4, 
                                    london_grouped_clustering.columns[[1] + list(range(5, london_grouped_clustering.shape[1]))]]

```


```python
# Cluster 0
df1.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg_Price</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1439</th>
      <td>2.200000e+06</td>
      <td>Pub</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Sandwich Place</td>
      <td>Performing Arts Venue</td>
      <td>Recording Studio</td>
      <td>Park</td>
      <td>Fish Market</td>
      <td>Filipino Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Farmers Market</td>
    </tr>
    <tr>
      <th>1508</th>
      <td>2.225000e+06</td>
      <td>Pub</td>
      <td>Sandwich Place</td>
      <td>Discount Store</td>
      <td>Hardware Store</td>
      <td>Supermarket</td>
      <td>Casino</td>
      <td>Bar</td>
      <td>Bus Station</td>
      <td>Gym</td>
      <td>River</td>
    </tr>
    <tr>
      <th>2046</th>
      <td>2.218333e+06</td>
      <td>Grocery Store</td>
      <td>Fried Chicken Joint</td>
      <td>Bus Stop</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Fast Food Restaurant</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
      <td>Farm</td>
    </tr>
    <tr>
      <th>2540</th>
      <td>2.213750e+06</td>
      <td>Italian Restaurant</td>
      <td>Pub</td>
      <td>Café</td>
      <td>Japanese Restaurant</td>
      <td>Asian Restaurant</td>
      <td>English Restaurant</td>
      <td>Juice Bar</td>
      <td>Cocktail Bar</td>
      <td>Grocery Store</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>3230</th>
      <td>2.240000e+06</td>
      <td>Chinese Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Metro Station</td>
      <td>Food Court</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Cluster 1
df2.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg_Price</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>499</th>
      <td>2.462500e+06</td>
      <td>Italian Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Indian Restaurant</td>
      <td>Jewelry Store</td>
      <td>Auto Garage</td>
      <td>Pizza Place</td>
      <td>Pilates Studio</td>
      <td>Pharmacy</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
    <tr>
      <th>680</th>
      <td>2.475000e+06</td>
      <td>Pub</td>
      <td>Construction &amp; Landscaping</td>
      <td>Bar</td>
      <td>Performing Arts Venue</td>
      <td>Park</td>
      <td>Dance Studio</td>
      <td>Deli / Bodega</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
    <tr>
      <th>1021</th>
      <td>2.500000e+06</td>
      <td>Performing Arts Venue</td>
      <td>Restaurant</td>
      <td>Zoo Exhibit</td>
      <td>Fast Food Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
      <td>Farm</td>
    </tr>
    <tr>
      <th>2223</th>
      <td>2.500000e+06</td>
      <td>Italian Restaurant</td>
      <td>Hotel</td>
      <td>Light Rail Station</td>
      <td>Dessert Shop</td>
      <td>Japanese Restaurant</td>
      <td>Café</td>
      <td>Lebanese Restaurant</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Deli / Bodega</td>
    </tr>
    <tr>
      <th>2611</th>
      <td>2.470154e+06</td>
      <td>Pub</td>
      <td>Lake</td>
      <td>Fast Food Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
      <td>Farm</td>
      <td>Farmers Market</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Cluster 2
df3.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg_Price</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>989</th>
      <td>2.344600e+06</td>
      <td>Sandwich Place</td>
      <td>Sports Club</td>
      <td>Auto Garage</td>
      <td>Locksmith</td>
      <td>Zoo Exhibit</td>
      <td>Farmers Market</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
    </tr>
    <tr>
      <th>1326</th>
      <td>2.376941e+06</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2168</th>
      <td>2.361750e+06</td>
      <td>Pub</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Bus Stop</td>
      <td>Park</td>
      <td>Platform</td>
      <td>Thai Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Bar</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
    <tr>
      <th>2395</th>
      <td>2.323750e+06</td>
      <td>Café</td>
      <td>Bar</td>
      <td>Italian Restaurant</td>
      <td>Pub</td>
      <td>Coffee Shop</td>
      <td>French Restaurant</td>
      <td>Park</td>
      <td>Caribbean Restaurant</td>
      <td>Pizza Place</td>
      <td>Spa</td>
    </tr>
    <tr>
      <th>3912</th>
      <td>2.338185e+06</td>
      <td>Gym / Fitness Center</td>
      <td>Convenience Store</td>
      <td>Sports Club</td>
      <td>Martial Arts School</td>
      <td>Bus Stop</td>
      <td>Rugby Pitch</td>
      <td>Farm</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Cluster 3
df4.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg_Price</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>2.283829e+06</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Pub</td>
      <td>Park</td>
      <td>Art Gallery</td>
      <td>Plaza</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
    </tr>
    <tr>
      <th>186</th>
      <td>2.292500e+06</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bar</td>
      <td>Indian Restaurant</td>
      <td>Restaurant</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Seafood Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>New American Restaurant</td>
    </tr>
    <tr>
      <th>258</th>
      <td>2.283095e+06</td>
      <td>Pub</td>
      <td>Indian Restaurant</td>
      <td>Playground</td>
      <td>Lawyer</td>
      <td>Farmers Market</td>
      <td>English Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
    </tr>
    <tr>
      <th>455</th>
      <td>2.300000e+06</td>
      <td>Cha Chaan Teng</td>
      <td>Noodle House</td>
      <td>Dessert Shop</td>
      <td>Hotel</td>
      <td>Sporting Goods Shop</td>
      <td>Hong Kong Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Cantonese Restaurant</td>
      <td>Bakery</td>
      <td>Toy / Game Store</td>
    </tr>
    <tr>
      <th>2125</th>
      <td>2.305400e+06</td>
      <td>Pub</td>
      <td>Ice Cream Shop</td>
      <td>Bakery</td>
      <td>Yoga Studio</td>
      <td>Coffee Shop</td>
      <td>Indian Restaurant</td>
      <td>Hotel</td>
      <td>Hostel</td>
      <td>Park</td>
      <td>Breakfast Spot</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Cluster 4
df5.head()
```




<div>
<style>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg_Price</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>536</th>
      <td>2425000.0</td>
      <td>Pub</td>
      <td>Grocery Store</td>
      <td>Bakery</td>
      <td>Indian Restaurant</td>
      <td>Mediterranean Restaurant</td>
      <td>Coffee Shop</td>
      <td>Ice Cream Shop</td>
      <td>Middle Eastern Restaurant</td>
      <td>Moroccan Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
    </tr>
    <tr>
      <th>1179</th>
      <td>2425000.0</td>
      <td>Theater</td>
      <td>Coffee Shop</td>
      <td>Bookstore</td>
      <td>Clothing Store</td>
      <td>Beer Bar</td>
      <td>Bakery</td>
      <td>Dessert Shop</td>
      <td>Indian Restaurant</td>
      <td>Boutique</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <th>1180</th>
      <td>2400000.0</td>
      <td>Chinese Restaurant</td>
      <td>Park</td>
      <td>Grocery Store</td>
      <td>English Restaurant</td>
      <td>Event Service</td>
      <td>Event Space</td>
      <td>Exhibit</td>
      <td>Fabric Shop</td>
      <td>Falafel Restaurant</td>
      <td>Farm</td>
    </tr>
    <tr>
      <th>1220</th>
      <td>2400000.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1746</th>
      <td>2400000.0</td>
      <td>Sandwich Place</td>
      <td>Lounge</td>
      <td>Health &amp; Beauty Service</td>
      <td>Park</td>
      <td>Convenience Store</td>
      <td>Food &amp; Drink Shop</td>
      <td>Flea Market</td>
      <td>Empanada Restaurant</td>
      <td>Food Court</td>
      <td>English Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



## Discussion

After studying the dataset, we may gain a better insight across the London house market based on the street average price and the amenities nearby. London Housing Market, though is said to be in a rut at the moment, is still vibrant and of potential.

We start off by analysising the result from different angles.

#### Grouping by Clusters
According to the five clusterss produced in the end, we could target housebuyers' interests when suggesting the best venues. For example, Cluster 0 may targeting customers looking for various food places and economical life expenses, for they show a most common of restaurants, pub and cafe with a relatively low average price. Instead, customers fancy high-end community may make their choices among estates on the streets in Cluster 1, with arts meseums and exhibitions surrounded.

#### Future Market Study and Prediction
In another hand, we could also make our prediction on the future market based on the level of infrastructures and amenities such as schools and hospitals. Further looking into the map, it is interesting to notice that while most venues are in West London (Chelsea, Kensinton, Marylebone), there are outstanding venues located at South-West London (Wimbledon, Baham) and North-West London (Kilburn, Hampstead), which may due to the city expansion. This may favor potential investors into the house market who want to purchase venues as an investment option. 

#### Further implementations
Further Study can also be brought out based on detailed requirements provided by the housebuyers, such as sorting the venues in each clusters by average price or picking out the estates with the most common venues specified. 

## Conclusion

Despite the fact that housing markets in London have witnessed a number of ups and downs, with volatile house prices in London tending to amplify changes in national house prices recently, there is still great potential lying in the market for investers and housebuyers. Recently as a result of the pandemic, increasing unemployment rate, wage cuts, business failures, and job uncertainty also brought huge impact to the house market in London. However, it is relatively hard to provide an insight of the market from the patterns of previous cycles for clients, so in this report we focused on the integration of data science and machine learning technics to assist clients making sensible and efficient choices. From the results provided, we could easily recommend profitable venues according to amenities and infrastructures surrounded.

We first retrieved data on London properties and the relative price from the HM Land Registry (http://landregistry.data.gov.uk/). Amenities and infrastructure dataframe is built with the Foursquare API. Merging the two datasets together, we were able to have a more detailed information about the market and start our study. K-means clustering technique is used to cluster all the data into 5 different clusters. Finally, we could use the results to better study the london house market and make our choice of data based on clients' requirment. 

This approach is an visualizable and efficient way for housebuyers to gain an insight of the house market and the methodology can be applied for other places with proper data provided. It is also worth notice that the due to the robustion of house market, making wise decision is important even with the help of algorithm technics and data analysis which is based on previous market performance but not predicting future incidences.


```python

```
