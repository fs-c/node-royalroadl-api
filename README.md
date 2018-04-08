# node-royalroadl-api

A small, __unofficial__ fiction data API for [roalroadl.com](https://royalroadl.com), 
written in TypeScript.

All HTML is parsed using [cheerio](https://github.com/cheeriojs/cheerio), relative 
dates are converted to absolute ones using [date.js](https://github.com/matthewmueller/date).

This __will__ break in the future as the public pages are subject to change at any time, 
but since cheerio is very forgiving it is likely that parts will remain functional.

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

## Properties

- `fiction` - an instance of the [FictionService](#FictionService).
- `fictions` - an instance of the [FictionsService](#FictionsService).

## FictionService

All methods of the FictionService return a Promise resolving to a 
`Fiction`, which is defined as follows: 

```typescript
export interface Fiction {
  type: string,
  title: string,
  image: string,
  status: string,
  tags: string[],
  warnings: string[],
  description: string,
  stats: FictionStats,
  author: FictionAuthor,
}

export interface FictionStats {
  pages: number,
  ratings: number,
  favorites: number,
  followers: number,
  views: {
    total: number,
    average: number,
  },
  score: {
    style: number,
    story: number,
    grammar: number,
    overall: number,
    character: number,
  },
}

export interface FictionAuthor {
  id: number,
  name: string,
  title: string,
  avatar: string,
}
```

#### getFiction(id: number): Promise(Fiction)

Returns the fiction at `royalroadl.com/fiction/${id}`. 

#### getRandom(): Promise(Fiction)

Equivalent to `royalroadl.com/fiction/random`, AKA 'Surprise me!'.

## FictionsService

All methods return a Promise resolving to an extension of `FictionBlurb`, 
depending on the enpoint used.

```typescript
export interface FictionBlurb {
  id: number,
  type: string,
  title: string,
  image: string,
  tags: string[],
}

export interface LatestBlurb extends FictionBlurb {
  latest: {
    name: string,
    link: string,
    created: number,
  }[],
}

export interface PopularBlurb extends FictionBlurb {
  description: string,
  stats: {
    pages: number,
    latest: number,
    rating: number,
    chapters: number,
    followers: number,
  },
}

export interface BestBlurb extends PopularBlurb {}

export interface SearchBlurb {
  id: number,
  pages: number,
  title: string,
  image: string,
  author: string,
  description: string,
}
```

#### getLatest(page: number = 1): Promise(LatestBlurb)

Returns the 20 fiction blurbs as they appear on 
`royalroadl.com/fictions/latest-updates?page=${page}`.

#### getPopular(page: number = 1): Promise(PopularBlurb)

Returns the 20 fiction blurbs as they appear on 
`royalroadl.com/fictions/active-popular?page=${page}`.

Note that the average rating here is a lot more precise than the one in `Fiction`. 

#### getBest(page: number = 1): Promise(bestBlurb)

Returns the 20 fiction blurbs as they appear on 
`royalroadl.com/fictions/best-rated?page=${page}`.

#### search(keyword: string, page: number = 1): Promise(SearchBlurb)

Returns the 20 fiction blurbs as they appear on 
`royalroadl.com/fictions/search?keyword=${keyword}&page=${page}`.