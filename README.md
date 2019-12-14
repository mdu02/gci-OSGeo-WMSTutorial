# gci-OSGeo-WMSTutorial

This page serves as a tutorial to illustrate the creation of a web map which displays WMS files, on Windows.

# Installing Docker

We will be running our geoserver in a container on a Docker container. You can find an open-source version of Docker (which should run on any modern PC) at [this github link](https://github.com/docker/toolbox/releases).

1. Download the .exe file from GitHub
2. Run the .exe file. There will be a prompt to allow an Oracle driver to install; click yes.
3. You should now have Docker Toolbox installed.

# Obtaining some data

So what type of data are you looking for? Try your city/country's Open Data set. I live in Toronto, which does release a good amount of geospatial data on their [open data portal](https://open.toronto.ca/).

The data being used in this tutorial:

[Contains information licensed under the Open Government Licence – Toronto.](https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-licence/)

With that being said, let's dive right in! Say I'm trying to write an article about the condition of TCHC buildings(community housing). We'll download the file from the [webpage](https://open.toronto.ca/dataset/toronto-community-housing-data/).

# Importing the data to GeoServer

Let's start up GeoServer and feed it this data. We'll be using Docker to run GeoServer. Open up the Docker Quickstart Terminal.

Possible problems: The bind mount doesn't work
Solution: Open VMBox and check if the mount is working.

1. In docker, cd to the directory with your data
2. Run the following commands, the second after the first is complete.
  ```
  docker pull kartoza/geoserver
  docker run --name "geoserver" -p 8080:8080 -t -v /housing-data:/opt/geoserver/data_dir/data/housing-data kartoza/geoserver 
  ```
3. 