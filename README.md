# Buncombe County Airbnbs (2020)

This project visualizes Airbnb listings and zoning districts in Buncombe County, Asheville, NC, using a web map. The map displays Airbnb listings with different icons based on property type and colors zoning districts based on the number of Airbnbs in each district.

## Project Structure

- `index.html`: The main HTML file that contains the code for creating and displaying the web map.
- `assets/airbnb_listings.geojson`: GeoJSON file containing Airbnb listings data.
- `assets/zoning_districts.geojson`: GeoJSON file containing zoning districts data.

## Features

1. **Map Initialization**:
   - Creates a map centered on downtown Asheville with a specified zoom level.
   - Adds a base map layer from CartoDB.

2. **Airbnb Listings**:
   - Loads Airbnb listings from a GeoJSON file.
   - Displays Airbnb listings with different icons based on property type (Entire house, Private room in house, Other).
   - Adds popups to each listing showing the property type.

3. **Zoning Districts**:
   - Loads zoning districts from a GeoJSON file.
   - Colors zoning districts based on the number of Airbnbs in each district using a choropleth classification system.
   - Adds a legend to explain the color coding and icons.

4. **Legend and Scale**:
   - Adds a legend to the map to explain the color coding of zoning districts and the icons for Airbnb listings.
   - Adds a scale bar to the map.

## How to Run

1. Clone the repository or download the project files.
2. Open `index.html` in a web browser to view the map.

## Dependencies

- [Leaflet](https://leafletjs.com/): A JavaScript library for interactive maps.
- [Leaflet-Ajax](https://github.com/calvinmetcalf/leaflet-ajax): A plugin for loading GeoJSON data via AJAX.
- [jQuery](https://jquery.com/): A fast, small, and feature-rich JavaScript library.
- [Chroma.js](https://gka.github.io/chroma.js/): A JavaScript library for color conversions and color scales.
- [Font Awesome](https://fontawesome.com/): A library for scalable vector icons.

## Code Explanation

### Map Initialization

```javascript
var mymap = L.map('map', {
    center: [35.5946,-82.5540],
    zoom: 11,
    maxZoom: 18,
    minZoom: 11,
    detectRetina: true
});

L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png').addTo(mymap);

### Airbnb Listings

var airbnb_listings = null;

var colors = chroma.scale('Dark2').mode('lch').colors(3);

for (i = 0; i < 3; i++) {
    $('head').append($("<style> .marker-color-" + (i + 1).toString() + " { color: " + colors[i] + "; font-size: 25px; text-shadow: 0 0 3px #ffffff;} </style>"));
}

airbnb_listings = L.geoJson.ajax("assets/airbnb_listings.geojson",{
    onEachFeature: function (feature, layer) {
        layer.bindPopup(feature.properties.property_t);
    },
    pointToLayer: function(feature, latlng) {
        var id = 0;
        if (feature.properties.property_t == "Entire house") { id = 0; }
        else if (feature.properties.property_t == "Private room in house")  { id = 1; }
        else { id = 2;}
        return L.marker(latlng, {icon: L.divIcon({className: 'fab fa-airbnb marker-color-' + (id + 1).toString() })});
    },
    attribution: 'Airbnb Listings &copy; Inside Airbnb | Asheville Zoning Districts &copy; City of Asheville Open Data | Base Map &copy; CartoDB | Map: JSugg'
}).addTo(mymap);
colors = chroma.scale('Purples').colors(5);

function setColor(density) {
    var id = 0;
    if (density > 106) { id = 4; }
    else if (density > 79 && density <= 106) { id = 3; }
    else if (density > 52 && density <= 79) { id = 2; }
    else if (density > 25 &&  density <= 52) { id = 1; }
    else  { id = 0; }
    return colors[id];
}

function style(feature) {
    return {
        fillColor: setColor(feature.properties.total_bnbs),
        fillOpacity: 0.4,
        weight: 2,
        opacity: 1,
        color: '#b4b4b4',
        dashArray: '4'
    };
}

L.geoJson.ajax("assets/zoning_districts.geojson", {
    style: style
}).addTo(mymap);
var legend = L.control({position: 'topright'});

legend.onAdd = function () {
    var div = L.DomUtil.create('div', 'legend');
    div.innerHTML += '<b>Airbnbs per District</b><br />';
    div.innerHTML += '<i style="background: ' + colors[4] + '; opacity: 0.5"></i><p>107+</p>';
    div.innerHTML += '<i style="background: ' + colors[3] + '; opacity: 0.5"></i><p>80-106</p>';
    div.innerHTML += '<i style="background: ' + colors[2] + '; opacity: 0.5"></i><p>53-79</p>';
    div.innerHTML += '<i style="background: ' + colors[1] + '; opacity: 0.5"></i><p>26-52</p>';
    div.innerHTML += '<i style="background: ' + colors[0] + '; opacity: 0.5"></i><p> 0-25</p>';
    div.innerHTML += '<hr><b>Property Type<b><br />';
    div.innerHTML += '<i class="fab fa-airbnb marker-color-1"></i><p>Entire house</p>';
    div.innerHTML += '<i class="fab fa-airbnb marker-color-2"></i><p>Private room in house</p>';
    div.innerHTML += '<i class="fab fa-airbnb marker-color-3"></i><p>Other</p>';
    return div;
};

legend.addTo(mymap);

L.control.scale({position: 'bottomleft'}).addTo(mymap);
