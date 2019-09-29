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

The following data sets will be used:

* colonias_cdmx_2019.csv
Contains the data of the colonies of each borough of the Mexico City. The last update of this data set was performed on June 6, 2019. [5]

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

### References
* [1] - https://en.wikipedia.org/wiki/Mexico_City
* [2] - https://en.wikipedia.org/wiki/Crime_in_Mexico
* [3] - https://datos.cdmx.gob.mx/pages/home/
* [4] - https://en.wikipedia.org/wiki/Cuauht%C3%A9moc,_Mexico_City
* [5] - https://datos.cdmx.gob.mx/explore/dataset/coloniascdmx/table/
