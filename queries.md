## Query 1

#### Select all the artists/bands with their song's name where the number of stream in Spotify is greater than the number of views of the corresponding Youtube video and the number of streams is above 2 billions.

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

select distinct ((?a_name) as ?Artist_name) ((?t_name) as ?Track_name) where {
    ?artist sg:artistName ?a_name;
            sg:performsIn ?yt_video.
    ?yt_video sg:videoViews ?num_views.
    ?artist sg:writes ?spotify_track.
    ?spotify_track sg:trackStreams ?num_streams;
                   sg:isRelatedTo ?yt_video;
                   sg:trackName ?t_name.
    filter(?num_streams > ?num_views && ?num_streams > 2000000000).
}
order by asc (?t_name)

```

## Query 2

#### Return all the artists/bands that have won more than one award for a category.

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

select ?artist_name ((?aw_category) as ?award_category) (count(?award) as ?total_award) where 
{
  	?artist sg:hasReceived ?award;
           	sg:artistName ?artist_name.
	?award	sg:awardCategory ?aw_category.
}

group by ?artist_name ?aw_category
having(?total_award>1)
order by ?artist_name desc (?total_award)

```

## Query 3

#### Are there artists/bands that have at least two songs with more than 1.5 billions streams, that have won a Grammy Award?

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

ask where {  
    ?artist sg:hasReceived ?award.
    ?award  sg:awardName ?name.
    filter regex(?name, "Grammy Award"). 
    { 
        select ?artist ?a_name (count(?track) as ?tot_track) where { 
            ?artist sg:artistName ?a_name;
                	sg:writes ?track.
            ?track sg:trackStreams ?streams.
            filter (?streams>1500000000)
	}
        group by ?artist ?a_name
        having(?tot_track>2)
    }
}

```
## Query 4

#### Find all the songs made by an english-american artist/band.

```test```
## Query 5

#### How many artists/bands are still active?

```

PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

select (count(?artist) as ?still_active) where {
	?artist sg:artistName ?name.
	filter not exists{?artist sg:endWorkingPeriod ?year.}
}

```
## Query N

#### query text

```test```
## Query N

#### query text

```test```