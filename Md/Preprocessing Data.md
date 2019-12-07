<h1 style = 'text-align: center;'>Preprocessing US.Wildlife.csv data</h1>

Before analysis, I will clean the data to make it look better.



## Data Preprocessing


```python
from pandas import DataFrame
import pandas as pd
from functools import reduce
import numpy as np
import urllib.parse
from urllib.parse import urljoin
import warnings
warnings.filterwarnings("ignore")

```


```python
species = pd.read_csv('US.Wildlife.csv', '\t', index_col=0) # read csv file and save it in species dataframe
```


```python
species.head(10)
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
      <td>NaN</td>
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
      <td>NaN</td>
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
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Abronia ammophila</td>
      <td>Yellowstone Sand Verbena</td>
      <td>NaN</td>
      <td>Nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Abronia ammophila var.</td>
      <td>No common name</td>
      <td>NaN</td>
      <td>Nyctaginaceae</td>
      <td>NaN</td>
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
      <td>NaN</td>
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
      <td>NaN</td>
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
      <td>NaN</td>
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
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>




```python
species.dtypes  #check data types
```




    Scientific Name    object
    Common Name        object
    Region             object
    Family             object
    First Listed       object
    Taxonomic Group    object
    Status             object
    Type               object
    dtype: object



There are five columns that need to be preprocessed.

At first, I will keep the U.S state codes in the Region column, other texts which are the description contents will be removed. In the First Listed column, I only use the year data and remove day and month. Then, I will change the data type of this column to integer. In the Common Name column, any species with no common name will be assigned common name as NaN and I will remove the brackets [Unnamed]. In the Family column, I will make all the family names to lowercase. Finally, in the Status column, any status listed as Original Data in Error will be changed to NaN.



### Region Column


```python
split_char = ";" #define a split character. 
                  #In Region column, everything before ";" is U.S state code(s). The texts after ";" will be removed
split_char1 = ","

for i in range (0,len(species)):
    st0 = species.ix[i,"Region"] #get the string data in Region column at row i
    try:
        temp = st0.split(split_char)    #try to split the string
        st1 = split_char.join(temp[:1]) #get the texts before ";" which are U.S state codes
        
    #After split the string by ";", we split it again by ","
    #If the string before commna has length = 2, then st1 will contain the state codes that we need to assign
    #Else, st1 is the description and there is no state code we need for region column, we set the column as nan
    
        temp1 = st1.split(split_char1)    #split st1 by ","
        st2 = split_char1.join(temp1[:1]) #get the text before ","
        if len(st2) == 2:
            species.ix[i,"Region"] = st1 #if length is 2 then st1 contains state codes, we assign st1 to column
        else:
            species.ix[i,"Region"] = np.nan #if not then st1 is the description, we set region as nan
    except:
        pass    #if the column is NaN then we cannot perform split, hence, pass
    
    
```

### First Listed Column


```python
split_char = "," #the year data is after the commna


for i in range (0,len(species)):
    st0 = species.ix[i,"First Listed"]
    try:
        temp = st0.split(split_char)      #try to split the string
        st1 = split_char.join(temp[1:])    #get the year after the commna
        species.ix[i,"First Listed"] = st1 #assign year to the column
    except:
        pass  #if there is no listing date then we cannot split the string so we pass and let it NaN as normal
   
```


```python
#convert NaN to 0 and change "First Listed" to integer type
species["First Listed"] = species["First Listed"].fillna("0").astype(int) 
species["First Listed"].replace(0, np.nan, inplace=True) #reasign NaN 
pd.set_option('precision', 0) #make sure the year is shown as integer without additional decimal (.0)

```


```python
species.dtypes #check the data types now
```




    Scientific Name     object
    Common Name         object
    Region              object
    Family              object
    First Listed       float64
    Taxonomic Group     object
    Status              object
    Type                object
    dtype: object



### Common Name Column


```python
split_char = "]"


for i in range (0,len(species)):
    st0 = species.ix[i,"Common Name"]
    temp = st0.split(split_char)
    st1 = split_char.join(temp[1:]) #select the texts after the bracket "]"
    if (len(st1) == 0): #if the length of texts after the bracket "]" is 0, then we do not need to change anything
        species.ix[i,"Common Name"] = st0
    else:   #else, we select the texts after the bracket "]" and the brackets before it will be removed
        species.ix[i,"Common Name"] = st1
```


```python
species["Common Name"].replace("No common name", np.nan, inplace=True) #species without common name is assigned as NaN
```

### Family Column


```python
for i in range(0,len(species)):
    text = species.ix[i,"Family"].lower() 
    species.ix[i,"Family"] = text   #change each data in the Family column to lowercase
```

### Status Column


```python
#The column data which state Original Data in Error will have descriptions behind character "-"
#To identify those columns, I split the status data in each row by "-"
#If there is error description behind "-", we change the data in that column to NaN; else we pass

split_char = "-"

for i in range(0,len(species)):
    temp = species.ix[i,"Status"].split(split_char)
    error = st1 = split_char.join(temp[1:])
    if (len(error) != 0):  #if len is not 0, we have texts behind "-" and it is an error description
        species.ix[i,"Status"] = np.nan  #change the column value to NaN
    else: #else, nothing changed
        pass
        
             
   

```


```python
#remove the whitespace before the Status in each column using lstrip()
for i in range(0,len(species)):
    try:
        species.ix[i,"Status"] = species.ix[i,"Status"].lstrip()
    except:
        pass
```


```python
species.head(20) #check our dataframe before saving it as CSV file
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
      <td>NC, VA</td>
      <td>pinaceae</td>
      <td>NaN</td>
      <td>Conifers and Cycads</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Ablautus schlingeri</td>
      <td>Oso Flaco robber fly</td>
      <td>CA</td>
      <td>asilidae</td>
      <td>NaN</td>
      <td>Insects</td>
      <td>Not Listed</td>
      <td>I</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Abronia alpina</td>
      <td>Ramshaw Meadows sand-verbena</td>
      <td>CA</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Abronia ammophila</td>
      <td>Yellowstone Sand Verbena</td>
      <td>NaN</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Abronia ammophila var.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Abronia ammphila</td>
      <td>sand-verbena</td>
      <td>WY</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Abronia bigelovii</td>
      <td>sand-verbena</td>
      <td>NM</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Abronia macrocarpa</td>
      <td>Large-fruited sand-verbena</td>
      <td>TX</td>
      <td>nyctaginaceae</td>
      <td>1988</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Abronia turbinata</td>
      <td>sand-verbena</td>
      <td>NV</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Abronia umbellata acutalata</td>
      <td>Rose-purple sand-verbena</td>
      <td>WA</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Abronia umbellata breviflora</td>
      <td>Pink sand-verbena</td>
      <td>CA, OR</td>
      <td>nyctaginaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Abutilon eremitopetalum</td>
      <td>NaN</td>
      <td>HI</td>
      <td>malvaceae</td>
      <td>1991</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Abutilon hulseanum</td>
      <td>Terciopelo</td>
      <td>PR, VI</td>
      <td>malvaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Abutilon menziesii</td>
      <td>Ko`oloa`ula</td>
      <td>HI</td>
      <td>malvaceae</td>
      <td>1986</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Abutilon parishii</td>
      <td>NaN</td>
      <td>AZ</td>
      <td>malvaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Abutilon sandwicense</td>
      <td>NaN</td>
      <td>HI</td>
      <td>malvaceae</td>
      <td>1991</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Abutilon virginianum</td>
      <td>NaN</td>
      <td>PR, VI</td>
      <td>malvaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Acacia koaia</td>
      <td>Koai`e</td>
      <td>HI</td>
      <td>fabaceae</td>
      <td>NaN</td>
      <td>Flowering Plants</td>
      <td>Not Listed</td>
      <td>P</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Acaena exigua</td>
      <td>Liliwai</td>
      <td>HI</td>
      <td>rosaceae</td>
      <td>1992</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>19</td>
      <td>Acalyptera susanae</td>
      <td>lace bug</td>
      <td>AR</td>
      <td>tingidae</td>
      <td>NaN</td>
      <td>Insects</td>
      <td>Not Listed</td>
      <td>I</td>
    </tr>
  </tbody>
</table>
</div>




```python
species.to_csv("CleanWildlife.csv", sep='\t') # save cleaned data in a CSV file
```
