## **World Cities Data Processing Documentation**

**1\. Data Loading and Initial Exploration**

* **Environment Setup:** Necessary libraries (pandas, numpy, matplotlib, seaborn, google.colab) are imported. Google Drive is mounted to access the dataset.  
* **Data Loading:** The worldcities.csv dataset is loaded into a pandas DataFrame (df).  
* **Basic Information:**  
  * The original shape (rows, columns) of the dataset is printed.  
  * Data types of each column are displayed.  
* **Data Quality Checks:**  
  * **Missing Values:** The number of missing values for each column is calculated and printed. Significant missing values are noted in iso2, admin\_name, capital, and population.  
  * **Duplicates:** The number of complete duplicate rows is checked (found to be 0 initially).  
  * **Summary Statistics:** Descriptive statistics (count, mean, std, min, max, quartiles) are calculated for numeric columns (lat, lng, population, id).  
  * **Outlier Detection:** A preliminary check for population outliers (cities with population \> 50 million) is performed (none found initially).  
  * **ISO Code Consistency:** A check is performed to identify potential mismatches between iso2 and iso3 codes based on length and prefix matching.  
  * **Coordinate Validity:** Latitude and longitude values are checked to ensure they fall within valid ranges (-90 to 90 for latitude, \-180 to 180 for longitude). No invalid coordinates were found.  
  * **Capital Designation:** The distribution of values in the capital column (including missing values) is examined.

**2\. Data Cleaning**

* **Text Cleaning:**  
  * city and admin\_name columns are cleaned by converting fully uppercase names (longer than 3 characters) to title case.  
* **Duplicate Removal:** Duplicate city entries are removed based on the combination of city, country, and admin\_name. The number of removed duplicates is printed.  
* **Column Dropping:** Unused or redundant columns (id, city\_ascii, iso2) are dropped from the DataFrame.  
* **Handling Missing Data:**  
  * **Specific Imputation:** The missing population for 'Charlotte Amalie' is manually filled.  
  * **Categorical Imputation:** Missing values in the capital column are filled with the string 'Not Capital'.  
  * **Numeric Imputation:** Missing population values are imputed using the mean population of the respective country.  
  * **Type Conversion:** The population column is converted to integer type after filling NaNs with 0 (temporarily, before removing 0-population cities).  
* **Handling Population Data:**  
  * **Type Conversion:** Population is explicitly converted to numeric, coercing errors.  
  * **Outlier Flagging:** Cities with potentially unrealistic populations (\> 50 million) are flagged (though none were found in this dataset after initial cleaning). A new boolean column population\_estimated is created to track imputed populations.  
  * **Zero Population Removal:** Cities with a population of 0 (including those initially imputed as 0 before country-average imputation) are removed from the dataset.  
* **Feature Engineering (Binning):**  
  * A new categorical column population\_category is created by binning the population into 'Small', 'Medium', 'Large', and 'Mega' categories based on defined population thresholds.  
* **Missing admin\_name Imputation (Geolocation):**  
  * The geopy library (specifically Nominatim via aiohttp for asynchronous requests) is used to fill missing admin\_name values.  
  * Rows with missing admin\_name are selected.  
  * An asynchronous function (fetch) is defined to query the Nominatim reverse geocoding API using latitude and longitude to retrieve state/county/region information.  
  * An asynchronous manager function (fetch\_all) handles multiple requests with a polite delay between them to respect API usage limits.  
  * The asynchronous fetching is executed for all locations with missing admin\_name.  
  * The retrieved administrative names are filled back into the DataFrame.  
  * Any remaining missing admin\_name values are filled with the corresponding country name as a fallback.  
* **Data Validation (Coordinates):**  
  * Latitude and longitude values are clamped to their valid ranges (-90/90 and \-180/180 respectively) to correct any potential minor inaccuracies introduced earlier or present in the original data (though the initial check found none outside these ranges).

**3\. Data Validation, Preview, and Export**

* **DataFrame Copy:** A copy of the cleaned DataFrame is created (cleaned\_df).  
* **Final Null Check:** A final check confirms that there are no remaining null values in the cleaned DataFrame.  
* **Interactive Preview:** An interactive spreadsheet view of the cleaned\_df is generated using google.colab.sheets for easy inspection.  
* **Data Export:** The final cleaned DataFrame (cleaned\_df) is saved to a new CSV file named cleaned\_worldcities.csv and downloaded.

**4\. Exploratory Data Analysis (EDA) on Cleaned Data**

* **Data Loading:** The cleaned\_worldcities.csv file is loaded into a new DataFrame (df\_cleaned). *Note: The notebook code seems to reload the original df in this section's first cell, but then loads df\_cleaned immediately after. Subsequent analysis uses df which appears to hold the cleaned data from the previous steps.*  
* **Basic Exploration:**  
  * The first few rows (head()) are displayed.  
  * Descriptive statistics (describe()) for numeric columns are shown again.  
  * Null values and duplicates are re-checked on the cleaned data (expected to be 0).  
  * The number of cities per country (value\_counts()) is calculated and displayed.

**5\. Visualization**

* **Top Countries by City Count:** A bar chart visualizes the top 10 countries with the highest number of cities listed in the dataset.  
* **Top Populated Cities:** A bar chart shows the top 10 most populated cities based on the population column.  
* **Interactive World Map (All Cities):** An interactive map is created using Folium. Circle markers represent each city, with popups showing city, country, admin name, and population upon clicking. The map is saved as worldcities\_map.html.  
* **Largest/Smallest Population Cities:**  
  * The cities with the absolute maximum and minimum recorded populations are identified and their details (city, country, population) are printed.  
  * Another Folium map is generated, specifically marking the largest population city with a red marker and the smallest with a blue marker, including popups with details.  
* **Hemisphere Analysis:**  
  * A hemisphere column ('Northern' or 'Southern') is added based on latitude.  
  * The data is grouped by hemisphere a  
  * nd population\_category.  
  * A summary table showing the count of cities in each category per hemisphere is generated.  
  * The percentage distribution of population categories within each hemisphere is calculated and displayed.  
  * Several visualizations are created for the hemisphere data:  
    * Stacked bar chart showing counts.  
    * Stacked bar chart showing percentages.  
    * Grouped bar chart showing counts.  
    * Heatmap showing counts.  
    * Pie charts (one for each hemisphere) showing the distribution of population categories.


    https://github.com/sajidhossam/World-Cities/blob/main/Visualaizations/Interactive%20Maps/worldcities_map.html