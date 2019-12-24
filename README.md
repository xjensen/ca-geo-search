# California Geospatial Search

This is a rough geocoder and geospacial search engine for the State of California. It's currently built on top of elasticsearch and pelias. More to come. More docs too. Need custom data.

## Open Street Map Data

https://download.geofabrik.de/north-america/us/california.html
https://download.geofabrik.de/north-america/us/california-latest.osm.pbf


## Open Addresses Data

http://results.openaddresses.io/
https://data.openaddresses.io/openaddr-collected-us_west.zip


## Who's on First Data

Warning: full planet download is usually required and eats up 27GB.

https://www.whosonfirst.org/data/available/
https://github.com/whosonfirst-data/whosonfirst-data-admin-us
https://github.com/whosonfirst-data/whosonfirst-data-postalcode-us

(Use the California ID in importPlace in pelias.json)
https://spelunker.whosonfirst.org/id/85688637/

## Docker Prep

To make this work, you'll need recent versions of Docker and Docker Compose. I doubt this will work on Windows. I'm assuming you have a Mac or Linux machine. 

That machine will need some beef, because we'll be pushing a lot of bits. Make sure you have about 40GB free disk space before starting. You'll probably want at least 16GB of RAM.

First, prepare the data directory for elasticsearch.

```
# You may need to put "sudo" at the front of some of these commands.

mkdir -m 1000 /tmp/pelias
chgrp 1000 /tmp/pelias
chmod -R a+rwx /tmp/pelias
mkdir /tmp/pelias/california
```

Install the pelias helper script.

```
git clone https://github.com/pelias/docker.git ~/.pelias
# "sudo" may be needed at front of the next command.
ln -s ~/.pelias/pelias /usr/local/bin/pelias
```

Check for success with the following command.

```
which pelias
```

From the project directory, run the following commands in succession. 

> Please note that the `download`, `prepare`, and `import` steps deal with large volumes of data. You'll think the script has stalled, when actually it's just silently downloading or processing. You'll be pulling close to 30GB of civic mapping data, by my count. Mapping data is huge! Make sure you have a lot of free disk space before trying this.

```
pelias compose pull
pelias elastic start
pelias elastic wait
pelias elastic create
pelias download all
pelias prepare all
pelias import all
pelias compose up
```

Run a test query.

```
http://localhost:4000/v1/search?text=sfo
```