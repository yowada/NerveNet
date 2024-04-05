## Hosting a site for map in synology nas

- The nas we are using is synology nas DS920, where a virtual machine (Debian 12 server) is being operated.This virtual machine is responsible for hosting the website.
- We've utilized a KML file that contains geographic data points for Dullu, Nepal. To make this data compatible with React Leaflet, we converted the KML file into GeoJSON format. This conversion allows us to efficiently work with and display these custom data points on the map within our React application.
- The frameworks used for the website are:
    - [Nextjs](https://nextjs.org/)
    - [Typescript](https://www.typescriptlang.org/)
    - [leaflet](https://leafletjs.com/)
    - [react-leaflet](https://react-leaflet.js.org/)
- To access the  codebase for the website [click here](https://github.com/sdhrt/dullu-map)
<hr>
To access the site <a href="//124.41.221.205:5173" target="blank">124.41.221.205:5173</a>