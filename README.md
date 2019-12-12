# gci-OSGeo-WMSTutorial

This page serves as a tutorial to illustrate the creation of a web map which displays WMS files.

# Installing Docker

We will be running our geoserver in a container on a Docker container. You can find an open-source version of Docker (which should run on any modern PC) at [this github link](https://github.com/docker/toolbox/releases).

1. Download the .exe file from GitHub
2. Run the .exe file. There will be a prompt to allow an Oracle driver to install; click yes.
3. You should have Docker Toolbox installed.

# Obtaining some data

So what type of data are you looking for? Try your city/country's Open Data set. I live in Toronto, which does release a good amount of geospatial data on their [open data portal](https://open.toronto.ca/).

The data being used in this tutorial:

[Contains information licensed under the Open Government Licence – Toronto.](https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-licence/)

With that being said, let's dive right in! Say I'm trying to build an app that shows you the location of the closest... bike rack. I can't help but think that we don't have enough bike infrastructure outside of Downtown, but whatever. Let's download the [file](https://ckan0.cf.opendata.inter.prod-toronto.ca/download_resource/c9568d88-dfb0-4422-813a-973a612717b1)
