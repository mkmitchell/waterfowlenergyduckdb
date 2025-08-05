Waterfowl energetics modeling to support Joint Venture planning.

How to use the model and methods.

How to use:
Set the beginning parameters for your run.
start_date = example: Date you want the model to start off on.  Default: "Aug 1 2023"
numofdays = How many days to run the model from the start date. Default: 228
removewater = Remove open water from the analysis.
customcurves = Allows the user to create and use custom curves if the provided ones aren't sufficient. Defined in customcurvesdict
smoothcrops = Smooths out the curves defined for crop availability. Default: True
kcalperduck = On average how many kcal per day a duck requires.  Default: 295
removeteal = Remove AGWT and BWTE from calculations? Default: False
geese = Include geese in the model?
kcalpergoose = Default: 500
goosecurve = The goose curve is defined by this dictionary: 'statebcr':{'pop':population max on a given day, 'curve':['time is divided evenly between these values where 1 will equal 'pop']}
goosereductionpct = Percent of the values listed above will be used
goosecrops = ['corn', 'milo', 'sorghum', 'rice','soybeans']
# Waterfowl in this analysis
keepducks = ['AGWT', 'AMWI', 'BWTE', 'GADW', 'MALL', 'NOPI', 'NSHO', 'RNDU', 'WODU']

baseaoiurl = 'https://giscog.blob.core.windows.net/waterfowlmodel/' # Data location
aois = ['ARmav', 'ARwg', 'KY', 'LAmav', 'LAwg', 'MO', 'MS', 'OK', 'TN', 'TX'] # used to read in files. example: baseaoiurl+aoi+'daily_obj3.csv'

Methods:
Create table for waterfowl energy using the data created from the createnewenergy.ipynb notebook.
This dataset has Lo and Hi energy values in Duck Energy Days.

Read in area of interest (aoi) from the LMVJV boundary feature service.
Read in couynty table from azure and create table
Read in BCR data from BCR feature service.
Creates table for BCR.
Join state and bcr geometry as a new table.
Join statebcr and the energy table

Convert this statebcr energy table to a dataframe
Calculate acres and set class and statebcr for all records

Read in population objectives which is a csv stored in azure.
This list population objectives by day for statebcr / species

Read in population curves
each statebcr csv file has one row for species then 228 rows, one for each day.  It's population objective curve.

Replace population curves if customcurves are setup.
Read in goose curves if setup.

Merge population objective # and population curve data.
Get max of the curve data and compare that to the population objective # to create a scale value.
Example: If max in the population curve is 100 and the population objective # is 80 the scale value would
be 0.8
The population curve values would be multiplied by the scale value.

Read in decomp by class and merge with energy table.

Create some population curves for every species by statebcr

Extrapolate 3 point habitat curve to the length of days (228)

## Start daily energy calculation
Set tracking values for the day iteration.
Iterate for every day
    Get habitat curve for the current day to define acreage availability.
    Calculate energy availability which is available habitat acres * energy value
    Supply = current - previous + leftover
        Example: yesterday there was 100 energy, today there is 110 energy.  leftover was 30
        current energy is 110 - yesterday of 100.  So 10 new additional energy.  leftover from yesterday
           was 30 so 10 + 30 = 40 energy today.
    Energy supply is spatially explicit across the landscape but demand is limited to the statebcr level.
    We divide the demand up based on energy supply.
    Remove energy goose eat if setup.
    Remove decomposition.
