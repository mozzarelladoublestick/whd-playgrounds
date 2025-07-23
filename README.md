# whd-playgrounds

Diese Vue 3 Webanwendung zeigt eine Übersicht der [Spielplätze](https://www.data.gv.at/katalog/dataset/bd8b518b-d812-46f9-b367-4c1b660cfc99#resources) in Wien auf einer Karte mit Möglichkeit zur Filterung nach Bezirk. Die Spielplätze werden bei großer Dichte auf der Map geclustert. Beim Hovern über einen Eintrag in der Liste zeigt der zugehörige Marker eine Animation um die Position erkenntlich zu machen, beim Klicken auf einen Eintrag wird der Ausschnitt der Karte mit dem zugehörigen Marker angezeigt. Beim Hovern über einen Marker wird der Name des Spielplatz anhand eines Tooltips angezeigt.

## Tech-Stack
- Vue 3
- [Handbuch Wien Pattern Library](https://handbuch.wien.gv.at/pattern-library/)
- [Leaflet.js](https://leafletjs.com/)

## Potential Improvements

- Aufgrund von CORS-Problemen wurde der Einfachheit halber nicht die Map aus der Pattern Library des [Handbuchs](https://handbuch.wien.gv.at/pattern-library/) verwendet sonder Leaflet direkt eingebunden, was zu einem inkonsistenten Styling (z.B. der Marker) geführt hat. Zusätzlich wurden eine fixe ```height``` bzw  ```width``` für manche Elemente (z.B. Liste, Map) gesetzt, was nicht ideal ist aber bei dem Projekt dieser Größe vertretbar ist.
- Die Optionen der ```wm-select```-Komponente werde in Firefox nicht mit der richtigen Schriftart gerendert.
- ...
  
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
