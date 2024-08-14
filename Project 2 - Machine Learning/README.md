# Car Price Prediction Using Machine Learning

This project focuses on predicting car prices using a machine learning approach. The dataset used comprises over 6,000 observations and about 16 features, including the selling price. The project demonstrates the end-to-end process of data cleaning, preprocessing, model training, and evaluation, with a particular emphasis on using MongoDB in machine learning workflows.

## Project Overview

### Objective
The primary objective of this project is to build a machine learning model capable of predicting the selling price of cars based on various features. The project aims to demonstrate the seamless integration of MongoDB with machine learning pipelines, from data storage to model evaluation.

### Dataset
- **Size:** ~6,000 observations
- **Features:** 16 features, including car make, model, year, mileage, fuel type, transmission, and selling price.
- **Condition:** The dataset was initially messy and inconsistent, requiring extensive cleaning and preprocessing.

## Workflow

### 1. Data Cleaning and Preparation
The dataset provided was highly inconsistent in terms of data types, formats, and representation. A thorough data cleaning process was undertaken using Pandas to remove any irregularities and errors:

- **Data Type Correction:** Ensured that each column had the correct data type (e.g., converting string representations of numbers to numeric types).
- **Formatting Consistency:** Standardized formats for categorical data such as car models and fuel types.
- **Data Errors:** Detected and removed erranous observations that could skew the analysis and model performance.

Code for Data Cleaning - [click here](https://github.com/MisbahullahSheriff/Data-Cleaning-Projects/tree/main/Car%20Details%20v3)

### 2. Data Storage in MongoDB
After cleaning, the data was loaded into MongoDB using GUI application, MongoDB Compass, to take advantage of its document-based storage and retrieval capabilities.

### 3. Data Preprocessing and Feature Engineering
To prepare the data for modeling, several preprocessing steps were implemented using scikit-learn:

- **Train-Test Split:** The dataset was split into training and testing sets.
- **Feature Engineering:** Applied transformations such as scaling for numeric features and one-hot encoding for categorical features.
- **Pipeline Setup:** Utilized scikit-learn pipelines and column transformers to streamline the preprocessing steps.

```python
# split the data
X = df.drop(columns="selling_price").assign(model=df.model.astype(str))
y = df.selling_price.copy()
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# numeric pipeline
num_cols = X_train.select_dtypes(include="number").columns
num_pipe = Pipeline([
	("imputer", SimpleImputer(strategy="median")),
	("scaler", StandardScaler())
])

# categorical pipeline
cat_cols = [col for col in X_train.columns if col not in num_cols]
cat_pipe = Pipeline([
	("imputer", SimpleImputer(strategy="most_frequent")),
	("encoder", OneHotEncoder(handle_unknown="ignore"))
])

# final preprocessor
preprocessor = ColumnTransformer(transformers=[
	("num", num_pipe, num_cols),
	("cat", cat_pipe, cat_cols)
])
```

### 4. Model Training and Evaluation
Multiple regression models were trained and evaluated to predict car prices:

#### Models Used:
- Linear Regression
- Ridge Regression
- Lasso Regression
- Support Vector Machine (SVM)
- Random Forest Regressor
- XGBoost Regressor

```python
algos = (
	("Linear Regression", LinearRegression()),
	("Ridge", Ridge()),
	("Lasso", Lasso()),
	("SVM", SVR()),
	("Random Forest", RandomForestRegressor(n_estimators=20, max_depth=5)),
	("XGBOOST", XGBRegressor(n_estimators=20, max_depth=5))
)
```
  
#### Model Evaluation:
The models were evaluated using Root Mean Squared Error (RMSE) on the test data.

### 5. Storing Results in MongoDB
The results of each model's performance were stored in MongoDB for further analysis and comparison.

```python
for alg, reg  in algos:
	# setup the regressor with preprocessor
	model = Pipeline([
		("pre", preprocessor),
		("alg", reg)
	])
	
	# train the model
	model.fit(X_train, y_train)
	print(f"> Trained {alg}")

	# evaluate the model
	y_pred = model.predict(X_test)
	rmse = mean_squared_error(y_test, y_pred, squared=False)
	print(f"> RMSE: {rmse:,.2f}")

	# insert data into MongoDB
	model_results = db["model_results"]
	result = {"model": alg, "RMSE": rmse}
	model_results.insert_one(result)
	print("> Inserted data into MongoDB")
	
	print()
```

### 6. Insights and Conclusion
The project demonstrated how MongoDB can be effectively integrated into a machine learning workflow, from data cleaning and storage to model evaluation. The results provided insights into which models performed best for this specific dataset, with XGBoost and Random Forest generally showing superior performance in predicting car prices.

#### Technologies Used:
- **MongoDB:** For data storage and retrieval.
- **Pymongo:** To interact with MongoDB using Python.
- **Pandas:** For data cleaning, manipulation, and summarization.
- **Scikit-learn:** For preprocessing, model training, and evaluation.
- **XGBoost:** For advanced regression modeling.











