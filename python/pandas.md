# Pandas Tricks


## Avoid `Int to Float` when `df.T.to_dict().values()`

Instead of use `list(df.T.to_dict().values())`, use **`[{c:getattr(r, c) for c in df} for r in df.itertuples()]`**

> Ref: [https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast](https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast)

```python
import pandas as pd

df = pd.DataFrame({'a':[3,2], 'b':[42.0,3.14]})
result = [{col:getattr(row, col) for col in df} for row in df.itertuples()]
print(result)
# [{'a': 3, 'b': 42.0}, {'a': 2, 'b': 3.1400000000000001}]
```


## Read Excel
```python
xl = pd.ExcelFile(fn)
            
# read Sheets as DataFrames
NaN_List = ['-1.#IND', '1.#QNAN', '1.#IND', '-1.#QNAN', '#N/A', '#NA', 'NULL', 'NaN', '-NaN', 'nan', '-nan']  # Nashville term is NA
df_Node = xl.parse("Node", converters={'milepost': str}, keep_default_na=False, na_values=NaN_List)
df_Segment = xl.parse("Segment", converters={'startNode': str, 'endNode': str}, keep_default_na=False, na_values=NaN_List)
```

## Set display format
```python
import pandas as pd

pd.set_option('display.width',200)
pd.set_option('display.max_rows',20)
pd.set_option('display.max_columns', 15)
pd.set_option('display.precision', 3)
```


## Iterate DataFrame by rows
```python
for i,r in df.iterrows():
    if pd.isnull(r['OSRM_meter']):
        url = createURL(r)
```


## New calculated column
```python
df.loc[:, 'diff'] = df.apply(lambda r: r['c1'] - r['c2'], axis=1)
```


## Filter by conditions
```python
cond1 = df['Project'].map(lambda x: x in [1,2,3])
cond2 = df['type'].map(lambda x: x == 'TODO')
df[cond1 & cond2]['SQL']
```

## Get row index of filtered dataset
```python
cond1 = df['entry_ts'].map(lambda x: x <= aTime)
cond2 = df['next_entry_ts'].map(lambda x: x > aTime)
s = df[cond1 & cond2]
idx = s.index.tolist()
```

## Add row index as new column
```python
g.loc[:,'seq'] = pd.Seies([i+1 for i in range(len(g))], index=g.index)
```


## Select rows whose field value not in list / dict / Series
```python
aList = 'EGKNTUVW'
df = df[~df.t1.isin([c for c in aList])]
```


## Read from Excel
```python
xl = pd.ExcelFile(fn)
df = xl.parse('sheet1')
```

## Read from CSV
```python
df = pd.read_csv(fn, index_col=false)
```


## Parse datetime while reading from CSV
```python
parseFunc = lambda x: pd.datetime.strptime(x, '%Y-%m-%d %H:%M:%S')
df = pd.read_csv(fn, parse_dates=['startDate'], date_parser=parseFunc)
```


## Read DF from Stored Procedure
```python
import pandas as pd
import pandas.io.sql as psql
import pyodbc

connStr ='DRIVER={};SERVER={};DATABASE={};UID={};PWD={}'.format(
    '{SQL Server}',
    'MS_SQLServer_name',
    'DB_name',
    'username',
    'password'
)
conn = pyodbc.connect(connStr)

# SP with no parameters
df = psql.read_sql("{call  p_GetOPRP_TP_05}", conn)

# SP with parameters
df = psql.read_sql("{{call  LOR_SIM.dbo.p_getTM2({0}, '{1}')}}".format(
    param.SCENARIO_ID, 
    subdiv
), conn)
```


## Groupby(), get_group(), get group size
```python
grp = df.groupby(['TRAIN_ID'])
g = grp.get_group('Q123')
sizeDict = grp.size().to_dict()
```


## Iterate group and sort by columns

```python
for tid in df_tm6_grp.groups.keys():
    g = df_tm6_grp.get_group(tid)
    g = g.sort_values(by=['ON_STATION_TS'], ascending=[True])

df.sort_values(by=['trainID', 'stationSeq'], ascending=[True, True], inplace=True)
```


## Groupby aggregate
```python
tmp = df.groupby(['start_MP', 'end_MP']).agg({
    'runTime': {
        'count_all': 'count',  # count
        'count_distinct': 'nunique',  
        'mean': 'mean',
        'median': 'median',
        'std': 'std',
        'min': 'min',
        'max': 'max',
    }
})
```


## Rename agg columns
```python
    grp = df.groupby(['DC_NBR', 'QUAD_ID']).agg({
        'ASSOCIATE_ID': {'count_': 'nunique'},  # distinct
        'LANE_ID': {'count_': 'count'},
        'VOLUME_PCT': {'sum_': 'sum'},
    }).reset_index(inplace=False)
    grp.columns = [''.join(reversed(col)).strip() for col in grp.columns.values]
```


## Pivot Table / Calculate multiple statistics of the same column
```python
    pt = df.groupby(['t4'])[['runDuration']].agg([
        pd.Series.mean,
        pd.Series.std,
        pd.Series.count,
    ], as_index=False).reset_index(inplace=False)
    
    # Flatten name
    pt.columns = [''.join(col).strip() for col in pt.columns.values]
    print(pt)
```


## Join two DataFrame
```python
    df = df.merge(df1, on=['id','type'], how='left')
```


## Rename columns
```python
df = df.rename(columns={
    'latitude': 'lat',
    'longitude': 'lon',
    'before': 'after',
})
```

## Concat two/more DataFrames
```python
    df = pd.concat([df1, df2, df3])  # df1, df2, df3 must have same columns
```


## Convert multi-index into column
```python
    df.reset_index(inplace=True)
```


## DataFrame to list of dicts
> REF: <https://stackoverflow.com/questions/29815129/pandas-dataframe-to-list-of-dictionaries>
> 
Use `df.T.to_dict().values()` or `df.to_dict('records')` (faster)
```python
In [1]: df
Out[1]:
   customer  item1   item2   item3
0         1  apple    milk  tomato
1         2  water  orange  potato
2         3  juice   mango   chips

In [2]: df.T.to_dict().values()
Out[2]:
[{'customer': 1.0, 'item1': 'apple', 'item2': 'milk', 'item3': 'tomato'},
 {'customer': 2.0, 'item1': 'water', 'item2': 'orange', 'item3': 'potato'},
 {'customer': 3.0, 'item1': 'juice', 'item2': 'mango', 'item3': 'chips'}]

In [20]: timeit df.T.to_dict().values()
1000 loops, best of 3: 395 µs per loop

In [21]: timeit df.to_dict('records')
10000 loops, best of 3: 53 µs per loop
```

## DataFrame to dict of dict
```python

>>> df
dc_id    store_id    osrm_miles
12       6000        123.4
12       6001        234.5
23       5801        167.5

grp = df.groupby(['dc_id']) 
return {
    int(dc_id): grp.get_group(dc_id)[['store_id', 'miles']].set_index('store_id').T.to_dict('list')
    for dc_id in grp.groups.keys()
}  # {dc_id: {store_id: [miles]}}
```

## DataFrame to dict
```python
xingDict = df.set_index('stree')[['lat', 'lon']].to_dict(orient='index')
```

> Ref: https://stackoverflow.com/questions/26716616/convert-a-pandas-dataframe-to-a-dictionary

```python

>>> df
    ID   A   B   C
0   p    1   3   2
1   q    4   3   2
2   r    4   0   9  

>>> df.set_index('ID').T.to_dict('list')
{'p': [1, 3, 2], 'q': [4, 3, 2], 'r': [4, 0, 9]}


>>> df = pd.DataFrame({'a': ['red', 'yellow', 'blue'], 'b': [0.5, 0.25, 0.125]})
>>> df
        a      b
0     red  0.500
1  yellow  0.250
2    blue  0.125

>>> df.to_dict('dict')
{'a': {0: 'red', 1: 'yellow', 2: 'blue'}, 
 'b': {0: 0.5, 1: 0.25, 2: 0.125}}

>>> df.to_dict('list')
{'a': ['red', 'yellow', 'blue'], 
 'b': [0.5, 0.25, 0.125]}

>>> df.to_dict('series')
{'a': 0       red
      1    yellow
      2      blue
      Name: a, dtype: object, 

 'b': 0    0.500
      1    0.250
      2    0.125
      Name: b, dtype: float64}

>>> df.to_dict('split')
{'columns': ['a', 'b'],
 'data': [['red', 0.5], ['yellow', 0.25], ['blue', 0.125]],
 'index': [0, 1, 2]}

>>> df.to_dict('records')
[{'a': 'red', 'b': 0.5}, 
 {'a': 'yellow', 'b': 0.25}, 
 {'a': 'blue', 'b': 0.125}]

>>> df.to_dict('index')
{0: {'a': 'red', 'b': 0.5},
 1: {'a': 'yellow', 'b': 0.25},
 2: {'a': 'blue', 'b': 0.125}}
```


## Convert from string to numeric

```python
laneDF[['QUAD_ID','LANE_ID','STORE_NBR']] = laneDF[['QUAD_ID','LANE_ID','STORE_NBR']].apply(pd.to_numeric)
```

## Convert `list of list` to DataFrame

```python
jsonStr = """
{
    "scheduleType": 1,
    "schedule": {
        "dcNumber": 123,
        "user": "abc",
        "nbrOfAssociates": 123,
        "laneInfo": [
            ["QUADRANT_ID", "LANE_ID", "STORE_NBR", "LANE_OPEN"],
            ["1", "1", "1", "Y"],
            ["2", "1", "2", "N"]
        ]
    }
}
"""
upData = json.loads(jsonStr)
s = upData['schedule']
headers = s['laneInfo'].pop(0) # remove top row and use it as column title
laneDF = pd.DataFrame(s['laneInfo'], columns=headers)
```

## iloc() and loc()

> Ref: <https://stackoverflow.com/questions/31593201/pandas-iloc-vs-ix-vs-loc-explanation-how-are-they-different>

> Note: in pandas version 0.20.0 and above, `ix` is deprecated and the use of `loc` and `iloc` is encouraged instead.

> - loc gets rows (or columns) with particular labels from the index.
> - iloc gets rows (or columns) at particular positions in the index (so it only takes integers).
> - ix usually tries to behave like loc but falls back to behaving like iloc if a label is not present in the index.

```python
>>> s = pd.Series(np.nan, index=[49, 48, 1, 2, 3])
>>> s
49   NaN
48   NaN
1    NaN
2    NaN
3    NaN

>>> s.iloc[:2] # slice the first 2 rows
49   NaN
48   NaN

>>> s.loc[:2] # slice up to and including label 2
49   NaN
48   NaN
1    NaN
2    NaN

>>> s.iloc[i, 'lat']  # row by number and column by name
```

## `NaN` filter
```python
# Take rows whose row['X'] != NaN
df = df[~pd.isna(df['X'])]
```
