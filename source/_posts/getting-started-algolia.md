---
title: Getting Started with Algolia
date: 2018-03-19
cover_index: /assets/algolia.jpg
cover_detail: /assets/algolia.jpg
---

Oh hey there 👋! Have you had the pain of doing search from scratch? Or the feeling of daunt when your PM is like “you know what would be great? If we could have a search bar on website!” and your first thought is...

![awwcmon](http://www.reactiongifs.com/r/hmwrk.gif)

Yeah, I’ve felt that pain all too many times before. Honestly, I avoided it like the plague a few sprints because even when I did get the search all sorted, I felt like it wasn’t _great_ and on top of that I would be half-way through documentation and wondering _wtf, where is that module suppose to go??_ really, not super fun. 

Nowadays though, we have some great tools and services that makes this much easier for us. RIP building search from scratch days. Gah, I love technology. Making my life easier one day at a time. 

This is one of the reasons I started playing with and eventually, joined the team at Algolia. I don’t want this to be one of those articles where you’re reading this like “oh god, she’s pitching me”. No, I genuinely would love to share with you what I learned with getting started at Algolia and how you can get up and coding stat. So let's dive in with some steps you need to get search ready to go. 

# Obtaining your API keys

![algolia keys marked up](https://cdn.glitch.com/45e6d35c-2e10-4020-8ad3-d5f1b9d3aae6%2FalgoliaAPIkeysMarkedUp.png?1514567735627)

Start by creating an account at [Algolia](https://www.algolia.com/cc/devto). Then grab your credentials from your [dashboard](https://www.algolia.com/licensing). You'll want to copy your `App Id`, `Search Only API Key` and `Admin API Key`.

Once this is complete, add them to whatever you’re using for your  `.env file` so your app knows how to connect to your Algolia application and index. Boom! That’s the hard part.

# Connecting your data source
If you have your data ready to go, we can begin by creating a function to call that url and push it into an index. Let’s use JavaScript as our language here. 

```javascript
const data_url = "https://raw.githubusercontent.com/algolia/datasets/master/movies/actors.json"

function indexData(data_url){
  return axios.get(data_url,{})
  .then(function(response){
    console.log(response.data[0]);
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
}
```

We will build on this function, but for now, it’s just curling that `data_url` we pass it, logging the first record we have in the data and returning out of the loop. We are using axios here for our api calls. Axios is a Javascript library used to make http requests from node.js or XMLHttpRequests from the browser and it supports the Promise API that is native to JS ES6. The advantage of using this over others, is that it performs automatic transforms of JSON data.

# Prepping Data for Algolia
Now that we are making our call, let’s start using our Algolia account that we set up earlier and upload data to our index! We’ll do this in two steps, first we will iterate over the data we have received back from our axios.get call and make an array of objects. This will let us use only the data we want to highlight in our index. Then, once that is complete we can send to Algolia’s index..

_Step 1:_ Instead of just returning a success response, let’s create a function to handle that upload by passing the response of our axios.get call. 


```javascript
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
}
```
Now in this function, we want to go through our data points and create our algolia objects, which should be pretty straight forward loop.

```javascript
function dataToAlgoliaObject(data_points){
  var algoliaObjects = [];
  for (var i = 0; i < data_points.length; i++) {
    var data_point = data_points[i];
    var algoliaObject = {
        objectID: data_point.objectID,
        name: data_point.name,
        rating: data_point.rating,
        image_path: data_point.image_path,
        alternative_name: data_point.alternative_name
      };
    algoliaObjects.push(algoliaObject);
  }

  return algoliaObjects;
}
```

_Step 2:_ Now that we have built up our objects, they are ready to be sent to Algolia! ![kermit party](https://media.giphy.com/media/D1iAIpluG6ygE/giphy.gif)
 Let’s change a few things with our indexData function. We can chain a `.then` on our call because of axios promise structure and use `async` and `await` to make sure nothing gets out of sort before we upload data.


```javascript
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(async (response) => {
     await sendDataToAlgolia(response);
     return;
  })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
}
```
# Send Data to Algolia
Now, let’s write that `sendDataToAlgolia` function. Here is where we will be utilizing the keys we put in our `.env` file earlier. We will also need to be sure we have something that initiates our index and passes it the name of the index that we want to use to store our data. Since the dataset we are using is around movie actors, that seems like a good enough name as any to use.

```javascript
const algoliaClient = algoliasearch(process.env.ALGOLIA_APP_ID, process.env.ALGOLIA_ADMIN_API_KEY);
const algoliaIndex = algoliaClient.initIndex("movie-actors");  

function sendDataToAlgolia(algoliaObjects){
  return new Promise((resolve, reject) => {
    algoliaIndex.addObjects(algoliaObjects, (err, content) => {
      if(err) reject(err);
      resolve();
    })
  });
}
```

# Setting your settings
We’ve got data! But, now we want to tell Algolia how we want that data to be used. We can do this via the dashboard or code. I personally like to do via code, so let’s review that here. We have a _lot_ of options, but we can do the basics:

_searchableAttributes_: list here what you would like searchable from the algoliaObject you created
_attributesToHighlight_: highlights the text you are searching for
_customRanking_: choose how your data will be displayed, desc() or asc()
_attributesToRetrieve_: return these attributes for dislaying in search results

```javascript
async function configureAlgoliaIndex(){
  algoliaIndex.setSettings({
    searchableAttributes: [
      'name'
    ],
    attributesToHighlight: [
      'name'
    ],
    customRanking: [
      'desc(rating)'
    ],
    attributesToRetrieve: [
      'name', 
      'rating',
      'image_path'
    ]
  });
}
```
Let’s add this function once we have successfully uploaded data to our index.

```javascript
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(async (response) => {
     await sendDataToAlgolia(response);
     return;
  })
  .then(async () => {
     await configureAlgoliaIndex();
     return;
  })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
}
```

Wow, now we have data in our index and the way we want it. So, we are done with the back end of things, now onto the part where people can _see_ our sweet sweet data and search for it.

# Connecting the front end
Algolia has something called widgets, which allow us to quickly add sections to our HTML page without writing lots of new code. Elements such as a search bar and a place for our Algolia objects to be seen on the page can be easily added in with a few lines of JavaScript. Let’s head over to our client file.

We want to start by creating an instance of instant search we can utilize in our app. You can use cookies to pass this data from the server to the front end or you can have the keys in there. For length sake, we’ll show keys in here.

```javascript
$(document).ready(function() {
  var instantsearch = window.instantsearch;

  // create an instantsearch instance with our app id and api key
    var search = instantsearch({
      appId: Cookies.get('app_id'),
      apiKey: Cookies.get('search_api_key'),
      indexName: Cookies.get('index_name'),
      urlSync: true,
      searchParameters: {
        hitsPerPage: 3
      }
    });
```

Now let’s connect a search input to your html so users have a place to search.
```javascript
search.addWidget(
  instantsearch.widgets.searchBox({
    container: '#search-box',
    placeholder: 'Search your favorite actors'
  })
);
```
Now we want to adds the results of our data, and in the return statement you can change what you want shown.
```javascript
  search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits',
      hitsPerPage: 12,
      templates: {
        empty: `<div class="col-md-12" style="text-align: center;"> We didn't find any results for the search <em>\"{{query}}\"</em></div`,
        item: function(hit) {
          try {
            return `
              <div class="col-md-4" style="text-align: center;">
                <p> 
                  <h3 class="hit-text">${hit._highlightResult.name.value}</h3>
                  <img src="https://image.tmdb.org/t/p/w45/${hit.image_path}" height="50" width="50">
                </p>
                <p>
                  Rating: ⭐️ ${hit.rating}
                </p>

              </div>
            `;
          } catch (e) {
            console.warn("Couldn't render hit", hit, e);
            return "";
          }
        }
      }
    })
  );
```
A good search experience shouldn't overwhelm the user with results, so, let’s add pagination to the results we're getting back. 

```javascript
  search.addWidget(
    instantsearch.widgets.pagination({
      container: '#pagination'
    })
  );
```
Last but not least… let’s start the search! This instantiates everything on your page.
```javascript
  search.start();
```

Of course, if you want to skip all this manual work, you can check out our [quickstart app on Glitch](https://glitch.com/~algolia-quickstart), remix it and you’ll have all this code with a basic working app out of the box with about 5 minutes of effort.😉 Happy reading and hope this helped!

![newgirl](https://media.giphy.com/media/2uhnLivbj0S6A/giphy.gif)

