# uk-wind-modelling-basic

The idea is to create some simple wind models for predicting wind farm generation

## Wind Farm Data

Lets the the wind generation ata from the Elexon API. Technically its a 1 hour forecast, but I think its good to start with.
Lets look at the data from the `WesterMost Rough` wind farm. Its located 53°49′N 0°09′E, which is in the North Sea, just off the coast of Hull.

A good package to use is https://github.com/OSUKED/ElexonDataPortal - `pip install ElexonDataPortal`

The expected wind generation can be generated from

`
from ElexonDataPortal import api
client = api.Client('your_api_key_here')

start_date = '2020-01-01'
end_date = '2020-01-02'

# data from `WesterMost Rough` wind farm
df_PHYBMDATA = client.get_PHYBMDATA(start_date=start_date, end_date=end_date, BMUnitId='T_WTMSO-1')
df_PHYBMDATA = df_PHYBMDATA[df_PHYBMDATA['recordType'] == 'PN']
df_PHYBMDATA = df_PHYBMDATA[['local_datetime','pnLevelFrom']]
`
*PN means Physical notification, this is how much the wind farm is expected to generate.

# NWP data

Lets get some NWP data from the GFS. This is a global model, so we will need to extract the data for the UK.

A good package to use is Herbie - https://herbie.readthedocs.io/en/stable/

# Create a range of dates
dates = pd.date_range(
    start="2023-01-01 00:00",
    periods=4,
    freq="6H",
)

# Get GFS data
fxx = range(0, 1)
# Make FastHerbie Object.
FH = FastHerbie(dates, model="gfs", fxx=fxx)
ds = FH.xarray(":(U|V)GRD:80 m")
ds = ds.sel(latitude=slice(53, 53), longitude=slice(0, 0))
u = ds.u.values
v = ds.v.values
time = ds.time.values

# ML

1. Decide on your train and test set, perhaps 1 year of each.
2. Start with a simple model, and try to predict the wind generation
3. Evaluate your results, and iterate.





