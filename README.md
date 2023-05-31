# Vue Map

This repo allows a user to acquire their current location, search locations on a map (indicated by a marker), and see a search history in a paged table.

Using Geolocation API and Geoapify's reverse geocoding API, user can click to acquire location by clicking on a button and allowing the browser to access their location.

The location search is powered by static images from Geoapify using the coordinates provided by the returned search results from Geoapify's geocoding API.

This project requires a key from Geoapify API (the free plan will be sufficient for the usage of this project).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
