
# WeatherPy

What's the weather like as we approach the equator?

Using Python requests, APIs, and JSON traversals we generated 500+ random latitudes and longitudes with the Citypy Python library and the OpenWeatherMap API, with API keys to retrieve and graph scatter plots to showcase the following relationships:

Latitude vs. Temperature (F)
Latitude vs. Humidity (%)
Latitude vs. Cloudiness (%)
Latitude vs. Wind Speed (mph)
Latitude vs. Longitude (for fun and to see the distribution of the data)

## Analysis

Based off of a sample of 617 random cities in the world we attempted to find patterns using data vizualization. As expected temperatures apear to be higher around the equator as compared to further away. There also appears to be no relationship between latitude and humidity, cloudiness and wind speed. 



```python
#Dependencies
import pandas as pd
import requests as req
import matplotlib.pyplot as plt
from citipy import citipy
from config import api_key
import random
import json
import numpy as np
import seaborn as sns
```

## Create List from Random Latitudes and Longitudes


```python
#create lists
lat_long = []
citylist = []
country = []

# Create a set of random lat and lng combinations in large size to account for keyerrors later
lat = np.random.uniform(low=-90.000, high=90.000, size=1800)
lng = np.random.uniform(low=-180.000, high=180.000, size=1800)
lat_long = zip(lat, lng)

#loop through lat, lng to find nearest city with citipy
for lat,lng in lat_long:
    nearestcity = citipy.nearest_city(lat,lng)
    city = nearestcity.city_name
    citylist.append(city)
    country.append(nearestcity.country_code)

```

## Create DataFrame


```python
#Create DataFrame for city and country
df = pd.DataFrame({'City': citylist,
              'Country': country})
#clean data of duplicates

df = df.drop_duplicates(subset=['City'],keep='first')
print(len(df))
```

    711



```python
#Create Columns for needed data
df['Latitude'] = ''
df['Longitude'] = ''
df['Temperature'] = ''
df['Humidity'] = ''
df['Cloudiness'] = ''
df['Wind Speed'] = ''
```

## API Call for Cities


```python
#loop through cities, count and print the urls
count = 1
for index,row in df.iterrows():
    count +=1
    city_name = row['City']
    Country_id = row['Country']
    
    #api call for all Cities 
    api_url = "http://api.openweathermap.org/data/2.5/forecast" \
    "?q={},{}&units=IMPERIAL&mode=json&APPID={}".format(city_name,Country_id,api_key)
    
    #url = target_url + city_name + ',' + country_code + '&units=' + units +'&mode= + '&APPID=' + api_key   
    
    country_info = req.get(api_url).json()
    
    #print all api url's for each city
    print(api_url)
    
    
    
    try:
        df.set_value(index,'Latitude',country_info['city']['coord']['lat'])
        df.set_value(index,'Longitude',country_info['city']['coord']['lon'])
        df.set_value(index,'Temperature',country_info['list'][0]['main']['temp'])
        df.set_value(index,'Humidity',country_info['list'][0]['main']['humidity'])
        df.set_value(index,'Cloudiness',country_info['list'][0]['clouds']['all'])
        df.set_value(index,'Wind Speed',country_info['list'][0]['wind']['speed'])
        
    except KeyError:
        df.set_value(index,'Latitude',np.nan)
        df.set_value(index,'Longitude',np.nan)
        df.set_value(index,'Temperature',np.nan)
        df.set_value(index,'Humidity',np.nan)
        df.set_value(index,'Cloudiness',np.nan)
        df.set_value(index,'Wind Speed',np.nan)

        
        print('Information Missing')
    print("------------------------")
    print(f"City # {count} : {row['City']}")
    print(f"This is the requested URL: {api_url}")


```

    http://api.openweathermap.org/data/2.5/forecast?q=amderma,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 2 : amderma
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=amderma,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 3 : cape town
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=beringovskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 4 : beringovskiy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=beringovskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kahului,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 5 : kahului
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kahului,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 6 : bluff
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 7 : mataura
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=morros,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 8 : morros
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=morros,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 9 : hilo
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 10 : taolanaro
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=umm lajj,sa&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 11 : umm lajj
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=umm lajj,sa&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=matay,eg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 12 : matay
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=matay,eg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=san pedro,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 13 : san pedro
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=san pedro,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=komsomolskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 14 : komsomolskiy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=komsomolskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=victor harbor,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 15 : victor harbor
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=victor harbor,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=carnarvon,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 16 : carnarvon
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=carnarvon,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=isangel,vu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 17 : isangel
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=isangel,vu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bilma,ne&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 18 : bilma
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bilma,ne&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tsihombe,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 19 : tsihombe
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tsihombe,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=severo-kurilsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 20 : severo-kurilsk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=severo-kurilsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 21 : new norfolk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=port elizabeth,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 22 : port elizabeth
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=port elizabeth,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ancud,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 23 : ancud
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ancud,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ilulissat,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 24 : ilulissat
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ilulissat,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 25 : atuona
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 26 : jamestown
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 27 : chuy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 28 : punta arenas
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 29 : busselton
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bambous virieux,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 30 : bambous virieux
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bambous virieux,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 31 : vaini
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=naze,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 32 : naze
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=naze,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 33 : ushuaia
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bonthe,sl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 34 : bonthe
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bonthe,sl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 35 : east london
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=veraval,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 36 : veraval
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=veraval,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hambantota,lk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 37 : hambantota
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hambantota,lk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hamilton,bm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 38 : hamilton
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hamilton,bm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nizhneyansk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 39 : nizhneyansk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nizhneyansk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 40 : kodiak
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=klaksvik,fo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 41 : klaksvik
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=klaksvik,fo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=luderitz,na&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 42 : luderitz
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=luderitz,na&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=barentsburg,sj&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 43 : barentsburg
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=barentsburg,sj&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=pasni,pk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 44 : pasni
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=pasni,pk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=viedma,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 45 : viedma
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=viedma,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 46 : butaritari
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=attawapiskat,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 47 : attawapiskat
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=attawapiskat,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=the valley,ai&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 48 : the valley
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=the valley,ai&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 49 : albany
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=thompson,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 50 : thompson
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=thompson,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=russell,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 51 : russell
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=russell,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=atar,mr&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 52 : atar
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=atar,mr&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=el badari,eg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 53 : el badari
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=el badari,eg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nachalovo,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 54 : nachalovo
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nachalovo,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=xinzhou,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 55 : xinzhou
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=xinzhou,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saint-philippe,re&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 56 : saint-philippe
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saint-philippe,re&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=gat,ly&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 57 : gat
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=gat,ly&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=san ramon,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 58 : san ramon
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=san ramon,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=krasnouralsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 59 : krasnouralsk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=krasnouralsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=emba,kz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 60 : emba
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=emba,kz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=grand river south east,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 61 : grand river south east
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=grand river south east,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lavrentiya,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 62 : lavrentiya
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lavrentiya,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saint-pierre,pm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 63 : saint-pierre
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saint-pierre,pm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=fukue,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 64 : fukue
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=fukue,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=pitimbu,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 65 : pitimbu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=pitimbu,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tigzirt,dz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 66 : tigzirt
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tigzirt,dz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=samusu,ws&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 67 : samusu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=samusu,ws&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lebu,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 68 : lebu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lebu,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=alekseyevsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 69 : alekseyevsk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=alekseyevsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 70 : longyearbyen
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 71 : rikitea
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=waingapu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 72 : waingapu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=waingapu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 73 : port alfred
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saint george,bm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 74 : saint george
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saint george,bm&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mount pleasant,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 75 : mount pleasant
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mount pleasant,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 76 : khatanga
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=calatayud,es&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 77 : calatayud
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=calatayud,es&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sahuaripa,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 78 : sahuaripa
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sahuaripa,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=faanui,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 79 : faanui
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=faanui,pf&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=caborca,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 80 : caborca
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=caborca,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ust-nera,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 81 : ust-nera
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ust-nera,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kavieng,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 82 : kavieng
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kavieng,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=trairi,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 83 : trairi
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=trairi,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mulanay,ph&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 84 : mulanay
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mulanay,ph&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=gayny,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 85 : gayny
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=gayny,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=quatre cocos,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 86 : quatre cocos
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=quatre cocos,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=broken hill,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 87 : broken hill
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=broken hill,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 88 : hermanus
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tuatapere,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 89 : tuatapere
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tuatapere,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=rochester,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 90 : rochester
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=rochester,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=phan thiet,vn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 91 : phan thiet
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=phan thiet,vn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=angoche,mz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 92 : angoche
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=angoche,mz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=finschhafen,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 93 : finschhafen
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=finschhafen,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 94 : bredasdorp
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saskylakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 95 : saskylakh
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saskylakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bam,ir&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 96 : bam
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bam,ir&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=deputatskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 97 : deputatskiy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=deputatskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=eyl,so&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 98 : eyl
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=eyl,so&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=barrow,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 99 : barrow
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=barrow,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=pingxiang,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 100 : pingxiang
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=pingxiang,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saurimo,ao&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 101 : saurimo
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saurimo,ao&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=eureka,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 102 : eureka
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=eureka,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=dikson,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 103 : dikson
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=dikson,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kalmunai,lk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 104 : kalmunai
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kalmunai,lk&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=puerto parra,co&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 105 : puerto parra
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=puerto parra,co&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 106 : qaanaaq
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mayumba,ga&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 107 : mayumba
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mayumba,ga&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=novomykolayivka,ua&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 108 : novomykolayivka
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=novomykolayivka,ua&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 109 : illoqqortoormiut
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 110 : hobart
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=souillac,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 111 : souillac
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=souillac,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=shingu,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 112 : shingu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=shingu,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=vao,nc&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 113 : vao
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=vao,nc&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=khandagayty,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 114 : khandagayty
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=khandagayty,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=vestmannaeyjar,is&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 115 : vestmannaeyjar
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=vestmannaeyjar,is&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tulungagung,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 116 : tulungagung
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tulungagung,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=belushya guba,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 117 : belushya guba
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=belushya guba,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=airai,pw&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 118 : airai
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=airai,pw&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 119 : mahebourg
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bojnurd,ir&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 120 : bojnurd
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bojnurd,ir&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 121 : yellowknife
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lima,pe&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 122 : lima
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lima,pe&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bairiki,ki&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 123 : bairiki
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bairiki,ki&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nhulunbuy,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 124 : nhulunbuy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nhulunbuy,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=talnakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 125 : talnakh
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=talnakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=chokurdakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 126 : chokurdakh
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=chokurdakh,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=karauzyak,uz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 127 : karauzyak
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=karauzyak,uz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=fairbanks,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 128 : fairbanks
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=fairbanks,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=beloha,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 129 : beloha
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=beloha,mg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hirado,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 130 : hirado
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hirado,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=christchurch,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 131 : christchurch
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=christchurch,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=moussoro,td&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 132 : moussoro
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=moussoro,td&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=linxia,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 133 : linxia
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=linxia,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bilibino,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 134 : bilibino
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bilibino,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=merauke,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 135 : merauke
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=merauke,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hutchinson,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 136 : hutchinson
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hutchinson,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bengkulu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 137 : bengkulu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bengkulu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ust-kulom,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 138 : ust-kulom
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ust-kulom,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 139 : upernavik
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=omsukchan,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 140 : omsukchan
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=omsukchan,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ukiah,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 141 : ukiah
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ukiah,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=hasaki,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 142 : hasaki
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=hasaki,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ponta do sol,cv&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 143 : ponta do sol
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ponta do sol,cv&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=namatanai,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 144 : namatanai
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=namatanai,pg&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 145 : provideniya
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bethel,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 146 : bethel
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bethel,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=san quintin,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 147 : san quintin
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=san quintin,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ketchikan,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 148 : ketchikan
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ketchikan,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=barpeta,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 149 : barpeta
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=barpeta,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=taoudenni,ml&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 150 : taoudenni
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=taoudenni,ml&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nagyatad,hu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 151 : nagyatad
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nagyatad,hu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 152 : kapaa
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=anloga,gh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 153 : anloga
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=anloga,gh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 154 : puerto ayora
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lompoc,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 155 : lompoc
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lompoc,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bubaque,gw&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 156 : bubaque
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bubaque,gw&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kango,ga&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 157 : kango
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kango,ga&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kidal,ml&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 158 : kidal
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kidal,ml&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=cochabamba,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 159 : cochabamba
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=cochabamba,bo&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ostrovnoy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 160 : ostrovnoy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ostrovnoy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=dingle,ie&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 161 : dingle
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=dingle,ie&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lobva,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 162 : lobva
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lobva,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=atikokan,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 163 : atikokan
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=atikokan,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kruisfontein,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 164 : kruisfontein
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kruisfontein,za&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=yatou,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 165 : yatou
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=yatou,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kutum,sd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 166 : kutum
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kutum,sd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kankon,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 167 : kankon
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kankon,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=coahuayana,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 168 : coahuayana
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=coahuayana,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=cherskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 169 : cherskiy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=cherskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=oriximina,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 170 : oriximina
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=oriximina,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=penzance,gb&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 171 : penzance
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=penzance,gb&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ribeira grande,pt&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 172 : ribeira grande
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ribeira grande,pt&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tandil,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 173 : tandil
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tandil,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=mentok,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 174 : mentok
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=mentok,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 175 : tiksi
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=san patricio,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 176 : san patricio
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=san patricio,mx&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=neuquen,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 177 : neuquen
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=neuquen,ar&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=port lincoln,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 178 : port lincoln
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=port lincoln,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=kavaratti,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 179 : kavaratti
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=kavaratti,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sao filipe,cv&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 180 : sao filipe
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sao filipe,cv&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sentyabrskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 181 : sentyabrskiy
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sentyabrskiy,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=ashland,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 182 : ashland
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=ashland,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=lodeynoye pole,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 183 : lodeynoye pole
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=lodeynoye pole,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=cap malheureux,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 184 : cap malheureux
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=cap malheureux,mu&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=coihaique,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 185 : coihaique
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=coihaique,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=touros,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 186 : touros
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=touros,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=corn island,ni&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 187 : corn island
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=corn island,ni&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=baykit,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 188 : baykit
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=baykit,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nome,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 189 : nome
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nome,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=grande prairie,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 190 : grande prairie
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=grande prairie,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=shimanovsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 191 : shimanovsk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=shimanovsk,ru&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 192 : avarua
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=vardo,no&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 193 : vardo
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=vardo,no&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=champerico,gt&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 194 : champerico
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=champerico,gt&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=narsaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 195 : narsaq
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=narsaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=nanortalik,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 196 : nanortalik
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=nanortalik,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=castro,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 197 : castro
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=castro,cl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sibolga,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 198 : sibolga
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sibolga,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=saint-francois,gp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 199 : saint-francois
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=saint-francois,gp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bose,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 200 : bose
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bose,cn&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sinnar,sd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 201 : sinnar
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sinnar,sd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=apac,ug&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 202 : apac
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=apac,ug&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=sioux lookout,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 203 : sioux lookout
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=sioux lookout,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=rincon,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 204 : rincon
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=rincon,us&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 205 : tuktoyaktuk
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=westport,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 206 : westport
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=westport,nz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=rantauprapat,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 207 : rantauprapat
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=rantauprapat,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=bondo,cd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 208 : bondo
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=bondo,cd&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=geraldton,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 209 : geraldton
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=geraldton,au&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=tasiilaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 210 : tasiilaq
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=tasiilaq,gl&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=zhanaozen,kz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 211 : zhanaozen
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=zhanaozen,kz&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=abashiri,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 212 : abashiri
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=abashiri,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=georgetown,sh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 213 : georgetown
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=georgetown,sh&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=itoman,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 214 : itoman
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=itoman,jp&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=netarhat,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 215 : netarhat
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=netarhat,in&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=itaituba,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 216 : itaituba
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=itaituba,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=palabuhanratu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    Information Missing
    ------------------------
    City # 217 : palabuhanratu
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=palabuhanratu,id&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=havre-saint-pierre,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 218 : havre-saint-pierre
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=havre-saint-pierre,ca&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=coxim,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 219 : coxim
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=coxim,br&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    http://api.openweathermap.org/data/2.5/forecast?q=goure,ne&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396
    ------------------------
    City # 220 : goure
    This is the requested URL: http://api.openweathermap.org/data/2.5/forecast?q=goure,ne&units=IMPERIAL&mode=json&APPID=c9fbd8f5c22c13ad30b5959dff8cc396



```python
 df.head()
```


```python
#omit NaN
new_df = df.dropna()
len(new_df)
```

## Distribution of Cities


```python
import cartopy.crs as ccrs

fig, ax = plt.subplots(figsize = (20,10))
ax = plt.axes(projection = ccrs.PlateCarree())
ax.stock_img()
plt.scatter(list(new_df['Longitude']),
           list(new_df['Latitude']),
           color= 'blue', marker='o', 
           transform= ccrs.Geodetic(),
           )
plt.savefig("Distribution.png")
plt.show()
```


```python
#Export City Data to CSV File
new_df.to_csv("City_Data.csv")
```

## Latitude vs. Temperature


```python
#Build a scatter plot for Latitude vs. Temperature (date unsure if this is what is wanted)

import time
date = time.strftime("%m/%d/%Y")

plt.scatter(new_df['Latitude'],new_df['Temperature'], edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8)
plt.title(f"Temperature vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.style.use('seaborn')
plt.ylim(-90, 150)
plt.xlim(-100, 100)
plt.savefig("Temperature.png")
plt.show()
```

## Latitude vs. Humidity 


```python
#Build a scatter plot for Latitude vs. Humidity (%)
import time
date = time.strftime("%m/%d/%Y")

plt.scatter(new_df['Latitude'],new_df['Humidity'], edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8)
plt.title(f"Latitude vs. Humidity {date}")
plt.xlabel("Latitude")
plt.ylabel("Humidity")
plt.style.use('seaborn')
plt.ylim(-10, 120)
plt.xlim(-75, 100)
plt.savefig("Humidity.png")
plt.show()
```

## Latitude vs. Cloudiness


```python
#Build a scatter plot for Latitude vs. Cloudiness (%)
import time
date = time.strftime("%m/%d/%Y")

plt.scatter(new_df['Latitude'],new_df['Cloudiness'], edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8)
plt.title(f"Latitude vs. Cloudiness {date}")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness")
plt.style.use('seaborn')
plt.ylim(-10, 120)
plt.xlim(-75, 100)
plt.savefig("Cloudiness.png")
plt.show()
```

## Latitude vs. Wind Speed


```python
#Build a scatter plot for Latitude vs. Wind Speed (mph)
import time
date = time.strftime("%m/%d/%Y")

plt.scatter(new_df['Latitude'],new_df['Wind Speed'], edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8)
plt.title(f"Latitude vs. Wind Speed {date}")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed")
plt.style.use('seaborn')
plt.ylim(-10, 50)
plt.xlim(-75, 100)
plt.savefig("WindSpeed.png")
plt.show()
```
