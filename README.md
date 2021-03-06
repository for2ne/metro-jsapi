MetroJS API [![Build Status](https://travis-ci.org/ymaps/metro-jsapi.svg?branch=master)](https://travis-ci.org/metrofun/metro-jsapi)
=========
MetroJS API is a tool for integrating interactive Yandex.Metro schemes into a website. Using this API, you can manage schemes and their contents, as well as create various applications. MetroJS is built with [Yandex.Maps JavaScript API].

![Example Image](examples/stationAnnotate.png)

Install
---------
```console
# Clone this repo (or your fork).
git clone https://github.com/metrofun/metro-jsapi.git
# Install all dependencies
npm install

# If you wanna run tests, install also dev dependencies and PhantomJS
npm install --dev
npm install -g phantomjs
```
Quick start
---------
###Step 1. Enable the API
Before using the API features, you must make sure your browser loads all required modules from Yandex.Maps API. Note, metro-jsapi requires Yandex.Maps version 2.1 or higher.

```html
<head>
    ...
    <!-- please note ymaps version is 2.1 -->
    <script src="//api-maps.yandex.ru/2.1/?lang=ru_RU&load=&mode=debug"></script>
    <script src="/metro.js" type="text/javascript"></script>
    ...
</head>
```

###Step 2. Install "metro-data"
All SVG schemes are stored in the dependency package – [metro-data].
By default images are loaded from "/node_modules/metro-data" path
```console
npm install git://github.com/ymaps/metro-data.git
```

###Step 3. Create a map container
Create a nonzero-size visible container where the map will be placed. Any block type of HTML element can be used as the container (for example, the div element with a set height and width). The map will completely fill this element.

```html
<body>
    <!-- The container's unique ID (id) will be used in the next step -->
    <div id="containter-id" style="width: 500px; height: 400px"></div>
</body>
```

###Step 4. Create a map
The map should be created after the web page has completely loaded. This will ensure that the map container has been created and can be accessed by its id. To initialize the map after loading the page, you can use the ready() function.
```html
<script type="text/javascript">
    ymaps.modules.require(['TransportMap']).spread(function (TransportMap) {
        TransportMap.create('moscow', 'containter-id').then(function (transportMap) {
            console.log('map created');
        }).done();
    });
</script>
```

Reference
---------

###TransportMap
Main class for creating and managing a transport map.

####Constructor
Parameter | Parameter properties | Type | req | Description
--- | --- | --- | :---: | ---
alias | | String | ✔ | City alias.Supported values:'moscow', 'spb', 'kiev', 'kharkov', 'minsk'
container | | String/Element | ✔ | Reference to an HTML element that contains the map, or the ID of this HTML element.
state | | Object | | Map parameters
| | center | Number| | Coordinates of the map center from [-1, -1] to [1, 1]. Default value: [0, 0];
| | shaded | Boolean | | Boolean flag to shade or not a map. Default value: false
| | selection | Number[]/Number | | List of selected **Station** codes. Default value: []
options | | Object | | Map options
| | maxZoom | Number| | Default value: 3
| | minZoom | Number | | Default value: 0
| | selectOnClick | Boolean | | Default value: true
| | shadeOnSelect | Boolean | | Default value: false
| | lang | String | | Default value: 'ru'
| | path | String | | Default value: 'node_modules/metro-data/'

####Fields
Name | Type | Description
--- | --- | ---
events | [event.Manager] | Map event manager. Supports subscriptions. Fires events.
station | **StationCollection** | **StationCollection** instance for the current map.
####Events
All [original events](http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Map.xml#events-summary) from the ymaps map and some additional:

| Name | Description |
| --- | --- |
selectionchange | Instance of the [Event] class. Fires when somebody selected/deselected a **Station** programmatically or by click. Names of fields that are available via the "[Event].get" method: target –  instance of **Station**, type – 'selected'/'deselected'.
shadechange | Instance of the [Event] class. Fires when somebody shades/unshades the map. Names of fields that are available via the "[Event].get" method: type – 'shade'/'unshade'.
####Methods
Name | Returns | Description
--- | --- | ---
shade() | | Fade in a map.Fires 'shadechange' event.
unshade() | | Fade out a map. Fires 'shadechange' event.
getCenter() | Number[] | Returns the coordinates of the current map center. **Note**:this is not a geo coordinates;
setCenter(center, zoom, options) | [Vow].Promise | See [ymaps.Map.setCenter]
getZoom() | Number | Returns the current map zoom level.
setZoom(zoom, options) | [Vow].Promise | See [ymaps.Map.setZoom]
getSchemeId() | Number | map Id  of the current scheme.
getMap() | [Map] | Returns ymaps [Map] instance.
destroy() |  | Destroys the map.
####Static methods
Name | Returns | Description
--- | --- | ---
create() | [Vow].Promise | Factory method. Accepts the same arguments, as the constructor


###StationCollection
Inherits [Collection].

Class for creating and managing **Station** instances.

####Constructor
Parameter | Parameter properties | Type | req | Description
--- | --- | --- | :---: | ---
schemeView | | **SchemeView** | ✔ | schemeView
selectOnClick | | Boolean | ✔ | Weather to select stations by click
####Fields
See [inherited fields](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/Collection.xml#properties-summary).
####Events
All [inherited events](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/Collection.xml#events-summary) and some additionals:

| Name | Description |
| --- | --- |
selectionchange | Instance of the [Event] class. Fires when somebody selected/deselected a **Station** programmatically or by click. Names of fields that are available via the "[Event].get" method: target –  instance of **Station**, type – 'selected'/'deselected'.
####Methods
All [inherited methods](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/Collection.xml#methods-summary) and some additionals:

Name | Returns | Description
--- | --- | ---
setSelectOnClick(bool) | |  Enable/disable stations selection by click
select(codes) | |  Selects stations by codes
deselect(codes) | |  Deselects stations. Without arguments deselects all
getSelection | Number[] | Returns codes of all selected stations
getByCode(code) | **Station** | Returns **Station** instance by its code number
search(request) | [Vow].Promise | Search stations by words starting with the letters %request%. And returns promise with matches
###Station
Inherits [collection.Item].

####Constructor
Parameter | Parameter properties | Type | req | Description
--- | --- | --- | :---: | ---
metadata | | Object | ✔ | Station meta description
| | labelId | String/Number | ✔ | Label node id
| | name | String | ✔ | Station title
schemeView | | **SchemeView** | ✔ |
options | | Object  | | Options will be passed to [collection.Item] constructor
####Fields
See [inherited fields](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/collection.Item.xml#properties-summary).

Name | Type | Description
--- | --- | ---
code | Number | Station code number
title | String | Station name
####Events
All [inherited events](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/collection.Item.xml#events-summary) and some additionals:

| Name | Description |
| --- | --- |
selectionchange | Instance of the [Event] class. Fires instance was selected/deselected programmatically or by click. Names of fields that are available via the "[Event].get" method: target – this, type – 'selected'/'deselected'.
####Methods
All [inherited methods](http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/collection.Item.xml#methods-summary) and some additionals:

Name | Returns | Description
--- | --- | ---
getLabelNode() | SVGElement | Non-cacheble getter for the label node.
select() | | Selects current station. Fires 'selectionchange' event.
deselect() | | Deselects current station. Fires 'selectionchange' event.
getCoordinates() | [Vow].Promise | Retrieves a pair of world geo coordinates
getPosition() | Number[] | Returns a pair of local CartesianProjection coordinates
annotate(properties, options, dontAddToMap?) | [Vow].Promise | Creates an **Annotation**([Placemark])
###Annotation
Inherits [Placemark].

Provides a placemark with a predefined layout.
####Constructor
See [inherited constructor](http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Placemark.xml#constructor-summary)
####Fields
See [inherited fields](http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Placemark.xml#properties-summary).
####Methods
See [inherited methods](http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Placemark.xml#methods-summary).

Running Tests
---------
This will run Mocha in PhantomJS
```console
npm test`
```

Browser support
---------
Chrome, Firefox, IE 9+, iOSSafari 4+, Android Browser 4+

[Yandex.Maps JavaScript API]:http://api.yandex.com/maps/doc/jsapi/
[metro-data]:https://github.com/ymaps/metro-data
[Vow]:https://github.com/dfilatov/vow/blob/0.3.x/README.md
[event.Manager]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/event.Manager.xml
[Event]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Event.xml
[ymaps.Map.setCenter]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Map.xml#setCenter
[ymaps.Map.setZoom]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Map.xml#setZoom
[Map]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Map.xml
[Collection]:http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/Collection.xml
[collection.Item]:http://api.yandex.ru/maps/doc/jsapi/beta/ref/reference/collection.Item.xml
[Placemark]:http://api.yandex.com/maps/doc/jsapi/2.x/ref/reference/Placemark.xml
