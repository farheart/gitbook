# Pandas Tricks


## Avoid `Int to Float` when `df.T.to_dict().values()`

Instead of use `list(df.T.to_dict().values())`, use **`[{c:getattr(r, c) for c in df} for r in df.itertuples()]`**

> Ref: [https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast](https://stackoverflow.com/questions/37897527/get-python-pandas-to-dict-with-orient-records-but-without-float-cast)
>
```python
import pandas as pd

df = pd.DataFrame({'a':[3,2], 'b':[42.0,3.14]})
result = [{col:getattr(row, col) for col in df} for row in df.itertuples()]
print(result)
# [{'a': 3, 'b': 42.0}, {'a': 2, 'b': 3.1400000000000001}]
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


## Add new calculated column
```python
df.loc[:'diff'] = df.apply(lambda r: r['c1'] - r['c2'], axis=1)
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


## DataFrame to dict
```python
xingDict = df.set_index('stree')[['lat', 'lon']].to_dict(orient='index')
```