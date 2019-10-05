# Crime Analysis of Mexico City
## Analysis and classification of the Mexico City's colonies (neighbourhoods) based on their crime rates


### Introduction/Business Problem
Mexico City is one of the most important cultural and financial centres in the Americas and is the destination for many foreign tourists [1]. However, like many cities, It is not safe from crime acts and the crime rates are only getting high [2].

Besides willing to know the most important places of the city, like the Historic Center of Mexico City or the Chapultepec Castle, tourists will eventually visit places like pubs, hotels, restaurants, etc. in order to complement their trips, but this places may be insecure [2].

To help solve this problem, It would be nice to count with a tool capable to tell if a particular place is under a risky zone at a given time: this tool will not only help tourists to avoid this zones, but also locals and people who needs to travel constantly accross a particular city like taxi drivers or transporters. With this tool, administrative dependencies of justice of a city as well as service providers like Uber, can bring more safety to their citizens and customers.

The aim of this project is to established the foundations of this tool by answering the question "What are the most insecure colonies for a tourist to visit in Cuauhtémoc?."

In order to accomplish this, an analysis will be carry on the data from the colonies and the crimes (provided by the Data Portal of Mexico City [3]) of one of the most historic and cultural boroughs of the Mexico City, Cuauhtémoc [4], by using the Foursquare Places API to retrieve the most important venues around a given colony.

The expected result will be a classification of the most prone colonies to crime acts.


### Data

The government of Mexico City offers a website portal called "Data Portal of Mexico City [3]" where it is possible to consult, explore, and download open data of the city. For the purposes of this project, 2 datasets will be extracted from this site, as described below.

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

* Research Folders PGJ of the Mexico City (archived) [6]
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


### Methodology

Ir order to carry out the corresponding analysis, it was decided to use IBM Cloud as the analytics platform to take advantage of the following services:

- Cloud Object Storage, to store the data sets as csv files and,
- Watson Studio, to create a project with a Jupyter Notebook with a Python 3.6 kernel and the data sets

After setting the work environment, the Colonies data set was first loaded. By making an elementary exploratory analysis, the following things were observed:

* The most popular colony is "Miguel Hidalgo"
* The most popular borough is "Iztapalapa"

Then, this data set was shaped and cleaned to then use it to create a map of the colonies. Fortunately the data set already contained geolocation data for each colony so only the information relative to the Cuahtemoc borough was gather. The final subset was formed by 63 colonies.

By using the Foursquare Places API and the Colonies data set, the top 10 recommended venues were searched for each of the colonies in a radius of 200 meters. This information was merged with the Colonies data set and a map was created: these steps will then allow the creation of a similar map for the classified colonies.

With the data set of Venues per Colony, it was started to shape, clean and analyze the Crimes data set, comming up with a data set of 38,235 records. An exploratory analysis brought the following findings:

* The most popular month for a crime to be commited is in october
* The most commited crime is "robbery of cell phone to a passerby without violence"
* The most popular crime category is "crime of low impact"
* The most popular colony for a crime to be commited is in the "center"

As with the previous data set, this one was shaped and cleaned.

For the classification process, it was decided to use the k-means algorithm, so the Crime data set was transformed by setting the crime categories as columns and grouping rows by colonies, calculating the mean for each crime category. By creating 7 clusters, the model determined the corresponding labels for each one: this new information was merged with the Venues per Colony data set.

Finally, the Venues per Colony data set was used to create a map with classfied venues (based on the classification of the colonies) by using different colors.


### Results

17 colonies were found to be most prone to crimes in Mexico City's Cuauhtemoc boroguh. Most of them are located along the west side, with almost 15,063 crimes committed in 2018 but it's also interesting to note that 8,368 crimes were commited just around the center of the city in the same year.

In the following map, venues are displayed with different colors depending on how the colony they belong was classified.

![alt text](https://github.com/iSaaC92G/Crime-Analysis-in-Mexico-City/blob/master/images/results/classified_venues.jpg "Classified Venues")

In this first approximation, it was used the number of commited crimes as a factor to determined which colonies are most prone to crimes. The following table shows that clusters 1, 3 and 4 contain the highest crime rates.

![alt text](https://github.com/iSaaC92G/Crime-Analysis-in-Mexico-City/blob/master/images/results/crime_rates_by_cluster.jpg "Crime Rates by Cluster")

Finally, filtering by the model's labels, these are the colonies included in the mentioned clusters

![alt text](https://github.com/iSaaC92G/Crime-Analysis-in-Mexico-City/blob/master/images/results/crime_rates_by_colony.jpg "Crime Rates by Colony")


### Discussion

Taking into account comments and assessments of the venues can help to improve the classification not just for colonies, but venues as well: this will required Natural Language Processing (NLP) methodologies to analyze the sentiment of such comments.

Regarding the Crime data set, It would be very useful to count with a scale to tell which crimes are worse than others (applying for both Crimte Category and Crime type): that way the classification would be more precise.

The Crime data set also provides the geolocation of each crime: with this information, in combination with the assessment of a given venue, it can help into the classification of venues themselves given place to the question "How secure is a point a interest?".
Aditionally, the Crime data set also provides date and time information about the crimes, which can lead to a classification based on time. This can help to predict crime activity in near real time and puts some interesting questions on the table "Are there 'seasons' where crimes occur most in a given place?, How risky is a place during the pass of the day?, How crime activity varies across the day and how authorities can take advantage of this?"


### Conclusion

The current analysis has showned how similar the Cuauhtemoc's colonies can be among them based on their crime activity and which of them are most prone to such activities; this information might help tourists to take better desicions about visiting a certain place by identifying these risky zones before getting into them.

This analysis also indicates a high density of crime activity in the center of the city and, at this point, this seems reasonable considering for example, from a high point of view, that most of the cutural and historical places are located there but, this is just an assumption: questions like "What is the relationship between a popular place and the crime rates that sorround it?" and "What are the causes of that relationship, if it exist?" might help set the ground for further analysis.

This project has answered the question initially established but, certainly, there's much room for improvement and refinement: iterating and gathering more information will lead not just to a better clasification but a better understanding of the data itself.


### References

* [1] - https://en.wikipedia.org/wiki/Mexico_City
* [2] - https://en.wikipedia.org/wiki/Crime_in_Mexico
* [3] - https://datos.cdmx.gob.mx/pages/home/
* [4] - https://en.wikipedia.org/wiki/Cuauht%C3%A9moc,_Mexico_City
* [5] - https://datos.cdmx.gob.mx/explore/dataset/coloniascdmx/information/
* [6] - https://datos.cdmx.gob.mx/explore/dataset/carpetas-de-investigacion-pgj-cdmx/information/
* [7] - https://developer.foursquare.com/docs/api/venues/explore
