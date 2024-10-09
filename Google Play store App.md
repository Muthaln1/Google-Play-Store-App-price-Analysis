### Introduction:

+ I assume I am a part of google analytics team working on the price analysis of Google play store Apps

+ As a team, I am trying to prototype a project to investigate whether it is profitable enough to secure funding, allowing decision makers to invest time and resources.


### Assumption:
+ We assume the account manager approaches us to prototype the project, considering that the department's  marketing budget does not allow her to invest in ads (which would boost sales). The only way to improve the revenue is by adjusting the price.

### GOAL:
+ To improve the revenue of undervalued apps to motivate developers to continue working on them.
+ To determine which paid apps are undervalued (here, "undervalued" means that their price could be increased without lowering demand).

#### Loading the data into Pandas dataframe:


```python
import pandas as pd

playstore = pd.read_csv("googleplaystore.csv")

# There were totally 10841 rows and 13 columns
print(playstore.shape)
```

    (10841, 13)


#### Sample data


```python
print(playstore.head(1))
```

                                                  App        Category  Rating  \
    0  Photo Editor & Candy Camera & Grid & ScrapBook  ART_AND_DESIGN     4.1   
    
      Reviews Size Installs  Type Price Content Rating        Genres  \
    0     159  19M  10,000+  Free     0       Everyone  Art & Design   
    
          Last Updated Current Ver   Android Ver  
    0  January 7, 2018       1.0.0  4.0.3 and up  


#### Data types of the fields/columns


```python
print(playstore.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10841 entries, 0 to 10840
    Data columns (total 13 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   App             10841 non-null  object 
     1   Category        10841 non-null  object 
     2   Rating          9367 non-null   float64
     3   Reviews         10841 non-null  object 
     4   Size            10841 non-null  object 
     5   Installs        10841 non-null  object 
     6   Type            10840 non-null  object 
     7   Price           10841 non-null  object 
     8   Content Rating  10840 non-null  object 
     9   Genres          10841 non-null  object 
     10  Last Updated    10841 non-null  object 
     11  Current Ver     10833 non-null  object 
     12  Android Ver     10838 non-null  object 
    dtypes: float64(1), object(12)
    memory usage: 1.1+ MB
    None


### Findings:

+ The 'Rating' field ranges between 0 and 5, but there was a presence of 19.0, indicating that there are outliers in the data


+ Upon inspecting the 'Category' field, we found the presence of 'int' data types


+ There was a column shifting of data in one of the rows with index 10472, moving data from the 'Rating' field to the 'Category' field


+ We used the Google Play Store to obtain the actual category to fill in the data, which is LIFESTYLE


+ As our project is focused on paid apps, we could drop the problematic row with index 10472, as it pertains to a free app


+ There are totally 10039 free apps priced 0.00 as showed below and 800 paid apps


```python
# Presence of Int data under the `Category` field:
# 1.9 does not belong to the category field

print(playstore['Category'].value_counts(dropna=False).sort_values())
```

    1.9                       1
    BEAUTY                   53
    PARENTING                60
    COMICS                   60
    EVENTS                   64
    ART_AND_DESIGN           65
    WEATHER                  82
    AUTO_AND_VEHICLES        85
    LIBRARIES_AND_DEMO       85
    HOUSE_AND_HOME           88
    FOOD_AND_DRINK          127
    MAPS_AND_NAVIGATION     137
    ENTERTAINMENT           149
    EDUCATION               156
    VIDEO_PLAYERS           175
    BOOKS_AND_REFERENCE     231
    DATING                  234
    TRAVEL_AND_LOCAL        258
    SHOPPING                260
    NEWS_AND_MAGAZINES      283
    SOCIAL                  295
    PHOTOGRAPHY             335
    HEALTH_AND_FITNESS      341
    FINANCE                 366
    LIFESTYLE               382
    SPORTS                  384
    COMMUNICATION           387
    PERSONALIZATION         392
    PRODUCTIVITY            424
    BUSINESS                460
    MEDICAL                 463
    TOOLS                   843
    GAME                   1144
    FAMILY                 1972
    Name: Category, dtype: int64



```python
# Rating should vary between 0-5, but there was a presence of 19.0 which is out of range

print(playstore['Rating'].value_counts().sort_index(ascending = False))
```

    19.0       1
    5.0      274
    4.9       87
    4.8      234
    4.7      499
    4.6      823
    4.5     1038
    4.4     1109
    4.3     1076
    4.2      952
    4.1      708
    4.0      568
    3.9      386
    3.8      303
    3.7      239
    3.6      174
    3.5      163
    3.4      128
    3.3      102
    3.2       64
    3.1       69
    3.0       83
    2.9       45
    2.8       42
    2.7       25
    2.6       25
    2.5       21
    2.4       19
    2.3       20
    2.2       14
    2.1        8
    2.0       12
    1.9       13
    1.8        8
    1.7        8
    1.6        4
    1.5        3
    1.4        3
    1.2        1
    1.0       16
    Name: Rating, dtype: int64



```python
# Inspecting the problematic row with index: 10472

x = playstore.loc[10472]

print(x)
```

    App               Life Made WI-Fi Touchscreen Photo Frame
    Category                                              1.9
    Rating                                               19.0
    Reviews                                              3.0M
    Size                                               1,000+
    Installs                                             Free
    Type                                                    0
    Price                                            Everyone
    Content Rating                                        NaN
    Genres                                  February 11, 2018
    Last Updated                                       1.0.19
    Current Ver                                    4.0 and up
    Android Ver                                           NaN
    Name: 10472, dtype: object



```python
# Code to update the row values 

playstore.loc[10472, ['App', 'Category','Rating','Reviews','Size','Installs','Type','Price','Content Rating','Genres','Last Updated','Current Ver','Android Ver']] = ['Life Made WI-Fi Touchscreen Photo Frame', 'LIFESTYLE',1.9,'19.0','3.0M','1,000+','Free','0','Everyone','NaN','February 11, 2018','1.0.19','4.0 and up']
```


```python
# Corrected row with updated category and details:
x = playstore.loc[10472]

print(x)
```

    App               Life Made WI-Fi Touchscreen Photo Frame
    Category                                        LIFESTYLE
    Rating                                                1.9
    Reviews                                              19.0
    Size                                                 3.0M
    Installs                                           1,000+
    Type                                                 Free
    Price                                                   0
    Content Rating                                   Everyone
    Genres                                                NaN
    Last Updated                            February 11, 2018
    Current Ver                                        1.0.19
    Android Ver                                    4.0 and up
    Name: 10472, dtype: object



```python
# Dropping the problematic row as we are working on paid apps

playstore.drop(index = 10472, inplace = True)
```

#### The `Price` field has the $ symbol, making it difficult to perform analysis



```python
print(playstore["Price"].value_counts(ascending = False))
```

    0         10040
    $0.99       148
    $2.99       129
    $1.99        73
    $4.99        72
              ...  
    $19.90        1
    $1.75         1
    $14.00        1
    $4.85         1
    $1.04         1
    Name: Price, Length: 92, dtype: int64


#### For analysis converting the `Price ` and `Reviews` data type from object data type to 'float' and 'int' respectively.



```python
playstore["Price"] = playstore["Price"].str.replace("$", "").astype("float")
playstore["Reviews"] = playstore["Reviews"].astype("int")
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/596582675.py:1: FutureWarning: The default value of regex will change from True to False in a future version. In addition, single character regular expressions will *not* be treated as literal strings when regex=True.
      playstore["Price"] = playstore["Price"].str.replace("$", "").astype("float")



```python
# Updated data type (Price and Reviews field)

print(playstore.info())
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 10840 entries, 0 to 10840
    Data columns (total 13 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   App             10840 non-null  object 
     1   Category        10840 non-null  object 
     2   Rating          9366 non-null   float64
     3   Reviews         10840 non-null  int64  
     4   Size            10840 non-null  object 
     5   Installs        10840 non-null  object 
     6   Type            10839 non-null  object 
     7   Price           10840 non-null  float64
     8   Content Rating  10840 non-null  object 
     9   Genres          10840 non-null  object 
     10  Last Updated    10840 non-null  object 
     11  Current Ver     10832 non-null  object 
     12  Android Ver     10838 non-null  object 
    dtypes: float64(2), int64(1), object(10)
    memory usage: 1.2+ MB
    None



```python
#Meanwhile, the 'Size' field uses M and K as units, indicating megabytes and kilobytes

print(playstore['Size'].unique())
```

    ['19M' '14M' '8.7M' '25M' '2.8M' '5.6M' '29M' '33M' '3.1M' '28M' '12M'
     '20M' '21M' '37M' '2.7M' '5.5M' '17M' '39M' '31M' '4.2M' '7.0M' '23M'
     '6.0M' '6.1M' '4.6M' '9.2M' '5.2M' '11M' '24M' 'Varies with device'
     '9.4M' '15M' '10M' '1.2M' '26M' '8.0M' '7.9M' '56M' '57M' '35M' '54M'
     '201k' '3.6M' '5.7M' '8.6M' '2.4M' '27M' '2.5M' '16M' '3.4M' '8.9M'
     '3.9M' '2.9M' '38M' '32M' '5.4M' '18M' '1.1M' '2.2M' '4.5M' '9.8M' '52M'
     '9.0M' '6.7M' '30M' '2.6M' '7.1M' '3.7M' '22M' '7.4M' '6.4M' '3.2M'
     '8.2M' '9.9M' '4.9M' '9.5M' '5.0M' '5.9M' '13M' '73M' '6.8M' '3.5M'
     '4.0M' '2.3M' '7.2M' '2.1M' '42M' '7.3M' '9.1M' '55M' '23k' '6.5M' '1.5M'
     '7.5M' '51M' '41M' '48M' '8.5M' '46M' '8.3M' '4.3M' '4.7M' '3.3M' '40M'
     '7.8M' '8.8M' '6.6M' '5.1M' '61M' '66M' '79k' '8.4M' '118k' '44M' '695k'
     '1.6M' '6.2M' '18k' '53M' '1.4M' '3.0M' '5.8M' '3.8M' '9.6M' '45M' '63M'
     '49M' '77M' '4.4M' '4.8M' '70M' '6.9M' '9.3M' '10.0M' '8.1M' '36M' '84M'
     '97M' '2.0M' '1.9M' '1.8M' '5.3M' '47M' '556k' '526k' '76M' '7.6M' '59M'
     '9.7M' '78M' '72M' '43M' '7.7M' '6.3M' '334k' '34M' '93M' '65M' '79M'
     '100M' '58M' '50M' '68M' '64M' '67M' '60M' '94M' '232k' '99M' '624k'
     '95M' '8.5k' '41k' '292k' '11k' '80M' '1.7M' '74M' '62M' '69M' '75M'
     '98M' '85M' '82M' '96M' '87M' '71M' '86M' '91M' '81M' '92M' '83M' '88M'
     '704k' '862k' '899k' '378k' '266k' '375k' '1.3M' '975k' '980k' '4.1M'
     '89M' '696k' '544k' '525k' '920k' '779k' '853k' '720k' '713k' '772k'
     '318k' '58k' '241k' '196k' '857k' '51k' '953k' '865k' '251k' '930k'
     '540k' '313k' '746k' '203k' '26k' '314k' '239k' '371k' '220k' '730k'
     '756k' '91k' '293k' '17k' '74k' '14k' '317k' '78k' '924k' '902k' '818k'
     '81k' '939k' '169k' '45k' '475k' '965k' '90M' '545k' '61k' '283k' '655k'
     '714k' '93k' '872k' '121k' '322k' '1.0M' '976k' '172k' '238k' '549k'
     '206k' '954k' '444k' '717k' '210k' '609k' '308k' '705k' '306k' '904k'
     '473k' '175k' '350k' '383k' '454k' '421k' '70k' '812k' '442k' '842k'
     '417k' '412k' '459k' '478k' '335k' '782k' '721k' '430k' '429k' '192k'
     '200k' '460k' '728k' '496k' '816k' '414k' '506k' '887k' '613k' '243k'
     '569k' '778k' '683k' '592k' '319k' '186k' '840k' '647k' '191k' '373k'
     '437k' '598k' '716k' '585k' '982k' '222k' '219k' '55k' '948k' '323k'
     '691k' '511k' '951k' '963k' '25k' '554k' '351k' '27k' '82k' '208k' '913k'
     '514k' '551k' '29k' '103k' '898k' '743k' '116k' '153k' '209k' '353k'
     '499k' '173k' '597k' '809k' '122k' '411k' '400k' '801k' '787k' '237k'
     '50k' '643k' '986k' '97k' '516k' '837k' '780k' '961k' '269k' '20k' '498k'
     '600k' '749k' '642k' '881k' '72k' '656k' '601k' '221k' '228k' '108k'
     '940k' '176k' '33k' '663k' '34k' '942k' '259k' '164k' '458k' '245k'
     '629k' '28k' '288k' '775k' '785k' '636k' '916k' '994k' '309k' '485k'
     '914k' '903k' '608k' '500k' '54k' '562k' '847k' '957k' '688k' '811k'
     '270k' '48k' '329k' '523k' '921k' '874k' '981k' '784k' '280k' '24k'
     '518k' '754k' '892k' '154k' '860k' '364k' '387k' '626k' '161k' '879k'
     '39k' '970k' '170k' '141k' '160k' '144k' '143k' '190k' '376k' '193k'
     '246k' '73k' '658k' '992k' '253k' '420k' '404k' '470k' '226k' '240k'
     '89k' '234k' '257k' '861k' '467k' '157k' '44k' '676k' '67k' '552k' '885k'
     '1020k' '582k' '619k']


#### `Size` field conversion from Kilobytes to Megabytes


```python
# Conversion of the 'Size' field to Megabytes and removing 'M' to convert the data type to 'float'

def clean_size(size):
    """Convert file size string to float and megabytes"""
    size = size.replace("M","")
    if size.endswith("k"):
        size = float(size[:-1])/1000
    elif size == "Varies with device":
        size = pd.np.NaN
    else:
        size = float(size)
    return size

# Applying the changes to the playstore dataframe
playstore['Size']= playstore['Size'].apply(clean_size)
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/1053158660.py:9: FutureWarning: The pandas.np module is deprecated and will be removed from pandas in a future version. Import numpy directly instead.
      size = pd.np.NaN



```python
# There are totally 10039 free apps priced 0.00 as showed below and 800 paid apps

print(playstore['Type'].value_counts(dropna= False))
```

    Free    10039
    Paid      800
    NaN         1
    Name: Type, dtype: int64


#### Seperating the paid apps from the dataframe for analysis


```python
# Extracting data from playstore dataframe to fit our analysis:

paid = playstore[playstore['Price'] != 0].sort_values(by ='Reviews', ascending = False).copy()

print(paid.head(2))
```

                App Category  Rating  Reviews  Size     Installs  Type  Price  \
    2241  Minecraft   FAMILY     4.5  2376564   NaN  10,000,000+  Paid   6.99   
    4347  Minecraft   FAMILY     4.5  2375336   NaN  10,000,000+  Paid   6.99   
    
         Content Rating                     Genres   Last Updated Current Ver  \
    2241   Everyone 10+  Arcade;Action & Adventure  July 24, 2018     1.5.2.1   
    4347   Everyone 10+  Arcade;Action & Adventure  July 24, 2018     1.5.2.1   
    
                 Android Ver  
    2241  Varies with device  
    4347  Varies with device  


### Findings:
+ There is duplicate data in the App field. This is due to the presence of the Reviews and Category fields.
+ Some apps are categorized under both Game and Family.
+ The reviews were collected at different points in time, and we should keep the highest review for each app, as it indicates the most current data.


```python
# Duplicate rows in the orginal 'playstore' dataframe
# 'ROBLOX' app has been mentioned 9 times in the Dataframe

print(playstore['App'].value_counts().head(5))
```

    ROBLOX                                               9
    CBS Sports App - Scores, News, Stats & Watch Live    8
    ESPN                                                 7
    Duolingo: Learn Languages Free                       7
    Candy Crush Saga                                     7
    Name: App, dtype: int64



```python
# Duplicate rows in the 'paid' dataframe created for the paid app analysis:
# 'Facetune - For Free' app has been mentioned thrice

print(paid['App'].value_counts())
```

    Facetune - For Free                                 3
    Dr. Panda & Toto's Treehouse                        3
    Human Anatomy Atlas 2018: Complete 3D Human Body    3
    Minecraft                                           2
    Monash Uni Low FODMAP Diet                          2
                                                       ..
    Chess and Mate                                      1
    With Helper Pro Pill Reminder                       1
    Planet O - Icon Pack                                1
    Morse Machine for Ham Radio                         1
    Word Search Tab 1 FR                                1
    Name: App, Length: 756, dtype: int64


### Apps Categorized Under Two Categories with Multiple Reviews, Causing Duplication


```python
# sampling some of the duplicate rows from the Actual dataframe 'playstore':
# Multiple category

print(playstore[playstore['App'] == 'ROBLOX'][['App','Category','Reviews']].sort_values(by ='Reviews', ascending = False ))
```

             App Category  Reviews
    2206  ROBLOX   FAMILY  4450890
    2088  ROBLOX   FAMILY  4450855
    1870  ROBLOX     GAME  4449910
    2016  ROBLOX   FAMILY  4449910
    1841  ROBLOX     GAME  4449882
    1748  ROBLOX     GAME  4448791
    1653  ROBLOX     GAME  4447388
    1701  ROBLOX     GAME  4447346
    4527  ROBLOX   FAMILY  4443407



```python
# sampling some of the duplicate rows under 'Paid' Apps dataframe used for the analysis:
# Multiple reviews

print(paid[paid['App'] == '''Dr. Panda & Toto's Treehouse'''][['App','Category','Reviews']].sort_values(by ='Reviews' ))
```

                                   App Category  Reviews
    2192  Dr. Panda & Toto's Treehouse   FAMILY     3396
    2205  Dr. Panda & Toto's Treehouse   FAMILY     3396
    8785  Dr. Panda & Toto's Treehouse   FAMILY     3397



```python
# Dropping the duplicate rows from the 'paid' dataframe

print("Number of rows before dropping duplicates: {}".format(paid.shape[0]))

# Remove duplicates but leaves the first occurence
paid.drop_duplicates(subset="App",inplace=True)

print("Number of rows after dropping duplicates: {}".format(paid.shape[0]))
```

    Number of rows before dropping duplicates: 800
    Number of rows after dropping duplicates: 756



```python
# Checking the presence of duplicate values

print('Duplicate Values:' ,paid.duplicated(subset="App").sum())
```

    Duplicate Values: 0


### Findings:

+ The distribution of App prices is right-skewed, with most prices falling below 50 dollars.
+ We will remove the outliers and focus on rows where the price is 50 dollars or below for better analysis.


```python
paid.hist(column="Price", grid=False, figsize=(12,6))
```




    array([[<Axes: title={'center': 'Price'}>]], dtype=object)




    
![png](output_36_1.png)
    



```python
print(paid['Price'].min())
```

    0.99


### Segmenting the data

+ Due to the presence of outliers, I am curating the data into the affordable_apps DataFrame so that the price range of apps is between a minimum of 0.99 cents and a maximum of 50 dollars.


```python
#Segmenting the data further and dividing the dataframe from 'paid' to 'affordable_apps'
# Rows reduced from 800 to 733

affordable_apps = paid[paid['Price']<= 50]
print(affordable_apps.shape[0])
```

    733



```python
#Since the Installs field contains characters, we are unable to use it for analysis

print(affordable_apps['Installs'].value_counts())
```

    1,000+         132
    10,000+        112
    100+            93
    100,000+        81
    10+             69
    5,000+          62
    500+            40
    50,000+         39
    50+             34
    1,000,000+      20
    1+              20
    5+              12
    500,000+        11
    0+               6
    10,000,000+      2
    Name: Installs, dtype: int64



```python
# Using function to clean the Installs field:

def installs(x):
        z = x['Installs']
        j = z.replace("+","")
        j = j.replace(",","")
        return j
        
affordable_apps['Installs'] = affordable_apps.apply(installs, axis =1)
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/4016678648.py:9: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps['Installs'] = affordable_apps.apply(installs, axis =1)



```python
# Conversion of Installs field to int data type
affordable_apps['Installs'] = affordable_apps['Installs'].astype(int)
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/3324768301.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps['Installs'] = affordable_apps['Installs'].astype(int)



```python
# Current data type of all the fields
print(affordable_apps.info())
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 733 entries, 2241 to 10798
    Data columns (total 13 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   App             733 non-null    object 
     1   Category        733 non-null    object 
     2   Rating          588 non-null    float64
     3   Reviews         733 non-null    int64  
     4   Size            662 non-null    float64
     5   Installs        733 non-null    int64  
     6   Type            733 non-null    object 
     7   Price           733 non-null    float64
     8   Content Rating  733 non-null    object 
     9   Genres          733 non-null    object 
     10  Last Updated    733 non-null    object 
     11  Current Ver     731 non-null    object 
     12  Android Ver     732 non-null    object 
    dtypes: float64(3), int64(2), object(8)
    memory usage: 80.2+ KB
    None


### Findings: 

+ The distribution of prices in the affordable apps dataframe is still skewed to the right, with many apps falling within the price range of $0 to $10


```python
# Histogram to visualize the dataframe field

affordable_apps.hist(column="Price", grid=False, figsize=(12,6))
```




    array([[<Axes: title={'center': 'Price'}>]], dtype=object)




    
![png](output_45_1.png)
    


### Segmenting the data using App price

### Findings:
+ Out of the 733 apps, 596 are considered cheap, 121 are categorized as reasonable, and 16 are classified as expensive.


```python
import matplotlib.pyplot as plt

# Creating masks to obtain data based on the condition provided:
cheap = affordable_apps['Price'] < 5

reasonable = (affordable_apps['Price'] >= 5) & (affordable_apps['Price'] <= 20)

expensive = affordable_apps['Price'] > 20
```


```python
# Cheaper Apps price distribution:
affordable_apps[cheap].hist(column = 'Price',grid=False,figsize=(6,6))
plt.title('Price Distribution of Cheaper Apps')
plt.xlabel('Price') 
plt.ylabel('Frequency') 
plt.show()
```


    
![png](output_48_0.png)
    



```python
# Reasonable Apps price distribution:

affordable_apps[reasonable].hist(column = 'Price',grid=False,figsize=(6,6))
plt.title('Price Distribution of Resonable Apps') 
plt.xlabel('Price')  # Label for the x-axis
plt.ylabel('Frequency')  # Label for the y-axis
plt.show()
```


    
![png](output_49_0.png)
    



```python
# Expensive Apps price distribution:

affordable_apps[expensive].hist(column = 'Price',grid=False,figsize=(6,6))
plt.title('Price Distribution of Expensive Apps') 
plt.xlabel('Price')  # Label for the x-axis
plt.ylabel('Frequency')  # Label for the y-axis
plt.show()
```


    
![png](output_50_0.png)
    


#### Adding an  `affordability ` field to determine whether the App is cheap, reasonable or expensive


```python
# Function to add a column to segment the data into cheaper and resonable apps:
# There were totally 596 apps cost less than 5$(Cheaper) and 137 apps that costs above 5$(Reasonable)

def cheaper(x):
        if x['Price'] < 5:
            return 'cheap'
        elif x['Price'] <= 20:
            return 'reasonable'
        else:
            return 'expensive'
    
affordable_apps['affordability'] = affordable_apps.apply(cheaper, axis =1)

```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/21670072.py:12: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps['affordability'] = affordable_apps.apply(cheaper, axis =1)


#### The affordable_apps dataframe consists of 596 apps that cost less than 5 dollars, 121 apps priced between 5 and 20 dollars, and 16 apps priced above 20 dollars.


```python
print(affordable_apps['affordability'].value_counts())
```

    cheap         596
    reasonable    121
    expensive      16
    Name: affordability, dtype: int64


### Findings:

+ There is no meaningful relationship between price and rating that could help us adjust the price without impacting the app's ratings. 

+ The correlation between price and rating is -0.0499, which is quite low.


```python
print(affordable_apps[['Price','Rating']].corr())
```

               Price    Rating
    Price   1.000000 -0.049897
    Rating -0.049897  1.000000



```python
#The data points are clearly visible, indicating that there is no significant relationship between the variables.

affordable_apps[cheap].plot(kind="scatter", x="Price", y="Rating")
plt.xlabel("Price (in dollars)")
plt.ylabel("Rating")
plt.title("Scatter Plot of Cheaper Price vs. Rating")
plt.show()
```


    
![png](output_57_0.png)
    



```python
# Price has no correlation with the Rating which helps us to tweak the price without affecting the rating:
# Price segmentation

print('Affordable_cheaper_apps_co-rrelation:',round(affordable_apps[cheap].corr().loc["Rating", "Price"],4))
print('Affordable_reasonable_apps_co-rrelation:',round(affordable_apps[reasonable].corr().loc["Rating", "Price"],4))
print('Affordable_expensive_apps_co-rrelation:',round(affordable_apps[expensive].corr().loc["Rating", "Price"],4))
```

    Affordable_cheaper_apps_co-rrelation: -0.0559
    Affordable_reasonable_apps_co-rrelation: 0.0377
    Affordable_expensive_apps_co-rrelation: -0.2693


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2026183766.py:4: FutureWarning: The default value of numeric_only in DataFrame.corr is deprecated. In a future version, it will default to False. Select only valid columns or specify the value of numeric_only to silence this warning.
      print('Affordable_cheaper_apps_co-rrelation:',round(affordable_apps[cheap].corr().loc["Rating", "Price"],4))
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2026183766.py:5: FutureWarning: The default value of numeric_only in DataFrame.corr is deprecated. In a future version, it will default to False. Select only valid columns or specify the value of numeric_only to silence this warning.
      print('Affordable_reasonable_apps_co-rrelation:',round(affordable_apps[reasonable].corr().loc["Rating", "Price"],4))
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2026183766.py:6: FutureWarning: The default value of numeric_only in DataFrame.corr is deprecated. In a future version, it will default to False. Select only valid columns or specify the value of numeric_only to silence this warning.
      print('Affordable_expensive_apps_co-rrelation:',round(affordable_apps[expensive].corr().loc["Rating", "Price"],4))



```python
affordable_apps.shape
```




    (733, 14)



### Goals:

I have curated 733 apps for prototyping the project, and the new goals are as follows:

+ To determine the new prices for apps that are undervalued.

+ To use the `affordability`,`Price`,`Genre`,`Category`,`Installs` and `Reviews` fields to establish success criteria for implementing the new price changes

### Price:
+ To determine the mean price of each affordability group and compare them within their respective groups to identify undervalued apps

+ To flag the apps that meet the above conditions in order to count the number of apps with price adjustment opportunities

+ To create a new price column for the undervalued apps, assigning them their respective mean prices.

### Findings:
+ There are 386 apps priced below the mean price of their respective groups, indicating an opportunity for price adjustments


#### The initial mean prices for the respective groups are:

 + Cheap: 2.58
 + Reasonable: 9.88
 + Expensive: 31.86

#### The new mean prices for the respective groups are:
 + Cheap: 3.16
 + Reasonable: 11.56
 + Expensive: 34.34


```python
# Considering the affordability group to determine the mean price:
aff_mean_test = affordable_apps.groupby(['affordability']).mean()[['Price']]

print(aff_mean_test)
```

                       Price
    affordability           
    cheap           2.580319
    expensive      31.865000
    reasonable      9.881240


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/3831942154.py:2: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      aff_mean_test = affordable_apps.groupby(['affordability']).mean()[['Price']]


### Price Criteria


```python
# Function to identify apps that have price below the group mean price - Cheap, Reasonable and Expensive
def price_criteriaa(x):
    aff = x['affordability']
    pri = x['Price']
    if pri < aff_mean_test.loc[aff].values[0]:
        return 1
    else:
        return 0

affordable_apps['price_criterion'] = affordable_apps.apply(price_criteriaa,axis =1)
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2941976404.py:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps['price_criterion'] = affordable_apps.apply(price_criteriaa,axis =1)



```python
print(affordable_apps['price_criterion'].value_counts())
```

    1    386
    0    347
    Name: price_criterion, dtype: int64


#### `New price` column based on their respective group's mean price


```python
# affordability mean
aff_mean_test = affordable_apps.groupby(['affordability']).mean()[['Price']]
print(aff_mean_test)

# Function to determine the new price
def new_price(x):
        aff = x['affordability']
        pri = x['Price']
        if pri < aff_mean_test.loc[aff][0]:
            return round(aff_mean_test.loc[aff][0],2)
        else:
            return round(pri,2)
        
# New column creation named 'New Price'        
affordable_apps['New Price'] = affordable_apps.apply(new_price,axis =1)
print(affordable_apps['New Price'].head(2))
```

                       Price
    affordability           
    cheap           2.580319
    expensive      31.865000
    reasonable      9.881240
    2241    9.88
    4034    2.58
    Name: New Price, dtype: float64


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2337159436.py:2: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      aff_mean_test = affordable_apps.groupby(['affordability']).mean()[['Price']]
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2337159436.py:15: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps['New Price'] = affordable_apps.apply(new_price,axis =1)



```python
# Considering the affordability group to determine the new mean price:
aff_mean_test_new = affordable_apps.groupby(['affordability']).mean()[['New Price']]

print(aff_mean_test_new)
```

                   New Price
    affordability           
    cheap           3.160520
    expensive      34.338125
    reasonable     11.557521


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/1259461002.py:2: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      aff_mean_test_new = affordable_apps.groupby(['affordability']).mean()[['New Price']]


### Genre criteria: 

+ To determine the mean price of affordable apps grouped by genre and compare them with their respective mean prices to identify undervalued apps

+ Apps with more than one genres should provide greater visibility to the audience leading to more downloads

### Findings:
+ There are 425 apps priced below their mean price based on the affordability & genre criteria

+ Out of 773 apps, 70 are tagged under multiple genres, while 663 are tagged under a single genre

#### The new mean price based on the genre and affordability criteria is as follows: 

+ cheap:      3.071
+ expensive:  33.15
+ reasonable: 11.057


```python
# Sampling the Genre data:

print(affordable_apps["Genres"].head())
```

    2241    Arcade;Action & Adventure
    4034                       Action
    7417                       Action
    8860                     Strategy
    9678           Puzzle;Brain Games
    Name: Genres, dtype: object



```python
# Counting the number of Apps with single and multi genres:

affordable_apps["genre_count"] = affordable_apps["Genres"].str.count(";")+1

print(affordable_apps["genre_count"].value_counts())
```

    1    663
    2     70
    Name: genre_count, dtype: int64


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2614788979.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      affordable_apps["genre_count"] = affordable_apps["Genres"].str.count(";")+1


#### Splitting the `Genres` column into `Genres_1`, `Genres_2` as 70 apps are categorized into more than one genre


```python
# Splitting the Genres into two columns to help with analysis based on the genres:

test = affordable_apps["Genres"].str.split(';', expand=True)

print(test.columns)

# Renaming the splitted columns:
test.columns = [f'Genres_{i+1}' for i in range(test.shape[1])]

print(test.columns)

#Joining the new columns back to the dataframe:
affordable_apps = affordable_apps.join(test)
affordable_apps.columns
```

    RangeIndex(start=0, stop=2, step=1)
    Index(['Genres_1', 'Genres_2'], dtype='object')





    Index(['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type',
           'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver',
           'Android Ver', 'affordability', 'price_criterion', 'New Price',
           'genre_count', 'Genres_1', 'Genres_2'],
          dtype='object')



#### Replacing the null values in the Genres_2 column with values from the Genres_1 column to smooth the data


```python
import numpy as np

# Due to the split,we have 'None' values in our columns. Replacing them with nan using numpy:
affordable_apps['Genres_2'].replace({None: np.nan}, inplace=True)

#Filling the nan values in Genres_2 columns with values from Genres_1:
affordable_apps['Genres_2'].fillna(affordable_apps['Genres_1'], inplace = True)
```


```python
# no presence of null values
print(affordable_apps['Genres_2'].isnull().sum())
```

    0



```python
# Mean price based on the genres_1 and genres_2:

gen_mean_1 = affordable_apps.groupby(["affordability", 'Genres_1']).mean()[["Price"]]
gen_mean_2 = affordable_apps.groupby(["affordability", 'Genres_2']).mean()[["Price"]]
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2479314140.py:3: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      gen_mean_1 = affordable_apps.groupby(["affordability", 'Genres_1']).mean()[["Price"]]
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2479314140.py:4: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      gen_mean_2 = affordable_apps.groupby(["affordability", 'Genres_2']).mean()[["Price"]]



```python
# Creating a function to generate new price based on the genre_1
def label_genres_1(row):
    aff = row["affordability"]
    gc = row['Genres_1']
    price = row["Price"]
    mean_price = gen_mean_1.loc[(aff, gc), "Price"]

    if price < mean_price:
        return round(mean_price,2)
    else:
        return round(price,2)

affordable_apps['New Price_genre_1'] = affordable_apps.apply(label_genres_1, axis="columns")
```


```python
# Creating a function to generate new price based on the genre_2

def label_genres_2(row):
    aff = row["affordability"]
    gc = row["Genres_2"]
    price = row["Price"]
    mean_price = gen_mean_2.loc[(aff, gc), "Price"]

    if price < mean_price:
        return round(mean_price,2)
    else:
        return round(price,2)

affordable_apps['New Price_genre_2'] = affordable_apps.apply(label_genres_2, axis="columns")
```


```python
# Assigning the Average mean of 'New Price_genre_1' & 'New Price_genre_2' columns to 'New Price_genre':

affordable_apps['New Price_genre'] = affordable_apps[['New Price_genre_1','New Price_genre_2']].mean(axis=1)
```


```python
# New mean price based on the affordability and genre criteria:

print(affordable_apps.groupby(['affordability']).mean()['New Price_genre'])
```

    affordability
    cheap          3.071032
    expensive     33.151250
    reasonable    11.056529
    Name: New Price_genre, dtype: float64


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2141833625.py:3: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      print(affordable_apps.groupby(['affordability']).mean()['New Price_genre'])


### Category criteria:

+ To determine the mean price of affordable apps grouped by category and compare them with their respective mean prices to identify undervalued apps

### Findings:
+ There are 376 apps priced below their mean price based on the affordability & category criteria

#### The new mean price based on the affordability and category criteria is as follows: 

+ cheap:         3.097
+ expensive:     33.40
+ reasonable:    11.14


```python
# Category mean:
categories_mean = affordable_apps.groupby(["affordability","Category"]).mean()[["Price"]]

# Function to count the undervalues apps:
def label_category(row):
    aff = row["affordability"]
    gc = row["Category"]
    price = row["Price"]

    if price < categories_mean.loc[(aff, gc)][0]:
        return 1
    else:
        return 0
    
# Assigning flags to identify the apps that satifies the category criteria
affordable_apps['category_criterion']= affordable_apps.apply(label_category, axis =1)
print(affordable_apps['category_criterion'].value_counts())
```

    1    376
    0    357
    Name: category_criterion, dtype: int64


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/3080861806.py:2: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      categories_mean = affordable_apps.groupby(["affordability","Category"]).mean()[["Price"]]



```python
# Function to determine the new price based on category:
def label_category(row):
    aff = row["affordability"]
    gc = row["Category"]
    price = row["Price"]
    mean_price = categories_mean.loc[(aff, gc), "Price"]  # Access using the tuple and column name

    if price < mean_price:
        return round(mean_price,2)
    else:
        return round(price,2)
    
# New column creation named 'New Price_categ'  
affordable_apps['New Price_categ'] = affordable_apps.apply(label_category,axis = 1)
```


```python
# New mean price based on the affordability and category criteria:

print(affordable_apps.groupby(['affordability']).mean()['New Price_categ'])
```

    affordability
    cheap          3.097483
    expensive     33.401250
    reasonable    11.141488
    Name: New Price_categ, dtype: float64


    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_18030/2444716169.py:3: FutureWarning: The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
      print(affordable_apps.groupby(['affordability']).mean()['New Price_categ'])


### Average_price: 

+ Using the provided prices, we have obtained the new prices based on three criteria: affordability, genre, and category

+ Each category provides mean prices based on its respective groups

+ We will obtain the mean price of all these criterias to determine the new prices for our undervalued apps

### Findings: 

+ The new price we have set for our undervalued apps shows us we have an opportunity to create value for our business

+ Out of 773 paid Apps, 466 apps have undergone a price change.

+ The analysis shows we could gain a net worth of 83 millon dollars 

#### Mean of all criteria prices, which will become the new prices for our undervalued apps:


```python
affordable_apps['avg_price'] = round((affordable_apps['New Price'] + affordable_apps['New Price_categ'] + affordable_apps['New Price_genre']) / 3,2)
```


```python
# Out of 733 paid apps, We have totally 466 Apps has new/updated price.

App_price_change = affordable_apps[affordable_apps['Price']!= affordable_apps['avg_price']]

print('Number of Apps that went through price change:',App_price_change['App'].count())
```

    Number of Apps that went through price change: 466


#### To determine the Impact created we use the below formula:

Impact = (New price - Old price) * No of Installs


```python
# Using the new price and no of installs to determine the impact we could create for individual apps:

affordable_apps['Impact_avg'] = (affordable_apps['avg_price'] - affordable_apps['Price']) *  affordable_apps['Installs']

print(affordable_apps['Impact_avg'].head())
```

    2241    36200000.0
    4034    15600000.0
    7417     3110000.0
    8860       50000.0
    9678      720000.0
    Name: Impact_avg, dtype: float64


### Our result was net worth of 83 million dollars


```python
total_impact_avg = affordable_apps['Impact_avg'].sum()
print(round(total_impact_avg))
```

    83418088


### More analysis: ### Rating vs Installs Vs Reviews:

### Findings:

+ The number of 'Installs' has a relationship with 'Reviews' having a high Correlation of (0.807499)

+ The higher the reviews, the more the installation for both cheap and reasonable apps

+ The distribution of ratings is heavily skewed to the left 

+ Out of 773 paid apps we are working on, 712 Apps has ratings above 3.0

+ Out of 773 paid apps we are working on, 459 Apps has installations above 1000


```python
print(affordable_apps[['Reviews','Installs']].corr())
```

               Reviews  Installs
    Reviews   1.000000  0.807499
    Installs  0.807499  1.000000



```python
# Reviews are more for cheaper Apps with single genre and reasonable apps with multi genre leading to more Installs:

Installs_count = affordable_apps.groupby(["affordability"]).agg(sum_reviews = ('Reviews','sum'),sum_installs = ('Installs','sum'),
                                                                          count =  ('Price', 'count')).sort_values( by = 'sum_reviews',ascending = False)
print(Installs_count.head(20))
```

                   sum_reviews  sum_installs  count
    affordability                                  
    cheap              3457661      41638407    596
    reasonable         3122293      15341117    121
    expensive             6954        164246     16


### Rating:


```python
# The chart is heavily skewed to the left as most of the rating values fall between 3 & 5:

affordable_apps.hist(column="Rating", grid=False, figsize=(12,6))
plt.show()
```


    
![png](output_99_0.png)
    


### To consider an App as successful, Setting the Minimum Rating criteria as  3.0 and above


```python
# Function to define rating criteria

def t_rating(x):
    rat = x['Rating']
    if rat < 3.0:
        return 0
    else:
        return 1
    
#Flags to identify Apps that has ratings above 3
affordable_apps['App_rating'] = affordable_apps.apply(t_rating,axis =1)
```


```python
# The 1 represents Apps with rating greater than 3 with a scale (1-5).

print(affordable_apps['App_rating'].value_counts())
```

    1    712
    0     21
    Name: App_rating, dtype: int64


###  To consider an App as successful,Setting the Minimum Installs criteria  1000 and above


```python
# Function to determine the Installs criteria:
def t_rating_1(x):
    rat = x['Installs']
    if rat < 1000:
        return 0
    else:
        return 1
    
#Flags to identify Apps that has installations above 1000
affordable_apps['App_Installs'] = affordable_apps.apply(t_rating_1,axis =1)
```


```python
# There are 459 Apps with Installs more than 1000:

print(affordable_apps['App_Installs'].value_counts())
```

    1    459
    0    274
    Name: App_Installs, dtype: int64



```python
# Filtering rows that went through the price change
App_price_change = affordable_apps[affordable_apps['Price']!= affordable_apps['avg_price']]
```


```python
# Out of 466 Apps that went through the change, 444 Apps satisfies our defined criteria on number of installations, minimum rating and price criteria 

criteriaa = ['price_criterion','App_rating','App_Installs']

x = App_price_change[criteriaa].mode(axis=1)

print(x.value_counts())
```

    1    444
    0     22
    dtype: int64


###  Findings:

+ Out of the 466 apps that underwent the price change, resulting in an impact of $83 million, 444 apps meet our criteria of having a minimum rating of 3.0 or above and at least 1,000 installs, thereby creating value for the business.