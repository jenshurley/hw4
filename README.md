

```python
## Summary 
# Three observable trends
# age
#fix the column names for age
```


```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
%matplotlib inline
```


```python
df = pd.read_json('purchase_data.json')
```

### Total Number of Players and Player Count


```python
df.SN.nunique()
```




    573



## Purchasing Analysis
#### Number of Unique Items


```python
df['Item ID'].nunique()
```




    183



#### Average Purchase Price


```python
'${:.2f}'.format(df.Price.mean())
```




    '$2.93'



#### Total Number of Purchases


```python
df.shape[0]
```




    780



#### Total Revenue


```python
df.Price.sum()
```




    2286.33



## Gender Demographics

#### Count of players by gender


```python
df.groupby(['SN', 'Gender']).count().reset_index()['Gender'].value_counts()
```




    Male                     465
    Female                   100
    Other / Non-Disclosed      8
    Name: Gender, dtype: int64



#### Percentage of players by gender


```python
ax2 = df.groupby(['SN', 'Gender']).count().reset_index()['Gender'].value_counts().plot(kind='bar', figsize=(10,7),
                                                  color="indigo", fontsize=13);
ax2.set_alpha(0.8)
ax2.set_ylabel("Gender Count", fontsize=18);
ax2.set_yticks([i for i in range(0,500,100)])

# create a list to collect the plt.patches data
totals = []

# find the values and append to list
for i in ax2.patches:
    totals.append(i.get_height())

# set individual bar lables using above list
total = sum(totals)

# set individual bar lables using above list
for i in ax2.patches:
    # get_x pulls left or right; get_height pushes up or down
    ax2.text(i.get_x()+.12, i.get_height(), \
            str(round((i.get_height()/total)*100, 2))+'%', fontsize=22,
                color='black')
```


![png](output_17_0.png)


## Gender Purchasing Analysis


```python
df.groupby(['Gender', 'SN']).count().reset_index()['Gender'].value_counts()
normed = df.groupby(['Gender', 'SN']).count().reset_index()['Gender'].value_counts(normalize=True)
absolute = df.groupby(['Gender', 'SN']).count().reset_index()['Gender'].value_counts(normalize=False)
gdf = pd.concat([normed, absolute], axis=1)
```


```python
df_gender = df.groupby('Gender').agg(['sum', 'mean', 'count'])
df_gender.index
```




    Index(['Female', 'Male', 'Other / Non-Disclosed'], dtype='object', name='Gender')




```python
level0 = df_gender.columns.get_level_values(0)
level1 = df_gender.columns.get_level_values(1)
df_gender.columns = level0 + ' ' + level1
# df_gender = df_gender[['sum', 'mean', 'count']]
```


```python
df_gender
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age sum</th>
      <th>Age mean</th>
      <th>Age count</th>
      <th>Item ID sum</th>
      <th>Item ID mean</th>
      <th>Item ID count</th>
      <th>Price sum</th>
      <th>Price mean</th>
      <th>Price count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>3068</td>
      <td>22.558824</td>
      <td>136</td>
      <td>11983</td>
      <td>88.110294</td>
      <td>136</td>
      <td>382.91</td>
      <td>2.815515</td>
      <td>136</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>14360</td>
      <td>22.685624</td>
      <td>633</td>
      <td>57965</td>
      <td>91.571880</td>
      <td>633</td>
      <td>1867.68</td>
      <td>2.950521</td>
      <td>633</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>301</td>
      <td>27.363636</td>
      <td>11</td>
      <td>1261</td>
      <td>114.636364</td>
      <td>11</td>
      <td>35.74</td>
      <td>3.249091</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_gender = df_gender[['Price sum', 'Price mean', 'Price count']]
```


```python
df_gender
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Price sum</th>
      <th>Price mean</th>
      <th>Price count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>382.91</td>
      <td>2.815515</td>
      <td>136</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>1867.68</td>
      <td>2.950521</td>
      <td>633</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>35.74</td>
      <td>3.249091</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_gender = pd.concat([df_gender,absolute], axis=1)
df_gender['Normalized'] = df_gender['Price sum'] / df_gender.Gender
```


```python
df_gender
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Price sum</th>
      <th>Price mean</th>
      <th>Price count</th>
      <th>Gender</th>
      <th>Normalized</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>382.91</td>
      <td>2.815515</td>
      <td>136</td>
      <td>100</td>
      <td>3.829100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>1867.68</td>
      <td>2.950521</td>
      <td>633</td>
      <td>465</td>
      <td>4.016516</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>35.74</td>
      <td>3.249091</td>
      <td>11</td>
      <td>8</td>
      <td>4.467500</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
import seaborn as sns
```


```python
age_df = df[['Age', 'SN']].drop_duplicates()
```


```python
age_df.shape
```




    (573, 2)




```python
sns.distplot(age_df['Age'], bins=10, kde=False)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a18025d68>




![png](output_31_1.png)



```python
ages = [0, 9.9, 14.9, 19.9, 24.9, 29.90, 34.90, 39.90, 99999]
age_groups = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
```


```python
df['Age_Group'] = pd.cut(df['Age'], ages, labels = age_groups)

age_df['Age_Group'] = pd.cut(age_df['Age'], ages, labels=age_groups)

age_out = pd.concat([age_df.Age_Group.value_counts(normalize=True),\
  age_df.Age_Group.value_counts()], axis=1)
 
age_out.to_dict()['Age_Group']

age_norm = df.groupby('Age_Group').agg(['sum', 'mean', 'count'])['Price']
age_norm.reset_index(inplace=True)
```

    /Users/jennifershurley/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:7: UserWarning: DataFrame columns are not unique, some columns will be omitted.
      import sys



```python
age_norm["unique_buyers"] = age_norm["Age_Group"].map(lambda x: age_out.to_dict()['Age_Group'].get(x))
```

    /Users/jennifershurley/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: UserWarning: DataFrame columns are not unique, some columns will be omitted.
      """Entry point for launching an IPython kernel.



```python
age_norm['normed_mean'] = age_norm['sum'] / age_norm['unique_buyers'].astype('float')
```


```python
age_norm
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age_Group</th>
      <th>sum</th>
      <th>mean</th>
      <th>count</th>
      <th>unique_buyers</th>
      <th>normed_mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>83.46</td>
      <td>2.980714</td>
      <td>28</td>
      <td>19</td>
      <td>4.392632</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>96.95</td>
      <td>2.770000</td>
      <td>35</td>
      <td>23</td>
      <td>4.215217</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>386.42</td>
      <td>2.905414</td>
      <td>133</td>
      <td>100</td>
      <td>3.864200</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>978.77</td>
      <td>2.913006</td>
      <td>336</td>
      <td>259</td>
      <td>3.779035</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>370.33</td>
      <td>2.962640</td>
      <td>125</td>
      <td>87</td>
      <td>4.256667</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>197.25</td>
      <td>3.082031</td>
      <td>64</td>
      <td>47</td>
      <td>4.196809</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>119.40</td>
      <td>2.842857</td>
      <td>42</td>
      <td>27</td>
      <td>4.422222</td>
    </tr>
    <tr>
      <th>7</th>
      <td>40+</td>
      <td>53.75</td>
      <td>3.161765</td>
      <td>17</td>
      <td>11</td>
      <td>4.886364</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
df['SN'].value_counts().head(15).plot.bar();
```


![png](output_38_0.png)


**Since the value count is the same for the 2nd[1] item and the 6th[5] spenders, I included all of those spenders.**


```python
top_spenders = list(df['SN'].value_counts()[:6].to_dict().keys())
```


```python
mask_spend = df['SN'].isin(top_spenders)
```


```python
top_spenders_df = df[mask_spend]
```


```python
top_spender_purchase_analysis = top_spenders_df.groupby('SN').Price.agg(['count', 'mean', 'sum'])
```


```python
top_spender_purchase_analysis = top_spender_purchase_analysis.rename(columns={\
            'count': 'Purchase Count', 'mean': 'Ave Purchase Price','sum': 'Total Purchase Value'})
top_spender_purchase_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Ave Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Hailaphos89</th>
      <td>4</td>
      <td>1.4675</td>
      <td>5.87</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>3.1850</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>2.4925</td>
      <td>9.97</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>3.3900</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>Sondastan54</th>
      <td>4</td>
      <td>2.5600</td>
      <td>10.24</td>
    </tr>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>3.4120</td>
      <td>17.06</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
df['Item Name'].value_counts().head(15).plot.bar();
```


![png](output_46_0.png)


**Since the value count is the same for the 5th item and the 8th items, I included those in top items.**


```python
top_items = list(df['Item Name'].value_counts()[:8].to_dict().keys())

```


```python
top_items
```




    ['Final Critic',
     'Arcane Gem',
     'Betrayal, Whisper of Grieving Widows',
     'Stormcaller',
     'Serenity',
     'Woeful Adamantite Claymore',
     'Retribution Axe',
     'Trickster']




```python
mask = df['Item Name'].isin(top_items)
```


```python
top_items_df = df[mask]
```


```python
top_items_df.sort_values(['Item Name']).head()
#TODO should item name be index? how to display top item at the top? 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
      <th>Age_Group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171</th>
      <td>21</td>
      <td>Male</td>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>Stryanastip77</td>
      <td>20-24</td>
    </tr>
    <tr>
      <th>742</th>
      <td>26</td>
      <td>Male</td>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>Inguron55</td>
      <td>25-29</td>
    </tr>
    <tr>
      <th>354</th>
      <td>20</td>
      <td>Male</td>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>Mindirra92</td>
      <td>20-24</td>
    </tr>
    <tr>
      <th>338</th>
      <td>17</td>
      <td>Male</td>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>Lisossanya98</td>
      <td>15-19</td>
    </tr>
    <tr>
      <th>416</th>
      <td>25</td>
      <td>Male</td>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>Hiarideu73</td>
      <td>25-29</td>
    </tr>
  </tbody>
</table>
</div>



list in a table 
-Purchase Count
-Average Purchase Price
-Total Purchase Value


```python
item_purchase_analysis = top_items_df.groupby('Item Name').Price.agg(['count', 'mean', 'sum']).sort_values\
    (by='count', ascending=False)
```


```python
item_purchase_analysis = item_purchase_analysis.rename(columns={\
            'count': 'Purchase Count', 'mean': 'Ave Purchase Price','sum': 'Total Purchase Value'})
item_purchase_analysis
#sort by purchase count
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Ave Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Final Critic</th>
      <td>14</td>
      <td>2.757143</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>2.230000</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>2.350000</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>Stormcaller</th>
      <td>10</td>
      <td>3.465000</td>
      <td>34.65</td>
    </tr>
    <tr>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>4.140000</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>Serenity</th>
      <td>9</td>
      <td>1.490000</td>
      <td>13.41</td>
    </tr>
    <tr>
      <th>Trickster</th>
      <td>9</td>
      <td>2.070000</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>1.240000</td>
      <td>11.16</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
#Identify the 5 most profitable items by total purchase value, then list (in a table): Item ID, Item Name, 
#Purchase Count, Item Price, Total Purchase Value
```


```python
most_profitable = df.groupby(['Item Name', 'Item ID', 'Price'])['Price'].agg(['sum', 'count']).\
  sort_values(by='sum', ascending=False).nlargest(5, 'sum')
  #could also put 'count' in where 'sum' is
```


```python
most_profitable = most_profitable.rename(columns={\
            'count': 'Purchase Count', 'sum': 'Total Purchase Value'})
most_profitable
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>Total Purchase Value</th>
      <th>Purchase Count</th>
    </tr>
    <tr>
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Price</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Retribution Axe</th>
      <th>34</th>
      <th>4.14</th>
      <td>37.26</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Spectral Diamond Doomblade</th>
      <th>115</th>
      <th>4.25</th>
      <td>29.75</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Orenmir</th>
      <th>32</th>
      <th>4.95</th>
      <td>29.70</td>
      <td>6</td>
    </tr>
    <tr>
      <th>Singed Scalpel</th>
      <th>103</th>
      <th>4.87</th>
      <td>29.22</td>
      <td>6</td>
    </tr>
    <tr>
      <th>Splitter, Foe Of Subtlety</th>
      <th>107</th>
      <th>3.61</th>
      <td>28.88</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>


