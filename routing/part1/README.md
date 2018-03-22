Bicyle in Paris
====

Get the data
----

Download an extract of France

```
wget http://download.geofabrik.de/europe/france/ile-de-france-latest.osm.pbf
```

Extract Paris

```
docker run -t -v $(pwd):/data guillaumerose/osmosis --read-pbf ile-de-france-latest.osm.pbf --bounding-box left=2.24 bottom=48.81 right=2.43 top=48.91 --write-pbf paris.osm.pbf
```

Prepare the data
---

```
docker run -t --rm -v $(pwd):/data osrm/osrm-backend osrm-extract -p /opt/bicycle.lua /data/paris.osm.pbf
docker run -t --rm -v $(pwd):/data osrm/osrm-backend osrm-partition /data/paris.osrm
docker run -t --rm -v $(pwd):/data osrm/osrm-backend osrm-customize /data/paris.osrm
```

Run the server
---

```
docker run -d --rm -t -i -p 5000:5000 -v $(pwd):/data osrm/osrm-backend osrm-routed --algorithm mld /data/paris.osrm
```



First route
---

```
curl 'http://localhost:5000/route/v1/driving/2.3337793350219727,48.86158097877283;2.3430919647216797,48.885855610021544' | jq .
curl 'http://localhost:5000/route/v1/driving/2.3337793350219727,48.86158097877283;2.3430919647216797,48.885855610021544?overview=full' | jq .
curl 'http://localhost:5000/route/v1/driving/2.3337793350219727,48.86158097877283;2.3430919647216797,48.885855610021544?steps=true' | jq .
```

Start a frontend
---

```
docker run -d -p 9966:9966 osrm/osrm-frontend
```

Open a browser and go to http://127.0.0.1:9966