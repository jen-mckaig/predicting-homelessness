# Predicting Homeless Population Sizes 

Homelessness in America is a deepening crisis that is inextricably linked to poverty and systemic injustice. The current system to prevent and to house homeless individuals is inadequate and ill-equipped. In this project, I gathered publicly available data to build a predictive model that provides insight into homelessness population growth.

<p align="center"> <a href="http://www.youtube.com/watch?feature=player_embedded&v=7f9dqQBYjcA" target="_blank"><img src="http://img.youtube.com/vi/7f9dqQBYjcA/0.jpg" 
alt="myimage" width="500" height="380" border="10" /></a> </p> 

[*video from Princeton Eviction Lab](https://evictionlab.org/why-eviction-matters/#video)

The United States Department of Housing and Urban Development(HUD) gives grants to "Continuum of Care" applicants through an annual competition. A Continuum of Care (CoC) is a regional or local planning body that coordinates the funding for housing and homeless prevention services. CoCs distribute resources to nonprofits and local government programs across the regions they represent. The population size and geographic areas covered by each CoC are dramatically different across state lines. 

Two critical activities entrusted to CoCs are the biannual physical count of homeless people and an annual enumeration of transitional housing units and homeless shelter beds. These counts provide an overview of the state of homelessness in a CoC area and help HUD allocate resources. The counts do not reflect the people who do not wish to be seen or those who may have found shelter on that particular day. 

## Scope of Project: 
For this final project at the Flatiron School, I had 10 days to determine a topic/hypothesis, assemble the dataset, and develop an appropriate model. This was a self-directed project with weekly student check-ins. 

## Primary Data Sources | Years 2007:2016

[US Department of Housing and Urban Development: CoC Homeless Counts](https://www.hudexchange.info/resource/3031/pit-and-hic-data-since-2007/)

[US Social Security Administration: Supplemental Security Income Annual Reports](https://www.ssa.gov/policy/docs/statcomps/ssi_sc/2016/index.html) 

[US Department of Labor's Bureau of Labor Statistics: Unemployment Rates](https://www.kaggle.com/jayrav13/unemployment-by-county-us/data)

[Princeton University: Eviction Lab Statistics](https://data-downloads.evictionlab.org/)

[Kaiser Family Foundation: State Mental Health Agency Expenditures per Capita](https://www.kff.org/other/state-indicator/smha-expenditures-per-capita/?currentTimeframe=0&sortModel=%7B%22colId%22:%22Location%22,%22sort%22:%22asc%22%7D)

[HUD Exchange: Continuum of Care Coverage](https://www.hudexchange.info/resource/4981/fy-2016-continuums-of-care-names-and-numbers/)
*(compiled the county list manually)*

## Compiling Data 

- Using python/pandas, annual HUD CoC files were converted into a single dataframe containing CoC number, year, homeless count. 

- Excel workbooks of annual Social Security data contained sheets for each state with spend itemized by county and demographic on each sheet. This data was compiled into a single dataframe.

- Unemployment data was downloaded via Kaggle. I grouped monthly observations into annual averages.  

- Location data was compiled from three sources and merged into a dataframe with CoC number, FIPS code, latitude, longitude, county, state, and state abbreviation.

- Eviction state/county data was merged with location data using FIPS/GEOID codes. Social security and unemployment data were then added to the eviction data.  

![newplot](https://user-images.githubusercontent.com/54602329/65059429-ffd9a280-d943-11e9-8e93-231a6809c334.png)

 
To converge the compiled data with CoC homeless counts,I grouped the observations into geographic areas using the CoC number. Features containing totals were summed together. Features containing percentages, averages, or medians were averaged. Data was merged to create the initial dataset with 3157 observations and 34 columns. 

## Exploratory Data Analysis

A scatterplot graph of population size by homeless count showed outliers with homeless counts above 20,000 & population sizes above 5,000,000. To keep these data points in the set, the homeless counts were capped at 5M+ and the population is capped at 20K+.

<img width="466" alt="1" src="https://user-images.githubusercontent.com/54602329/76548922-e2eed980-6465-11ea-9b29-f93aa1678538.png">

I checked for missing data and found that 498 observations were missing from the eviction rate feature and missing 448 observations were missing from the eviction filing rate feature. Missing values were imputed with the median values for each feature. 

The distribution of many of the features are not normal, violating one assumption of linear regression. A logrithmic function did improve most of the feature distributions, but I determined not to use this in the final model. 

<img width="882" alt="Screen Shot 2020-03-12 at 1 43 57 PM" src="https://user-images.githubusercontent.com/54602329/76550332-5b569a00-6468-11ea-95cb-58dbee261c23.png">
<img width="882" alt="Screen Shot 2020-03-12 at 1 49 33 PM" src="https://user-images.githubusercontent.com/54602329/76550341-5f82b780-6468-11ea-89e7-3ecf5cebf515.png">

## Feature Engineering

Data on per capita spend for years 2014-2016 were scraped from the samhsa.gov site and the Kaiser Foundation provided the rest of the data for 2007-2013. Two new features were added, per capita smha and total (budget) smha.

![mental](https://user-images.githubusercontent.com/54602329/65061125-44b30880-d947-11e9-9251-b95a9c650932.png)

## Feature Selection

Highly correlated features were initially removed in order to prevent bias in the model, but after determining that the final model would be a random forest algorithm, they were left in. 

## Transformations

After scaling the data, testing logarithmic functions and  polynomial regression, I determined that in the final model no transformations or scaling will be used in order to maximize interpretability.


## Initial tests

#### Models with logged, scaled and interaction features + dropping features with > .90 correlation 

Linear Regression - with logged & interaction features, R2: 0.77

Decision Tree Regression with Grid Search CV, R2: 0.85

Random Forest Regression with Grid Search CV, R2: 0.93


# Final Model: Random Forest Regression 

After calculating the cumulative sum of the features, any features that were contributing below the 95% threshold were removed.

Random Forest Regression with Grid Search CV

Parameters = {'criterion': 'mse', 'max_depth': 14, 'max_samples': 0.5, 'min_samples_split': 2, 'n_estimators': 1000}

R2: 0.94

MAE: 288

OOB: 0.93

![download](https://user-images.githubusercontent.com/54602329/76556163-695be900-646f-11ea-86cc-8431accef69c.png)



## Conclusion

The number of renter occupied households is a strong indicator of homelessness, followed by elderly populations who are living in poverty. It is clear that the housing crisis is driven by a lack of affordable housing and renter protections.

A predictive model can potentially eliminate the problematic manual count and/or grant competitions, allowing for systematic distribution of resources. Funding for homelessness services should be determined by need, irrespective of the current political climate and free from potential institutional bias. Homelessness can be determined at the county level, which could lead to a community-based approach and stronger data collection. 

There are additional contributing factors to homelessness such as domestic violence, natural disasters, immigration policy, addiction, and divorce. Including some or all measurements of these factors could improve the model as well. 
