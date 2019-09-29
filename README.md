# Crime Analysis in Mexico City
## Analysis and classification of the Mexico City's colonies (neighbourhoods) based on their crime rates


### Introduction/Business Problem
Mexico City is one of the most important cultural and financial centres in the Americas and is the destination for many foreign tourists [1]. However, like many cities, It is not safe from crime acts and the crime rates are only getting high [2].

Besides willing to know the most important places of the city, like the Historic Center of Mexico City or the Chapultepec Castle, tourists will eventually visit places like pubs, hotels, restaurants, etc. in order to complement their trips, but this places may be insecure [2].

To help solve this problem, It would be nice to count with a tool capable to tell if a particular place is under a risky zone at a given time: this tool will not only help tourists to avoid this zones, but also locals and people who needs to travel constantly accross a particular city like taxi drivers or transporters. With this tool, administrative dependencies of justice of a city as well as service providers like Uber, can bring more safety to their citizens and customers.

The aim of this project is to established the foundations of this tool by answering the question "What are the most insecure colonies for a tourist to visit?."

In order to accomplish this, an analysis will be carry on the data from the colonies and the crimes (provided by the Data Portal of Mexico City [3]) of one of the most historic and cultural boroughs of the Mexico City, Cuauhtémoc [4], by using the Foursquare Places API to retrieve the most important venues around a given colony.

The expected result will be a classification of the most prone colonies to crime acts.


### Data

The government of Mexico City offers a website portal called "Data Portal of Mexico City [3]" where it is possible to consult, explore, and download open data of the city. For the purposes of this project, 2 datasets will be extract from this site, as described below.

* Colonies of Mexico City (colonias_cdmx_2019.csv) [5]
Contains the territorial delimitation of the colonies of Mexico City. The last update of this data set was performed on june 6, 2019.

It is comprised by 1,812 records and 9 variables, listed below:

  * COLONIA (Colony), text
  * ENTIDAD (Entity), integer number
  * Geo Point, location
  * Geo Shape, locations
  * CVE_ALC, integer number
  * ALCALDIA (Borough), text
  * CVE_COL, text
  * SECC_COM, text
  * SECC_PAR, text

Example:

![alt text](https://github.com/iSaaC92G/Crime-Analysis-in-Mexico-City/blob/master/images/data%20sets/colonies_data_set_example.jpg "Colonies Data Set Example")

* Research Folders PGJ of the Mexico City (archived, ) [6]
Contains the research folders of crimes at the street level from january, 2016 to june, 2019. Because this data set is comprised by 808,871 records for all boroughs, It will be reduced to the subset of records that belong to the Cuauhtémoc borough and where the crimes took place in 2018.

It is comprised by 39,431 records and 18 variables, listed below:

    * año_hechos (year of the crime), text
    * mes_hechos (month of the crime), text
    * fecha_hechos (date of the crime), text
    * delito (crime), text
    * categoria_delito (crime category), text
    * fiscalía (prosecution), text
    * agencia (agency), text
    * unidad_investigacion (investigation unit), text
    * colonia_hechos (crime colony), text
    * alcaldia_hechos (crime borough), text
    * fecha_inicio (start date), text
    * mes_inicio (start month), text
    * ao_inicio, text
    * calle_hechos (crime street), text
    * calle_hechos2 (crime street 2), text
    * longitud (longitude), text
    * latitud (latitude), text
    * Geopoint, location

Example:

![alt text](https://github.com/iSaaC92G/Crime-Analysis-in-Mexico-City/blob/master/images/data%20sets/crime_data_set_example.jpg "Crime Data Set Example")

In addition to these data sets, It will also be used the data provided by the Foursquare Places API by using its "Get Venue Recommendations" endpoint [7], which will contain data of popular venues around each of the Cuauhtémoc's colonies.

Example:

```json
{
  "meta": {
    "code": 200,
    "requestId": "5991c2dbdd57972dfdf5831b"
  },
  "response": {
    "headerLocation": "SoHo",
    "headerFullLocation": "SoHo, New York",
    "headerLocationGranularity": "neighborhood",
    "query": "coffee",
    "totalResults": 101,
    "groups": [
      {
        "items": [
          {
            "venue": {
              "id": "573498df498e6df2eb8b36a7",
              "name": "La Colombe Torrefaction",
              "location": {
                "address": "154 Prince St",
                "crossStreet": "B/T W. Broadway & Thompson",
                "lat": 40.7258839175593,
                "lng": -74.0010660462815,
                "distance": 186,
                "postalCode": "10012",
                "cc": "US",
                "city": "New York",
                "state": "NY",
                "country": "United States",
                "formattedAddress": [
                  "154 Prince St (B/T W. Broadway & Thompson)",
                  "New York, NY 10012",
                  "United States"
                ]
              },
              "categories": [
                {
                  "id": "4bf58dd8d48988d1e0931735",
                  "name": "Coffee Shop",
                  "pluralName": "Coffee Shops",
                  "shortName": "Coffee Shop",
                  "icon": {
                    "prefix": "https://ss3.4sqi.net/img/categories_v2/food/coffeeshop_",
                    "suffix": ".png"
                  },
                  "primary": true
                }
              ],
              "stats": {
                "checkinsCount": 2097,
                "usersCount": 893,
                "tipCount": 12
              },
              "url": "https://lacolombe.com",
              "price": {
                "tier": 1,
                "message": "Cheap",
                "currency": "$"
              },
              "rating": 9.2,
              "hours": {
                "status": "Likely open",
                "isOpen": true,
                "isLocalHoliday": false
              },
              "hereNow": {
                "count": 1,
                "summary": "One other person is here"
              }
            },
            "tips": [
              {
                "createdAt": 1468075940,
                "text": "The iced coffee is no longer an iced red eye, it's cold brew. If you want the red eye ask for it; it's the same price.",
                "type": "user",
                "canonicalUrl": "https://foursquare.com/item/57810fa4498e1542398875fa",
                "likes": {
                  "count": 1,
                  "groups": [],
                  "summary": "1 like"
                },
                "agreeCount": 3,
                "user": {
                }
              }
            ],
            "referralId": "e-0-573498df498e6df2eb8b36a7-0"
          }
        ]
      }
    ]
  }
}
```

A succesfull call to this endpoint will result in a response containing a JSON file, as showned above. The retrieved venues will be contained in an array located in "response" > "groups" > "items" of the response's structure.


### References
* [1] - https://en.wikipedia.org/wiki/Mexico_City
* [2] - https://en.wikipedia.org/wiki/Crime_in_Mexico
* [3] - https://datos.cdmx.gob.mx/pages/home/
* [4] - https://en.wikipedia.org/wiki/Cuauht%C3%A9moc,_Mexico_City
* [5] - https://datos.cdmx.gob.mx/explore/dataset/coloniascdmx/information/
* [6] - https://datos.cdmx.gob.mx/explore/dataset/carpetas-de-investigacion-pgj-cdmx/information/
* [7] - https://developer.foursquare.com/docs/api/venues/explore
