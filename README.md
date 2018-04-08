# node-royalroadl-api

A small, __unofficial__ fiction data API for [roalroadl.com](https://royalroadl.com), 
written in TypeScript.

```
npm i -s node-royalroadl-api
```

```javascript
const { RoyalRoadAPI } = require('royalroadl-api');

const api = new RoyalRoadAPI();

(async () => {
  const popular = await api.fictions.getPopular();
  const titles = popular.slice(10).map((fic) => fic.title);

  console.log(`The top 10 popular fictions are: ${titles.join(', ')}`);
})();
```