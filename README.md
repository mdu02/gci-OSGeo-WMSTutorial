# gci-OSGeo-WMSTutorial

This page will illustrate the creation of a web map which displays WMS files on Windows.

# Installing Docker

We will be running our geoserver in a container on a Docker container. You can find an open-source version of Docker Toolbox (which should run on any modern PC running Windows) at [this github link](https://github.com/docker/toolbox/releases).

1. Download the .exe file from GitHub.
2. Run the .exe file. There will be a prompt to allow an Oracle driver to install; click yes.
3. You should now have Docker Toolbox installed.

# Obtaining some data

So what type of data are you looking for? Try your city/country's Open Data set. I live in Toronto which
releases a good amount of geospatial data on their [open data portal](https://open.toronto.ca/).

The data being used in this tutorial [contains information licensed under the Open Government Licence – Toronto.](https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-licence/).

With that being said, let's dive right in! Say I'm trying to write an article about the condition of TCHC buildings (community housing). We'll download the file from their [webpage](https://ckan0.cf.opendata.inter.prod-toronto.ca/download_resource/f1bf1cef-7d09-407c-80c2-bb2a8b75abfa?format=shp&projection=4326).

# Importing the data to GeoServer

Let's start up GeoServer and feed it this data. We'll be using Docker to run GeoServer. Open up the Docker Quickstart Terminal.

1. Note the IP address, e.g. 192.168.99.100. From now on, we will refer to this as [host_ip].

![](./tutorial-images/host_ip.png)

2. In docker, [cd](https://en.wikipedia.org/wiki/Cd_\(command\)) to the directory containing the data directory.

![](./tutorial-images/data_directory_location.png)

3. Run the following commands sequentially. (I'm in the wrong directory in the terminal, by the way)
  ```
  docker pull kartoza/geoserver
  docker create --name "geoserver" -p 8080:8080  kartoza/geoserver
  ```

![](./tutorial-images/pull_finished.png)

The terminal will give you a 128-byte container ID. You can use any number of leading digits as [con_id] as long as they are unique with other containers.
For example, for a container with ID c42b939b336b66d21ca9c4775271c64a1ebd3a95784d005b7e5f46af1d3904ba, replace [con_id] with 'c', 'c4', 'c42', etc.

4. Run these two commands as well. Make sure you're in the directory containing your data folder.
  ```
  docker cp  housing-data [con_id]:/app
  docker start -t [con_id]
  ```
Wait for the server to spin up.

![](./tutorial-images/spun_up.png)

5. Open up the server's admin console at ```[host_ip]:8080/geoserver```

6. Login as admin. The credentials are user: ```admin``` and pw: ```geoserver```

![](./tutorial-images/login_screen.png)

7. Create a new workspace. The name can be anything you want, but I've made mine my GitHub username. 
The URI is an unique identifier, which does not necessarily have to be in the format of an actual URL.

![](./tutorial-images/new_workspace.png)

8. Click "Stores" on the left bar, then "Add new Store", then "Shapefile". 
In the file selector, choose the .SHP file that we moved into the container. It will be under the /app directory. Access the root of the container from the dropdown in the top left.

![](./tutorial-images/shp_location.png)

9. Click publish on the next page. Click on 'Compute from Data' and 'Compute from native bounds', then 'Save'. Your data is now ready.

![](./tutorial-images/bounding_boxes.png)

# Troublefixing geoserver problems
If the container crashes, run 
```
docker run -t [con_id]
```
 again.
 
# Creating the Web map
In this section, I will assume you know the basic structure of an [HTML page](https://www.w3schools.com/html/). Therefore, this will just cover how to use WMS to display a page.

1. In the head of the HTML file, include the library and CSS for OpenLayers, as shown below. The latest links will be [here](https://openlayers.org/download/).
```html
<script src="https://cdn.rawgit.com/openlayers/openlayers.github.io/master/en/v6.1.1/build/ol.js"></script>
<link rel="stylesheet" href="https://cdn.rawgit.com/openlayers/openlayers.github.io/master/en/v6.1.1/css/ol.css">
```
2. Create a div. Give it an id, which we will be targeting with the javascript later.
Remember to include CSS to size the div, either here or in the head.
```html
<div id="map" style="width: 900px; height: 600px;"></div>
```
3. Since we have a simple map, we'll just use inline JavaScript. Put in a script tag. Steps 4-6 will be contained within this script tag.
```html
<script type = "text/javascript">
...
</script>
```
4. Create an image layer to serve the community housing locations.
``` javascript
var housing_img = new ol.layer.Image({
	source: new ol.source.ImageWMS({
	url: '[host_ip]:8080/geoserver/[workspace_name]/wms',
	params: {'LAYERS': '[workspace_name]:[layer_name]'},
	ratio: 1,
	serverType: 'geoserver'
	})
});
```
In this layer, note a few things. The image object only requires one parameter, the source. 
We create the source, which contains our WMS URL, the layer name, and the type of server.
The WMS URL refers to the workspace name in step 7 of the import step.
5. Create a tile layer, which will serve the underlying map.
```javascript
var os_layer = new ol.layer.Tile({
	source: new ol.source.OSM({
		url: 'https://tile.openstreetmap.org/{z}/{x}/{y}.png',
		attributions: [ol.source.OSM.ATTRIBUTION],
		maxZoom: 18
	})
});
```
In this layer, note a few things. Again, the only param is the source. The URL is from OpenStreetMap's tile server. Don't use this in actual app. Read on OSM's usage policy [here](https://operations.osmfoundation.org/policies/tiles/), 
and remember to attribute the data. OpenLayers happens to include a constant to allow you to attribute OSM 
6. Create the map.
```javascript
var map = new ol.Map({
	layers: [os_layer, housing_img],
	target: 'map',
	view: new ol.View({
		center: ol.proj.fromLonLat([-79.3832, 43.6532]),
		maxZoom: 18,
		zoom: 11
	})
});
```
Note a few things. The Map object is the basic object of the OpenLayers library. It contains a few layers, the ones we just created. It targets 'map', the itemid of our map div.
It contains a view, which specifies where in the world the map will display. ```[-79.3832, 43.6532]``` is the coordinates of downtown Toronto.

# Trying everything out
At this point, you should be able to open your html file, which can display a map with a layer above, being served through WMS from a groserver.
![](./tutorial-images/final_map.png)
