# Multiple ways to drop duplicates


## Syntax: DataFrame.drop_duplicates(subset=None, keep=’first’, inplace=False)
#### sorting by first name
data.sort_values("First Name", inplace = True)
 
#### dropping ALL duplicate values
data.drop_duplicates(subset ="First Name",
                     keep = False, inplace = True)


# How to update the value of a row in a Python Dataframe?

Original Data frame:
 <!-- TABLE_GENERATE_START -->
|  Num  | NAME|
| ----- | ----|
|0 |  12 |   John|
|1 |  14 | Camili|
|2 |  13 | Rheana|
|3  | 12 | Joseph|
|4 |  14 | Amanti|
|5 |  13  | Alexa|
|6 |  15  |  Siri|
<!-- TABLE_GENERATE_END -->

#### 1. Using Python at() method to update the value of a row
Syntax:
dataframe.at[index,'column-name']='new value'

Example:
In this example, we have provided the at() function with index 6 of the data frame and column ‘NAME’. Thus, the value of the column ‘NAME’ at row index 6 gets updated.
data.at[6,'NAME']='Safa'

Output:

|Num    |NAME|
| ----- | ----|
|0  | 12  |  John|
|1  | 14 | Camili|
|2  | 13 | Rheana|
|3  | 12 | Joseph|
|4  | 14 | Amanti|
|5  | 13 |  Alexa|
|6  | 15 |   Safa|


#### 2. Python loc() function to change the value of a row/column

Syntax:
dataframe.loc[row index,['column-names']] = value

Example:
data.loc[0:2,['Num','NAME']] = [100,'Python']


Output:

|Num    |NAME|
| ----- | ----|
|0  |100 | Python|
|1  |100  |Python|
|2  |100 | Python|
|3  | 12  |Joseph|
|4  | 14  |Amanti|
|5   |13  | Alexa|
|6  | 15  |  Siri|


#### 3. Python replace() method to update values in a dataframe
Syntax:
dataframe.replace("old string", "new string")

Example:
data.replace("Siri", 
           "Code", 
           inplace=True)
           
           
Output:

|Num  |  NAME|
| ----- | ----|
|0  | 12 |   John|
|1  | 14  |Camili|
|2  | 13 | Rheana|
|3  | 12  |Joseph|
|4  | 14 | Amanti|
|5  | 13  | Alexa|
|6  | 15  |  Code  |       
           
           
#### 4. Using iloc() method to update the value of a row
Syntax:
dataframe.iloc[index] = value

Example:
data.iloc[[0,1,3,6],[0]] = 100

output
|Num  |  NAME|
| ----- | ----|
|0  |100 |   John|
|1 | 100 | Camili|
|2  | 13  |Rheana|
|3  |100  |Joseph|
|4 |  14 | Amanti|
|5  | 13 |  Alexa|
|6  |100 |   Siri|

