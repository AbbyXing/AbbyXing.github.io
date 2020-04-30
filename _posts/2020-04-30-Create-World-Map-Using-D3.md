---
layout: post
title:  "D3.js: Plotting Data on World Map"
description: a world map shows the number of Covid-19 confirmed cases.
---

# Intro

Map is an effective and efficient way for data visualization under some circumstances. Generally it is used when displaying the data that related to geography. For example, showing population, GDP, or average income of each country. Unlike a tedious bar chart or point chart, map visualization transmits information in a more straightforward way and makes comparison easier especially when tending to focus on specific countries. With data showing on a map, we can clearly see the distribution of data in different regions thus make conclusions easier.  

Considering the special time period we are facing, in this article, we will make a map visualization using D3.js to show the number of Covid-19 confirmed cases in each country.

# Result:
Here is the visualization we get after finishing. Based on this map, we can see the distribution of cases today by the color scale of each country. The complete visualizer can be found [here](http://abbyxing.com/covid-glb).

![final-result][final-result]

# Code
## HTML

```html
<div id="my_dataviz" class="row"></div>
```

## JS

```js
// svg
var width = 700,
	height = 400;
var svg = d3.select("#my_dataviz")
	.append("svg")
	.attr("width", width)
	.attr("height", height);

// Projection
const projection = d3
	.geoEckert4()
	.scale(130)
    .translate([0.5 * width, 0.5 * height]);

// Path and Graticule
const path = d3.geoPath().projection(projection);
var graticule = d3.geoGraticule10();

// Color Scale
var colorScaleCases = d3.scaleThreshold()
    .domain([0, 100, 400, 800, 5000, 10000, 30000, 50000])
    .range(d3.schemeReds[9]);

// Load External Data
var dataForMap = d3.map();
var promises = [
	// map data
	d3.json("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson"),
	// covid-19 data
	d3.json("https://corona.lmao.ninja/v2/countries")
];

// Run ready function after loading and preprocessing the data
Promise.all(promises).then(function(allData){
	var topo = allData[0];
	var example = allData[1];
	example.forEach(function(d){
		// Preprocessing data
		dataForMap.set(d.countryInfo.iso3, +d.cases);
	});

	countries = ready(topo, countries);
});

// Ready function
function ready(topo, countries) {

	// Draw graticule
	svg
		.append("g")
		.attr("class", "grid")
		.append("path")
		.datum(graticule)
		.attr("class", "graticule")
		.attr("d", path)
		.attr("opacity", 0.8)
		.attr('stroke-width', '0.5px')
	    .attr('stroke', '#ccc')
	    .attr("fill", "none");

	// Draw countries
	var countries = svg
		.append("g")
		.attr("class", "geomap")
	    .selectAll(".country")
	    .data(topo.features)
	    .enter()
	    .append("path")
	    .attr("class", function(d){
	    	return "country " + d.id;
	    })
	    .attr("d", path)
	// Set the color for each country
      	.attr("fill", function (d) {
      		d.total = 0;
      		if(dataForMap.has(d.id))
	        	d.total = +dataForMap.get(d.id);
	        return colorScaleCases(d.total);
	    });
    return countries;
}
```

[final-result]: https://res.cloudinary.com/daxtpgi3q/image/upload/v1588218943/blogs/2020-04-30-covid-world-map/svgexport-0_owvxbs.svg