# Data Mining on the United States Wildlife

Welcome!
This is my individual project 3 for my data mining class. In this project, I will do some data mining and analysis about the United States Wildlife. 
The programming language I use is Python with supports from plotly.

---

# Introduction

The ecosystem in our planet is getting worse day by day. Not only the environmental change but also the extinction of many species result in detrimental effects to the ecosystem. Many people are not aware of the importance of wildlife since they think animals and plants have no influence on their lives. However, in fact, wildlife plays a key role in improving the quality of human life. For example, a variety of wildlife species can promote the biodiversity which benefits human health and maintains ecosystem's functionality. Furthermore, many medicines that address popular health issues such disorders and heart diseases are the productions of chemicals from plants and animals.

Since many species are at risk of extinction, it is extremely important to have appropriate conservation plans to protect our planet's ecosystem.

This project presents data mining on United States Wildlife to identify which regions need to be prioritized in conservation. In addition, the most endangered species family will be mentioned to understand which plants and animals should be the targets of protection.

---

## Scraping the data

In this section, I scrape my data from U.S Wildlife & Fish Service website: https://www.fws.gov/
The data is retrieved from the search page: https://ecos.fws.gov/ecp0/reports/ad-hoc-species-report-input
There is a problem that the url does not change when I selection more attributes for the table. Fortunately, I figured out a solution for this. More details are provided in my scraping notebook below.

[Scraping Process](https://github.com/hieu2695/U.S-Wildlife/blob/master/ScrapingProcess.md)


## Data Preprocessing

In this part, I clean my data for better usage. 
There are two main things I want to do:
1. Extract the U.S state codes and remove any description in the Region column.
2. Extract the Year in the First Listed Date column.
There are some minor issues with other columns which are also managed in this step.

[Data Preprocessing](https://github.com/hieu2695/U.S-Wildlife/blob/master/Preprocessing%20Data.md)

## Data Analysis

[Data Analysis](https://github.com/hieu2695/U.S-Wildlife/blob/master/html/Analysis.html)


