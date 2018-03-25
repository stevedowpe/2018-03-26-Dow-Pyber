

```python
# INSTRUCTIONS
#Your objective is to build a [Bubble Plot](https://en.wikipedia.org/wiki/Bubble_chart) 
#that showcases the relationship between four key variables:
#* Average Fare ($) Per City
#* Total Number of Rides Per City
#* Total Number of Drivers Per City
#* City Type (Urban, Suburban, Rural)

#In addition, you will be expected to produce the following three pie charts:
#* % of Total Fares by City Type
#* % of Total Rides by City Type
#* % of Total Drivers by City Type

# THREE OBSERVATIONS: 
# (1) As would be expected, there are significantly more drivers and rides in the urban areas.
# (2) The rural area cities get 5.3% of the total rides, but 6.7% of the total fare. 
#     This is the most favorable ride-to-fare ratio for would-be drivers. 
# (3) The average fare increases with the decrease of # of rides. 
```


```python
#Dependencies
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```


```python
# load CSV
city_data = "raw_data/city_data.csv"
ride_data = "raw_data/ride_data.csv"
```


```python
# Read with pandas- city
city_df = pd.read_csv(city_data)
city_df.head()

# Clean up assistance with Luis... to address repeat of some city info... 
city_df_clean = pd.DataFrame({'type': city_df.groupby('city').first()['type'],
                                'driver_count': city_df.groupby('city').sum()['driver_count']}).reset_index()
city_df_clean.head(2)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>21</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>67</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Read with pandas- ride
ride_df = pd.read_csv(ride_data)
ride_df.head(2)

#ride_df["city"].value_Counts()

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
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
    </tr>
  </tbody>
</table>
</div>




```python
#* Average Fare ($) Per City
ave_fare_per_city = ride_df.groupby("city").mean()["fare"]
ave_fare_per_city.head(2)
```




    city
    Alvarezhaven    23.928710
    Alyssaberg      20.609615
    Name: fare, dtype: float64




```python
#* Total Number of Rides Per City
rides_per_city = ride_df.groupby("city").count()["ride_id"]
rides_per_city.head(2)
```




    city
    Alvarezhaven    31
    Alyssaberg      26
    Name: ride_id, dtype: int64




```python
#* Total Number of Drivers Per City
# Notes to self.... Plan -- create a pd.DataFrame for Ave fare per city and rides per city and then merge those into the city DF 
#because that is already grouped by City... may need to reset index -- this should leave us with a single dataframe to plot -- 
#(Luis indicated that it would be OK to merge the files initially and then just clean up the repetitive lists, but using .first 
# and .sum, and then reset the index just before the plot)  

fare_and_rides = pd.DataFrame({"Ave_Fare":ave_fare_per_city, "# of Rides": rides_per_city})
fare_and_rides = fare_and_rides.reset_index()
fare_and_rides.head(2)

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
      <th>city</th>
      <th># of Rides</th>
      <th>Ave_Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>31</td>
      <td>23.928710</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>26</td>
      <td>20.609615</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge new data to city date
merged_df = pd.merge(city_df_clean,fare_and_rides,on="city")
merged_df.head(2)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th># of Rides</th>
      <th>Ave_Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>21</td>
      <td>Urban</td>
      <td>31</td>
      <td>23.928710</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>67</td>
      <td>Urban</td>
      <td>26</td>
      <td>20.609615</td>
    </tr>
  </tbody>
</table>
</div>




```python
urban = merged_df.loc[(merged_df["type"]== "Urban")]
urban.head(2)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th># of Rides</th>
      <th>Ave_Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>21</td>
      <td>Urban</td>
      <td>31</td>
      <td>23.928710</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>67</td>
      <td>Urban</td>
      <td>26</td>
      <td>20.609615</td>
    </tr>
  </tbody>
</table>
</div>




```python
suburban = merged_df.loc[(merged_df["type"]== "Suburban")]
suburban.head(2)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th># of Rides</th>
      <th>Ave_Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>Anitamouth</td>
      <td>16</td>
      <td>Suburban</td>
      <td>9</td>
      <td>37.315556</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Campbellport</td>
      <td>26</td>
      <td>Suburban</td>
      <td>15</td>
      <td>33.711333</td>
    </tr>
  </tbody>
</table>
</div>




```python
rural = merged_df.loc[(merged_df["type"]== "Rural")]
# Did the following line to check to make sure the sizing works
#rural.loc[(rural["# of Rides"]==4)]
rural.head(2)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th># of Rides</th>
      <th>Ave_Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17</th>
      <td>East Leslie</td>
      <td>9</td>
      <td>Rural</td>
      <td>11</td>
      <td>33.660909</td>
    </tr>
    <tr>
      <th>18</th>
      <td>East Stephen</td>
      <td>6</td>
      <td>Rural</td>
      <td>10</td>
      <td>39.053000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Note to self -- Using the matplotlib way did not allow me to overlay the graphs in single location
#urban.plot(kind="scatter", x="# of Rides", y="driver_count", color="r")
#suburban.plot(kind="scatter", x="# of Rides", y="driver_count", color="b")
#rural.plot(kind="scatter", x="# of Rides", y="driver_count", color="g")
#plt.show()

# create indiv scatter plots for each city type
plt.scatter(urban["# of Rides"],urban["driver_count"], color="r", s = urban["Ave_Fare"])
plt.scatter(suburban["# of Rides"],suburban["driver_count"], color="b", s = suburban["Ave_Fare"])
plt.scatter(rural["# of Rides"],rural["driver_count"], color="g", s = rural["Ave_Fare"])

# Note to self -- Problem with creation of  / implementation of handle for each plot... for line graph only?
#urban, = plt.plot(urban["# of Rides"],urban["driver_count"], color="r")
#suburban, = plt.plot(suburban["# of Rides"],suburban["driver_count"], color="b")
#rural, = plt.plot(rural["# of Rides"],rural["driver_count"], color="g")


# create chart labels and legend
plt.title("# of Rides vs Drivers AND Over City Type and Average Rate")
plt.xlabel("# of Rides")
plt.ylabel("# of Drivers")
#plt.legend(urban.color = "red", suburban.color = blue, rural.color = green, loc="best")
plt.savefig("Dow-Rides-vs-Drivers-Fare-City-Type.png")
plt.show()
```


![png](output_12_0.png)



```python
# Version two of the graph - now with average rate on y-axis:

# create indiv scatter plots for each city type
plt.scatter(urban["# of Rides"],urban["Ave_Fare"], color="r", s = urban["driver_count"])
plt.scatter(suburban["# of Rides"],suburban["Ave_Fare"], color="b", s = suburban["driver_count"])
plt.scatter(rural["# of Rides"],rural["Ave_Fare"], color="g", s = rural["driver_count"])


# create chart labels and legend
plt.title("# of Rides vs Average Fare AND Considering City Type and # of Drivers")
plt.xlabel("# of Rides")
plt.ylabel("Average Fare in $")
plt.savefig("Dow-Rides-vs-Fare-Drivers-City-Type.png")
plt.show()
```


![png](output_13_0.png)



```python
#* % of Total Fares by City Type
sum_fare_urban = round(urban["Ave_Fare"] * urban["# of Rides"]).sum()
sum_fare_urban

```




    40077.0




```python
sum_fare_suburban = round(suburban["Ave_Fare"] * suburban["# of Rides"]).sum()
sum_fare_suburban
```




    19321.0




```python
sum_fare_rural = round(rural["Ave_Fare"] * rural["# of Rides"]).sum()
sum_fare_rural
```




    4255.0




```python
total_fare = ride_df.sum()["fare"]
total_fare
```




    63651.30999999986




```python
# Not needed
#percent_fare_urban = int((sum_fare_urban / total_fare)*100)
#percent_fare_urban
#percent_fare_suburban = int((sum_fare_suburban / total_fare)*100)
#percent_fare_suburban
#percent_fare_rural = int((sum_fare_rural / total_fare)*100)
#percent_fare_rural
```


```python
#fares_pie_data = [percent_fare_urban,percent_fare_suburban,percent_fare_rural]

fares_pie_data = [sum_fare_urban,sum_fare_suburban,sum_fare_rural]
fares_pie_labels = ["Urban", "Suburban", "Rural"]
colors = ["lightcoral", "lightskyblue", "yellowgreen"]

plt.title("% of Total Fares by City Type")
plt.pie(fares_pie_data,labels=fares_pie_labels,colors=colors,autopct="%1.1f%%",shadow=True,startangle=140)
plt.axis("equal")
plt.savefig("Dow-Percent-Total-Fares-by-City-Type.png")
```


![png](output_19_0.png)



```python
#* % of Total Rides by City Type
sum_rides_urban=urban["# of Rides"].sum()
sum_rides_suburban=suburban["# of Rides"].sum()                        
sum_rides_rural=rural["# of Rides"].sum()  
```


```python
rides_pie_data = [sum_rides_urban,sum_rides_suburban,sum_rides_rural]
rides_pie_labels = ["Urban", "Suburban", "Rural"]
colors = ["lightcoral", "lightskyblue", "yellowgreen"]

plt.title("# of Rides by City Type")
plt.pie(rides_pie_data,labels=rides_pie_labels,colors=colors,autopct="%1.1f%%",shadow=True,startangle=140)
plt.axis("equal")
plt.savefig("Dow-Percent-Total-Rides-by-City-Type.png")
```


![png](output_21_0.png)



```python
#* % of Total Drivers by City Type
sum_drivers_urban=urban["driver_count"].sum()
sum_drivers_suburban=suburban["driver_count"].sum()                        
sum_drivers_rural=rural["driver_count"].sum()  
```


```python
drivers_pie_data = [sum_drivers_urban,sum_drivers_suburban,sum_drivers_rural]
drivers_pie_labels = ["Urban", "Suburban", "Rural"]
colors = ["lightcoral", "lightskyblue", "yellowgreen"]

plt.title("# of Drivers by City Type")
plt.pie(drivers_pie_data,labels=drivers_pie_labels,colors=colors,autopct="%1.1f%%",shadow=True,startangle=140)
plt.axis("equal")
plt.savefig("Dow-Percent-Total-Drivers-by-City-Type.png")
```


![png](output_23_0.png)

