# graphdb-spotify-yt
Repository for the project of the Database 2 (Graph Databases) course, SoundGraph group.
### Repository structure
```
├── data : contains the CSV files of the data
├── notebooks : python notebooks for extracting the data from CSV file
├── RDF : output folder of the .ttl and .rdf files
├── LICENSE
├── README.md
├── queries.md
```

### Data
The dataset used for the project is available [here](https://www.kaggle.com/datasets/salvatorerastelli/spotify-and-youtube) at the Kaggle website.

The other data files are extracted from the Wikidata, Spotify and Youtube endpoints, respectively using [Wikidata](notebooks/wikidata_api.ipynb),
[Spotify](notebooks/spotify.ipynb) and [Youtube](notebooks/youtube_api_channels.ipynb).

### Ontology 
![](graph_ontology.png)
### Queries
The queries are present in the md file [queries](queries.md). Each query is composed of a description, stating what the query is doing and the code of the query itself.

### Run
In order to run the graph database, the following steps are necessary:

1. import the ```ttl``` and ```rdf``` files into a  ```graphdb repository```.
2. copy a specific query from the file [queries](queries.md) and paste it into the query section of graphdb.
