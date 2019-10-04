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

Ir order to carry out my analysis, I decided to use IBM Cloud as my analytics platform to take advantage of the following services:

- Cloud Object Storage, to store the datasets
- Watson Studio, to create a project with a Jupyter Notebook with a Python 3.6 kernel and the data sets

After setting the work environment, I first loaded the Colonies data set to shape it and clean it in order to use it to create a map of the colonies. Fortunately the data set already contained geolocation data of each colony so I just made sure to gather only the information relative to the Cuahtemoc borough. The final subset was formed by 63 colonies.

Then, by using the Foursquare Places API and the new Colonies data set, I searched for the top 10 recommended venues for each colony in a radius of 200 meters. I merged this information with the Colonies data set and created a map. These steps will then allow me to re-create this same map with the classified colonies.

With the data set of Venues per Colony, I started to shape, clean and analyze the Crimes data set, comming up with a data set of 38,235 records. So my attention was focused in two variables: the Crime Category and the Crime Colony.

For the classification process, I decided to use the k-means algorithm, so I transformed the Crime data set by setting the crime categories as columns and grouping rows by colonies, calculating the mean for each crime category. By creating 6 clusters, the model determined the corresponding labels for each cluster and I use them to assign the corresponding label to Venues per Colony data set.

* I choosed Crime Category as my main indicator of crime because it gave a great overview of the kind of crimes commited. While the Crime variable brings a more detailed look of a crime it will require a more 


### Results

In the following map, venues are displayed with different colors depending on how the colony they belong was classified.

[image]

In this first approximation, It was used the number of commited crimes as a factor to determined which colonies are most prone colonies to crimes. The following table shows that clusters 3 and 5 contain the highest crime rates.

[image]

Finally, filtering by the model's labels, these are the colonies included in the previous clusters

[image]

Aditionally, these are the venues found on those colonies

    * El Sauce Y La Palma "qkas"
    * Delicias Celene
    * Centro Arista
    * Deportivo Cuauhtémoc
    * Yubá
    * BP Gasolinera Aldama
    * Walmart Buenavista
    * Feria de Buenavista
    * Panda Express
    * 61 Blues Jazz & Other Grooves
    * Bonebreakers Dojo Central
    * Arrachera's Grill
    * Mecanico Jeans Centro
    * Plaza Tlaxcoaque
    * Domino's Pizza
    * MÁSCARA DE LÁTEX 20 De Noviembre
    * Fust ONE
    * Pasta Di Roma
    * BoneBreakers Central
    * VegAmo
    * Panqué de Nata Finnniiiisssimo Sabor!
    * Café El Cordobés
    * Ricos Tacos De Cochinita Pibil
    * Oaxaca en México
    * Puerto Alameda
    * taqueria San Antonio
    * Café Épico
    * Centro Cultural El Cerrojo
    * La Castellana
    * Plaza Rio de Janeiro
    * Helados Cometa
    * Rosetta
    * Abarrotes Delirio
    * MODO Museo del Objeto del Objeto
    * revancha
    * Meroma
    * Maison de Famille
    * Galería OMR
    * Campobaja
    * Central Del Pueblo
    * el mueble perfecto
    * Oxxo
    * El Craneo
    * Plaza de Santa Catarina
    * Cancino Cabrera
    * Musica en Vinyl Roma
    * Yakumanka By Gaston Acurio
    * Boba Fusion Tea Bar
    * Las Costillas De San Luis
    * Broka
    * Plaza Luis Cabrera
    * Balmori
    * Nudo Negro
    * TINTO GRISHO
    * Artesanías y Manualidades Calzada de Gpe.
    * Desayunos y almuerzos "La Perla"
    * Pulquería La Rosita
    * Zéfiro
    * Coox Hanal
    * Chocolatería Mayordomo
    * Hostería La Bota
    * Jekemir Café
    * Santa Clara Regina
    * Chocolatería Mayordomo
    * Jerónimas
    * Ex Convento Regina Coelli
    * Casa Serra Sucesores, S.A. de C.V.
    * Mirador Monumento a la Revolución Mexicana
    * Monumento a la Revolución Mexicana
    * Terraza Timberland
    * Revolution Square
    * Terraza Cha Cha Chá
    * La nueva Jauja
    * Finca Santa VeraCruz
    * La Cantera
    * Frontón México
    * Museo Nacional de la Revolución
    * El Antiguo Edhen
    * Ehden
    * Café "Equis"
    * Roldan 37
    * Tacos Don Chano
    * Chilli-Aquilli
    * El Cafeto Dulceria
    * La Peninsular
    * Peluches Santísima
    * Plaza Atarazanas - alhóndiga
    * Casa De Francia
    * Havre Cancino
    * Maison Kayser
    * Peluquería Urbana STUDIO
    * California Pizza Kitchen
    * Reforma 222
    * DE MAR A MAR
    * Luau
    * IOS Offices
    * Bellinghausen
    * El Taquito Restaurante Taurino
    * Tacos El Patan
    * Aurora mexico
    * Palacio de Hierro
    * Jalil sabor a hogar
    * Zuckys
    * Sonora Grill Prime
    * Le Bistro Palacio
    * Corredor Salamanca
    * Museo Soumaya Casa Guillermo Tovar De Teresa
    * Benefit Brow Bar
    * Café El Asturiano
    * Fonda Los Tios
    * Centro Histórico
    * El Cardenal
    * Mercaderes
    * Casino Español
    * Downtown México
    * Azul Histórico
    * Que Bo!
    * Helados Santa Clara Cafetería
    * Zinco Jazz Club
    * Museo del Estanquillo
    * Taqueria Yeshua
    * Museo de la Luz
    * El Castillo de la Fantasía
    * Casa Tlaxcala
    * Callejón De Giron
    * Plaza de Loreto
    * Teatro del Pueblo
    * Ostionería Veracruz
    * Distribuidora Nieto
    * Finca Loreto
    * Hamburguesas Amador
    * Los Huaraches De Rosy
    * Grupo CVA
    * Los Auténticos Tacos del Paisa
    * Barbacoa El Profe
    * Las Baguettes De Isabela
    * Barbacoa de horno "Toños"
    * Bodega de Playeras Gildan
    * Baguettes de Isabella
    * Crazy Transfer Matriz
    * Baguette
    * Plaza Algarin
    * Mercado Isabel La Católica
    * Manufacturas Gaviota
    * Tiangis Cultural Chpo'


### Discussion

Taking into account comments and assessments of the venues can also help to improve the classification not just for colonies, but venues as well: this will required Natural Language Processing (NLP) methodologies to analyze the sentiment of such comments.

Regarding the Crime data set, It would be very useful to count with a scale to tell which crimes are worse than others (applying for both Crimte Category and Crime type): that way the classification would be more precise.

The Crime data set also provides the geolocation of each crime: with this information, in combination with the assessment of a given venue, it can help into the classification of venues themselves given place to the question "How secure is a point a interest?".
Aditionally, the Crime data set also provides date and time information about the crimes, which can lead to a classification based on time. This can help to predict crime activity in near real time and puts some interesting questions on the table "Are there 'seasons' where crimes occur most in a given place?, How risky is a place during the pass of the day?, How crime activity varies across the day and how authorities can take advantage of this?"


### Conclusion

10 colonies were found to be most prone to crimes in Mexico City's Cuauhtemoc boroguh. Most of them are located along the west side, with almost 8,176 crimes committed in 2018 but equally comparable to those committed around the center of the city, with 8,272 crimes, in the same year.

This suggests that crime activity is highly concentrated in the center of the city and, at this point, this seems reasonable considering for example, from a high point of view, that most of the cutural and historical places are located there. This brings the following questions:

- What is the correlation between a popular place and the crime rates that sorround it? What are the causes?


### References

* [1] - https://en.wikipedia.org/wiki/Mexico_City
* [2] - https://en.wikipedia.org/wiki/Crime_in_Mexico
* [3] - https://datos.cdmx.gob.mx/pages/home/
* [4] - https://en.wikipedia.org/wiki/Cuauht%C3%A9moc,_Mexico_City
* [5] - https://datos.cdmx.gob.mx/explore/dataset/coloniascdmx/information/
* [6] - https://datos.cdmx.gob.mx/explore/dataset/carpetas-de-investigacion-pgj-cdmx/information/
* [7] - https://developer.foursquare.com/docs/api/venues/explore
