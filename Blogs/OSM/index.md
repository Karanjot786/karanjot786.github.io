#  Showing POI representing accident data on OSM using leafletjs.

## OSM 

OSM stands for OpenStreetMap. It is a collaborative project to create a free and open-source map of the world, which can be used and edited by anyone. OpenStreetMap is built by a community of volunteer contributors who contribute geographic data, such as roads, buildings, landmarks, and points of interest, to create a detailed and comprehensive map of the world.

## leafletjs

Leaflet is an open-source JavaScript library for interactive web maps. It provides a lightweight and flexible solution for displaying maps on websites or web applications. Leaflet makes it easy to add map functionality to web pages, allowing developers to create interactive maps with various features such as markers, lines, polygons, popups, zooming, panning, and more.

## Quick Start

### Preparing your page

Before writing any code for the map, you need to do the following preparation steps on your page:

Include Leaflet CSS file in the head section of your document:
```html
 <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.3/dist/leaflet.css"
     integrity="sha256-kLaT2GOSpHechhsozzB+flnD+zUyjE2LlfWPgU04xyI="
     crossorigin=""/>
```

Include Leaflet JavaScript file after Leaflet’s CSS:
```html
 <!-- Make sure you put this AFTER Leaflet's CSS -->
 <script src="https://unpkg.com/leaflet@1.9.3/dist/leaflet.js"
     integrity="sha256-WBkoXOwTeyKclOHuWtc+i2uENFpDZ9YPdf5Hf+D7ewM="
     crossorigin=""></script>
```

Put a div element with a certain id where you want your map to be:

```html
 <div id="map"></div>
```

Include script file in the body section of your document:
```html
  <script src="index.js"></script>
```


Make sure the map container has a defined height, for example by setting it in CSS:
```css
#map { height: 99vh; }
```

### Script file

```javascript
// Fetch the CSV file using Fetch API
fetch('data.csv')
  .then(response => response.text())
  .then(data => {
    // Parse the CSV data
    const rows = data.split('\n');
    const headers = rows[0].split(',');
    const jsonData = [];

    for (let i = 1; i < rows.length; i++) {
      const row = rows[i].split(',');
      const rowData = {};
      for (let j = 0; j < headers.length; j++) {
        rowData[headers[j]] = row[j];
      }
      jsonData.push(rowData);
    }

    // Create Leaflet map
    const map = L.map('map').setView([30.788823, 75.8498616], 13); // Set initial map view
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { // Add OpenStreetMap tile layer
      attribution: '© OpenStreetMap contributors'
    }).addTo(map);

    // Define custom icon
    const customIcon = L.icon({
      iconUrl: 'https://gndec.ac.in/~hsrai/maps/coe/coe2.jpeg', // Specify the URL of the custom icon image
      iconSize: [20, 20], // Specify the size of the icon
      iconAnchor: [16, 16] // Specify the anchor point of the icon
    });

    // Loop through the data and add markers to the map with custom icon
    for (let i = 0; i < jsonData.length; i++) {
      // Check for valid latitude and longitude values
      if (jsonData[i].lat !== undefined && jsonData[i].lon !== undefined && !isNaN(jsonData[i].lat) && !isNaN(jsonData[i].lon)) {
        const lat = parseFloat(jsonData[i].lat); // Convert latitude string to number
        const lon = parseFloat(jsonData[i].lon); // Convert longitude string to number
        const marker = L.marker([lat, lon], {icon: customIcon}).addTo(map); // Add marker with custom icon
        marker.bindPopup(`<b>${jsonData[i].title}</b><br>${jsonData[i].description}`); // Add popup with data

      // Add circle with specified radius
      
      L.circle([lat, lon], {        
      color: "red",
      fillColor: "#f03",
      fillOpacity: 0.5,
      radius: 100,}).addTo(map);
      }
    }
  })
  .catch(error => {
    console.error('Error fetching CSV file:', error);
  });
  ```
