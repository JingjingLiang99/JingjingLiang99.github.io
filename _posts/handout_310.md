```python
import pandas as pd
import numpy as np
import math
import ast

# visualization
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns

RSEED = 30
```


```python
df = pd.read_csv("leasing_opportunity.csv")
```


```python
df.head()
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>building_id</th>
      <th>created</th>
      <th>description</th>
      <th>display_address</th>
      <th>features</th>
      <th>latitude</th>
      <th>listing_id</th>
      <th>longitude</th>
      <th>manager_id</th>
      <th>photos</th>
      <th>price</th>
      <th>street_address</th>
      <th>is_deal</th>
      <th>gender</th>
      <th>expected_price</th>
      <th>followup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3</td>
      <td>53a5b119ba8f7b61d4e010512e0dfc85</td>
      <td>24/06/2016 7:54</td>
      <td>A Brand New 3 Bedroom 1.5 bath ApartmentEnjoy ...</td>
      <td>Metropolitan Avenue</td>
      <td>[]</td>
      <td>40.7145</td>
      <td>7211212</td>
      <td>-73.9425</td>
      <td>5ba989232d0489da1b5f2c45f6688adc</td>
      <td>['https://photos.renthop.com/2/7211212_1ed4542...</td>
      <td>3000.0</td>
      <td>792 Metropolitan Avenue</td>
      <td>1</td>
      <td>female</td>
      <td>2700.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2</td>
      <td>c5c8a357cba207596b04d1afd1e4f130</td>
      <td>12/06/2016 12:19</td>
      <td></td>
      <td>Columbus Avenue</td>
      <td>['Doorman', 'Elevator', 'Fitness Center', 'Cat...</td>
      <td>40.7947</td>
      <td>7150865</td>
      <td>-73.9667</td>
      <td>7533621a882f71e25173b27e3139d83d</td>
      <td>['https://photos.renthop.com/2/7150865_be3306c...</td>
      <td>5465.0</td>
      <td>808 Columbus Avenue</td>
      <td>0</td>
      <td>male</td>
      <td>5200.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1</td>
      <td>c3ba40552e2120b0acfc3cb5730bb2aa</td>
      <td>17/04/2016 3:26</td>
      <td>Top Top West Village location, beautiful Pre-w...</td>
      <td>W 13 Street</td>
      <td>['Laundry In Building', 'Dishwasher', 'Hardwoo...</td>
      <td>40.7388</td>
      <td>6887163</td>
      <td>-74.0018</td>
      <td>d9039c43983f6e564b1482b273bd7b01</td>
      <td>['https://photos.renthop.com/2/6887163_de85c42...</td>
      <td>2850.0</td>
      <td>241 W 13 Street</td>
      <td>1</td>
      <td>male</td>
      <td>2900.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>28d9ad350afeaab8027513a3e52ac8d5</td>
      <td>18/04/2016 2:22</td>
      <td>Building Amenities - Garage - Garden - fitness...</td>
      <td>East 49th Street</td>
      <td>['Hardwood Floors', 'No Fee']</td>
      <td>40.7539</td>
      <td>6888711</td>
      <td>-73.9677</td>
      <td>1067e078446a7897d2da493d2f741316</td>
      <td>['https://photos.renthop.com/2/6888711_6e660ce...</td>
      <td>3275.0</td>
      <td>333 East 49th Street</td>
      <td>0</td>
      <td>female</td>
      <td>3500.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>28/04/2016 1:32</td>
      <td>Beautifully renovated 3 bedroom flex 4 bedroom...</td>
      <td>West 143rd Street</td>
      <td>['Pre-War']</td>
      <td>40.8241</td>
      <td>6934781</td>
      <td>-73.9493</td>
      <td>98e13ad4b495b9613cef886d79a6291f</td>
      <td>['https://photos.renthop.com/2/6934781_1fa4b41...</td>
      <td>3350.0</td>
      <td>500 West 143rd Street</td>
      <td>0</td>
      <td>female</td>
      <td>3200.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.columns
```




    Index(['bathrooms', 'bedrooms', 'building_id', 'created', 'description',
           'display_address', 'features', 'latitude', 'listing_id', 'longitude',
           'manager_id', 'photos', 'price', 'street_address', 'is_deal', 'gender',
           'expected_price', 'followup'],
          dtype='object')




```python
df.describe()
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
      <th>longitude</th>
      <th>price</th>
      <th>is_deal</th>
      <th>expected_price</th>
      <th>followup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48755.000000</td>
      <td>4.875500e+04</td>
      <td>53675.000000</td>
      <td>4.875500e+04</td>
      <td>26872.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>-73.955557</td>
      <td>3.842052e+03</td>
      <td>0.234094</td>
      <td>3.791076e+03</td>
      <td>7.498735</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.185096</td>
      <td>2.220097e+04</td>
      <td>0.423435</td>
      <td>2.220123e+04</td>
      <td>4.614796</td>
    </tr>
    <tr>
      <th>min</th>
      <td>-118.271000</td>
      <td>4.300000e+01</td>
      <td>0.000000</td>
      <td>-4.000000e+02</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>-73.991700</td>
      <td>2.500000e+03</td>
      <td>0.000000</td>
      <td>2.400000e+03</td>
      <td>4.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>-73.977900</td>
      <td>3.150000e+03</td>
      <td>0.000000</td>
      <td>3.100000e+03</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>-73.954800</td>
      <td>4.100000e+03</td>
      <td>0.000000</td>
      <td>4.100000e+03</td>
      <td>11.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>0.000000</td>
      <td>4.490000e+06</td>
      <td>1.000000</td>
      <td>4.489600e+06</td>
      <td>15.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 53675 entries, 0 to 53674
    Data columns (total 18 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   bathrooms        49930 non-null  object 
     1   bedrooms         49949 non-null  object 
     2   building_id      49949 non-null  object 
     3   created          49949 non-null  object 
     4   description      48498 non-null  object 
     5   display_address  49316 non-null  object 
     6   features         49352 non-null  object 
     7   latitude         49352 non-null  object 
     8   listing_id       49272 non-null  object 
     9   longitude        48755 non-null  float64
     10  manager_id       48755 non-null  object 
     11  photos           48755 non-null  object 
     12  price            48755 non-null  float64
     13  street_address   48745 non-null  object 
     14  is_deal          53675 non-null  int64  
     15  gender           53675 non-null  object 
     16  expected_price   48755 non-null  float64
     17  followup         26872 non-null  float64
    dtypes: float64(4), int64(1), object(13)
    memory usage: 7.4+ MB
    


```python
df.hist(figsize=(13,10))
plt.show()
```


![png](output_6_0.png)


## 1. data cleaning
- 如何处理NaN
- 如何处理异常值（price = 1000000）

## 2. feature understaing
- 看看feature和target(is_deal)之间的correlation

## feature engineering
- string --> catagorial --> feature
- bin --> catagorial feature

## 3. data partitioning
- k-fold

## 4. modeling
- logistic
- decision tree
- random forest
- xgboost

## 5. explanation and report

# 1. feature engineering


```python
df.columns
```




    Index(['bathrooms', 'bedrooms', 'building_id', 'created', 'description',
           'display_address', 'features', 'latitude', 'listing_id', 'longitude',
           'manager_id', 'photos', 'price', 'street_address', 'is_deal', 'gender',
           'expected_price', 'followup'],
          dtype='object')




```python
df_final = pd.DataFrame()
```

## 1.1 deal with bathrooms, bedrooms


```python
df['bathrooms'] = pd.to_numeric(df['bathrooms'], errors='coerce')
df['bedrooms'] = pd.to_numeric(df['bedrooms'], errors='coerce')
```


```python
df_final[['bathrooms', 'bedrooms']] = df[['bathrooms', 'bedrooms']]
df_final.head()
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
      <th>bathrooms</th>
      <th>bedrooms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>



## 1.2 deal with building_id


```python
df.columns
```




    Index(['bathrooms', 'bedrooms', 'building_id', 'created', 'description',
           'display_address', 'features', 'latitude', 'listing_id', 'longitude',
           'manager_id', 'photos', 'price', 'street_address', 'is_deal', 'gender',
           'expected_price', 'followup'],
          dtype='object')




```python
print('building_id has ', len(df['building_id'].unique()), ' unique values')
```

    building_id has  7662  unique values
    


```python
print('listing_id has ', len(df['listing_id'].unique()), ' unique values')
```

    listing_id has  49229  unique values
    


```python
print('manager_id has ', len(df['manager_id'].unique()), ' unique values')
```

    manager_id has  3470  unique values
    

# 1.3 deal with description


```python
df['description']
```




    0        A Brand New 3 Bedroom 1.5 bath ApartmentEnjoy ...
    1                                                         
    2        Top Top West Village location, beautiful Pre-w...
    3        Building Amenities - Garage - Garden - fitness...
    4        Beautifully renovated 3 bedroom flex 4 bedroom...
                                   ...                        
    53670    30TH/3RD, MASSIVE CONV 2BR IN LUXURY FULL SERV...
    53671    HIGH END condo finishes, swimming pool, and ki...
    53672    Large Renovated One Bedroom Apartment with Sta...
    53673    Stylishly sleek studio apartment with unsurpas...
    53674    Look no further!!!  This giant 2 bedroom apart...
    Name: description, Length: 53675, dtype: object




```python
!pip install textblob
```

    Collecting textblob
      Downloading textblob-0.15.3-py2.py3-none-any.whl (636 kB)
    Requirement already satisfied: nltk>=3.1 in f:\python\lib\site-packages (from textblob) (3.5)
    Requirement already satisfied: click in f:\python\lib\site-packages (from nltk>=3.1->textblob) (7.1.2)
    Requirement already satisfied: joblib in f:\python\lib\site-packages (from nltk>=3.1->textblob) (0.16.0)
    Requirement already satisfied: tqdm in f:\python\lib\site-packages (from nltk>=3.1->textblob) (4.47.0)
    Requirement already satisfied: regex in f:\python\lib\site-packages (from nltk>=3.1->textblob) (2020.6.8)
    Installing collected packages: textblob
    Successfully installed textblob-0.15.3
    


```python
import nltk
nltk.download('punkt')
```

    [nltk_data] Downloading package punkt to
    [nltk_data]     C:\Users\HP\AppData\Roaming\nltk_data...
    [nltk_data]   Unzipping tokenizers\punkt.zip.
    




    True




```python
from textblob import TextBlob

string = 'a happy day'
blob = TextBlob(string)
blob.sentiment
```




    Sentiment(polarity=0.8, subjectivity=1.0)




```python
string = 'a happy day. i am so happy'
blob = TextBlob(string)
blob.sentences
```




    [Sentence("a happy day."), Sentence("i am so happy")]




```python
# description_list = list(df['description'])

# from textblob import TextBlob

# dscpt_polarity = []
# dscpt_subjectivity = []

# for text in description_list:
#     blob = TextBlob(str(text))
    
#     polarity = []
#     subjectivity = []
#     for sentence in blob.sentences:
#         polarity.append(sentence.sentiment.polarity)
#         subjectivity.append(sentence.sentiment.subjectivity)
    
#     if len(polarity) > 0:
#         avg_polarity = sum(polarity)/len(polarity)
#     else:
#         avg_polarity = 0
# #         avg_polarity = float("nan")
        
#     if len(subjectivity) > 0:
#         avg_subjectivity = sum(subjectivity)/len(subjectivity)
#     else:
#         avg_polarity = 0
# #         avg_subjectivity = float('nan')

#     dscpt_polarity.append(avg_polarity)
#     dscpt_subjectivity.append(avg_subjectivity)

```


```python
from textblob import TextBlob
import numpy as np

def get_sentiment(text):
    blob = TextBlob(str(text))
    if len(blob.sentences) == 0:
        return (0,0)
    polarity = [sentence.sentiment.polarity for sentence in blob.sentences]
    subjectivity = [sentence.sentiment.subjectivity for sentence in blob.sentences]
    polarity_avg = np.mean(polarity)
    subjectivity_avg = np.mean(subjectivity)
    return (polarity_avg, subjectivity_avg)
```


```python
df['description'].iloc[0]
```




    "A Brand New 3 Bedroom 1.5 bath ApartmentEnjoy These Following Apartment Features As You Rent Here? Modern Designed Bathroom w/ a Deep Spa Soaking Tub? Room to Room AC/Heat? Real Oak Hardwood Floors? Rain Forest Shower Head? SS steel Appliances w/ Chef Gas Cook Oven & LG Fridge? washer /dryer in the apt? Cable Internet Ready? Granite Counter Top Kitchen w/ lot of cabinet storage spaceIt's Just A Few blocks To L Train<br /><br />Don't miss out!<br /><br />We have several great apartments in the immediate area.<br /><br />For additional information 687-878-2229<p><a  website_redacted "




```python
get_sentiment(df['description'].iloc[0])
```




    (0.16035353535353536, 0.3071969696969697)




```python
df[['dscpt_polarity', 'dscpt_subjectivity']] = df[['description']].apply(get_sentiment, axis=1, result_type="expand")
```


```python
df_final[['dscpt_polarity', 'dscpt_subjectivity']] = df[['dscpt_polarity', 'dscpt_subjectivity']]
df_final.head()
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>dscpt_polarity</th>
      <th>dscpt_subjectivity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3.0</td>
      <td>0.136364</td>
      <td>0.454545</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.616667</td>
      <td>0.666667</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>4.0</td>
      <td>0.850000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



# Hint 1: features这一列如何进行特征工程

features表示该公寓的一些特色，比如是否有电梯，是否有健身房等。这个特色会影响到一个租客最终是否签约。这一列本身的数据类似是string，string里面有list，list里面的每个element是真正的feature。我们如何提取这些featur进行训练呢？

原来

| features |   |   |   |   |
|---       |---|---|---|---| 
| doorman  |   |   |   |   |
| elevater |   |   |   |   |
| NaN      |   |   |   |   |


特征工程后

| doorman | elevater  |   |   |   |
|---      |---|---|---|---| 
| 1       | 0  |   |   |   |
| 0       | 1  |   |   |   |
| 0       | 0  |   |   |   |




```python
df['features'].head()
```




    0                                                   []
    1    ['Doorman', 'Elevator', 'Fitness Center', 'Cat...
    2    ['Laundry In Building', 'Dishwasher', 'Hardwoo...
    3                        ['Hardwood Floors', 'No Fee']
    4                                          ['Pre-War']
    Name: features, dtype: object



df.loc[df['features'].isnull(), 'features']


```python
df['features'] = df['features'].fillna('[]')
```


```python
df.loc[~df['features'].str.contains('\['), 'features'].unique()
```




    array(['92758310b2a962b943484e8be0237a19',
           '4bdc3d8c1aaa90d997ce2cb77680679b',
           '624c1fbd75e5f99e6a7164cce1b1b8a4',
           'ff707b7f0a2ea94a26c784f06c9fec69',
           '699c325b818541f314b691b76f3238d7',
           'a1e83422f637157e3c14ab88d585b769',
           '5c11016f3d21d673b15db9f058c90200',
           'a621bebe105d26cd338d4c54f35f52e6',
           'c80aad40b6abd065b4aefee40a2154f6',
           '8b53ccf4338806ab1be3dd0267711649',
           'f510c42fc9e8765f19317cf2f84e3114',
           '536aaedf27d13fb487c142dae8133211',
           'c71cf1f472cf9b4b4517ea23fb6f2c91',
           'e6472c7237327dd3903b3d6f6a94515a',
           'b4518af7cc5d79517aca4157f1614478',
           '407ffad8d323810a4712d1446d5ad457',
           '90ddcebc5f1df89389cbc519535e0f2a',
           'df86259b169a78c024e4d5206d48cf44',
           '4bb850e243db09298a0bda50f9a99c81',
           '7892b9714d30934b1863796d61fbb81c',
           '6c2a16187e6855c132bb496b875a4ef7',
           'ef6323ef586350bb4f8dc78d924e856a',
           'd69d4e111612dd12ef864031c1148543',
           'a4a468c229a6094d3811489361d08819',
           '0492625ca98a35bd7d75849f6b4944ae',
           '7775d78b3c97487fba1df0727f4b167b',
           'fd1d4ee0095d41d209e23530141eaabf',
           '952078cf22eae4e31eb70604c9d4220c',
           'b4405fd648b8ebd2b98861d6aa8e4969',
           '19f0651b8a5f76062fe2c9369e2dd76c',
           '1f286bebfce89e810809880775f11a26',
           '72db53045d9648ddb3023fa80885a49c',
           '8d2926bae27fce9f700b488934563844',
           '836119ecf05f96f399c7f3df34dee1a7',
           '287e23e5a3f64c939cdb8109713b25e4',
           'edd073f8dea85637f8b7d589faa7902b',
           'd0f2082e8b795095061785ef5e8ac9e7',
           'f07272f8ceb99db4c1a7cbbd9ae7b75b',
           '0cb0dc4da84b0d97c6e3ccb369aba83e',
           '63da1f6025a369fa2c4b036130784a84',
           '465641627d253016ac8683943fd94ffe',
           '32396a0a600b721a4a7655d4ddcedc4d',
           'd23f9a9e6b9f6003f26017bda6dd1cb2',
           'b9e74dc56199a8e6577aa8bb3550f1e4',
           '89b262f811547861d9cccc374be275d0',
           '6b3fc4bf56631914dce770d406ed8007',
           '10b1ae0a38d50b7ba0cee612d14af9eb',
           '0bafd514443193d057e8a60e45cb7ea6',
           'b8459916140cbfc69344eab991b441bd',
           'bf2f68c09a8f9b6287b4e1f7cbedce65',
           'c9d2c946c5c94b549d2029b024e5aaa1',
           '0ed22c41834a89b0905341a4883bcfd8',
           'cfa86c14c312b92a7d035bd3437c2043',
           '97c4e11df6603e1dd21d88c7fc18013d',
           'e2aa71ea7e46aa975a6cefa35f64d28b',
           '524a48d3526730bec7262442b3aa9a94',
           '829999af312c93d6b1d1520dbad4983f',
           'f289f53a367315caae592753ba0bc63d',
           'b7a1f2d6ea66497b087690e796ca43ba',
           '381152a75a238372d3d475934219eb6d',
           '205f1a20fe0ebac01902c71a15dfbc9b',
           'b68f249ae0aeb220626960fa1934c418',
           '3ab7c26842f970df4f7dc555dffc387d',
           'f2e973c3ef0c652556f3006a51bce015',
           '09e098b612b87107ff4f047cff3acfec',
           '84a317f6bce6e3c05dec6fb070dd7e0b',
           'd2b7068bc4e5e74ba13954559d1d120d',
           '6894090792e447da3d8c2817c6577ea5',
           'f32c29c47890054822a062c48ed0229f',
           'd4444244953f1ba2045b446e0b40543f',
           'a820f872b24c51a030b4d25059fbaac8',
           'ce9605e5f21262b82deb0c5ef10dcf45',
           'f5e44ef976867c828130d0e6e6cdbda5',
           '29351e3e25283e3c6c324f7d0904153b',
           'bc9a2ff7c931ce7de08f50d429134a16',
           '87b5619c569d2d9a8fb8c91d901b7b54',
           '8d425b08cdf4dec134bf5b714ebac267',
           'c0a635e032b6a0f40ba7bc1e5ebf4809',
           '1badf2eefe8228387979149413ec071b',
           'b0bea7357fc07d2716c68644dc8a312c',
           'f2e87488529935faf3c8411e63259564',
           '12b91a24a6af821a5912bc417ec43e8d',
           '3969aa2ff2f266ea82a886e0c084abc7',
           'a936cd07895efded15c36140a62a78b7',
           '94db062fa1178ab999bdd777dafec65f',
           '17204fa7e500e3ea4efb3e0f801669aa',
           '8c581022a9e77237c66336f8f17fa14d',
           'ba82cb585bc2be6819241b2d1c900b88',
           '44736fb7de33f8ea3c34c0500e6932c3',
           '9ab3ec6493d605fa0effea43b1141f6f',
           'ae7c6a59583fd40eac4d3855316f0ac2',
           '468e656e59a57f84bf2bf37e480aa832',
           '0c1091b0e6fcebff367ba688403f5c84',
           '9daa743d34f2bf6917a2ce0c1716784f',
           'babf967aeb47132007ac2dd76c204d49',
           '77a2ec69c31af35bad629aeef64322d9',
           '77a807f61bff3244a81c0d8f0ea0c508',
           '1014a43e89b749c41b1385d09539b94d',
           '973160cfdb58538169ea1c24f87561e3',
           '2279c1b4b458c7060354f77cef33b24e',
           '53f240454714b223c7aaa73437c24e8a',
           'cd60972dd4dc63f11aff82ac42c8ba98',
           'eef47ed8f4645412e7130f334aa53ca7',
           'd9039c43983f6e564b1482b273bd7b01',
           '90cdd68b0a4ed66ef71d66e55d3fbd8a',
           'c0999793234144881eeec9cecb1c4b8f',
           '95f23db28b91c719b620588a76baf84a',
           'efb12e02cfab31245ab533767b69aeb5',
           '7c5e4fc025b70c6540d6b0e06716b9dd',
           '5d460ddca5fd522ceca75feccf5ef81d',
           'e9920062e07ee893c10e38d0259665b0',
           'c1f6424c975f2dab361f8e4f5a1095f1',
           'e6da894cf7ae0e99fe17a29c72e387c9',
           '908e2fdb7d9ac7d0d9220b60f1292c3a',
           '539c94e309b65a4089e490808c35c154',
           '449b6bcfbef98f7cd8fdb1acfac20097',
           '1f83758a257b3ded8e8cce14b14bf533',
           'd0322634dbf7c6e01bc2b58f91bb0d90',
           'cd078a92365061d97bec61e145a713b2',
           'dc87fe2210e71c6fb37a56ed032754bf',
           '6e88a2a196e4107504206effc554c922',
           '79043887df9d409475245ca4fc3bfabe',
           'd4da529f5295859bb5c4ef8abbb0a38f',
           '4e0c2b0aee8d6e71d3ba9eb83ee09836',
           '1d3f3acbd945bd0a122e0625f3959f89',
           '60048dafd9977698423f6d9e45f0d4c2',
           'c55f334138bd24e209d9c170545c7f62',
           'cc53f401bf7b6f9d7f0cc42025e3cc65',
           '8fcfffa4e06cfe09f55215ea1a5e5c84',
           '617e27a9baf846838a0132bd8871e178',
           'd82432ca41910a345ee9591c37280484',
           '1729cc4d62ab0cec44dcfd6757d44d65',
           'c8b10a317b766204f08e613cef4ce7a0',
           '867bf3046143d15b9e618c8afba40025',
           '4c4362be5604f9054c2d6d4f82f9d24e',
           'b2d16e8bb0e9470fcb2ab15c07b9b4f1',
           '293a5d2579bc2eb7b20736ae2395e9e5',
           '3e0c28a1f4b9c11b809ff21dbb920b5c',
           '05a5fa4dd06e3f64d73d88d7a6885a51',
           'f2dc994e73107776552b84345c7b2b8d'], dtype=object)




```python
df.loc[~df['features'].str.contains('\['), 'features'] = '[]'
```

# 目标是
```
[['Doorman', 'Elevator', 'Fitness Center', 'Cats Allowed', 'Dogs Allowed'],
 ['Hardwood Floors', 'No Fee'],
 ..]]

--> 

['Doorman', 'Elevator', 'Fitness Center', 'Cats Allowed', 'Dogs Allowed', 'Hardwood Floors', 'No Fee']
```


```python
feature_nested_list = df['features'].apply(ast.literal_eval)
feature_nested_list
```




    0                                                       []
    1        [Doorman, Elevator, Fitness Center, Cats Allow...
    2        [Laundry In Building, Dishwasher, Hardwood Flo...
    3                                [Hardwood Floors, No Fee]
    4                                                [Pre-War]
                                   ...                        
    53670    [Elevator, Laundry in Building, Laundry in Uni...
    53671    [Common Outdoor Space, Cats Allowed, Dogs Allo...
    53672    [Doorman, Elevator, Pre-War, Dogs Allowed, Cat...
    53673    [Doorman, Elevator, Pre-War, Dogs Allowed, Cat...
    53674                                    [Hardwood Floors]
    Name: features, Length: 53675, dtype: object




```python
feature_nested_list.explode()
```




    0                    NaN
    1                Doorman
    1               Elevator
    1         Fitness Center
    1           Cats Allowed
                  ...       
    53673           Elevator
    53673            Pre-War
    53673       Dogs Allowed
    53673       Cats Allowed
    53674    Hardwood Floors
    Name: features, Length: 274245, dtype: object




```python
feature_unnested = feature_nested_list.explode().dropna()
feature_unnested
```




    1                Doorman
    1               Elevator
    1         Fitness Center
    1           Cats Allowed
    1           Dogs Allowed
                  ...       
    53673           Elevator
    53673            Pre-War
    53673       Dogs Allowed
    53673       Cats Allowed
    53674    Hardwood Floors
    Name: features, Length: 266310, dtype: object




```python
print('Total number of apartment is ', len(df['features']))
print('Total number features is', len(feature_unnested))
```

    Total number of apartment is  53675
    Total number features is 266310
    


```python
top_features = feature_unnested.value_counts()[:10]
top_features
```




    Elevator               25759
    Hardwood Floors        23445
    Cats Allowed           23271
    Dogs Allowed           21786
    Doorman                20793
    Dishwasher             20353
    No Fee                 17871
    Laundry in Building    16279
    Fitness Center         13182
    Pre-War                 9120
    Name: features, dtype: int64




```python
top_feature_list = list(top_features.index)
```


```python
top_feature_list
```




    ['Elevator',
     'Hardwood Floors',
     'Cats Allowed',
     'Dogs Allowed',
     'Doorman',
     'Dishwasher',
     'No Fee',
     'Laundry in Building',
     'Fitness Center',
     'Pre-War']



```
dict {'Elevator': [0, 0, 1, 1, ...],
      'Hardwood Floors': [1, 0, ...]}
```


```python
from collections import defaultdict
feature_map = defaultdict(list)

for feature_nested in feature_nested_list:
    for feature_selected in top_feature_list:
        if feature_selected in feature_nested:
            feature_map[feature_selected].append(1)
        else:
            feature_map[feature_selected].append(0)
```


```python
df['features']
```




    0                                                       []
    1        ['Doorman', 'Elevator', 'Fitness Center', 'Cat...
    2        ['Laundry In Building', 'Dishwasher', 'Hardwoo...
    3                            ['Hardwood Floors', 'No Fee']
    4                                              ['Pre-War']
                                   ...                        
    53670    ['Elevator', 'Laundry in Building', 'Laundry i...
    53671    ['Common Outdoor Space', 'Cats Allowed', 'Dogs...
    53672    ['Doorman', 'Elevator', 'Pre-War', 'Dogs Allow...
    53673    ['Doorman', 'Elevator', 'Pre-War', 'Dogs Allow...
    53674                                  ['Hardwood Floors']
    Name: features, Length: 53675, dtype: object




```python
feature_df = pd.DataFrame.from_dict(feature_map)
feature_df
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
      <th>Elevator</th>
      <th>Hardwood Floors</th>
      <th>Cats Allowed</th>
      <th>Dogs Allowed</th>
      <th>Doorman</th>
      <th>Dishwasher</th>
      <th>No Fee</th>
      <th>Laundry in Building</th>
      <th>Fitness Center</th>
      <th>Pre-War</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>53670</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53671</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53672</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>53673</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>53674</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>53675 rows × 10 columns</p>
</div>




```python
df_final = pd.concat([df_final, feature_df], axis=1)
df_final
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>dscpt_polarity</th>
      <th>dscpt_subjectivity</th>
      <th>Elevator</th>
      <th>Hardwood Floors</th>
      <th>Cats Allowed</th>
      <th>Dogs Allowed</th>
      <th>Doorman</th>
      <th>Dishwasher</th>
      <th>No Fee</th>
      <th>Laundry in Building</th>
      <th>Fitness Center</th>
      <th>Pre-War</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3.0</td>
      <td>0.136364</td>
      <td>0.454545</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.616667</td>
      <td>0.666667</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>4.0</td>
      <td>0.850000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>53670</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.175000</td>
      <td>0.775000</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53671</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.160000</td>
      <td>0.540000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53672</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.214286</td>
      <td>0.428571</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>53673</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>53674</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.750000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>53675 rows × 14 columns</p>
</div>



## 1.5 deal with photos


```python
df['photos'][0]
```




    "['https://photos.renthop.com/2/7211212_1ed4542ec81621d70d1061aa833e669c.jpg', 'https://photos.renthop.com/2/7211212_7dfc41dced69245065df83d08eed4a00.jpg', 'https://photos.renthop.com/2/7211212_c17853c4b869af6f53af08b0f5820b4c.jpg', 'https://photos.renthop.com/2/7211212_787ad8ea0c089792e7453e2121f8ac89.jpg', 'https://photos.renthop.com/2/7211212_2e88b0d293ee333c804c2f00536eee49.jpg']"




```python
df['photos'] = df['photos'].fillna('[]')
df[~df['photos'].str.contains('\[')]
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>building_id</th>
      <th>created</th>
      <th>description</th>
      <th>display_address</th>
      <th>features</th>
      <th>latitude</th>
      <th>listing_id</th>
      <th>longitude</th>
      <th>manager_id</th>
      <th>photos</th>
      <th>price</th>
      <th>street_address</th>
      <th>is_deal</th>
      <th>gender</th>
      <th>expected_price</th>
      <th>followup</th>
      <th>dscpt_polarity</th>
      <th>dscpt_subjectivity</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
photo_nested_list = df['photos'].apply(ast.literal_eval)
photo_nested_list[:2]
```




    0    [https://photos.renthop.com/2/7211212_1ed4542e...
    1    [https://photos.renthop.com/2/7150865_be3306c5...
    Name: photos, dtype: object




```python
num_of_photos = photo_nested_list.apply(len)
num_of_photos[:2]
```




    0     5
    1    11
    Name: photos, dtype: int64




```python
df_final['num_of_photos'] = num_of_photos
df_final[['num_of_photos']].head()
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
      <th>num_of_photos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



# 1.6 deal with gender


```python
pd.get_dummies(df['gender'], drop_first=True)
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
      <th>male</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>53670</th>
      <td>1</td>
    </tr>
    <tr>
      <th>53671</th>
      <td>0</td>
    </tr>
    <tr>
      <th>53672</th>
      <td>0</td>
    </tr>
    <tr>
      <th>53673</th>
      <td>0</td>
    </tr>
    <tr>
      <th>53674</th>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>53675 rows × 1 columns</p>
</div>




```python
df_final['gender'] = pd.get_dummies(df['gender'], drop_first=True)
```

# 1.7 other features


```python
df_final.to_csv('final.csv')
```


```python
labels = df['is_deal']
```


```python
print(sum(labels == 0))
print(sum(labels == 1))
```

    41110
    12565
    

# 2. 数据分割和均一化

## 2.1 均一化


```python
df_final.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 53675 entries, 0 to 53674
    Data columns (total 19 columns):
     #   Column               Non-Null Count  Dtype  
    ---  ------               --------------  -----  
     0   bathrooms            49352 non-null  float64
     1   bedrooms             49352 non-null  float64
     2   dscpt_polarity       53675 non-null  float64
     3   dscpt_subjectivity   53675 non-null  float64
     4   Elevator             53675 non-null  int64  
     5   Hardwood Floors      53675 non-null  int64  
     6   Cats Allowed         53675 non-null  int64  
     7   Dogs Allowed         53675 non-null  int64  
     8   Doorman              53675 non-null  int64  
     9   Dishwasher           53675 non-null  int64  
     10  No Fee               53675 non-null  int64  
     11  Laundry in Building  53675 non-null  int64  
     12  Fitness Center       53675 non-null  int64  
     13  Pre-War              53675 non-null  int64  
     14  num_of_photos        53675 non-null  int64  
     15  gender               53675 non-null  uint8  
     16  price                48755 non-null  float64
     17  expected_price       48755 non-null  float64
     18  followup             26872 non-null  float64
    dtypes: float64(7), int64(11), uint8(1)
    memory usage: 7.4 MB
    


```python
features_standard = ['num_of_photos', 'price', 'expected_price', 'followup']
df_standard = df_final[features_standard]
df_standard.head()
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
      <th>num_of_photos</th>
      <th>price</th>
      <th>expected_price</th>
      <th>followup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
      <td>3000.0</td>
      <td>2700.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11</td>
      <td>5465.0</td>
      <td>5200.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8</td>
      <td>2850.0</td>
      <td>2900.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3275.0</td>
      <td>3500.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
      <td>3350.0</td>
      <td>3200.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
df_standard =sc.fit_transform(df_standard)
df_standard = sc.transform(df_standard)
```


```python
df_final[features_standard] = df_standard
```


```python
df_final
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>dscpt_polarity</th>
      <th>dscpt_subjectivity</th>
      <th>Elevator</th>
      <th>Hardwood Floors</th>
      <th>Cats Allowed</th>
      <th>Dogs Allowed</th>
      <th>Doorman</th>
      <th>Dishwasher</th>
      <th>No Fee</th>
      <th>Laundry in Building</th>
      <th>Fitness Center</th>
      <th>Pre-War</th>
      <th>num_of_photos</th>
      <th>gender</th>
      <th>price</th>
      <th>expected_price</th>
      <th>followup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3.0</td>
      <td>0.136364</td>
      <td>0.454545</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>3000.0</td>
      <td>2700.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>1</td>
      <td>5465.0</td>
      <td>5200.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.616667</td>
      <td>0.666667</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>8</td>
      <td>1</td>
      <td>2850.0</td>
      <td>2900.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>3275.0</td>
      <td>3500.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>4.0</td>
      <td>0.850000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>3350.0</td>
      <td>3200.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>53670</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.175000</td>
      <td>0.775000</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>1</td>
      <td>3200.0</td>
      <td>3500.0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>53671</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.160000</td>
      <td>0.540000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>8</td>
      <td>0</td>
      <td>3950.0</td>
      <td>3500.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>53672</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.214286</td>
      <td>0.428571</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>2595.0</td>
      <td>2200.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>53673</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9</td>
      <td>0</td>
      <td>3350.0</td>
      <td>3900.0</td>
      <td>15.0</td>
    </tr>
    <tr>
      <th>53674</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.750000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>2200.0</td>
      <td>2300.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>53675 rows × 19 columns</p>
</div>



## 2.2 数据分割


```python
from sklearn.model_selection import train_test_split

# 30% examples in test data
train, test, train_labels, test_labels = train_test_split(df_final, labels, 
                                                          stratify = labels,
                                                          test_size = 0.3, 
                                                          random_state = RSEED)

train = train.fillna(train.mean())
test = test.fillna(test.mean())

# Features for feature importances
features = list(train.columns)
```


```python
print('training sample is ', train.shape)
print('test sample is', test.shape)
```

    training sample is  (37572, 19)
    test sample is (16103, 19)
    


```python
##Descriptive analysis
```


```python
# To begin this exploratory analysis, first import libraries and define functions and utilities to work with the data.

from mpl_toolkits.mplot3d import Axes3D
from sklearn.preprocessing import StandardScaler
import matplotlib.pyplot as plt # plotting
import numpy as np # linear algebra
import os # accessing directory structure
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)

# for beautiful plots and some types of graphs
import seaborn as sns
```


```python
def plotCorrelationMatrix(df, graphWidth, segmentName=None):
    filename = segmentName if segmentName else getattr(df, "dataframeName", segmentName)
    df = df.dropna('columns') # drop columns with NaN
    df = df[[col for col in df if df[col].nunique() > 1]] # keep columns where there are more than 1 unique values
    if df.shape[1] < 2:
        print(f'No correlation plots shown: The number of non-NaN or constant columns ({df.shape[1]}) is less than 2')
        return
    corr = df.corr()
    plt.figure(num=None, figsize=(graphWidth, graphWidth), dpi=80, facecolor='w', edgecolor='k')
    corrMat = plt.matshow(corr, fignum = 1)
    plt.xticks(range(len(corr.columns)), corr.columns, rotation=90)
    plt.yticks(range(len(corr.columns)), corr.columns)
    plt.gca().xaxis.tick_bottom()
    plt.colorbar(corrMat)
    plt.title(f'Correlation Matrix for {filename}', fontsize=15)
    plt.show()
```


```python
df_final1=df_final
```


```python
df_final1["is_deal"]= df["is_deal"]
```


```python
df_final1
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
      <th>bathrooms</th>
      <th>bedrooms</th>
      <th>dscpt_polarity</th>
      <th>dscpt_subjectivity</th>
      <th>Elevator</th>
      <th>Hardwood Floors</th>
      <th>Cats Allowed</th>
      <th>Dogs Allowed</th>
      <th>Doorman</th>
      <th>Dishwasher</th>
      <th>No Fee</th>
      <th>Laundry in Building</th>
      <th>Fitness Center</th>
      <th>Pre-War</th>
      <th>num_of_photos</th>
      <th>gender</th>
      <th>price</th>
      <th>expected_price</th>
      <th>followup</th>
      <th>is_deal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.5</td>
      <td>3.0</td>
      <td>0.136364</td>
      <td>0.454545</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>3000.0</td>
      <td>2700.0</td>
      <td>13.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>1</td>
      <td>5465.0</td>
      <td>5200.0</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.616667</td>
      <td>0.666667</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>8</td>
      <td>1</td>
      <td>2850.0</td>
      <td>2900.0</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>3275.0</td>
      <td>3500.0</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>4.0</td>
      <td>0.850000</td>
      <td>1.000000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>3350.0</td>
      <td>3200.0</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>53670</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.175000</td>
      <td>0.775000</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>1</td>
      <td>3200.0</td>
      <td>3500.0</td>
      <td>7.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53671</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.160000</td>
      <td>0.540000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>8</td>
      <td>0</td>
      <td>3950.0</td>
      <td>3500.0</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53672</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.214286</td>
      <td>0.428571</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>2595.0</td>
      <td>2200.0</td>
      <td>5.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>53673</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.500000</td>
      <td>1.000000</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9</td>
      <td>0</td>
      <td>3350.0</td>
      <td>3900.0</td>
      <td>15.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>53674</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.000000</td>
      <td>0.750000</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>2200.0</td>
      <td>2300.0</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>53675 rows × 20 columns</p>
</div>




```python
df_final1.hist(figsize=(13,10))
plt.show()
```


![png](output_80_0.png)



```python
df_final1.to_csv('final2.csv')
```


```python
plotCorrelationMatrix(df_final1, 8, "All inquiries")
```


![png](output_82_0.png)


# 3. 模型training
## 3.1 Baseline Model: Logistic Regression


```python
performance = {}
```


```python
from sklearn.linear_model import LogisticRegression
from sklearn import tree
from sklearn.metrics import precision_score, recall_score, accuracy_score, f1_score
```


```python
logregModel = LogisticRegression(C=400) #instantiate
logregModel.fit(train, train_labels) # fit model
```




    LogisticRegression(C=400, class_weight=None, dual=False, fit_intercept=True,
                       intercept_scaling=1, l1_ratio=None, max_iter=100,
                       multi_class='auto', n_jobs=None, penalty='l2',
                       random_state=None, solver='lbfgs', tol=0.0001, verbose=0,
                       warm_start=False)




```python
preds = logregModel.predict(train)

print("Precision = {}".format(precision_score(train_labels, preds, average='macro')))
print("Recall = {}".format(recall_score(train_labels, preds, average='macro')))
print("Accuracy = {}".format(accuracy_score(train_labels, preds)))
```

    Precision = 0.3829487077111448
    Recall = 0.4999478750390937
    Accuracy = 0.7658362610454594
    


```python
preds = logregModel.predict(test)

pres, rec, acc = (
    precision_score(test_labels, preds, average='macro'),
    recall_score(test_labels, preds, average='macro'),
    accuracy_score(test_labels, preds)
)
print("Precision = {}".format(pres))
print("Recall = {}".format(rec))
print("Accuracy = {}".format(acc))

performance['logistic'] = (pres, rec, acc)
```

    Precision = 0.3829337970438455
    Recall = 0.49995945836373956
    Accuracy = 0.7658200335341241
    


```python

```


```python
preds.sum()
```




    1




```python
test_labels.sum()
```




    3770




```python
logregModel.coef_
```




    array([[-0.29929301,  0.09454247,  0.07624918, -0.11582866, -0.05648102,
             0.07078875,  0.02854568, -0.32849005, -0.48165352, -0.04013258,
             0.33048016,  0.14803438, -0.04653605, -0.38846947, -0.60667547,
             0.2961993 ,  0.27502064,  0.27141961,  0.01495243]])




```python
list(zip(train.columns, logregModel.coef_[0]))
```




    [('bathrooms', -0.29929301245574685),
     ('bedrooms', 0.09454246900499783),
     ('dscpt_polarity', 0.07624917741119284),
     ('dscpt_subjectivity', -0.11582866052570917),
     ('Elevator', -0.05648102051132694),
     ('Hardwood Floors', 0.0707887473203212),
     ('Cats Allowed', 0.028545678014498817),
     ('Dogs Allowed', -0.32849004812842847),
     ('Doorman', -0.4816535157251992),
     ('Dishwasher', -0.040132580538014065),
     ('No Fee', 0.33048016290616455),
     ('Laundry in Building', 0.14803437913491685),
     ('Fitness Center', -0.04653605494041259),
     ('Pre-War', -0.38846947244239505),
     ('num_of_photos', -0.6066754688644703),
     ('gender', 0.29619930433736713),
     ('price', 0.27502064058348635),
     ('expected_price', 0.2714196058080121),
     ('followup', 0.014952430083192688)]



## 3.2 Model Selection
### 3.1.1 decision tree


```python
# Make a decision tree and train
treeModel = tree.DecisionTreeClassifier()
treeModel.fit(train, train_labels)
```




    DecisionTreeClassifier(ccp_alpha=0.0, class_weight=None, criterion='gini',
                           max_depth=None, max_features=None, max_leaf_nodes=None,
                           min_impurity_decrease=0.0, min_impurity_split=None,
                           min_samples_leaf=1, min_samples_split=2,
                           min_weight_fraction_leaf=0.0, presort='deprecated',
                           random_state=None, splitter='best')




```python
preds2 = treeModel.predict(test)

pres, rec, acc = (
    precision_score(test_labels, preds2, average='macro'),
    recall_score(test_labels, preds2, average='macro'),
    accuracy_score(test_labels, preds2)
)
print("Precision = {}".format(pres))
print("Recall = {}".format(rec))
print("Accuracy = {}".format(acc))

performance['decision_tree'] = (pres, rec, acc)
```

    Precision = 0.5254073872315648
    Recall = 0.5233045692037128
    Accuracy = 0.6722349872694529
    


```python
fi_model = pd.DataFrame({'feature': features,
                   'importance': treeModel.feature_importances_}).\
                    sort_values('importance', ascending = False)
fi_model.head(10)
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
      <th>feature</th>
      <th>importance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>expected_price</td>
      <td>0.170748</td>
    </tr>
    <tr>
      <th>16</th>
      <td>price</td>
      <td>0.149383</td>
    </tr>
    <tr>
      <th>14</th>
      <td>num_of_photos</td>
      <td>0.115779</td>
    </tr>
    <tr>
      <th>18</th>
      <td>followup</td>
      <td>0.107736</td>
    </tr>
    <tr>
      <th>2</th>
      <td>dscpt_polarity</td>
      <td>0.106574</td>
    </tr>
    <tr>
      <th>3</th>
      <td>dscpt_subjectivity</td>
      <td>0.106042</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bedrooms</td>
      <td>0.040414</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Laundry in Building</td>
      <td>0.020219</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Dishwasher</td>
      <td>0.020166</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Elevator</td>
      <td>0.019413</td>
    </tr>
  </tbody>
</table>
</div>



### 3.1.2 random forest


```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import confusion_matrix, classification_report

# Create the model with 100 trees
RM_model = RandomForestClassifier(n_estimators=100, 
                               random_state=RSEED, 
                               max_features = 'sqrt',
                               n_jobs=-1, verbose = 1)

# Fit on training data
RM_model.fit(train, train_labels)
```

    [Parallel(n_jobs=-1)]: Using backend ThreadingBackend with 8 concurrent workers.
    [Parallel(n_jobs=-1)]: Done  34 tasks      | elapsed:    0.2s
    [Parallel(n_jobs=-1)]: Done 100 out of 100 | elapsed:    0.7s finished
    




    RandomForestClassifier(bootstrap=True, ccp_alpha=0.0, class_weight=None,
                           criterion='gini', max_depth=None, max_features='sqrt',
                           max_leaf_nodes=None, max_samples=None,
                           min_impurity_decrease=0.0, min_impurity_split=None,
                           min_samples_leaf=1, min_samples_split=2,
                           min_weight_fraction_leaf=0.0, n_estimators=100,
                           n_jobs=-1, oob_score=False, random_state=30, verbose=1,
                           warm_start=False)




```python
preds3 = RM_model.predict(test)

pres, rec, acc = (
    precision_score(test_labels, preds3, average='macro'),
    recall_score(test_labels, preds3, average='macro'),
    accuracy_score(test_labels, preds3)
)
print("Precision = {}".format(pres))
print("Recall = {}".format(rec))
print("Accuracy = {}".format(acc))

performance['random_forest'] = (pres, rec, acc)
```

    Precision = 0.589082748323531
    Recall = 0.5173644129603331
    Accuracy = 0.7590511084891014
    

    [Parallel(n_jobs=8)]: Using backend ThreadingBackend with 8 concurrent workers.
    [Parallel(n_jobs=8)]: Done  34 tasks      | elapsed:    0.0s
    [Parallel(n_jobs=8)]: Done 100 out of 100 | elapsed:    0.0s finished
    


```python
fi_model = pd.DataFrame({'feature': features,
                   'importance': RM_model.feature_importances_}).\
                    sort_values('importance', ascending = False)
fi_model.head(10)
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
      <th>feature</th>
      <th>importance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>expected_price</td>
      <td>0.170170</td>
    </tr>
    <tr>
      <th>16</th>
      <td>price</td>
      <td>0.146372</td>
    </tr>
    <tr>
      <th>14</th>
      <td>num_of_photos</td>
      <td>0.113439</td>
    </tr>
    <tr>
      <th>18</th>
      <td>followup</td>
      <td>0.107116</td>
    </tr>
    <tr>
      <th>2</th>
      <td>dscpt_polarity</td>
      <td>0.101994</td>
    </tr>
    <tr>
      <th>3</th>
      <td>dscpt_subjectivity</td>
      <td>0.100606</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bedrooms</td>
      <td>0.054621</td>
    </tr>
    <tr>
      <th>15</th>
      <td>gender</td>
      <td>0.021725</td>
    </tr>
    <tr>
      <th>0</th>
      <td>bathrooms</td>
      <td>0.020426</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Elevator</td>
      <td>0.018922</td>
    </tr>
  </tbody>
</table>
</div>



### 3.1.3 xgboost


```python
from xgboost import XGBClassifier

param = {
    'eta': 0.3, 
    'max_depth': 3,  
    'objective': 'binary:logistic',
} 

xgb_model = XGBClassifier(**param)
xgb_model.fit(X=train, y=train_labels)
```




    XGBClassifier(base_score=0.5, booster='gbtree', colsample_bylevel=1,
                  colsample_bynode=1, colsample_bytree=1, eta=0.3, gamma=0,
                  gpu_id=-1, importance_type='gain', interaction_constraints='',
                  learning_rate=0.300000012, max_delta_step=0, max_depth=3,
                  min_child_weight=1, missing=nan, monotone_constraints='()',
                  n_estimators=100, n_jobs=0, num_parallel_tree=1,
                  objective='binary:logistic', random_state=0, reg_alpha=0,
                  reg_lambda=1, scale_pos_weight=1, subsample=1,
                  tree_method='exact', validate_parameters=1, verbosity=None)




```python
xgb_preds = xgb_model.predict(test)

pres, rec, acc = (
    precision_score(test_labels, xgb_preds, average='macro'),
    recall_score(test_labels, xgb_preds, average='macro'),
    accuracy_score(test_labels, xgb_preds)
)
print("Precision = {}".format(pres))
print("Recall = {}".format(rec))
print("Accuracy = {}".format(acc))

performance['xgboost'] = (pres, rec, acc)
```

    Precision = 0.649815180338051
    Recall = 0.5215188122870623
    Accuracy = 0.7672483388188537
    

## 3.2 Compare


```python
pd.DataFrame(performance, index=['precision', 'recall', 'accuracy'])
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
      <th>logistic</th>
      <th>decision_tree</th>
      <th>xgboost</th>
      <th>random_forest</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>precision</th>
      <td>0.382934</td>
      <td>0.525407</td>
      <td>0.649815</td>
      <td>0.589083</td>
    </tr>
    <tr>
      <th>recall</th>
      <td>0.499959</td>
      <td>0.523305</td>
      <td>0.521519</td>
      <td>0.517364</td>
    </tr>
    <tr>
      <th>accuracy</th>
      <td>0.765820</td>
      <td>0.672235</td>
      <td>0.767248</td>
      <td>0.759051</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(classification_report(test_labels, xgb_preds))
```

                  precision    recall  f1-score   support
    
               0       0.77      0.98      0.87     12333
               1       0.53      0.06      0.11      3770
    
        accuracy                           0.77     16103
       macro avg       0.65      0.52      0.49     16103
    weighted avg       0.72      0.77      0.69     16103
    
    


```python
confusion_matrix(test_labels, best_preds)
```




    array([[12244,    89],
           [ 3668,   102]], dtype=int64)




```python
from xgboost import plot_importance
from matplotlib import pyplot
plot_importance(xgb_model, max_num_features=10, height=0.8)
pyplot.show()
```


![png](output_109_0.png)


## 可以进行的模型改进

1. 处理数据不平衡问题，防止出现某一个类别几乎dominate
2. 模型参数调整
3. 其他变量的研究
