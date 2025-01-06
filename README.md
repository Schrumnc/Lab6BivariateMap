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
# Airbnb Listings
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
