# Conditional Dataframe
input:
 | Type    |  Set|
 |---|---|
|A      |    Z|
| B     |     Z     |      
| B     |     X|
| C         | Y|

* only 1 condition

```shell
df['color'] = np.where(df['Set']=='Z', 'green', 'red')
```

output:
|  Set| Type|  color|
|---|---|---|
| Z  |  A | green|
| Z  |  B | green|
|X   | B  |  red|
|Y   | C  |  red|

* If you have more than two conditions then use np.select. For example, if you want color to be
- yellow when (df['Set'] == 'Z') & (df['Type'] == 'A')
- otherwise blue when (df['Set'] == 'Z') & (df['Type'] == 'B')
- otherwise purple when (df['Type'] == 'B')
- otherwise black,

```shell
conditions = [
    (df['Set'] == 'Z') & (df['Type'] == 'A'),
    (df['Set'] == 'Z') & (df['Type'] == 'B'),
    (df['Type'] == 'B')]

choices = ['yellow', 'blue', 'purple']

df['color'] = np.select(conditions, choices, default='black')

```

output:
|Set |Type  | color|
|Z  |  A | yellow|
|Z |   B |   blue|
|X    |B  |purple|
|Y   | C  | black|



# If Statement based on Groupby sum
input:
|CategoryA | CategoryB | Count|
|---|---|---|
|1    |       A    |       0|
|1   |        A    |       -1|
|2    |       B    |       1|
|2    |       B    |       1|
|3     |      C   |        1|
|3     |      C   |        -1|


```shell
m = df.groupby(['CategoryA', 'CategoryB']).transform('sum').gt(0)

df['decision'] = np.where(m, 'keep', 'delete')
```

```shell
df['decision'] = df['CategoryB'].map(df.groupby('CategoryB')['Count']
                         .apply(lambda x :np.where(x.sum() > 0, 'keep', 'delete')))
```

```shell
df['decision'] = np.where(df.groupby(['CategoryA', 'CategoryB'])['Count']
                            .transform('sum') > 0, 'keep', 'delete')
```

Output:
|   CategoryA |  CategoryB|    Count|   decision|  
|---|---|---|---|
|        1    |       A  |      0  |   delete|  
|      1    |       A  |     -1  |   delete|  
|       2    |       B   |     1  |     keep|  
|        2    |       B   |     1  |     keep|  
|       3    |       C   |     1  |   delete|  
|        3   |        C |      -1  |   delete|  


# Concatenate strings (string.join) using Pandas groupby
input:

| name| 	text| 	date| 
|---|---|---|
| 	name1| 	du| 	2014-11-02| 
| 	name1	| aj| 	2014-12-01| 
| name1| 	oj| 	2014-12-02| 
| 	name2	| fin| 	2014-11-01| 
| name2	| katt| 	2014-11-02| 
| name2	| mycket| 	2014-12-01| 
| name2	| lite| 	2014-12-01| 


```shell
df['text'] = df[['name','text','month']]
            .groupby(['name','month'])['text']
            .transform(lambda x: ','.join(x))

df[['name','text','month']].drop_duplicates()
```

```shell
df.groupby(['name','month'])['text']
    .apply(lambda x: ','.join(x)).reset_index()
```

```shell
df.groupby(['name','month'])['text']
    .apply(','.join).reset_index()
```


output:

|   name    |     text | month|
|---|---|---|
| name1    |   hej,du |    11|
|  name1  |      aj,oj   |  12|
| name2  |   fin,katt  |   11|
| name2 | mycket,lite   |  12|


# groupby + transform
create Most_Common_Price

input:
 | Item  |  Price | 
 |---|---|
|Coffee | 1     | 1  |   
 |Coffee | 2    |  1   |   
|Coffee | 2     | 1   |   
 |Tea    | 3   |   3   | 
 |Tea   |  4   |   3   |
|Tea   |  4   |   3    |

```shell
df['Most_Common_Price'] = (df.groupby('Item')['Price']
                            .transform(lambda x: x.value_counts().idxmax()))
```

```shell
df['Item'] = (df['Item'].map(df.groupby('Item')['Price']
                        .agg(lambda x: x.value_counts().idxmax()))
```
output:
 | Item  |  Price | Minimum Most_Common_Price|
 |---|---|---|
|Coffee | 1     | 1  |     2|
 |Coffee | 2    |  1   |    2|
|Coffee | 2     | 1   |    2|
 |Tea    | 3   |   3   |    4|
 |Tea   |  4   |   3   |    4|
|Tea   |  4   |   3    |   4|

