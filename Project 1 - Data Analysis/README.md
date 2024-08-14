# NYC Taxi Trips Data Analysis

This project involves an in-depth analysis of the NYC Taxi Trips dataset, which consists of approximately 4 million observations and 19 features. The dataset was provided in Parquet format and was used to answer several key questions regarding taxi operations in New York City. The project leverages MongoDB for data storage and retrieval, and Python for data manipulation, querying, and visualization.

## Project Overview

### Objective
The primary objective of this project is to explore the NYC Taxi Trips dataset to uncover insights related to taxi operations, passenger behavior, and trip characteristics. This involves analyzing various metrics such as peak hours, fare amounts, distances traveled, and more.

### Dataset
- **Size:** ~4 million observations
- **Features:** 19 features, including trip duration, pickup and dropoff locations, fare amount, passenger count, and more.
- **Format:** Parquet

## Workflow

### 1. Setting Up the Environment
To begin, a connection to the local MongoDB server was established using the `pymongo` library. This connection served as the backbone for storing and retrieving the large dataset efficiently.

### 2. Data Ingestion
The dataset, stored in Parquet format, was read into a Pandas DataFrame. Given the size of the data, Pandas provided an efficient way to manage and manipulate the data before importing it into MongoDB.

- **Reading the Data:**
    ```python
    import pandas as pd
    project_dir = os.getcwd()
    data_dir = os.path.join(project_dir, "data")
    data_file = os.path.join(data_dir, "yellow_tripdata_2024-05.parquet")
    df = pd.read_parquet(data_file)
    ```

- **Converting to JSON:**
    The DataFrame was then converted to a JSON format, which is compatible with MongoDB’s document storage structure.

    ```python
    data_json = df.to_dict(orient='records')
    ```

- **Inserting Data into MongoDB:**
    The JSON data was inserted into a MongoDB collection using `pymongo`.

    ```python
    from pymongo import MongoClient
    client = MongoClient('mongodb://localhost:27017/')
    db = client['taxis']
    collection = db['taxi_data']
    collection.insert_many(data_json)
    ```

### 3. Data Analysis Using MongoDB
Once the data was stored in MongoDB, various queries were written to analyze the dataset. The analysis focused on answering seven key questions related to taxi operations:

1. **What are the peak hours for taxi pickups?**
2. **What is the average trip distance by passenger count?**
3. **What is the distribution of payment types?**
4. **What are the top 10 days with the highest number of trips?**
5. **What is the total fare amount collected by each payment type?**
6. **What are the average trip distances for each hour of the day?**
7. **What is the distribution of trips by passenger count?**

These queries were written using MongoDB's powerful aggregation framework to filter, group, and summarize the data.

- **Example Query: Peak Hours Analysis**
    ```python
    q1 = collection.aggregate([
        {"$project": {"hour": {"$hour": "$pickup_datetime"}}},
        {"$group": {"_id": "$hour", "num_trips": {"$count": {}}}},
        {"$sort": {"_id": 1}}
    ])
    ```

### 4. Data Visualization
The results of the MongoDB queries were converted back into Pandas DataFrames for further analysis and visualization.

- **Visualization Example:**
    ```python
    import matplotlib.pyplot as plt
    df1 = pd.DataFrame(q1)
    fig, ax = plt.subplots()
    (
      df1
      .rename(columns={"_id": "hour"})
	  .set_index("hour")
      .plot(
        kind="bar",
        ax=ax,
        figsize=(12, 4),
        rot=0,
        xlabel="Hour of the Day",
        ylabel="No. of Trips",
        title="Frequency of Trips per Hour of the Day",
        edgecolor="black"
      )
    )
    plt.show()
    ```

Using `matplotlib` and `pandas`, various plots and charts were created to visualize the results, such as bar charts for peak hours, fare distribution, and for distance vs. fare analysis.

### 5. Insights and Findings
The analysis provided several key insights into NYC's taxi operations:
- **The peak hours in terms of trips are from 3pm to 7pm**
- **The average trip distance travelled seems to be higher when there're more passengers in the rides**
- **The first 3 weeks of the month seem to be the busiest in terms of most trips**
- **The max. average distance travelled in a day is early in the morning, from 5am to 7am**
- **Majority of trips in the month of May seem to comprise of a single passenger**

## Technologies Used
- **MongoDB:** For data storage and aggregation queries.
- **Pymongo:** To interact with MongoDB using Python.
- **Pandas:** For data manipulation and conversion between formats.
- **Matplotlib:** For data visualization.
