Multiple ways to drop duplicates


Syntax: DataFrame.drop_duplicates(subset=None, keep=’first’, inplace=False)
 # sorting by first name
data.sort_values("First Name", inplace = True)
 
# dropping ALL duplicate values
data.drop_duplicates(subset ="First Name",
                     keep = False, inplace = True)
 
# displaying data
data



