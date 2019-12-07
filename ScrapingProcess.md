<h1 style = 'text-align: center;'>United States Wildlife</h1>

My topic for the final project in DATS6103 is to do data mining about U.S Wildlife.

The project will contain three main parts: scraping, data preprocessing and data analyis.

In this section, I will perform the scraping process. The website I selected to extract data is https://www.fws.gov/
 (U.S Fish and Wildlife Service).


```python
#import libararies
import requests
import urllib.request
import time
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np

```

## Scrape the data from https://www.fws.gov/


The link to search species is: 
https://ecos.fws.gov/ecp0/reports/ad-hoc-species-report-input

After we select query options in the search page, we will navigate to this base url:
https://ecos.fws.gov/ecp0/reports/ad-hoc-species-report

No matter which query options we choose, the result will be the page above. Therefore, when we request the url, the information we can obtain are only scientific name and common name. To retrieve other attributes such as location, status, group, family and date listed, we need to figure out another way.

After exploring the search page, I found out that we can set the name of the attribute we want to retrieve "on" and add it to the base url. The result website will include the information we need in its table.


```python
base_url ="https://ecos.fws.gov/ecp0/reports/ad-hoc-species-report"

listUSA = "&mapstatus=1"  #choose species in United States
status = "?fstatus=on"    #show status
group = "&fgroup=on"      #show taxonomic group
family = "&ffamily=on"    #show family
region = "&fcurrdist=on"  #show location in U.S
date = "&flistingdate=on" #show first date listed
grouptype = "&fvip=on"    #show group type


#add all filter options to the base url
url = base_url + status + group + family + region + date + grouptype + listUSA 



```


```python
url #here is the new url that contains all information we need for analysis
```




    'https://ecos.fws.gov/ecp0/reports/ad-hoc-species-report?fstatus=on&fgroup=on&ffamily=on&fcurrdist=on&flistingdate=on&fvip=on&mapstatus=1'



## Scrape the table in the page




In the next parts, we will scrape the table in the result page and save it to a dataframe.

The table will contain 8 columns corresponding to 8 attributes: Scientific Name, Common Name, Region/State, Family, First Listed Date, Taxonomic Group, Listing Status, Group Type.


```python
html = requests.get(url) 


```


```python
html
```




    <Response [200]>




```python
soup = BeautifulSoup(html.content, 'lxml')
```


```python
table = soup.find("table") #find the table
```


```python
rows = table.find_all("tr") #find all the rows in the table. Each row represents a species
```


```python
#create empty list to store dataframe as an element  
species = []

for row in rows:
    temp = []
    cells = row.find_all("td") #find all columns in each row. We will have 8 columns for each row
    
    if len(cells) == 8: #if a row has 8 columns then function next queries
                        #It may be not necessary, just to make sure no distinguished row exits in the table
            
            temp.append([item.text for item in cells]) #save the columns into a list called temp
    
    #temp contains only 1 item and this item is also a list, each element in the item is a column data 
    #Later when we convert temp to a dataframe, the dataframe will contain 8 columns and 1 row
    
    species.append(pd.DataFrame(temp)) #convert temp to a dataframe
                                       #then save the dataframe into the empty list we created
                                       #after the for-loop, we obtain a list of many dataframes
```


```python
species[:5] #lets have a look at the list species

```




    [Empty DataFrame
     Columns: []
     Index: [],
                    0           1        2         3   4                    5  \
     0  Abies fraseri  Fraser fir  NC, VA;  Pinaceae  NA  Conifers and Cycads   
     
                 6  7  
     0  Not Listed  P  ,
                          0                     1    2         3   4        5  \
     0  Ablautus schlingeri  Oso Flaco robber fly  CA;  Asilidae  NA  Insects   
     
                 6  7  
     0  Not Listed  I  ,
                     0                             1    2              3   4  \
     0  Abronia alpina  Ramshaw Meadows sand-verbena  CA;  Nyctaginaceae  NA   
     
                       5           6  7  
     0  Flowering Plants  Not Listed  P  ,
                        0                         1 2              3   4  \
     0  Abronia ammophila  Yellowstone Sand Verbena    Nyctaginaceae  NA   
     
                       5           6  7  
     0  Flowering Plants  Not Listed  P  ]




```python
species[26] #here is a random element in the list and it is a dataframe
```




<div>
<style scoped>
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
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Accipiter cooperii</td>
      <td>Cooper's hawk</td>
      <td>CA;</td>
      <td>Accipitridae</td>
      <td>NA</td>
      <td>Birds</td>
      <td>Not Listed</td>
      <td>V</td>
    </tr>
  </tbody>
</table>
</div>



## Save the scraped data into a dataframe


```python
df = pd.concat(species) #combine all the dataframes in the list into a dataframe called df

```


```python
df.head(10)   #let's see out dataframe df
              #df has 8732 rows representing 8732 species. The number is the same as shown in the result page
```




<div>
<style scoped>
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
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Abies fraseri</td>
      <td>Fraser fir</td>
      <td>NC, VA;</td>
      <td>Pinaceae</td>
      <td>NA</td>
      <td>Conifers and Cycads</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Ablautus schlingeri</td>
      <td>Oso Flaco robber fly</td>
      <td>CA;</td>
      <td>Asilidae</td>
      <td>NA</td>
      <td>Insects</td>
      <td>Not Listed</td>
      <td>I</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia alpina</td>
      <td>Ramshaw Meadows sand-verbena</td>
      <td>CA;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia ammophila</td>
      <td>Yellowstone Sand Verbena</td>
      <td></td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia ammophila var.</td>
      <td>No common name</td>
      <td></td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia ammphila</td>
      <td>[Unnamed] sand-verbena</td>
      <td>WY;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia bigelovii</td>
      <td>[Unnamed] sand-verbena</td>
      <td>NM;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia macrocarpa</td>
      <td>Large-fruited sand-verbena</td>
      <td>TX; U.S.A. (TX)</td>
      <td>Nyctaginaceae</td>
      <td>Sep 28, 1988</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia turbinata</td>
      <td>[Unnamed] sand-verbena</td>
      <td>NV;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Abronia umbellata acutalata</td>
      <td>Rose-purple sand-verbena</td>
      <td>WA; Possibly extinct,last observed in 1940</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.drop_duplicates(inplace = True) #the result page still contains some duplicates and we need to remove them

colnames = ["Scientific Name","Common Name","Region","Family","First Listed",
            "Taxonomic Group","Status","Type"] #a list of column names that we will use

df.columns = colnames  #name our columns
df.index = range(len(df.index))  #reindex
```


```python
df.head(10)
```




<div>
<style scoped>
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
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Scientific Name</th>
      <th>Common Name</th>
      <th>Region</th>
      <th>Family</th>
      <th>First Listed</th>
      <th>Taxonomic Group</th>
      <th>Status</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Abies fraseri</td>
      <td>Fraser fir</td>
      <td>NC, VA;</td>
      <td>Pinaceae</td>
      <td>NA</td>
      <td>Conifers and Cycads</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Ablautus schlingeri</td>
      <td>Oso Flaco robber fly</td>
      <td>CA;</td>
      <td>Asilidae</td>
      <td>NA</td>
      <td>Insects</td>
      <td>Not Listed</td>
      <td>I</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Abronia alpina</td>
      <td>Ramshaw Meadows sand-verbena</td>
      <td>CA;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Abronia ammophila</td>
      <td>Yellowstone Sand Verbena</td>
      <td></td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Abronia ammophila var.</td>
      <td>No common name</td>
      <td></td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Abronia ammphila</td>
      <td>[Unnamed] sand-verbena</td>
      <td>WY;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Abronia bigelovii</td>
      <td>[Unnamed] sand-verbena</td>
      <td>NM;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Abronia macrocarpa</td>
      <td>Large-fruited sand-verbena</td>
      <td>TX; U.S.A. (TX)</td>
      <td>Nyctaginaceae</td>
      <td>Sep 28, 1988</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Abronia turbinata</td>
      <td>[Unnamed] sand-verbena</td>
      <td>NV;</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Abronia umbellata acutalata</td>
      <td>Rose-purple sand-verbena</td>
      <td>WA; Possibly extinct,last observed in 1940</td>
      <td>Nyctaginaceae</td>
      <td>NA</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>



## Save the data into a CSV file

In the final step, I will save my scraped data into a csv file


```python
df.to_csv("US.Wildlife.csv", sep='\t') # save it in a CSV file
```
