Fetching, storing, and generating map tiles for use in Leaflet, Google maps, etc.

`npm install maptile`

## Features
+ Automatic canvas-based tile generation for GeoJSON points and polygons.
+ Automatic polygon simplification using ogr2ogr (must have GDAL installed) for reducing complexities at high zoom levels.
+ Helper projections (lat/lon, meters, pixels)
+ Fit into custom/existing server structure; not forced to install standalone tile-server.
+ Optional caching; live data when caching is off (or low).

## Forthcoming
+ Documentation and tests, like every good module
+ Overridable storage and fetch procedures, currently just storing and fetching from relative path. Would be nice to control where these went on a needs basis.
+ Add some kind of buffer method to expand the bounds. For example, a point near the edge of a tile might have a radius that extends beyond the edge of the tile but would not be drawn in the builder for the neighbor tile due to the origin being in the former tile. This would be useful for complex builder operations that aren't simply rendering polys or other queryable shapes onto a map.

------

A couple of the math transforms were repurposed from Leaflet's source: https://github.com/Leaflet/Leaflet

------

## Requirements

You'll need to install Cairo for canvas support. Check out the instructions here, it's fairly simple: http://github.com/learnboost/node-canvas.

------

Here is some code for using this currently, make sure to check out the example.js file.

```javascript
// Define the maptile object
var coolMap = new maptile.Map({
  path: __dirname + '/../public/tiles/some-map/{z}/{x}/{y}.png',
  builder: function(tile, next) {
    someKindaQuery.findWithinPoly(geojson, function(err, points){
      tile.drawGeojson(points, {fillStyle: "rgba(165,46,25,0.8)"}, next)
    });
  }
})

// Get a map tile, in an express endpoint for example

var tileEndpoint = function(req, res){
  var coords = {
      x: parseInt(req.params.x)
    , y: parseInt(req.params.y)
    , z: parseInt(req.params.z)
  }

  coolMap.getTile(coords, function(err, buffer){
    res.send(buffer) // png buffer data
  })  
}
```
