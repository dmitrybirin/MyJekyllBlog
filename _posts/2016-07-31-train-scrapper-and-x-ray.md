---
layout: post
title:  "Train Scrapper and X-Ray"
date:   2016-07-31 23:12:00 +0300
categories: scrappers javascript x-ray nightmare
---

Finally I've got my rzd scrapper working.

I hope to get it on node server someday, so I decide to work with javascript and node.js as platform
and mongoDB as database, cause... well, cause I'm interested in it)

Firstly, of course, I thought it will be easy, just get the phantomjs or selenium to the page and scrap some data.
But after all, it was a nightmare and the [Nightmare.js][njs].
Nightmare is a great instrument that can do a lot of work like going to the pages, clicking everywhere, evaluate scripts...
And it works out of the box.
The problems are:

- in that box is the Electron. Electron is cool but it's > 40Mb module and really I don't need all that heavy machinery in my simple task.
- Nightmare is good with automating the web, but not so good for srapping for my taste.

So, for the actual data scrapping I've used [X-Ray.js][xjs]
X-ray has a great syntax to scrap data and it doing scrapping well.
For the simple example, the code below get the raw HTML `rzdTable`, that Nightmare gave to me, 
and in every row `.trlist__trlist-row` searches for an information for the number and brand selectors.

```javascript
x(rzdTable, '.trlist__trlist-row', [{
    number: '.trlist__cell-pointdata__tr-num | trimNumber',
    brand: '.trlist__cell-pointdata__tr-brand | trimN']})
```

... and transform scrapped data into into good old array of objects 

```
[ { number: '771А', brand: '«САПСАН»' },
  { number: '779А', brand: '«САПСАН»' },
  { number: '019У', brand: '«МЕГАПОЛИС»' },
  { number: '003А', brand: '«Экспресс»' },
  { number: '025А', brand: '«Смена-А. Бетанкур»' },
  { number: '053Ч', brand: '«ГРАНД ЭКСПРЕСС»' },
  { number: '001А', brand: '«Красная стрела»' } ...]
```

The cool thing, you can even operate with more deep level data.
For example, rzd site has train information in rows. 
But prices for car types in rows of these rows. That can be managed by these lines:

```
 x(rzdTable, '.trlist__trlist-row', [{
            number: '.trlist__cell-pointdata__tr-num | trimNumber',
            cars: x('.trlist__table-price tbody tr', [{
                type: 'td',
                freeSeats: '.trlist__table-price__freeseats | toInt',
                price: '.trlist__table-price__price span | trimRub | toInt'   
            }])
```  

Noted the `property: 'selector | smth'` construction?
It's filters. Filters helps to transorm scrapped data on the fly with your custom functions.

On the mongo part of the scrapper, I wanted to say, that [mongojs library][mjs] for the mongo driver is heaven sent. 
It much easier to work with the same connection of db throught your app.

Well, it's enough for the moment.
Scrapper still in ongoing-development-phase, but you can find it on my [github page][gh]

D.

[gh]:https://github.com/dmitrybirin/trainscrapper
[njs]:http://www.nightmarejs.org
[mjs]:https://github.com/mafintosh/mongojs
[xjs]:https://github.com/lapwinglabs/x-ray