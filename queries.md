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
TODO not finished

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>


SELECT distinct ?nat (MAX(?total_award) as ?max_awards) 
WHERE {
  {
        
    SELECT ?nat ?artist_name (COUNT(?award) as ?total_award)
    WHERE {
      ?artist sg:hasReceived ?award;
              sg:artistName ?artist_name;
              sg:hasNationality ?nat.
    }
    GROUP BY ?artist_name ?nat
  }
}
GROUP BY ?nat
ORDER BY ?nat DESC (?max_awards)


```


## Query 9

#### Find all the genres associated to each italian artist/band.

```
prefix sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
prefix countries: <http://eulersharp.sourceforge.net/2003/03swap/countries#>

select (?a_name as ?artist_name) ?genre where { 
    ?artist sg:artistName ?a_name;
            sg:hasNationality countries:it;
            sg:hasGenre ?genre.
    minus {
        ?artist sg:hasNationality ?otherNat.
        filter (?otherNat != countries:it)
    }
}
group by ?a_name ?genre
order by ?a_name
```

## Query 10

#### Find the artist/band with more nationalities.

```



```

## Query 11

#### Are there more blues artists/bands than rock artists/bands?

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX countries: <http://eulersharp.sourceforge.net/2003/03swap/countries#>
ask where { 
    {
        select (count(?artist) as ?blues_artist) where {
        	?artist sg:hasGenre sg:genre_blues.
    	}
    }
    {
        select (count(?artist) as ?rock_artist) where {
        	 ?artist sg:hasGenre sg:genre_classic_rock.
    	}
    }   
    filter(?blues_artist>?rock_artist)
} 


```

## Query 12

#### Find the number of award received each year.

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
select * 
where {
    {
        select ?year (count(?award) as ?awards_in_year)
        where {
            BIND("NO YEAR" as ?year)
            ?award sg:awardName ?awardName .
            filter not exists{?award sg:awardYear ?award_year}
        }
        group by ?year
    }
    UNION
    {
        select ?year (count(?award) as ?awards_in_year)
        where {
            ?award sg:awardYear ?year .
        }
        group by ?year
        order by desc(?year)
    }
}
```
## Query 13

#### Find the youngest artist/band.

```
prefix sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>

select distinct ?artist_name ?start_period where {
	{
        select (max(?start) as ?start_period) where {
           ?artist sg:startWorkingPeriod ?start.
        }
    }
    ?artist sg:startWorkingPeriod ?start_period;
            sg:artistName ?artist_name.
}
```

## Query 14
#### Find the top 100 artists by most streams achieved with an album

```
PREFIX sg: <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
SELECT ?artist_name (MAX(?total_streams) AS ?max)
WHERE {
	SELECT ?artist_name ?spt_alb (sum(?streams) as ?total_streams)
    WHERE {
        ?artist sg:composes ?spt_alb;
                  sg:artistName ?artist_name.
        ?spt_trk sg:isInAlbum ?spt_alb;
                sg:trackStreams ?streams.
    }
    GROUP BY ?artist_name ?spt_alb 
}
GROUP BY ?artist_name
ORDER BY DESC (?max)
LIMIT 100
```

## Query 15

#### 

```



```

## Query 16

#### Find the album with the most streams for each artist in the top 100

```



```