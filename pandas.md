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
```


## Iterate DataFrame by rows

```python
for i,r in df.iterrows():
    if pd.isnull(r['OSRM_meter']):
        url = createURL(r)
```


## Add new column

```python
df.loc[:'diff'] = df.apply(lambda r: r['c1'] - r['c2'], axis=1)
```


## Filter by conditions
```python
cond1 = df['Project'].map(lambda x: x in [1,2,3])
cond2 = df['type'].map(lambda x: x == 'TODO')
df[cond1 & cond2]['SQL']
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


## Concat two/more DataFrames

```python
    df = pd.concat([df1, df2, df3])  # df1, df2, df3 must have same columns
```