# California Geospatial Search

## Open Street Map

https://download.geofabrik.de/north-america/us/california.html
https://download.geofabrik.de/north-america/us/california-latest.osm.pbf


## Open Addresses

http://results.openaddresses.io/
https://data.openaddresses.io/openaddr-collected-us_west.zip


## Who's on First

Warning: full planet download is usually required and eats up 27GB.

https://www.whosonfirst.org/data/available/
https://github.com/whosonfirst-data/whosonfirst-data-admin-us
https://github.com/whosonfirst-data/whosonfirst-data-postalcode-us

(Use the ID in importPlace in pelias.json)
https://spelunker.whosonfirst.org/id/85688637/

Example:
https://github.com/pelias/docker/blob/master/projects/san-jose-metro/pelias.json

## Docker Prep

To make this work, you'll need recent versions of Docker and Docker Compose. I doubt this will work on Windows, so I'm assuming you have a Mac or Linux machine. 

That machine will need some beef, because we'll be pushing a lot of bits. Make sure you have about 40GB free disk space before starting. You'll need at least 16GB of RAM. Make sure you max out Docker's resources in Docker's preferences on Mac, or via `docker-machine` on Linux.

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

Run the following commands in succession. 

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