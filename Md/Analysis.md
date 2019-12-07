<h1 style = 'text-align: center;'>United States Wildlife</h1>



# I. Introduction

The ecosystem in our planet is getting worse day by day. Not only the environmental change but also the extinction of many species result in detrimental effects to the ecosystem. Many people are not aware of the importance of wildlife since they think animals and plants have no influence on their lives. However, in fact, wildlife plays a key role in improving the quality of human life. For example, a variety of wildlife species can promote the biodiversity which benefits human health and maintains ecosystem's functionality. Furthermore, many medicines that address popular  health issues such disorders and heart diseases are the productions of chemicals from plants and animals. 

Since many species are at risk of extinction, it is extremely important to have appropriate conservation plans to protect our planet's ecosystem.

This project presents data mining on United States Wildlife to understand which regions need to be prioritized in conservation. In addition, the most endangered species family will be dicussed.

# II. Data Analysis

## 1. Import Data 


```python
# import some libraries
from pandas import DataFrame
import seaborn as sns
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from functools import reduce
import plotly.graph_objects as go
import urllib.parse
from urllib.parse import urljoin
import re
from wordcloud import WordCloud
from PIL import Image
import squarify
import warnings
warnings.filterwarnings("ignore")


```


```python
pd.set_option('precision', 0) 
df = pd.read_csv('CleanWildlife.csv', '\t', index_col=0) # read csv file
```


```python
df.head(10) # checking the first 10 rows in our dataset to ensure everything is fine
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
  </tbody>
</table>
</div>



All columns are fine. The species without listing status and listing date are common in the USA. They may not face the risk of being extinct at the moment.

## 2. Taxonomic Group Distribution

In this section, we will see the number of each species group in the United States


```python
group = df.groupby("Taxonomic Group").size() # count the number of species by taxonomic group
group.sort_values(ascending = True, inplace = True) # ordering
count_group = pd.DataFrame({"Group":group.index, "Count":group.values}) # grouping the counts and group names in a df

```


```python
plt.figure(figsize=(10,7)) # set figure size

# using bar plot to see the number of species in each taxonomic group
plt.barh(y=count_group.Group, width=count_group.Count, color ="dodgerblue")
plt.title('Number of species by group in the United States\n', fontsize ='x-large')
plt.ylabel('Taxonomic Group', fontsize ='large')
plt.xlabel('Number of species', fontsize='large')
plt.show()
```


![png](output_10_0.png)


As seen from the graph, the most popular taxonomic groups in United States are very familiar: plants, insects, snails, fishes, mammals and birds.

The group with least species are Hydroids and Annelid Worms. Hydroids are very small animals and most of them live in salt water such as marine environment. Annelid Worms adapt to various ecologies but their most living states are aslo marine environment. Another similarity between Hydroids and Annelid Worms is that both species are invertebrates. 

Other groups with small number of species such as corals, sponges, conifers and cycads also prefere marine environments or seaside surfaces.

## 3. Overview of species distribution


```python
def count(data): 
    
    """
      Function to count the number of species in each state in the United States
          data: dataframe that will be used to count 
      The function return a dataframe with two columns: state codes and number of species
  
    """
    
    split_char = ","
    state = [] # empty list to store states

    for i in range (0,len(data)):
        words = data.ix[i,"Region"] # get state codes
        try:
            temp = words.split(split_char) # try to split the string by "," each element in temp is a state code
            for item in temp:
                item = item.replace(" ","") # remove whitespace
                state.append(item) #save the state codes into the empty list we created above
        except:  # if we cannot split, the region is NaN so we pass it
            pass 
    
    codelist = pd.Series(state).drop_duplicates().tolist() #get a list of state codes in our dataframe

    count = []
    for item in codelist:
        count.append(state.count(item)) # count the number of species in each state

    # combine state code and counts of species into a dataframe
    state_count = pd.concat((pd.DataFrame(codelist).T, pd.DataFrame(count).T)).T 
    state_count.columns = ("Code","No. of Species") # colnames

    return state_count
    
```

### Let's see the species distribution by different regions in the United States.


```python
count_species = count(df) # count the number of species by states


# use plotly to make Choropleth Map 
# I learned from plotly: https://plot.ly/python/choropleth-maps/

fig = go.Figure(data=go.Choropleth(
        locations=count_species["Code"], 
        z = count_species["No. of Species"].astype(float), 
        locationmode = 'USA-states', 
        colorscale = "blues",
        colorbar_title = "Number of Species",
        marker_line_color = "white"
))

fig.update_layout(
    title_text = "Species Distribution by U.S States",
    geo_scope='usa')
    
```


        <script type="text/javascript">
        window.PlotlyConfig = {MathJaxConfig: 'local'};
        if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
        if (typeof require !== 'undefined') {
        require.undef("plotly");
        define('plotly', function(require, exports, module) {
            /**
* plotly.js v1.50.1
* Copyright 2012-2019, Plotly, Inc.
* All rights reserved.
* Licensed under the MIT license
*/
        });
        require(['plotly'], function(Plotly) {
            window._Plotly = Plotly;
        });
        }
        </script>




<div>


            <div id="dd4b4a82-7298-4aca-a831-30b047af860e" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("dd4b4a82-7298-4aca-a831-30b047af860e")) {
                    Plotly.newPlot(
                        'dd4b4a82-7298-4aca-a831-30b047af860e',
                        [{"colorbar": {"title": {"text": "Number of Species"}}, "colorscale": [[0.0, "rgb(247,251,255)"], [0.125, "rgb(222,235,247)"], [0.25, "rgb(198,219,239)"], [0.375, "rgb(158,202,225)"], [0.5, "rgb(107,174,214)"], [0.625, "rgb(66,146,198)"], [0.75, "rgb(33,113,181)"], [0.875, "rgb(8,81,156)"], [1.0, "rgb(8,48,107)"]], "locationmode": "USA-states", "locations": ["NC", "VA", "CA", "WY", "NM", "TX", "NV", "WA", "OR", "HI", "PR", "VI", "AZ", "AR", "IL", "WI", "AK", "AL", "FL", "GA", "IA", "ID", "IN", "KS", "KY", "LA", "MA", "MD", "ME", "MI", "MN", "MO", "MS", "MT", "ND", "NE", "NH", "NY", "OH", "OK", "PA", "SC", "SD", "TN", "UT", "VT", "WV", "CO", "GU", "MP", "CT", "DE", "NJ", "RI", "AS", "PW", "DC"], "marker": {"line": {"color": "white"}}, "type": "choropleth", "z": [413.0, 316.0, 1734.0, 137.0, 270.0, 582.0, 472.0, 233.0, 461.0, 1481.0, 187.0, 130.0, 475.0, 193.0, 154.0, 113.0, 84.0, 530.0, 592.0, 396.0, 85.0, 184.0, 133.0, 77.0, 208.0, 122.0, 86.0, 110.0, 70.0, 115.0, 95.0, 165.0, 177.0, 106.0, 39.0, 55.0, 64.0, 133.0, 130.0, 121.0, 132.0, 218.0, 54.0, 397.0, 447.0, 49.0, 152.0, 236.0, 43.0, 34.0, 58.0, 93.0, 91.0, 36.0, 17.0, 4.0, 24.0]}],
                        {"geo": {"scope": "usa"}, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Species Distribution by U.S States"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('dd4b4a82-7298-4aca-a831-30b047af860e');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>



The region that have most diverse ecosystem is Carlifornia, and Hawaii is ranked second.

### We can see the distribution of endangered species


```python
df_end = df[df.Status == "Endangered"] # subset a dataframe with endangered species
df_end.index = range(len(df_end.index)) # reset the index
df_end.head(10) # have a look at the data frame
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
      <td>1</td>
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
      <td>2</td>
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
      <td>3</td>
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
      <td>4</td>
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
      <td>5</td>
      <td>Acanthomintha obovata ssp. duttonii</td>
      <td>San Mateo thornmint</td>
      <td>CA</td>
      <td>lamiaceae</td>
      <td>1985</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Accipiter striatus venator</td>
      <td>Puerto Rican sharp-shinned hawk</td>
      <td>PR</td>
      <td>accipitridae</td>
      <td>1994</td>
      <td>Birds</td>
      <td>Endangered</td>
      <td>V</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Achatinella spp.</td>
      <td>Oahu tree snails</td>
      <td>HI</td>
      <td>achatinellidae</td>
      <td>1981</td>
      <td>Snails</td>
      <td>Endangered</td>
      <td>I</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Achyranthes mutica</td>
      <td>NaN</td>
      <td>HI</td>
      <td>amaranthaceae</td>
      <td>1996</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Achyranthes splendens var. rotundata</td>
      <td>Round-leaved chaff-flower</td>
      <td>HI</td>
      <td>amaranthaceae</td>
      <td>1986</td>
      <td>Flowering Plants</td>
      <td>Endangered</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>




```python
count_species_end = count(df_end) # count the number of endangered species by states


# use plotly to make Choropleth Map 
fig = go.Figure(data=go.Choropleth(
        locations=count_species_end["Code"], 
        z = count_species_end["No. of Species"].astype(float), 
        locationmode = 'USA-states', 
        colorscale = "portland",
        colorbar_title = "Number of Species",
        marker_line_color = "white"
))

fig.update_layout(
    title_text = "Endangered Species Distribution by U.S States",
    geo_scope='usa')
    
```


<div>


            <div id="d8c0ec48-0467-49f9-9814-b42178fd708a" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("d8c0ec48-0467-49f9-9814-b42178fd708a")) {
                    Plotly.newPlot(
                        'd8c0ec48-0467-49f9-9814-b42178fd708a',
                        [{"colorbar": {"title": {"text": "Number of Species"}}, "colorscale": [[0.0, "rgb(12,51,131)"], [0.25, "rgb(10,136,186)"], [0.5, "rgb(242,211,56)"], [0.75, "rgb(242,143,56)"], [1.0, "rgb(217,30,30)"]], "locationmode": "USA-states", "locations": ["TX", "HI", "CA", "PR", "ID", "MT", "GU", "MP", "CT", "MA", "MD", "NY", "RI", "VI", "KY", "TN", "NC", "NH", "NJ", "PA", "VA", "VT", "FL", "GA", "AZ", "MO", "OR", "WV", "UT", "WA", "NM", "AR", "OK", "CO", "AL", "IA", "IL", "IN", "ME", "MN", "OH", "WI", "MI", "WY", "NV", "AS", "PW", "NE", "KS", "DE", "LA", "MS", "SC", "ND", "SD", "AK", "DC"], "marker": {"line": {"color": "white"}}, "type": "choropleth", "z": [79.0, 488.0, 209.0, 56.0, 4.0, 5.0, 25.0, 18.0, 6.0, 10.0, 10.0, 12.0, 5.0, 9.0, 37.0, 80.0, 44.0, 7.0, 6.0, 10.0, 53.0, 4.0, 89.0, 47.0, 43.0, 24.0, 22.0, 21.0, 22.0, 10.0, 33.0, 25.0, 12.0, 16.0, 86.0, 10.0, 22.0, 19.0, 6.0, 11.0, 17.0, 13.0, 13.0, 5.0, 24.0, 6.0, 3.0, 9.0, 9.0, 3.0, 11.0, 30.0, 20.0, 4.0, 9.0, 3.0, 1.0]}],
                        {"geo": {"scope": "usa"}, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Endangered Species Distribution by U.S States"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('d8c0ec48-0467-49f9-9814-b42178fd708a');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


It is interesting that the place with most endangered species is Hawaii and California is at the second place.
The taxonomic groups with least numbers of species may struggle to survive and grow, thus, they would be very rare and be potentially listed as endangered. In the previous section about taxonomic group, we see that these groups prefer marine environments. Therefore, Hawaii which is surrounded by seas would be the best home for them and has more endangered species than California.


From this section, we can conclude that Califorina and Hawaii should receive wildlife conservation priority.

## 4. Species Family

In this section, we will see which families have highes numbers of endangered species.

### At first, let's have a glance at the frequencies of all species


```python
#retrieve all the words in column Family and save into string
string = " ".join(fname for fname in df.Family)

# Create and generate a word cloud image
wordcloud = WordCloud(collocations=False, width=3000, height=2000) # collocations = False to avoid double words
wordcloud.generate(string)

# Display the generated image
fig = plt.figure(figsize = (40, 30), facecolor = 'k', edgecolor = 'k')
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis("off")
plt.show()
    
```


![png](output_25_0.png)


The family with most species is Asteraceae which is a sunflower family. Some families with high number of species such as Fabaceae, Brassicaceae, Scrophulariaceae and Polygonaceae are also flowing plants.

In terms of animals, invertebrate families have the most variety in species. As we can see from the wordcloud picture, some examples of animal families are Hydrobiidae (snails), Unionidae (mussels) and Carabidae (ground beetles). In vertebrates, Cyprinidae (a family of freshwater fishes) is the family with the highest number of species.


### The frequencies of endangered species


```python
string = " ".join(fname for fname in df_end.Family)

# Create and generate a word cloud image
wordcloud = WordCloud(collocations=False, width=3000, height=2000)
wordcloud.generate(string)

# Display the generated image
fig = plt.figure(figsize = (40, 30), facecolor = 'k', edgecolor = 'k')
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis("off")
plt.show()
    
```


![png](output_28_0.png)


Plant famliy with most endangered species is Asteraceae. Asteraceae not only has the highest number of species in the United States but also is the plant family that requires conservation priority. 

Although Unionidae does not have as many species as Asteraceae, this invertebrate family has a comparable number of endangered species to Asteraceae. We can also see that Cyprinidae and Hydrobiidae are the second and the third animal families with most number of endangered species, respectively. The reason would be that animals such as fishes, mussels and snails are often used to feed other types of animals, and many of them possess useful ingredients for human health. Therefore, they are often the targets of fishers. 





The analysis from this section suggests that we need to have appropriate policies to regulate people in fishing animals to prevent some underwater animal families from extinction.

## 5. Wildlife Research of U.S Fish & Wildlife Service

In this final section, I will do little analysis about the research of U.S Fish and Wildlife Serivice which is the organization I scraped my data from.


```python
df_listed = df[df.Status != "Not Listed"]
status = df.groupby("Status").size()
status.sort_values(ascending = True, inplace = True)
count_status = pd.DataFrame({"Status":status.index, "Count":status.values})

```


```python
size = status*100/status.values.sum()
labels = count_status["Status"].tolist()
```


```python
colors = ['yellow','yellowgreen','darkturquoise','violet','forestgreen',
          'chocolate','mediumspringgreen','dodgerblue','gold','orangered','lavender']
          
status.plot.pie(shadow = False, labels = None, radius = 1, colors=colors,
                wedgeprops={"edgecolor":"gray",'linewidth': 0.5, 'antialiased': True}, figsize = (10,10))

plt.title('Status Distribution', fontsize = 'xx-large')
plt.xlabel('')
plt.ylabel('')
plt.legend(bbox_to_anchor = (1,1), loc = 'best', labels=['%s, %1.2f %%' % (l, s) for l, s in zip(labels, size)])
plt.show()

```


![png](output_35_0.png)


According to U.S Wildlife and Fish Service, nearly 15% of the species in the United States are endangered. It is a high proportion and we need to have conservation plans to prevent further issues to the ecosystem.

### Number of Species Listed per Year


```python
date = df.groupby("First Listed").size()
year =[]
for i in range(1970,2025,5):
    year.append(i)
year = df["First Listed"].drop_duplicates().dropna()
year = year.tolist()


```


```python
plt.figure(figsize=(20,10))
date.plot(linewidth = 2)
plt.xticks(year, rotation = 30, ha = 'right')
plt.title('Number of Species Found per Year\n', fontsize ='xx-large')
plt.xlabel('Year', fontsize ='x-large')
plt.ylabel('Number of Species', fontsize ='x-large')
plt.show()
```


![png](output_39_0.png)



```python
df_date = df[df["First Listed"].notnull()] #save the species that have listing dates into a dataframe
df_date["First Listed"] = df_date["First Listed"].astype(int) #make sure year column is shown as int

#plot
plt.figure(figsize=(20,10))
sns.countplot(x='First Listed',  hue='Type', data=df_date)
plt.xticks(rotation = 30, ha = 'right')
plt.title('Number of Species Found per Year\n', fontsize ='xx-large')
plt.xlabel('Year', fontsize ='x-large')
plt.ylabel('Number of Species', fontsize ='x-large')
plt.legend(bbox_to_anchor = (0,1))

```




    <matplotlib.legend.Legend at 0x1a256d3d50>




![png](output_40_1.png)


During 1970s, there were many vertebrate species found in the United States. From the end of 1970s to 2000, more flowering plants species were found and successfully grown in the USA. In recent years, there have been fewer species discovered and most of them are animals.


```python
colors = ['crimson','darkturquoise','violet','orangered',
          'gold','mediumspringgreen','dodgerblue']
date_st = df_date["Status"].value_counts() #count the number of listed species by status
date_st = pd.DataFrame({"Status":date_st.index, "Count":date_st.values}) #convert the series into a dataframe

labels = ["Endangered", "Threatened", "Recovery", "Not Listed","Extinction","Threatened*","Review"]

# create plot using squarify
plt.figure(figsize = (20,10))
squarify.plot(sizes=date_st["Count"], label= labels, alpha=.75, color = colors, text_kwargs={'fontsize':15})
plt.axis('off')
plt.title("Listed Species Status Distribution\n ",fontsize=20)
plt.show()
```


![png](output_42_0.png)


As seen from the graph, the majority of listed species are endangered and threatened, which is in our expectation. These listed species require long time research to be indentified, hence, they were added to the U.S Wildlife list later than others. Some species without listing dates may be foreign species that United States import from other countries and regions.

# III. Conclusion

In summary, Hawaii and California are two states with most numbers of endangered species. Thus, there should be conservation priorities in these regions to maintain their biodiversity as well as prevent plants and animals living there from extinction. Another remarkable finding is the alarming condition of underwater families such as Unionidae (mussels), Hydrobiidae (snails) and Cyprinidae (freshwater fishes). Since they are favorite target of fishers, their population is dwindling and some species in these families are at risk of extinction. We need appropriate regulation about fishing to protect them.