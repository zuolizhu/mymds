---
title: "DataPreprocessing"
date: 2018-01-08T19:41:58-05:00
draft: true
---

Data preprocessing is crucial step to make machine learning model.

---

1. Import the necessary libraries in Python

   ```python
   # numpy: Mathematics tool library
   # matplotlib.pylot: creating plot Chart
   # pandas: import and manage datasets
   import numpy as np
   import matplotlib.pylot as plt
   import pandas as pd
   ```

   ​

2. Load dataset 

   - working directory can be set directly in Spyder if in OSX system.
   - or save the python file to desired folder and run the whole file (either play button or press F5) in Windows system.

   To read csv file

   ```python
   dataset = pd.read_csv('Data.csv')
   ```

   ​

3. Distinguish the matrix of features(independent) and the dependent variable vector

   - Matrix of features

     ```python
     # Take all the columns except last one
     X = dataset.iloc[:, :-1].values
     ```

   - Dependent variable

     ```python
     # Take last column values
     y = dataset.iloc[:, 3].values
     ```

     Note: **Python index start at 0**. The 3 means the 4th column.

   ​

4. Handle missing data

   Use `sklearn.preprocessing.Imputer` library to process missing data.

   ​	A comment strategy to handle a missing data is to **take the mean of the column**.

   ```python
   # Handle missing data
   from sklearn.preprocessing import Imputer
   imputer = Imputer(missing_values = 'NaN', strategy = 'mean', axis = 0)
   imputer = imputer.fit(X[:, 1:3])
   X[:, 1:3] = imputer.transform(X[:, 1:3])
   ```

   Note: `imputer.fit(X[:, 1:3])` is to control which columns should use the strategy.

   `imputer.transform(X[:, 1:3])` is to **swap the processed data** with original column.

   ​

5. Encode categorical data

   ```python
   # Encoding categorical data
   from sklearn.preprocessing import LabelEncoder, OneHotEncoder
   labelencoder_X = LabelEncoder()
   X[:, 0] = labelencoder_X.fit_transform(X[:, 0])
   # To avoid relation order, use dummy encoding
   onehotencoder = OneHotEncoder(categorical_features = [0])
   X = onehotencoder.fit_transform(X).toarray()
   # To encode dependent variable
   labelencoder_y = LabelEncoder()
   y = labelencoder_y.fit_transform(y)
   ```

   ​

6. Splitting the Dataset into the Training set and test set

   ```python
   # Splitting the dataset into the Training set and Test set
   from sklearn.model_selection import train_test_split
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   ```

   Note: `sklearn.cross_validation` will be removed in 0.20. 
   Using [`sklearn.model_selection.train_test_split`](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html#sklearn.model_selection.train_test_split) instead.

   ​

7. Feature scaling

   - Example using scaling: the age and salary, the number of salary is far greater than age. This will cause a problem in math equation.

   - Scaling will also speed up computation time!

     ```python
     # Feature scaling
     from sklearn.preprocessing import StandardScaler
     scale_X = StandardScaler()
     X_train = scale_X.fit_transform(X_train)
     X_test = scale_X.transform(X_test)
     ```

     ​


---

Data preprocessing template

1. Without handling missing data

   ```python
   # Data Preprocessing Template

   # Importing the libraries
   import numpy as np
   import matplotlib.pyplot as plt
   import pandas as pd

   # Importing the dataset
   dataset = pd.read_csv('Data.csv')
   X = dataset.iloc[:, :-1].values
   y = dataset.iloc[:, 3].values

   # Splitting the dataset into the Training set and Test set
   from sklearn.model_selection import train_test_split
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)

   # Feature Scaling
   """from sklearn.preprocessing import StandardScaler
   sc_X = StandardScaler()
   X_train = sc_X.fit_transform(X_train)
   X_test = sc_X.transform(X_test)
   sc_y = StandardScaler()
   y_train = sc_y.fit_transform(y_train)"""
   ```

   ​

2. Categorical and missing data processing template

   ```python
   # Missing data handle and encode category

   # Importing the libraries
   import numpy as np
   import matplotlib.pyplot as plt
   import pandas as pd

   # Importing the dataset
   dataset = pd.read_csv('Data.csv')
   X = dataset.iloc[:, :-1].values
   y = dataset.iloc[:, 3].values

   # Taking care of missing data
   from sklearn.preprocessing import Imputer
   imputer = Imputer(missing_values = 'NaN', strategy = 'mean', axis = 0)
   imputer = imputer.fit(X[:, 1:3])
   X[:, 1:3] = imputer.transform(X[:, 1:3])

   # Encoding categorical data
   # Encoding the Independent Variable
   from sklearn.preprocessing import LabelEncoder, OneHotEncoder
   labelencoder_X = LabelEncoder()
   X[:, 0] = labelencoder_X.fit_transform(X[:, 0])
   onehotencoder = OneHotEncoder(categorical_features = [0])
   X = onehotencoder.fit_transform(X).toarray()
   # Encoding the Dependent Variable
   labelencoder_y = LabelEncoder()
   y = labelencoder_y.fit_transform(y)
   ```

   ​

Whole code of note:

```python
# Data Preprocessing

# Importing the libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Importing the dataset
dataset = pd.read_csv('Data.csv')

# Distinguish the matrix of features and the dependent variable vector
# Matrix of features
# Take all the columns except last one
X = dataset.iloc[:, :-1].values
# Dependent variable
# Take last column values
y = dataset.iloc[:, 3].values

# Taking care of missing data
from sklearn.preprocessing import Imputer
imputer = Imputer(missing_values = 'NaN', strategy = 'mean', axis = 0)
# Process stratagy above into second and third column
# Since rest colomns does not have missing data
imputer = imputer.fit(X[:, 1:3])
X[:, 1:3] = imputer.transform(X[:, 1:3])

# Encoding categorical data
from sklearn.preprocessing import LabelEncoder, OneHotEncoder
labelencoder_X = LabelEncoder()
X[:, 0] = labelencoder_X.fit_transform(X[:, 0])
# To avoid relation order, use dummy encoding
onehotencoder = OneHotEncoder(categorical_features = [0])
X = onehotencoder.fit_transform(X).toarray()

# To encode dependent variable
labelencoder_y = LabelEncoder()
y = labelencoder_y.fit_transform(y)

# Splitting the dataset into the Training set and Test set
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)

# Feature scaling
# Example: Age and salary, the number of salary is far greater than age.
from sklearn.preprocessing import StandardScaler
scale_X = StandardScaler()
X_train = scale_X.fit_transform(X_train)
X_test = scale_X.transform(X_test)
```

