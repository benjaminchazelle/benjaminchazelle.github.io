# Tell me Lyon
A DTU 02806 Final Project

## Team
Benjamin Chazelle
Emilie Trouillard
Emma Demarecaux

## 0. Introduction

Link to the vizualisation : https://benjaminchazelle.github.io/

Link to the GitHub repository: https://github.com/benjaminchazelle/benjaminchazelle.github.io

This notebook is the result of the final project assignement in the course 02806 : *Social Data Analysis and Visualization*.  We have built a narrative visualization of the city of Lyon, in France.

## 1.    Motivation

We are a team of three French students, and therefore, we have chosen to present you the city of Lyon, in France, which is often, in fact pretty much always, overlooked by the French most popular attraction, Paris.
Just for starters, it's rich in Roman history but there's actually a whole lot more going for this city than you might have thought. Lyon has around 510,000 people in the city, making it the third most populated city in France. It is big enough to keep things interesting, but small enough so that it’s easy to stay in touch with new found friends.

The Eiffel Tower? The Louvre? The River Seine? Sure, Paris is a beautiful city, but it's a cliché. Our objective is to leave you with some stunning examples of what's on offer in Lyon and to help you organize your trip to come and visit Lyon. You will be guided for your choice of accommodation and transportation depending on what you want to see.

Our initial dataset can be downloaded here: https://data.grandlyon.com/culture/point-dintfrft-touristique-mftropole-de-lyon/. It is made of many different data that can interest tourists in Lyon: accommodations, cultural places or monuments, shops, restaurants etc. We chose this one because it is rich and the most important thing is that it has geographic data, so that we can create a map. It will be explained in the following sections but this dataset is not the only one we used, even though it is the foundation of our project.

Here is the video of the first part of our project which explains the central idea that we have investigated in our final project: https://www.youtube.com/watch?v=MhXFzofHI5M

## 2.    Basic stats



The data set has 5127 rows and a lot of interesting features that can be put to good use.

Some of the most interesting features are the data coordinates, which enable us to locate each place to a point on the map, the data types which help us categorizing them into restaurants, private accommodations, hotels, hostels, parks, cathedrals, etc. Here is an overview of the different *French* features that we have:


```python
import pandas as pd
import json
from pandas.io.json import json_normalize
import numpy as np
```


```python
data_tourism = json.load(open('tourismdata.json',encoding='utf8'))
data_tourism = pd.DataFrame(json_normalize(data_tourism['features']))
data_tourism.columns
```




    Index(['geometry.coordinates', 'geometry.type', 'properties.adresse',
           'properties.classement', 'properties.codepostal', 'properties.commune',
           'properties.date_creation', 'properties.email', 'properties.facebook',
           'properties.fax', 'properties.gid', 'properties.id',
           'properties.id_sitra1', 'properties.last_update',
           'properties.last_update_fme', 'properties.nom', 'properties.ouverture',
           'properties.producteur', 'properties.siteweb',
           'properties.tarifsenclair', 'properties.tarifsmax',
           'properties.tarifsmin', 'properties.telephone',
           'properties.telephonefax', 'properties.type', 'properties.type_detail',
           'type'],
          dtype='object')



and some observation examples of the most interesting attributes:


```python
data_tourism[['geometry.coordinates','properties.adresse','properties.classement','properties.id','properties.nom','properties.siteweb', 'properties.telephone', 'properties.type', 'properties.type_detail']].head()

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
      <th>geometry.coordinates</th>
      <th>properties.adresse</th>
      <th>properties.classement</th>
      <th>properties.id</th>
      <th>properties.nom</th>
      <th>properties.siteweb</th>
      <th>properties.telephone</th>
      <th>properties.type</th>
      <th>properties.type_detail</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[4.898995, 45.703226]</td>
      <td></td>
      <td>3 étoiles</td>
      <td>207259</td>
      <td>Théâtre des Célestins</td>
      <td></td>
      <td>0478298247</td>
      <td>HEBERGEMENT_LOCATIF</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>[4.834113, 45.765248]</td>
      <td>68 rue Pierre Corneille</td>
      <td></td>
      <td>4755951</td>
      <td>Cinéma Lumière Fourmi</td>
      <td>http://www.cinemas-lumiere.com</td>
      <td>0478053840</td>
      <td>EQUIPEMENT</td>
      <td>Divertissement;Cinéma</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[4.834435, 45.763234]</td>
      <td>1 place Francisque Régaud</td>
      <td></td>
      <td>205813</td>
      <td>Le Grand Café des Négociants</td>
      <td>http://www.lesnegociants.com</td>
      <td>0478425005</td>
      <td>RESTAURATION</td>
      <td>Brasserie</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[4.869025, 45.74568]</td>
      <td>Musée Lumière, 25 rue du 1er Film</td>
      <td></td>
      <td>123723</td>
      <td>Bibliothèque Raymond Chirat</td>
      <td>http://www.institut-lumiere.org</td>
      <td>0478781895</td>
      <td>EQUIPEMENT</td>
      <td>Loisirs culturels;Bibliothèque - Médiathèque</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[4.823302, 45.758917]</td>
      <td>8 rue Professeur Pierre Marion</td>
      <td>5 étoiles</td>
      <td>4681580</td>
      <td>Villa Maïa</td>
      <td>http://www.villa-maia.com</td>
      <td>0478160101</td>
      <td>HOTELLERIE</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



We made a selection among all the rows of the dataset and we only kept the cultural or natural places, as well as the accommodations. We then split them in two separate datasets: one for touristic places and one for the hotels. But the processing methods were quite similar for both of them.

### About accommodations in Lyon

Below is presented the preprocessing steps for the hotel data.



```python
import pandas as pd
import json
from pandas.io.json import json_normalize
import numpy as np

data = json.load(open('tourismdata.json',encoding='utf-8'))

data = pd.DataFrame(json_normalize(data['features']))

## selection of the accommodation types
acc = data[data['properties.type']\
                  .isin(['HEBERGEMENT_COLLECTIF','HEBERGEMENT_LOCATIF','HOTELLERIE'])]

def number_stars(hotel):
    row = hotel['properties.classement']
    for i in range(5):
        if str(i+1) in row:
            return(i+1)
    return(0)

def telephone(hotel):
    numbers = hotel['properties.telephone']
    k = numbers.find(';')
    if k == -1:
        return numbers
    else:
        return numbers[:k]

def category(hotel):
    cat = hotel['properties.type']
    if cat == 'HEBERGEMENT_COLLECTIF':
        return 'Hostel'
    elif cat == 'HEBERGEMENT_LOCATIF':
        return 'Private accomodation'
    elif cat == 'HOTELLERIE':
        return 'Hotel'
    else:
        return ''

acc['properties.star'] = acc.apply(number_stars, axis=1)
acc['properties.clean_telephone'] = acc.apply(telephone, axis=1)
acc['properties.eng_type'] = acc.apply(category, axis=1)
acc2 = acc[['geometry.coordinates', 'properties.nom', 'properties.star', 'properties.clean_telephone',
           'properties.eng_type']]

H = acc.to_dict(orient='index')
H1 = list(H.values())

#with open('hotels_with_photo.json', 'w') as outfile:  
#            json.dump(H1, outfile)
acc2.head()
```

    C:\Users\user\Anaconda2\envs\py35\lib\site-packages\ipykernel_launcher.py:39: SettingWithCopyWarning:
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead

    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    C:\Users\user\Anaconda2\envs\py35\lib\site-packages\ipykernel_launcher.py:40: SettingWithCopyWarning:
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead

    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    C:\Users\user\Anaconda2\envs\py35\lib\site-packages\ipykernel_launcher.py:41: SettingWithCopyWarning:
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead

    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy





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
      <th>geometry.coordinates</th>
      <th>properties.nom</th>
      <th>properties.star</th>
      <th>properties.clean_telephone</th>
      <th>properties.eng_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[4.898995, 45.703226]</td>
      <td>Théâtre des Célestins</td>
      <td>3</td>
      <td>0478298247</td>
      <td>Private accomodation</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[4.823302, 45.758917]</td>
      <td>Villa Maïa</td>
      <td>5</td>
      <td>0478160101</td>
      <td>Hotel</td>
    </tr>
    <tr>
      <th>8</th>
      <td>[4.861741, 45.760433]</td>
      <td>Ibis Budget Lyon Part-Dieu</td>
      <td>2</td>
      <td>0472682530</td>
      <td>Hotel</td>
    </tr>
    <tr>
      <th>12</th>
      <td>[4.863167, 45.770576]</td>
      <td>Loc Dalhias</td>
      <td>0</td>
      <td>0478041835</td>
      <td>Private accomodation</td>
    </tr>
    <tr>
      <th>16</th>
      <td>[4.828288, 45.76439]</td>
      <td>Duplex du change</td>
      <td>3</td>
      <td>0664287134</td>
      <td>Private accomodation</td>
    </tr>
  </tbody>
</table>
</div>



The next steps were to find some pictures and the rating of each accommodation. For this we used the Google Maps API Places and sent requests with the name and position of all the places.

### About hotspots

The preprocessing of the hotspot dataset is very similar to the one that has just been presented.

The only difference is that we wanted to have a criterion to rank the places according to their popularity.
In order to get this, we used an open-source program called *populartimes* to extract the number of reviews each place got on Google Maps. We used this number as a metric for the popularity of the places.



```python
data_hotspot = json.load(open('hotspots_final.json',encoding='utf8'))
data_hotspot = pd.DataFrame(data_hotspot)
data_hotspot.head()
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
      <th>geometry.coordinates_x</th>
      <th>properties.free</th>
      <th>properties.nb5</th>
      <th>properties.nb_reviews</th>
      <th>properties.nom_x</th>
      <th>properties.photo</th>
      <th>properties.photos</th>
      <th>properties.place_id</th>
      <th>properties.rating</th>
      <th>properties.reference</th>
      <th>properties.reviews</th>
      <th>properties.siteweb_x</th>
      <th>properties.variety</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[4.823473, 45.76263]</td>
      <td></td>
      <td>3</td>
      <td>14</td>
      <td>Jardin du Rosaire dans le Parc des Hauteurs</td>
      <td>CmRaAAAALQmB81dL1HiW9_8-q1A8FB0YKLsTz3ICtk_d3f...</td>
      <td>[CmRaAAAALQmB81dL1HiW9_8-q1A8FB0YKLsTz3ICtk_d3...</td>
      <td>ChIJw-GySKnr9EcRfSIko-dbnPM</td>
      <td>4.4</td>
      <td>CmRSAAAA3hcqK5sFexx-VhvjKUuBPjSaPLGZ4zcIDM8Hv3...</td>
      <td>[5, 5, 5, 4, 4]</td>
      <td></td>
      <td>Park</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[4.827601, 45.760562]</td>
      <td></td>
      <td>1</td>
      <td>131</td>
      <td>L'Horloge Astronomique</td>
      <td>CmRaAAAAUBwFJxO7harYmRH33b04qMETWV4E_Mf7hSWUzO...</td>
      <td>[CmRaAAAAUBwFJxO7harYmRH33b04qMETWV4E_Mf7hSWUz...</td>
      <td>ChIJIZwn_Ivq9EcRVTOVoORU99s</td>
      <td>3.5</td>
      <td>CmRSAAAAfbOvXdBmK7-eQB7YXXb-OP26pSiYWYxpMRVc-k...</td>
      <td>[4, 4, 5, 3, 1]</td>
      <td>http://cathedrale-lyon.cef.fr</td>
      <td>Other</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[4.822097, 45.734224]</td>
      <td></td>
      <td>2</td>
      <td>126</td>
      <td>Parc des Berges du Rhône</td>
      <td>CmRaAAAAYLKkKsQkD8gdjG5yO3oEMNytOZL2GmkPr99UH1...</td>
      <td>[CmRaAAAAYLKkKsQkD8gdjG5yO3oEMNytOZL2GmkPr99UH...</td>
      <td>ChIJ27d-kdLr9EcR84gALAV1zEY</td>
      <td>4.1</td>
      <td>CmRRAAAAptyaN4KWvEoQ6nRn9id0c2J3FT7QujLDYQYyNv...</td>
      <td>[4, 5, 5, 4, 4]</td>
      <td>http://www.mairie7.lyon.fr/vdl/sections/fr/arr...</td>
      <td>Park</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[5.005882, 45.76637]</td>
      <td></td>
      <td>1</td>
      <td>29</td>
      <td>Parc République</td>
      <td>CmRaAAAAQ0Klu1Dndgeinu75F3J9_QDVuouVy59cMHmfUs...</td>
      <td>[CmRaAAAAQ0Klu1Dndgeinu75F3J9_QDVuouVy59cMHmfU...</td>
      <td>ChIJX1i45KXH9EcRO3zbCVeqm2g</td>
      <td>4.1</td>
      <td>CmRRAAAAu9j9ydCOqT2P_f2IphL54qrEQG2MA3-F0qoYOD...</td>
      <td>[4, 4, 4, 4, 5]</td>
      <td>http://www.meyzieu.fr</td>
      <td>Park</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[4.720173, 45.805924]</td>
      <td></td>
      <td>4</td>
      <td>88</td>
      <td>Parc de l'Hippodrome</td>
      <td>CmRaAAAAtVymwWx5wLHJtz0tMj63xhaVuzcdnP1aEE7Dap...</td>
      <td>[CmRaAAAAtVymwWx5wLHJtz0tMj63xhaVuzcdnP1aEE7Da...</td>
      <td>ChIJhRiiE0zt9EcRN-XljxEK2JY</td>
      <td>4.2</td>
      <td>CmRSAAAA7gRH-Kc6YwGTH6WbDCK3NOZi1zZuz1pDSfqrk2...</td>
      <td>[5, 5, 5, 3, 5]</td>
      <td>http://www.salvagny.org</td>
      <td>Park</td>
    </tr>
  </tbody>
</table>
</div>



### About metros in Lyon

We downloaded another dataset for this part, which came from the same website as before. It can be found here:https://data.grandlyon.com/equipements/entrfefsortie-des-stations-de-mftro-du-rfseau-tcl/. It has 160 rows, each one of them corresponding to a metro entrance in the city - hence there are several rows per station. The data is available in a geojson format, so it was quite straightforward to integrate it in the project structure.


```python
data_metro0 = json.load(open('metrodata.json',encoding='utf8'))
data_metro0 = pd.DataFrame(json_normalize(data_metro0['features']))
data_metro0.columns
```




    Index(['geometry.coordinates', 'geometry.type', 'properties.desserte',
           'properties.gid', 'properties.id', 'properties.id_station',
           'properties.last_update', 'properties.last_update_fme',
           'properties.nom', 'type'],
          dtype='object')



The detail about the lines of metro passing at each station was not part of the initial dataset and was added manually, and the irrelevant columns were taken out of the dataset.


```python
data_metro = json.load(open('metro.json',encoding='utf8'))
data_metro = pd.DataFrame(data_metro)
data_metro.head()
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
      <th>geometry.coordinates</th>
      <th>properties.lignes</th>
      <th>properties.nom</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[4.84759563537, 45.7595036164]</td>
      <td>[B]</td>
      <td>Place Guichard</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[4.84712980621, 45.7593644344]</td>
      <td>[B]</td>
      <td>Place Guichard</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[4.84773933033, 45.7594174111]</td>
      <td>[B]</td>
      <td>Place Guichard</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[4.84763568316, 45.7590652618]</td>
      <td>[B]</td>
      <td>Place Guichard</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[4.84701961424, 45.7537367341]</td>
      <td>[B,D]</td>
      <td>Saxe-gambetta</td>
    </tr>
  </tbody>
</table>
</div>



Given our aim of providing practical help to the user to plan a touristic trip in Lyon, we think the public transport part is important to show. We only focused on the metro as it is the easiest to use and the most popular among all means of public transport.

### About bikes in Lyon

Another mean of transport that can be interesting for the user - escpecially if they come to Lyon during the summer - is biking. There are some shared bikes in Lyon that you can take from (and put back to) a station. The thing is, those stations have limited bike capacity. Two different indicators are important to know:
- the number of available bikes at a station
- the number of available bike stands at a station - you can't just leave your bike in the surroundings, it has to be plugged in to a stand

Those numbers vary from a station to another and from time to time.
The data is available in real time via the API of JCDecaux.com. The thing is, we wanted to get some data for all times in the day, not only at a specific hour. That's why we wrote a short script to send a get request to the API every 30 minutes, and it ran for almost 3 days. We then took the mean of the 2 or 3 values we had for each situation.



```python
import datetime
import time

def scrap(N):
    i = 0
    for i in range(N):
        name = datetime.datetime.now().strftime("%Y%m%d%H%M%S")
        name = name + ".json"
        d = requests.get('https://api.jcdecaux.com/vls/v1/stations?contract=Lyon&apiKey=120d82ecfbbcb551fa2aebb8bb8094eb54a63b9b')

        with open(name , 'w') as outfile:  
            json.dump(d.json(), outfile)
        time.sleep(1800)

#scrap(200)             # don't run this if you don't have 3 days to lose !
```

After gathering all this data and aggregating them, we ended up with two tables:
- one with hourly data for each station
- one with the basic information about the stations


```python
data_bike_station = json.load(open('station_data.json',encoding='utf8'))
data_bike_station = pd.DataFrame(data_bike_station)
data_bike_station = data_bike_station.transpose()
data_bike_station.head()
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
      <th>address</th>
      <th>name</th>
      <th>position</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>rue de la Technologie</td>
      <td>10001 - IUT / FEYSSINE</td>
      <td>{'lat': 45.785632, 'lng': 4.882366}</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>Avenue Albert Einstein au niveau du virage</td>
      <td>10002 - INSA (FAR)</td>
      <td>{'lat': 45.782222, 'lng': 4.876951}</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>Rue Salvador Allende / Angle promenade du Lys</td>
      <td>10004 - TONKIN</td>
      <td>{'lat': 45.776217, 'lng': 4.862636}</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>Face Entrée du Parc de la Tête d'Or</td>
      <td>10005 - BOULEVARD DU 11 NOVEMBRE</td>
      <td>{'lat': 45.779702, 'lng': 4.860029}</td>
    </tr>
    <tr>
      <th>10006</th>
      <td>Place Charles Hernu / côté sud</td>
      <td>10006 - CHARPENNES (FAR)</td>
      <td>{'lat': 45.770224999999996, 'lng': 4.863102}</td>
    </tr>
  </tbody>
</table>
</div>



The index of the previous dataframe is the id of the station. It is used to link the dataset to the one of the hourly data below.


```python
data_bike_hourly = json.load(open('hourly_data.json',encoding='utf8'))
data_bike_hourly = pd.DataFrame(data_bike_hourly)
D = data_bike_hourly.to_dict(orient='index')
dict(list(D[0].items())[0:5])


```




    {'10102': {'available_bike_stands': 21.0,
      'available_bikes': 9.0,
      'time': 1.25},
     '6028': {'available_bike_stands': 9.0, 'available_bikes': 7.0, 'time': 0.25},
     '7057': {'available_bike_stands': 8.0,
      'available_bikes': 16.0,
      'time': 22.25},
     '8037': {'available_bike_stands': 2.3333333333333335,
      'available_bikes': 16.666666666666668,
      'time': 12.75},
     '9033': {'available_bike_stands': 37.333333333333336,
      'available_bikes': 2.6666666666666665,
      'time': 15.75}}



## 3.    Genre



- For the Visual narrative, here are the tools we have used from each category:

    -   *Visual Structuring:*
        - A consistent Visual Platform *Tell me Lyon*: each slide is provided with a legend, a description of the places when hovering them, and a data exploration part where you need to choose your options
        - A time bar in the shared-bikes slide to let you know the available bikes and stands every hours of the day

    -   *Highlighting:*
        - Close-Ups
        - Feature Distinction
        - Motion
        - Zooming

    -   *Transition Guidance:*
        - Animated Transitions, especially in the description part of the shared-bikes slide when you hover different bike stations

- For the Narrative Structure, here are the tools we have used from each category:
    -   *Ordering:*
        - Even though the order of the slides has been chosen on purpose, there is no clear path for the users to take through our visualization. Our visualization's ordering is then random access.

    -   *Interactivity:*
        - Hover Highlighting / Details
        - Filtering / Selection / Search
        - Navigation Buttons

    -   *Messaging: Captions / Headlines*
        - Captions / Headlines
        - Multi-Messaging
        - Accompanying Article: for each place there is the website provided in order to book an accommodation or get more information about a specific monument for instance

## 4.    Visualizations

We have chosen to use a map slideshow to help you travel and discover several interesting aspects about Lyon. In fact, it is easy to access the information you need and you can go back and forth in a easier way than in a video for example, which has to be quite linear. Each slide contains a lot of other genres of visualization such as annotated maps, bar charts and even animations.

From the slide-show menu, you can access our set of interactive maps : *Hot spots*, *Shared-bikes*, *Hotels* and *Transports*. We think that it is the best way to build an interaction with the user. In each slide you can choose to display what you're interested in.

The best thing to do is to start with the slide *Hot spots*. You can visualize all the interesting places in Lyon along with their popularity - see the number besides the star in the tooltip, which is the popularity ranking of the hotspot. You can choose to display only the trendy places, and select categories, if you are for instance interested in cathedrals. You are also able to use the search bar if you are looking for a place in particular. This is the slide where you want to choose the most trendy places in which you will discover the amazing French culture. You can find a complete description of every places by hovering them. There is also a link to the website if you want to book tickets or get some more information.

The most interesting thing about this slide is that you can select all places you absolutely do not want to miss during your trip. These selections will appear in your wish list and will be saved in all the slides so that we can recommand you, according to your selection, a place to sleep and the metro stations that you want to use.

If you go then in the *Hotels* slide, you will be able to see all the accommodations available, sorted by three categories : *private accommodation*, *hotel* and *hostel*. You can select the maximum radius you want to live from the places of your whishlist. Other selections can be made on overall quality and customer satisfaction. In this slide, we guide you to choose the best accommodation adapted to your trip and your requirements.

In the slide *Transports*, we guide you to the closest stations from your wished places. You can select the metro lines you are interested in and we give you the closest stations.

Finally, the slide *Shared-bikes*, because we know we are aiming mostly at Danish people, is a way for you to know the available bikes and stands in the different bike stations every hours of the day. There is an animation which gives you a general overview of the popularity of some stations in Lyon based on different colors. This gives as well a picture of the human flows throughout the day within the city.

The global structure of the visualization is based on AngularJS 1.6 (https://angularjs.org/). The map has been created using Leaflet 1.3.0 (https://leafletjs.com/). Most of the components were built with Semantic UI 2.3 (https://semantic-ui.com/) and jQuery 3.3.1 (https://jquery.com/); and the slideshow structure with Angular-Slider (https://github.com/ventu ocket/angular-slider). As expected, D3 5.4 (https://d3js.org/) was used for the maps, graphs, markers etc. Finally we used Node.js in order to get more data (photos, rating, review).

There are two ways of using our visualization. First, the viewer can just create his wish list and go through the acommodations, bikes and metros in order to prepare his trip in a simple way. The other possibility is to deeply dig into the numerous data that are displayed and better understand the characteristics of the city through the visualizations. For instance one can notice that there are more cheap hotels with good ratings than hotels with many stars and bad customer satistaction. Another example is the shared-bike slide animation, which really tells the story of bike users movement throughout the day.
Note: as we say in French, *qui cherche trouve*...!

## 5.    Discussion

### What went well?

We have succeeded in giving a good overview of the interesting places in Lyon, finding a good popularity metric (we tried several of them which were not relevant before finding the right one.)

We wanted to create a visualization that was as interactive as possible, and we think the result is very good in that perspective. Furthermore, it is pleasant to use as there is consistency in the colors and the shapes of the different slides.

More than a story, it was also important for us to give the user the history behind the visualizations we proposed through "Tell Me Lyon". Indeed, it seemed important to us that the user not only see coloured markers on a map, but be aware of the reality of the objects we were talking about and their past histories.

### What is still missing? What could be improved? Why?

When we did the video we were planning to include more slides, such as one with the bad city aspects (pollution, noise etc), as well as one dedicated to the famous annual light show in the whole city. We dropped those parts of the project due to some difficulties to get relevant data sets; and to focus more on the ones we had in order to develop the storytelling.

## 6.    Contributions


- Emma and Emilie focused more on gathering all the data and preprocessing it (hotpsots and hotels for Emma, bikes and transports for Emilie).

- Benjamin took the lead on the code and actually building the visualization. He also worked to enhance datasets with others, in particular, hotspots dataset with Google Maps Places data.

- The structure of the user interface in the different slides were tought and built together.

- Emma and Emilie were responsible of this explainer page.
