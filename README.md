# superfund_housing_prices
Analysis on the effect of housing prices near EPA Superfund Sites


##Note: This project is currently active and text/code/files can change


## Project Overview and Abstract
This project attempts to test the hypothesis that upon a location being formally designated as a Superfund Site during the Comprehensive Environmental Response , Compensation, and Liability Act (CERCLA) process, prices in the surrounding neighborhoods experience a decrease in their value. 

However, the CERCLA process is often lengthy and contaminated sites are typically formally designated by a state agency as a clean-up site prior to the site being listed on the National Priorities List (NPL) and being included in the Superfund Program. Later analysis of this project will attempt o identify effects, if any, of formal contaminated site designation aside from Superfund on the surrounding housing prices. 


## Background Information

The Environmental Protection Agency (EPA) is charged with administrating over the Superfund program, which aims to locate, investigate, and clean up hazardous waste sites. The Superfund program was established by CERCLA in 1980. Additional information on the Superfund program can be found on the EPA website: https://www.epa.gov/superfund


## Methods

This section details the methods used to obtain and transform the Superfund Site location data, housing price data, and subsequent data analysis. 

### EPA Superfund Site Location Data Extraction

The EPA maintains a database of active and archived Superfund sites available through their Superfund Enterprise Management System (SEMS): https://www.epa.gov/enviro/sems-overview)

Data were retrieved from this database using API retrieval. This process with done in two steps as the SEMS database has different table names for active and archived sites. Different attempts were made to retrieve the Superfund Site data for all locations without using the for-loop shown below; however, previous attempts either did not result in data being retrieved or took so long as to not be useful. The code to obtain the information for active sites is below:

'''
x = 0
for state in states:
    request = requests.get(f'https://data.epa.gov/efservice/SEMS_ACTIVE_SITES/SITE_STATE/CONTAINING/{state}/JSON').json()
    temp_df = pd.DataFrame(request)
    if x == 0:
        sf_active_df = temp_df.copy()
        print(f'{state} added to DataFrame')
        x = 1
    else:
        sf_active_df = pd.concat([sf_active_df, temp_df])
        print(f'{state} added to DataFrame')
'''

A list of United States state codes (e.g., CA for California) was iterated through and added to the API URL and returned the data in a JSON format, which was then converted into a Pandas data frame. Successive data was then concatenated to main active site data frame. 

Issues arose while retrieving archived data in the form of "JSONDecodeError: Extra data:" errors for the following states: FL, GA, IA, KS, KY, MD, MA, MN, MO, MT, NE, NJ, NM, NY, ND, OH, PA, PR, RI, SD, and TX. A solution was found by downloading the archived data as CSV files and then converted into data frames. The code is below:

'''
x = 0
for state in states:
    try:
        request = requests.get(f'https://data.epa.gov/efservice/SEMS_ARCHIVED_SITES/SITE_STATE/CONTAINING/{state}/CSV')
#         url_data = request.content
        temp_df = pd.DataFrame(request)
        if x == 0:
            sf_archived_df_csv = temp_df.copy()
            print(f'{state} added to DataFrame')
            x = 1
        else:
            sf_archived_df_csv = pd.concat([sf_archived_df, temp_df])
            print(f'{state} added to DataFrame')
    except Exception:
        print(Exception)
        print(f'Exception occurred on {state}')
'''

### Superfund Site Data Transformation

1)
 
