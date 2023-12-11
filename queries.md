## Query 1

#### Select all the artists/bands with their song's name where the number of stream in Spotify is greater than the number of views of the corresponding YouTube video and the number of streams is above 2 billions.

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

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX countries: <http://eulersharp.sourceforge.net/2003/03swap/countries#>

select ?track_name
where {
    ?artist sg:writes ?track ;
            sg:hasNationality countries:gb ;
            sg:hasNationality countries:us .
    ?track sg:trackName ?track_name .
}
```
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
## Query 6

#### Find the percentage of official videos that are not been published by the official channel of the artist/band (compared to all the official videos).

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT  ((?Nmatch / ?Ntotal)*100 AS ?percentage)
       
WHERE {
  {
    SELECT (COUNT(DISTINCT ?yt_video) AS ?Nmatch) 
    WHERE {
      ?artist sg:performsIn ?yt_video;
              sg:hasOfficialChannel ?ofchannel.
      ?yt_video sg:isOfficialVideo true;
                sg:isPublishedBy ?ytchannel.
      FILTER(?ofchannel != ?ytchannel).
    }
  }
  {
    SELECT (COUNT(DISTINCT ?totalVideo) AS ?Ntotal)
    WHERE {
      ?totalVideo sg:isOfficialVideo true.
    }
  }
}
```
## Query 7

#### Find the nationality of the artist/band that has more views in its official YouTube channel.

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

select ?artist_name ?nationality ?channel_view_count where {
	
    ?artist2 sg:hasOfficialChannel ?off_channel2;
             sg:artistName ?artist_name;
             sg:hasNationality ?nationality.
    ?off_channel2 sg:channelViewCount ?channel_view_count2.
    filter (?channel_view_count2=?channel_view_count).
    {
        select (max(?c_view_count) as ?channel_view_count) where {
            ?artist sg:hasOfficialChannel ?off_channel.
            ?off_channel sg:channelViewCount ?c_view_count.
        }
        
    }
    
         	
}
```

## Query 8

#### Find the artist of each nation that has won more awards.

```



```