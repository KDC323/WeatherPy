# Dependencies
import json
import requests as req
import random
import seaborn as sns
import pandas as pd
import math as math
import time
import numpy as np
import matplotlib.pyplot as plt
from citipy import citipy
Openweather api key
api_key = "478ee01ae8b8eae871901fe2bef506c7"
Create a pandas dataframe called location_data
Set two columns in the data frame to be random numbers for both Latitude and Longitude Values
location_data = pd.DataFrame()
location_data['lat_values'] = [np.random.uniform(-90,90) for x in range(1500)]
location_data['lng_values'] = [np.random.uniform(-180, 180) for x in range(1500)]

location_data.head()#check

Create two more columns in location_data for city and country. Start your first for loop employing the citipy library to pic the city nearest to the previous created random values for latitude and longitude.  Get both the city name and country code and put the values in the correct columns
location_data['city'] = ""
location_data['country'] = ""

count = 0
for index, row in location_data.iterrows():
    near_city = citipy.nearest_city(row['lat_values'], row['lng_values']).city_name
    near_country = citipy.nearest_city(row['lat_values'], row['lng_values']).country_code
    location_data.set_value(index,"city",near_city)
    location_data.set_value(index,"country",near_country)
location_data.head()#check    
Looking at sample size of location_data
len(location_data)
removing the duplicates of location_data based on city, and country values.  Could have used unique.
location_data=location_data.drop_duplicates(['city','country'],keep = "first")
#.unique
Created Temperature, Humidity, Cloudiness, Wind Speed, Longitude (actual), and Latitude (actual) for location_data
location_data['Temp'] = ""
location_data['Humidity'] = ""
location_data['Cloudiness'] = ""
location_data['Wind Speed'] = ""
location_data["Lat"] =""
location_data["Longitude"] =""
Set my sample size, created the target_url variable, and picked what types of units these values will display in


# Set the sample size.
sample_size = 500

target_url = 'http://api.openweathermap.org/data/2.5/weather?q='
units = 'imperial'
location_data.head()#check
Performed API calls
record = 0
for index, row in location_data.iterrows():
    city_name = row['city']
    country_code = row['country']
    url = target_url + city_name + ',' + country_code + '&units=' + units + '&APPID=' + api_key
    print (url)
    try: 
        weather_response = req.get(url)
        weather_json = weather_response.json()
        latitude = weather_json["coord"]["lat"]
        longitude = weather_json["coord"]["lon"]
        temp = weather_json["main"]["temp"]
        humidity = weather_json["main"]["humidity"]
        cloud = weather_json["clouds"]["all"]
        wind = weather_json["wind"]["speed"]
        location_data.set_value(index,"Temp", temp)
        location_data.set_value(index,"Humidity",humidity)
        location_data.set_value(index,"Wind Speed", wind)
        location_data.set_value(index,"Cloudiness",cloud)
        location_data.set_value(index,"Lat", latitude)
        location_data.set_value(index,"Longitude",longitude)
        print("Retrieved data for %s, %s" % (city_name, country_code))
    except:
        print("No data for %s, %s" % (city_name,country_code))
    record += 1
    if record % 59 == 0:
        time.sleep(60)
        
 location_data.head(20)#check# check the total sample size.
len(location_data)# Save the DataFrame as a csv
location_data.to_csv("weatherpy_data.csv", encoding="utf-8", index=False)
		

Used Pandas to change string values to numerics for plotting
location_data['Lat'] = pd.to_numeric(location_data['Lat'], errors = 'coerce')
location_data['Temp'] = pd.to_numeric(location_data['Temp'], errors = 'coerce')
location_data['Humidity'] = pd.to_numeric(location_data['Humidity'], errors = 'coerce')
location_data['Wind Speed'] = pd.to_numeric(location_data['Wind Speed'], errors = 'coerce')
location_data['Cloudiness'] = pd.to_numeric(location_data['Cloudiness'], errors = 'coerce')

#then drop na
location_data.head()#check
Plots

Temperature Vs. Latitude
x= location_data["Lat"]
y= location_data["Temp"]
plt.scatter (x,y, edgecolor = 'black')
plt.title('Temperature (F) vs. Latitude')
plt.ylabel("Temperature (F)")
plt.xlabel('Latitude')
plt.show()
Humidity (%) vs. Latitude
x= location_data["Lat"]
y= location_data["Humidity"]
plt.scatter (x,y,edgecolor = 'black')
plt.title('Humidity (%) vs. Latitude')
plt.ylabel("Humidity (%)")
plt.xlabel('Latitude')
plt.show()
Cloudiness (%) vs. Latitude
x= location_data["Lat"]
y= location_data["Cloudiness"]
plt.scatter (x,y,edgecolor = 'black')
plt.title('Cloudiness (%) vs. Latitude')
plt.ylabel("Cloudiness (%)")
plt.xlabel('Latitude')
plt.show()
Wind Speed (mph) vs. Latitude
x= location_data["Lat"]
y= location_data["Wind Speed"]
plt.scatter (x,y, edgecolor = 'black')
plt.title('Wind Speed (mph) vs. Latitude')
plt.ylabel("Wind Speed (mph)")
plt.xlabel('Latitude')
plt.show()