# Useful Regex Examples to Validate Data in Pandas
##### Inspired by: https://github.com/rasbt/python_reference

## Sections

- [Identify files via file extensions](#Identify-files-via-file-extensions)
- [Username validation](#Username-validation)
- [Checking for valid email addresses](#Checking-for-valid-email-addresses)
- [Check for a valid URL](#Check-for-a-valid-URL)
- [Validating numbers](#Validating-numbers)
- [Validating dates](#Validating-dates)
- [Check Valid Time Format](#Check-Valid-Time-Format)
- [Validating HTML tags](#Validating-HTML-tags)
- [Validating MAC addresses](#Validating-MAC-addresses)  
<br>  
- [Other resources](https://github.com/MarkCruse/regex-pandas/blob/master/regex_sources.md)  

<br>
<br>

### Import Needed Libraries


```python
import pandas as pd
from pathlib import Path

import warnings
warnings.filterwarnings("ignore")
```

### Read mock data into dataframe


```python
filePath = Path("data")
file_in = filePath.joinpath('mucked_mock_data.csv')
df = pd.read_csv(file_in,usecols=['id','file_name','email','url','numbers','date','time12','time24','html','mac'],dtype={'id':'string'})
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1000 entries, 0 to 999
    Data columns (total 10 columns):
     #   Column     Non-Null Count  Dtype 
    ---  ------     --------------  ----- 
     0   id         1000 non-null   string
     1   email      1000 non-null   object
     2   date       1000 non-null   object
     3   mac        1000 non-null   object
     4   file_name  1000 non-null   object
     5   url        1000 non-null   object
     6   time12     1000 non-null   object
     7   time24     1000 non-null   object
     8   numbers    1000 non-null   object
     9   html       1000 non-null   object
    dtypes: object(9), string(1)
    memory usage: 78.2+ KB


## Identify files via file extensions

[[back to top](#Sections)]

A regular expression to check for file extensions.  

Note: This approach is not recommended for thorough limitation of file types (parse the file header instead). However, this regex is still a useful alternative to e.g., a Python's `endswith` approach for quick pre-filtering for certain files of interest.


```python
pattern = r'(?i)(\w+)\.(jpeg|jpg|png|gif|tif|svg|ppt)$'

# remove `(?i)` to make regexpr case-sensitive

df['file_name'].astype(str).str.contains(pattern,regex=True).value_counts()

```




    False    726
    True     274
    Name: file_name, dtype: int64



## Username validation

[[back to top](#Sections)]

Checking for a valid user name that has a certain minimum and maximum length.

Allowed characters:
- letters (upper- and lower-case)
- numbers
- dashes
- underscores


```python
min_len = 4 # minimum length for a valid username
max_len = 6 # maximum length for a valid username

pattern = r"^[A-Z0-9_-]{%s,%s}$" %(min_len, max_len)

# add `(?i)` and change `A-Z` to `a-z` to allow lower-case letters

print (df['id'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid IDs:')
df[['id']][~df['id'].astype(str).str.contains(pattern,regex=True)]
```

    True     811
    False    189
    Name: id, dtype: int64
    
    Invalid IDs:





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>JMU2^^</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BJE27^</td>
    </tr>
    <tr>
      <th>9</th>
      <td>KBE2^6</td>
    </tr>
    <tr>
      <th>19</th>
      <td>PLE22^</td>
    </tr>
    <tr>
      <th>23</th>
      <td>RJE20^</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>981</th>
      <td>ERO2^5</td>
    </tr>
    <tr>
      <th>988</th>
      <td>PBA27^</td>
    </tr>
    <tr>
      <th>995</th>
      <td>MRO21^</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
    </tr>
    <tr>
      <th>998</th>
      <td>KSK2^6</td>
    </tr>
  </tbody>
</table>
<p>189 rows × 1 columns</p>
</div>



<br>
<br>

## Checking for valid email addresses

[[back to top](#Sections)]

A regular expression that captures most email addresses.


```python
pattern = r"(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)"
print (df['email'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid email addresses:')
df[['id','email']][~df['email'].astype(str).str.contains(pattern,regex=True)]
```

    True     904
    False     96
    Name: email, dtype: int64
    
    Invalid email addresses:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>email</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>SSH279</td>
      <td>ssherbrooke2@list-manage_com</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZFA290</td>
      <td>zfabri4@biblegateway_com</td>
    </tr>
    <tr>
      <th>10</th>
      <td>KTI286</td>
      <td>ktitterella@sciencedirect_com</td>
    </tr>
    <tr>
      <th>15</th>
      <td>LDE229</td>
      <td>ldeglanvillef@reference_com</td>
    </tr>
    <tr>
      <th>17</th>
      <td>LMA248</td>
      <td>lmacandrewh@japanpost_jp</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>915</th>
      <td>PSC264</td>
      <td>pschankepf@constantcontact_com</td>
    </tr>
    <tr>
      <th>928</th>
      <td>GPA2^4</td>
      <td>gpaulips@deviantart_com</td>
    </tr>
    <tr>
      <th>943</th>
      <td>SSE2^6</td>
      <td>ssellorq7@parallels_com</td>
    </tr>
    <tr>
      <th>982</th>
      <td>EWE255</td>
      <td>eweldra@facebook_com</td>
    </tr>
    <tr>
      <th>984</th>
      <td>SOT251</td>
      <td>sotimonyrc@salon_com</td>
    </tr>
  </tbody>
</table>
<p>96 rows × 2 columns</p>
</div>



<font size="1px">source: [http://stackoverflow.com/questions/201323/using-a-regular-expression-to-validate-an-email-address](http://stackoverflow.com/questions/201323/using-a-regular-expression-to-validate-an-email-address)</font>

<br>
<br>

## Check for a valid URL

[[back to top](#Sections)]

Checks for an URL if a string ...

- starts with `https://`, or `http://`, or `www.`
- or ends with a dot extension


```python
pattern = '^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$'
print (df['url'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid URL addresses:')
df[['id','url']][~df['url'].astype(str).str.contains(pattern,regex=True)]
```

    True     905
    False     95
    Name: url, dtype: int64
    
    Invalid URL addresses:





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>RBO218</td>
      <td>http://umn_edu</td>
    </tr>
    <tr>
      <th>19</th>
      <td>PLE22^</td>
      <td>wwss://rambler.ru</td>
    </tr>
    <tr>
      <th>25</th>
      <td>MKA246</td>
      <td>wwss://symantec.com</td>
    </tr>
    <tr>
      <th>26</th>
      <td>RDE242</td>
      <td>wwss://dropbox.com</td>
    </tr>
    <tr>
      <th>31</th>
      <td>BGA222</td>
      <td>https://squarespace_com</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>951</th>
      <td>OSK202</td>
      <td>https://phpbb_com</td>
    </tr>
    <tr>
      <th>952</th>
      <td>MAL207</td>
      <td>https://phoca_cz</td>
    </tr>
    <tr>
      <th>962</th>
      <td>HSA226</td>
      <td>wws://cdbaby.com</td>
    </tr>
    <tr>
      <th>971</th>
      <td>JCA269</td>
      <td>wws://si.edu</td>
    </tr>
    <tr>
      <th>990</th>
      <td>TPO207</td>
      <td>wwss://weebly.com</td>
    </tr>
  </tbody>
</table>
<p>95 rows × 2 columns</p>
</div>



<font size="1px">source: [http://code.tutsplus.com/tutorials/8-regular-expressions-you-should-know--net-6149](http://code.tutsplus.com/tutorials/8-regular-expressions-you-should-know--net-6149)</font>

<br>
<br>

## Validating numbers

[[back to top](#Sections)]

### Positive integers


```python
pattern = '^\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT Positive integers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    False    588
    True     412
    Name: numbers, dtype: int64
    
    NOT Positive integers:





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>SSH279</td>
      <td>-977</td>
    </tr>
    <tr>
      <th>7</th>
      <td>AIM266</td>
      <td>-348</td>
    </tr>
    <tr>
      <th>8</th>
      <td>MVA286</td>
      <td>202.32</td>
    </tr>
    <tr>
      <th>9</th>
      <td>KBE2^6</td>
      <td>784.98</td>
    </tr>
    <tr>
      <th>10</th>
      <td>KTI286</td>
      <td>-436</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>992</th>
      <td>EPE264</td>
      <td>-348</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>-745.20</td>
    </tr>
    <tr>
      <th>995</th>
      <td>MRO21^</td>
      <td>-129</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
    <tr>
      <th>999</th>
      <td>PBA290</td>
      <td>52.57</td>
    </tr>
  </tbody>
</table>
<p>588 rows × 2 columns</p>
</div>



### Negative integers


```python
pattern = '^-\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT Negative integers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    False    595
    True     405
    Name: numbers, dtype: int64
    
    NOT Negative integers:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCH204</td>
      <td>559</td>
    </tr>
    <tr>
      <th>1</th>
      <td>JMU2^^</td>
      <td>570</td>
    </tr>
    <tr>
      <th>3</th>
      <td>RBO218</td>
      <td>685</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZFA290</td>
      <td>919</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BJE27^</td>
      <td>827</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>-745.20</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
    <tr>
      <th>997</th>
      <td>MTH287</td>
      <td>898</td>
    </tr>
    <tr>
      <th>998</th>
      <td>KSK2^6</td>
      <td>208</td>
    </tr>
    <tr>
      <th>999</th>
      <td>PBA290</td>
      <td>52.57</td>
    </tr>
  </tbody>
</table>
<p>595 rows × 2 columns</p>
</div>



### All integers


```python
pattern = '^-{0,1}\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT All integers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    True     817
    False    183
    Name: numbers, dtype: int64
    
    NOT All integers:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>MVA286</td>
      <td>202.32</td>
    </tr>
    <tr>
      <th>9</th>
      <td>KBE2^6</td>
      <td>784.98</td>
    </tr>
    <tr>
      <th>16</th>
      <td>KTR262</td>
      <td>swx</td>
    </tr>
    <tr>
      <th>17</th>
      <td>LMA248</td>
      <td>300.13</td>
    </tr>
    <tr>
      <th>22</th>
      <td>SJE228</td>
      <td>yhl.77</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>984</th>
      <td>SOT251</td>
      <td>-640.69</td>
    </tr>
    <tr>
      <th>990</th>
      <td>TPO207</td>
      <td>128.83</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>-745.20</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
    <tr>
      <th>999</th>
      <td>PBA290</td>
      <td>52.57</td>
    </tr>
  </tbody>
</table>
<p>183 rows × 2 columns</p>
</div>



### Positive numbers


```python
pattern = '^\d*\.{0,1}\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT Positive numbers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    False    549
    True     451
    Name: numbers, dtype: int64
    
    NOT Positive numbers:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>SSH279</td>
      <td>-977</td>
    </tr>
    <tr>
      <th>7</th>
      <td>AIM266</td>
      <td>-348</td>
    </tr>
    <tr>
      <th>10</th>
      <td>KTI286</td>
      <td>-436</td>
    </tr>
    <tr>
      <th>11</th>
      <td>PCR216</td>
      <td>-718</td>
    </tr>
    <tr>
      <th>12</th>
      <td>BSY254</td>
      <td>-645</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>991</th>
      <td>PMU217</td>
      <td>-144</td>
    </tr>
    <tr>
      <th>992</th>
      <td>EPE264</td>
      <td>-348</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>-745.20</td>
    </tr>
    <tr>
      <th>995</th>
      <td>MRO21^</td>
      <td>-129</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
  </tbody>
</table>
<p>549 rows × 2 columns</p>
</div>



### Negative numbers


```python
pattern = '^-\d*\.{0,1}\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT Negative numbers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    False    549
    True     451
    Name: numbers, dtype: int64
    
    NOT Negative numbers:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCH204</td>
      <td>559</td>
    </tr>
    <tr>
      <th>1</th>
      <td>JMU2^^</td>
      <td>570</td>
    </tr>
    <tr>
      <th>3</th>
      <td>RBO218</td>
      <td>685</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZFA290</td>
      <td>919</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BJE27^</td>
      <td>827</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>993</th>
      <td>CLE240</td>
      <td>792</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
    <tr>
      <th>997</th>
      <td>MTH287</td>
      <td>898</td>
    </tr>
    <tr>
      <th>998</th>
      <td>KSK2^6</td>
      <td>208</td>
    </tr>
    <tr>
      <th>999</th>
      <td>PBA290</td>
      <td>52.57</td>
    </tr>
  </tbody>
</table>
<p>549 rows × 2 columns</p>
</div>



### All numbers


```python
pattern = '^-{0,1}\d*\.{0,1}\d+$'

print (df['numbers'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nNOT All numbers:')
df[['id','numbers']][~df['numbers'].astype(str).str.contains(pattern,regex=True)]
```

    True     902
    False     98
    Name: numbers, dtype: int64
    
    NOT All numbers:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>numbers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>16</th>
      <td>KTR262</td>
      <td>swx</td>
    </tr>
    <tr>
      <th>22</th>
      <td>SJE228</td>
      <td>yhl.77</td>
    </tr>
    <tr>
      <th>25</th>
      <td>MKA246</td>
      <td>qqx</td>
    </tr>
    <tr>
      <th>39</th>
      <td>MFA20^</td>
      <td>zvb</td>
    </tr>
    <tr>
      <th>40</th>
      <td>KLI228</td>
      <td>gju</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>970</th>
      <td>HCH280</td>
      <td>amm</td>
    </tr>
    <tr>
      <th>973</th>
      <td>CWR222</td>
      <td>wat</td>
    </tr>
    <tr>
      <th>974</th>
      <td>TFA240</td>
      <td>ggu</td>
    </tr>
    <tr>
      <th>980</th>
      <td>LSC201</td>
      <td>-422.66.39</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>fnm</td>
    </tr>
  </tbody>
</table>
<p>98 rows × 2 columns</p>
</div>



<font size="1px">source: [http://stackoverflow.com/questions/1449817/what-are-some-of-the-most-useful-regular-expressions-for-programmers](http://stackoverflow.com/questions/1449817/what-are-some-of-the-most-useful-regular-expressions-for-programmers)</font>

<br>
<br>

## Validating dates

[[back to top](#Sections)]

Validates dates in `mm/dd/yyyy` format.


```python
pattern = '^(0[1-9]|1[0-2])\/(0[1-9]|1\d|2\d|3[01])\/(19|20)\d{2}$'

print (df['date'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid Dates:')
df[['id','date']][~df['date'].astype(str).str.contains(pattern,regex=True)]
```

    True     818
    False    182
    Name: date, dtype: int64
    
    Invalid Dates:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CCH204</td>
      <td>06/07/8020</td>
    </tr>
    <tr>
      <th>12</th>
      <td>BSY254</td>
      <td>23/08/2019</td>
    </tr>
    <tr>
      <th>27</th>
      <td>OSA279</td>
      <td>10/18/8019</td>
    </tr>
    <tr>
      <th>31</th>
      <td>BGA222</td>
      <td>13/23/2020</td>
    </tr>
    <tr>
      <th>48</th>
      <td>RHA26^</td>
      <td>10/36/2019</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>982</th>
      <td>EWE255</td>
      <td>16/06/2020</td>
    </tr>
    <tr>
      <th>985</th>
      <td>IJE250</td>
      <td>08/58/2019</td>
    </tr>
    <tr>
      <th>986</th>
      <td>NFO205</td>
      <td>16/24/2020</td>
    </tr>
    <tr>
      <th>989</th>
      <td>ISH204</td>
      <td>17/31/2020</td>
    </tr>
    <tr>
      <th>997</th>
      <td>MTH287</td>
      <td>24/07/2019</td>
    </tr>
  </tbody>
</table>
<p>182 rows × 2 columns</p>
</div>



### Check Valid Time Format

[[back to top](#Sections)]

### 12-Hour format


```python
pattern = r'^(1[012]|[1-9]):[0-5][0-9](\s)?(?i)(am|pm)$'

print (df['time12'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid 12 Time:')
df[['id','time12']][~df['time12'].astype(str).str.contains(pattern,regex=True)]
```

    True     816
    False    184
    Name: time12, dtype: int64
    
    Invalid 12 Time:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>time12</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>ZFA290</td>
      <td>13:70 PM</td>
    </tr>
    <tr>
      <th>9</th>
      <td>KBE2^6</td>
      <td>19:70 PM</td>
    </tr>
    <tr>
      <th>15</th>
      <td>LDE229</td>
      <td>14:10 PM</td>
    </tr>
    <tr>
      <th>21</th>
      <td>MFU219</td>
      <td>18:10 PM</td>
    </tr>
    <tr>
      <th>36</th>
      <td>DFA252</td>
      <td>23:10 PM</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>964</th>
      <td>GBE24^</td>
      <td>4:70 PM</td>
    </tr>
    <tr>
      <th>976</th>
      <td>HLA206</td>
      <td>11:70 PM</td>
    </tr>
    <tr>
      <th>988</th>
      <td>PBA27^</td>
      <td>35:10 PM</td>
    </tr>
    <tr>
      <th>992</th>
      <td>EPE264</td>
      <td>22:10 PM</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>5:70 PM</td>
    </tr>
  </tbody>
</table>
<p>184 rows × 2 columns</p>
</div>



### 24-Hour format


```python
pattern = r'^([0-1]{1}[0-9]{1}|20|21|22|23):[0-5]{1}[0-9]{1}$'

print (df['time24'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid 24 Time:')
df[['id','time24']][~df['time24'].astype(str).str.contains(pattern,regex=True)]
```

    True     604
    False    396
    Name: time24, dtype: int64
    
    Invalid 24 Time:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>time24</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>JMU2^^</td>
      <td>3:40</td>
    </tr>
    <tr>
      <th>2</th>
      <td>SSH279</td>
      <td>9:51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>RBO218</td>
      <td>4:27</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZFA290</td>
      <td>1:10</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BJE27^</td>
      <td>0:02</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>993</th>
      <td>CLE240</td>
      <td>6:07</td>
    </tr>
    <tr>
      <th>994</th>
      <td>FSA218</td>
      <td>3:28</td>
    </tr>
    <tr>
      <th>996</th>
      <td>MDU2^1</td>
      <td>3:01</td>
    </tr>
    <tr>
      <th>997</th>
      <td>MTH287</td>
      <td>1:39</td>
    </tr>
    <tr>
      <th>998</th>
      <td>KSK2^6</td>
      <td>8:07</td>
    </tr>
  </tbody>
</table>
<p>396 rows × 2 columns</p>
</div>



<br>
<br>

## Validating HTML tags

[[back to top](#Sections)]

Also this regex is only recommended for "filtering" purposes and not a ultimate way to parse HTML. For more information see this excellent discussion on StackOverflow:  
[http://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags/](http://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags/) 


```python
pattern = r"""</?\w+((\s+\w+(\s*=\s*(?:".*?"|'.*?'|[^'">\s]+))?)+\s*|\s*)/?>"""

print (df['html'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid HTML:')
df[['id','html']][~df['html'].astype(str).str.contains(pattern,regex=True)]
```

    True     667
    False    333
    Name: html, dtype: int64
    
    Invalid HTML:





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>html</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>SSH279</td>
      <td>&lt;op^group&gt;</td>
    </tr>
    <tr>
      <th>3</th>
      <td>RBO218</td>
      <td>&lt;^body&gt;</td>
    </tr>
    <tr>
      <th>6</th>
      <td>KSL286</td>
      <td>&lt;/^ime&gt;</td>
    </tr>
    <tr>
      <th>8</th>
      <td>MVA286</td>
      <td>&lt;inpu^&gt;</td>
    </tr>
    <tr>
      <th>9</th>
      <td>KBE2^6</td>
      <td>&lt;^d&gt;</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>988</th>
      <td>PBA27^</td>
      <td>&lt;/^r&gt;</td>
    </tr>
    <tr>
      <th>991</th>
      <td>PMU217</td>
      <td>&lt;fieldse^&gt;</td>
    </tr>
    <tr>
      <th>993</th>
      <td>CLE240</td>
      <td>&lt;/r^c&gt;</td>
    </tr>
    <tr>
      <th>995</th>
      <td>MRO21^</td>
      <td>&lt;da^a&gt;</td>
    </tr>
    <tr>
      <th>997</th>
      <td>MTH287</td>
      <td>&lt;/^i^le&gt;</td>
    </tr>
  </tbody>
</table>
<p>333 rows × 2 columns</p>
</div>



<font size="1px">source: [http://haacked.com/archive/2004/10/25/usingregularexpressionstomatchhtml.aspx/](http://haacked.com/archive/2004/10/25/usingregularexpressionstomatchhtml.aspx/)</font>

<br>
<br>

## Validating MAC addresses

[[back to top](#Sections)]


```python
pattern = r'^(?i)([0-9A-F]{2}[:-]){5}([0-9A-F]{2})$'

print (df['mac'].astype(str).str.contains(pattern,regex=True).value_counts())
print ('\nInvalid MAC Address:')
df[['id','mac']][~df['mac'].astype(str).str.contains(pattern,regex=True)]
```

    True     977
    False     23
    Name: mac, dtype: int64
    
    Invalid MAC Address:





<div>

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>mac</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>71</th>
      <td>BFL271</td>
      <td>xx:AB:57:ED:9C:90</td>
    </tr>
    <tr>
      <th>152</th>
      <td>LME284</td>
      <td>7C:2F:40:57:E3:xx</td>
    </tr>
    <tr>
      <th>235</th>
      <td>ADU275</td>
      <td>D5:xx:D1:E1:2B:2B</td>
    </tr>
    <tr>
      <th>305</th>
      <td>JST206</td>
      <td>E2:EF:0B:xx:14:29</td>
    </tr>
    <tr>
      <th>308</th>
      <td>ALY250</td>
      <td>xx:53:48:16:D4:F3</td>
    </tr>
    <tr>
      <th>331</th>
      <td>ACR252</td>
      <td>D2:xx:35:EA:EC:75</td>
    </tr>
    <tr>
      <th>336</th>
      <td>PGI272</td>
      <td>1B:54:6C:A3:28:xx</td>
    </tr>
    <tr>
      <th>417</th>
      <td>RWA220</td>
      <td>4F:C6:4F:D0:81:xx</td>
    </tr>
    <tr>
      <th>464</th>
      <td>BDU262</td>
      <td>AE:xx:A2:46:97:E6</td>
    </tr>
    <tr>
      <th>477</th>
      <td>WHE221</td>
      <td>26:E4:7B:C8:46:xx</td>
    </tr>
    <tr>
      <th>533</th>
      <td>FCH278</td>
      <td>55:63:xx:4A:A5:7C</td>
    </tr>
    <tr>
      <th>554</th>
      <td>SDE264</td>
      <td>xx:BD:2F:3D:A8:CD</td>
    </tr>
    <tr>
      <th>593</th>
      <td>APA2^^</td>
      <td>60:F2:ED:AB:E2:xx</td>
    </tr>
    <tr>
      <th>648</th>
      <td>NAD220</td>
      <td>42:7D:53:73:7C:xx</td>
    </tr>
    <tr>
      <th>696</th>
      <td>LAY216</td>
      <td>7F:93:0F:00:xx:E1</td>
    </tr>
    <tr>
      <th>818</th>
      <td>MTH251</td>
      <td>2C:E7:B9:xx:EB:5C</td>
    </tr>
    <tr>
      <th>895</th>
      <td>KFE207</td>
      <td>xx:A9:85:BD:0F:40</td>
    </tr>
    <tr>
      <th>900</th>
      <td>SME264</td>
      <td>17:B4:xx:6B:D5:89</td>
    </tr>
    <tr>
      <th>921</th>
      <td>RSH287</td>
      <td>94:6D:1F:AD:02:xx</td>
    </tr>
    <tr>
      <th>927</th>
      <td>FHA206</td>
      <td>2D:D4:xx:65:81:E1</td>
    </tr>
    <tr>
      <th>938</th>
      <td>NJO209</td>
      <td>98:D9:xx:AE:E9:D6</td>
    </tr>
    <tr>
      <th>978</th>
      <td>CMA274</td>
      <td>E4:xx:BC:E1:23:71</td>
    </tr>
    <tr>
      <th>984</th>
      <td>SOT251</td>
      <td>80:A5:9D:2F:E0:xx</td>
    </tr>
  </tbody>
</table>
</div>
