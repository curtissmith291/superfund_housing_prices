# Superfund Site Housing Prices Project

## Note: This project is currently active and text/code/files can change


## Project Overview and Abstract
This project attempts to test the hypothesis that upon a location being formally designated as a Superfund Site during the Comprehensive Environmental Response , Compensation, and Liability Act (CERCLA) process, prices in the surrounding neighborhoods experience a decrease in their value. 

However, the CERCLA process is often lengthy and contaminated sites are typically formally designated by a state agency as a clean-up site prior to the site being listed on the National Priorities List (NPL) and being included in the Superfund Program. Later analysis of this project will attempt o identify effects, if any, of formal contaminated site designation aside from Superfund on the surrounding housing prices. 


## Background Information

The Environmental Protection Agency (EPA) is charged with administrating over the Superfund program, which aims to locate, investigate, and clean up hazardous waste sites. The Superfund program was established by CERCLA in 1980. Additional information on the Superfund program can be found on the EPA website: https://www.epa.gov/superfund


## Methods

This section details the methods used to obtain and transform the Superfund Site location data, housing price data, and subsequent data analysis. 

### EPA Superfund Site Location Data Extraction

The EPA maintains a database of Superfund sites available through their Superfund Enterprise Management System (SEMS): https://www.epa.gov/enviro/sems-overview)

Data were retrieved from this database using API retrieval. This process with done in two steps as the SEMS database has different table names for active and archived sites. Different attempts were made to retrieve the Superfund Site data for all locations without using the for-loop shown below; however, previous attempts either did not result in data being retrieved or took so long as to not be useful. The code to obtain the information for Superfund sites is below:

```
x = 0
for state in states:
    try:
        request = requests.get(f'https://data.epa.gov/efservice/SEMS_ACTIVE_SITES/SITE_STATE/CONTAINING/{state}/JSON').json()
        temp_df = pd.DataFrame(request)
        if x == 0:
            sf_active_df = temp_df.copy()
            print(f'{state} added to DataFrame')
            x = 1
        else:
            sf_active_df = pd.concat([sf_active_df, temp_df])
            print(f'{state} added to DataFrame')
    except Exception:
        print(Exception)
        print(f'Exception occurred on {state}')
print('----------------------\n\
Data download complete \n\
----------------------')
```

A list of United States state codes (e.g., CA for California) was iterated through and added to the API URL and returned the data in a JSON format, which was then converted into a Pandas data frame. Successive data was then concatenated to main Superfund sites data frame. 


### Superfund Site Data Transformation

1) Data retrieved from the EPA database had "&"s replaced with "%26"s; a function was applied to replace "%26"s with "&"s. 

2) The individual address components (i.e., street address, city, state, and zip code) were appended and added to new column. 

3) Coordinates were not provided for a majority of the Superfund sites; coordinates were obtained using the Nominatum from the geopy module, then added to a new column. Of the original 13,167 Superfund Site IDs, 6,516 had addresses suitable for determining latitude/longitude coordinates. Due to the nature of some Superfund Sites being very large and ofter away from developed locations, addresses are given as the intersection of streets, which do not return physical address, thus, are not able to return coordinates. The 6,516 should be more than enough to for our analysis. 

# To-Do
- locate housing transaction data - finding enough housing data nearby site is going to be tricky
- locate nearby superfund site to housing data; using refactored distance calculator for prior project
- Will need to hand enter milestone dates for Superfund sites as they are not in the EPA Database
- 

# Thoughts
- ranking system for Sites? Categorize by contamination type?
- Categorize by urban/suburban/rural? Are there rules for that, e.g. number of miles from city center?
- Census Bureau has interesting APIs that could be used for interesting analysis: https://www.census.gov/data/developers/data-sets.html
 
 
