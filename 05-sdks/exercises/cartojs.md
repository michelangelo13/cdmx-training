# Webmapping apps with CARTO.js <a name="cartojs"></a>

## CARTO.js

[CARTO.js](https://carto.com/docs/carto-engine/carto-js/) is the JavaScript library that allows to create web mapping apps using CARTO services quickly and efficiently. It's built upon the following components:

* [jQuery](http://jquery.com)
* [Underscore.js](http://underscorejs.org)
* [Backbone.js](http://backbonejs.org/)
* It can use either [Google Maps API](https://developers.google.com/maps/) or [Leaflet](http://leafletjs.com/)

Know more about CARTO.js on the [official documentation](https://carto.com/docs/carto-engine/carto-js/) and this [academy tutorial](https://carto.com/academy/courses/cartojs-ground-up/).

Next sections and an excerpt of the documentation with the most useful methods and a the end you'll find live examples of them that you can modify and hack in real time.

### Create Visualizations and Layers

#### `createVis()`

The most basic way to display your map from CARTO.js involves a call to:

```javascript
cartodb.createVis(div_id, viz_json_url)
```

Couched between the `<script> ... </script>` tags, `createVis` puts a map and CartoDB data layers into the DOM element you specify. In the snippet below we assume that `<div id='map'></div>` placed earlier in an HTML file.

```javascript
window.onload = function() {
  var vizjson = 'link from share panel';
  cartodb.createVis('map', vizjson);
}
```

And that’s it! All you need is that snippet of code, a script block that sources CARTO.js, and inclusion of the CARTO.js CSS file. It’s really one of the easiest ways to create a custom map on your webpage. `createVis` also accepts options that you specifiy outside of the CartoDB Editor. They take the form of a [JS object](http://www.w3schools.com/js/js_objects.asp), and can be passed as a third optional argument.

```javascript
var options = {
  center: [40.4000, -3.6833], // Madrid
  zoom: 7,
  scrollwheel: true
};

cartodb.createVis('map', vizjson, options);
```

### `createLayer()`

If you want to exercise more control over the layers and base map, `createLayer` may be the best option for you. You specifiy the base map yourself and load the layer from one or multiple viz.json files. Unlike `createVis`, `createLayer` needs a map object, such as one created by Google Maps or Leaflet. This difference allows for more control of the basemap for the JavaScript/HTML you’re writing.

A basic [Leaflet map](http://leafletjs.com/reference.html#map-class) without your data can be created as follows:

```javascript
window.onload = function() {
  // Choose center and zoom level
  var options = {
    center: [41.8369, -87.6847], // Chicago
    zoom: 7
  }

  // Instantiate map on specified DOM element
  var map = new L.Map(dom_id, options);

  // Add a basemap to the map object just created
  L.tileLayer('http://tile.stamen.com/toner/{z}/{x}/{y}.png', {
    attribution: 'Stamen'
  }).addTo(map);
}
```

The map we just created doesn’t have any CartoDB data layers yet. If you’re just adding a single layer, you can put your data on top of the basemap from above. If you want to add more, you just repeat the process. We’ll be doing much more with this later. This is the basic snippet to put your data on top of the map you just created. Drop this in below the `L.tileLayer` section.

```javascript
var vizjson = 'link from share panel';
cartodb.createLayer(map, vizjson).addTo(map);
```

## UI Functions

### Tooltips

A tooltip is an infowindow that appears when you hover your mouse over a map feature with vis.addOverlay(options). A tooltip appears where the mouse cursor is located on the map.

To add a tooltip to a map you need to do two steps:

First, define tooltip variable:

```javascript
  var tooltip = layer.leafletMap.viz.addOverlay({
            type: 'tooltip',
            layer: layer,
            template: '<div class="cartodb-tooltip-content-wrapper"><p>{% raw %}{{name}}{% endraw %}</p></div>',
            width: 200,
            position: 'bottom|right',
            fields: [{ name: 'name' }]
  });
```
Second, add tooltip to the map:

```javascript
     $('body').append(tooltip.render().el);
```

### Infowindows

Infowindows provide additional interactivity for your published map, controlled by layer events. It enables interaction and overrides the layer interactivity. A pop-up information window appears when a viewer clicks on a map feature.

In order to add the CARTO.js infowindow you need to add this line within your code:

```javascript
cdb.vis.Vis.addInfowindow(map, layer, ['fields']);
```

However, you can create custom infowindows with different tools (`Moustache.js`, HTML or `underscore.js`). Whatever choice you make, you would need to create a template first and then add the infowindow with the template. Here we will see how to do it using `Moustache.js`.

[Mustache.js](http://mustache.github.io/) is a `logic-less` logic-template. That means that only tags you create templates that are replaced with a value or series of values, it works by expanding tags in a template using values provided in a hash or object.

Example: Custom infowindow template to display `cartodb_id`:

```html
  <script type="infowindow/html" id="infowindow_template">
    <div class="cartodb-popup v2">
  <a href="#close" class="cartodb-popup-close-button close">x</a>
  <div class="cartodb-popup-content-wrapper">
    <div class="cartodb-popup-content">
      <h4>ID</h4>
      <p>{% raw %}{{cartodb_id}}{% endraw %}</p>
    </div>
  </div>
  <div class="cartodb-popup-tip-container"></div>
</div>
</script>
```

Then you can apply the custom infowindow template to the map with:

```javascript
cdb.vis.Vis.addInfowindow(
          map, layer, [columnName],
          {
             infowindowTemplate: $('#infowindow_template').html()
          });
```

### Legends

In order to add legends with CARTO.js you would need to define the elements and colors of the legend with HTML, then you could use the legend classes of CARTO.js to create the legends.

There are two kind of legend classes:

First, `cartodb-legend choropleth`, applied in Choropleth maps:

```html
<div class='cartodb-legend choropleth'>
    <div class="legend-title">Population</div>
      <ul>
        <li class="min">
          1256
        </li>
        <li class="max">
          8300
        </li>
        <li class="graph count_441">
        <div class="colors">
        <div class="quartile" style="background-color:#FFFFB2"></div>
        <div class="quartile" style="background-color:#FED976"></div>
        <div class="quartile" style="background-color:#FEB24C"></div>
        <div class="quartile" style="background-color:#FD8D3C"></div>
        <div class="quartile" style="background-color:#FC4E2A"></div>
        <div class="quartile" style="background-color:#E31A1C"></div>
        <div class="quartile" style="background-color:#B10026"></div>
        </div>
        </li>
      </ul>
  </div>
```

Second, `cartodb-legend category`, applied in simple or category maps:

```html
    <div class='cartodb-legend category'>
      <div class="legend-title" style="color:#284a59">Countries</div>
        <ul>
          <li><div class="bullet" style="background-color:#fbb4ae"></div>Spain</li>
          <li><div class="bullet" style="background-color:#ccebc5"></div>Portugal</li>
          <li><div class="bullet" style="background-color:#b3cde3"></div>France</li>
        </ul>
      </div>
```

## Examples

Each example points to a real time viewer showcasing a different procedure using CARTO.js. Feel free to change and play with the code and refresh the webpage to return to the initial state.


TODO
----