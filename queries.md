## Query 1

#### Select all the artists with their song's name where the number of stream in Spotify is greater than the number of views of the corresponding Youtube video and the number of streams is above 2 billions.

```
PREFIX : <https://www.dei.unipd.it/db2/ontology/soundgraph#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

select distinct ((?a_name) as ?Artist_name) ((?t_name) as ?Track_name) where {
    ?artist :artistName ?a_name;
           :performsIn ?yt_video.
    ?yt_video :videoViews ?num_views.
    ?artist :writes ?spotify_track.
    ?spotify_track :trackStreams ?num_streams;
                   :isRelatedTo ?yt_video;
                   :trackName ?t_name.
    filter(?num_streams > ?num_views && ?num_streams > 2000000000).
}
order by asc (?t_name)

```

## Query 2

#### query text

```test```

## Query N

#### query text

```test```
## Query N

#### query text

```test```
## Query N

#### query text

```test```
## Query N

#### query text

```test```
## Query N

#### query text

```test```