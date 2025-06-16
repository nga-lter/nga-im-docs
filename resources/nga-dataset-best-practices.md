# NGA LTER Dataset Best Practices v1.0
Rules and suggestions for formatting tabular data files from the NGA LTER site.


## Intro
### General notes
These best practices are designed to help create data files that make it easier to document and archive datasets while making those data files easier programmatically visualize, integrate, or analyze. Conforming to these best practices should be considered part of the data file QC process, and can be implemented at the data processing phase that makes the most sense for the individual dataset and PI.

This document will be revisited as needed, with revisions published as needed, but not more often than once per year. Once released, each version will be numbered and will include the release year in the title.
### Tidy Data
This is a format for making the data easiest to interact with programmatically, but it may not be the easiest for viewing as a summary of observations. The gist of it:
 1. Each variable forms a column
 2. Each observation forms a row
 3. Each type of observational unit forms a table
In short, one observation per row (this means many more rows than is typical)

[Here’s an informal summary](https://cran.r-project.org/web/packages/tidyr/vignettes/tidy-data.html) of tidy data, and [here’s a link](https://vita.had.co.nz/papers/tidy-data.pdf) to the longer, detailed paper explaining the structure and rationale.

If we want to use the Jupyter notebooks to easily create summary charts, section plots, and to aggregate data from different tables and sources, we should aspire to create some version of each dataset that is tidy or ‘tidy-ish’. This could be the same version of the data that we archive, but needn’t necessarily be so.

## Naming conventions
### Data file names
#### Cruise data (including underway and acrobat data)
nga_[cruise-code]_\[data-type\]_L\[processing-levels\]\(_version\).\[file-type\]
#### Mooring data
nga_[mooring-code]\_\[YYYY\]\_[data-type]_L\[processing-levels\]\(_version\).\[file-type]
#### Model results
I haven’t included a template for model results. Those should include some of the same things (site, date(s), type, etc.), and probably other contextual info that I haven’t considered. I’ll talk with Claudine and Jerome about what these should look like. 
### File name componenets
#### processing-level = {0, 1, 2, ...}
These will be definied for each data type and will be documented in the readme.txt file in the [Research Workspace](www.reashearchworkspace.com) project for the data.
#### version = {1, 2, 3, …}
Version numbers should be incremented if edits are made to submitted data. This will help us, and potential reusers of the data, understand which versions of the data have been or should be used. To clearly differentiate between versions, document the changes made to the dataset in the metadata.
#### cruise-code = { SKQYYYYNN[T|S], TGXYYYYMM, WSDYYYYMM } , suggested formats defined below
For cruises aboard Sikuliaq, Nanuq, or other UNOLS vessels, we’ll use the cruise designation supplied by the vessel operators, like this: \[vessel-code\]\[YYYY\]\[NN\]\[T|S\]

For our other cruises on non-UNOLS vessels , we’ll the format the cruise code like this: \[vessel-code\]\[YYYY\]\[MM\], where
 - vessel-code = { SKQ, TGX, WSD,  }
 - YYYY = year of cruise
 - MM = month of departure
 - NN = consecutive cruise number for the calendar year
 - T|S = { Transit leg, Science leg }

Ex. SKQ201807S, TGX201809
#### Mooring-codes = {GAK1, GEO1, GEO2}
Where ‘GAK1’ is the GAK1 mooring, ‘GEO1’ is the mooring replaced annually, and ’GEO2’ is the separately moored real-time component, etc. A full list of NGA moorings ~~is~~ will be made available on the NGA LTER website, and we'll link to that once it exists.


## Data File Formatting
### Column names
\[parameter or info type_\]\(specification\]_\[units\]

### Data-type
A unique, descriptive, short, and consistent code that describes what type of data is in the file. 

Use underscores rather than spaces. Do not use commas in column names.

Exs. “PAR”, “Beam_attenuation_\[%\]”, “Fluoresence_\[mg/m3\]”, ”Date”, ”Julian_day”, “Station_number”, etc., etc.
### Standard Columns
We want to agree on the minimum columns that will be included in field data sets and the order in which they will appear. This will make it easier to bring datasets into ODV for analysis, and into scripts for visualizing data or integrating multiple files into larger synthetic data products.

Proposed columns based on ODV requirements.  These would be required as the first nine columns in this standard order, with varying types and numbers of data columns following. The names of the columns are based on ODV defaults.

Index | Variable_name | Variable_description | Value_type | Notes
----- | ------------- | -------------------- | ---------- | -----
1 | Platform | Cruise code, mooring or glider name | text | ODV Required
2 | Station | use for cruise data sampled at oceanographic stations | text | ODV Required
3 | Type | C = CTD, U = Underway, B = Bottles, M = mooring instrument | text | ODV Required
4 | Date_time | ISO 8601 formatted datetime in UTC | text | ODV Required
5 | Longitude_\[decimal_degrees_east\] |  | float | ODV Required
6 | Latitude_\[decimal_degrees_north\] |  | float | ODV Required
7 | Bot. Depth \[m\] | Bottom depth at station | float | ODV Required
8 | Cast_Number | Consecutive cast number | integer | 
9 | Depth_[m] | Depth of measurement, positive down, could be Pressure_\[dbar\] | float | ODV Required
10 | Bottle_Number | Number (integer) of the bottle from which the sample was taken, if necessary. | text | 
' ' | ... | Data columns |  |  
' ' | 1 ... | Quality flag columns | integer |  
' ' | Cruise_Alias | Alternate name for the cruise, if necessary | text | 
' ' | alternate_filename | other filename for the data. This field might be used to record the source file for an aggregation | text |  

### Types in Columns
Keep data types consistent in each column. Use only numeric values in numeric columns, and only characters or strings in text columns. If columns contain categorical data or codes, ensure that the values or code lists are documented.

Assign missing value codes whenever data is missing or values have been removed. 
 - “NA” - use when the column values are not applicable to the data, i.e. the “Station” and “Cast”  columns for underway data.
 - “NaN” - when there is no data in a record/row, e.g. instrument failed during a cast or a sample was lost or contaminated

### Dates
In just about all cases, put dates in ISO 8601, e.g.
	
Date-time in UTC:
 - YYYY-MM-DDThh:mm:ssZ (YYYYMMDDThhmmssZ)
 - 2018-04-22T13:05:22Z

Date in UTC with offset for AK timezone:
 - YYYY-MM-DDThh:mm:ss+/-hh:mm (YYYYMMDDThhmmss+/-hhmm)
 - 2018-04-22T13:05:22-09:00

Date in local AK time:
 - YYYY-MM-DDThh:mm:ss
 - 2018-04-22T04:05:22

(Where the hyphens are optional; Z means the time is in UTC; and the +/- is the offset from UTC.)

For Julian Dates (or ‘Ordinal Dates’), it’s best to include the year, i.e. YYYY-DDD. To avoid confusion, always label the field as ‘Julian Date’

#### Interested in using some other, [objectively incorrect date format](https://xkcd.com/1179/) in your local files?
In Excel, dates in ISO 8601 can be converted to Excel date format by following the instructions listed on https://www.smartftp.com/en-us/support/kb/2684
To convert the date time string into a date column:
 1. Enter the following formula into the first cell of an empty column: **=DATEVALUE(MID(A1,1,10))+TIMEVALUE(MID(A1,12,8))**
 2. Select the cell and drag the fill handle to the end of the column
 3. Right-click on the column header and select Format Cells...
 4. Select any of the available Date formats


### Latitude and Longitude
Let’s always call these **Latitude_[decimal_degrees_north]** and **Longitude_[decimal_degrees_east]**.

Record all latitude values in decimal degrees north latitude and all longitude values in decimal degrees east longitude, ideally with six decimal places for electronically logged positions and fewer for hand-logged positions (though please include as are appropriate). 

Example latitudes: 60.210913; 59.138789; 61.804241
Example longitudes: -146.3489247; -151.234978; -147.764816

### Cast_Number
The consecutive cast number from the start of the cruise.
### Station_Name
The name of the station at which the cast was made. 
### Quality Flags
Let’s format quality flag column names like this: (parameter(s)\_)\[(quality_)flag].

Examples: “quality_flag”, “pressure_and_depth_flag”, “temperature_flag”

Include at least one data quality flag in each file. Whenever more than one parameter (or measurement, observation, analytical result, etc.) is present in a data file, it might become necessary to include more than one quality flag column. This could be necessary in order to specify which column (or columns) contain the values described by the quality flag. When more than one quality flag column is used, specify the applicable columns in the name of the quality flag column.

#### Flag Values
Code | Value | Definition 
---- | ----- | ---------- 
1 | Good | Passed documneted QC tests 
2 | Not evaluated, not available, unknown | Used for data when no QC test was performed, or the information on data quality is not available 
3 | Questionable | Failed non-critical documented metric or subjective test 
4 | Bad | Failed critical documented QC test(s) or as assigned by the data provider 
5 | Estimate | Cell values were interpolated, extrapolated, or otherwise estimated 
6 | Below detection limit | Value is below the detection limit of the analytical methods applied 
7 | Missing data | Used as a placeholder when data are missing


