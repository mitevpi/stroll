
![first path](assets/screenshots/tree.png)

# Stroll

[![Generic badge](https://img.shields.io/badge/Docs-Web-Green.svg)](https://nbbj-digital.github.io/stroll/) [![Generic badge](https://img.shields.io/badge/Docs-MD-Green.svg)](docs/README.md) [![Generic badge](https://img.shields.io/badge/Samples-JS-Green.svg)](samples/strollSamples.js) ![NPM](https://img.shields.io/npm/l/@nbbj/stroll.svg)

[![npm](https://img.shields.io/npm/v/@nbbj/stroll.svg)](https://www.npmjs.com/package/@nbbj/stroll) [![npm bundle size](https://img.shields.io/bundlephobia/min/@nbbj/stroll.svg)](https://bundlephobia.com/result?p=@nbbj/stroll) ![npm](https://img.shields.io/npm/dw/@nbbj/stroll.svg) ![npm2](https://img.shields.io/npm/dt/@nbbj/stroll.svg)

![GitHub issues](https://img.shields.io/github/issues/nbbj-digital/stroll.svg) ![David](https://img.shields.io/david/dev/nbbj-digital/stroll.svg) ![Azure DevOps builds](https://img.shields.io/azure-devops/build/PMitev/NBBJ%20Public/3.svg) ![GitHub last commit](https://img.shields.io/github/last-commit/nbbj-digital/stroll.svg)

This is a computational library which finds the most nature-filled walks/paths that can be taken in order to stimulate creativity and boost mental health.
Pathfinding is performed via Weighted Graph computation, with weights being given for characteristics such as proximity to parks, and amount of nature in the field of view.

Originally started at the [TT West AEC Hackathon in Seattle, 2019](http://core.thorntontomasetti.com/aec-tech-2019-seattle/aec-tech-seattle-hackathon/aec-tech-seattle-github-repos/).

## Proof of Concept

Currently, the library computes only an A to B path within the boundaries of the user-defined grid (created from the user's origin and desired radius). Future development will target more route-finding options such as loops, distance constraints, sun/shade preferences, etc.

![first path](assets/screenshots/firstMap.png)

### Working Model

The working model to compute the "Nature Score" of a given point in the urban environment is composed of the following:

1. Green Score - What percentage of an image's dominant color palette is green in color.
2. Park Score - How many public parks/green spaces are near the given point, as computed by Yelp.

Next steps for development are to add the following criteria to the "Nature Score" computation:

1. Trees - Use a ML model with transfer learning to recognize natural feature such as trees, lakes, etc. which may not be captured by the "Green Score" in the color palette.
2. Sun/Shade - Compute "sunny-ness"/"shady-ness" of a given route.
3. Rain/Cover - Compute a route based on cover during a rain event.

## Usage

To use this module, install locally using the command below, or clone this repository and import the .js files directly from source.

```cmd
npm i @nbbj/stroll
```

### Environment Variables

Before using this module, `GMAPS_KEY` and `YELP_KEY` environment variables must be set on your machine with your specific Google Maps API Key and Yelp API Key.

#### User API Usage Notice

Please be advised that this module will request Google Street View images from the Google Maps API, and therefore **WILL** [incur a cost](https://developers.google.com/maps/documentation/streetview/usage-and-billing). Managing the cost and/or other API-specific limits/resource constraints are entirely the responsibility of the user using this module.

### Imports

Imports can be done through the aggregating index.js file or via individual members.

```js
const Stroll = require('./index.js'); // from source
const stroll = require('@nbbj/stroll') // from npm
```

```js
// from source
const ColorParse = require('./ColorParse');
const YelpData = require('./YelpData');
const WeatherData = require('./WeatherData');
const RouteData = require('./RouteData');

// from npm
const { RouteData } = require('@nbbj/stroll');
const { ColorParse } = require('@nbbj/stroll');
```

### Methods

#### Pathfinding Data

Building graphs and calculating paths of travel.

```js

// Create a custom grid around a origin lat/long
let grid = RouteData.GetPointGrid(47.660273, -122.409887, 1, 0.5);

RouteData.GetGraph(grid, 0.7) // get a graph from point grid
  .then(graph => RouteData.FindAllNaturePaths(graph)) // find all possible paths
  .then(paths => RouteData.FindTopNaturePaths(paths)) // return sorted paths
  .then(results => {
    console.log(results); // do something with results (array of all possible paths)
  }).catch(err => console.error(err));
```

#### Yelp Data

Yelp data for nearby public parks/green amenities. Used for finding public parks and other notable green spaces near any point on earth (lat/long).

```js
YelpData.ParkSearch(47.660273, -122.409887, 1000).then(results => {
  console.log(results);
});
```

#### Weather Data

Weather data analysis. Currently not used in the nature score calculation, but itended to be added.

```js
let sunData = WeatherData.GetSunPositionToday(47.6694956, -122.31547389999999);
console.log(sunData);
```

#### Color Palette Analysis

Color palette analysis in field of view. Used for calculating how much of the visible color palette at any point on earth (lat/long) is green (nature).

```js
ColorParse.GetPalette(46.414382, 10.013988, 151.78).then(colors => {
  console.log(colors);
});
```

```js
ColorParse.GetPaletteNames(46.414382, 10.013988).then(names => {
  console.log(names);
})
```

```js
ColorParse.GetPaletteAnalysis(47.660259, -122.408417).then(result => {
  console.log(result);
})
```

## Development

### Building

The module can be built by running `npm run build` in the root directory of this repository. Documentation is built using the [Documentation module](https://www.npmjs.com/package/documentation) from npm, and by running `npm run doc` in the root directory of this repository. This will create [markdown](docs/README.md) and [HTML documentaion](docs/index.html).

### Testing

Testing is handled using [jest](https://jestjs.io/) and code coverage is evaluated using [nyc](https://www.npmjs.com/package/nyc). Tests can be initiated by running `npm test` in the root directory of this repository.

## Commands

The following commands are available during development.

```sh
npm test # run tests with Jest
npm run coverage # run tests with coverage and open it on browser
npm run lint # lint code
npm run docs # generate docs
npm run build # generate docs and transpile code
```