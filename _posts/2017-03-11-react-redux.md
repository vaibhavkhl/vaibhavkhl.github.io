---
layout: post
title: Building a react-redux app
comments: True
---

In this post we will only cover how to integrate React & Redux and will not go into the basics of react and redux.

We will build a simple app which consumes lastfm api and allow users to search artists.

We will use official [create-react-app](https://github.com/facebookincubator/create-react-app) to scaffold our app.
This does all the configuration of babel and webpack for us.

So lets install this npm package and create our app

{% highlight sh %}
npm install -g create-react-app

create-react-app my-app
cd my-app/
npm start
{% endhighlight %}

Now our react app is up and running.

Currently our index.js file looks like [this](https://github.com/vaibhavkhl/reddit-react-redux/blob/d98a1dd62c21bbc7659cb7b990a64cd1dd7e852f/src/index.js)

{% highlight sh %}
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
{% endhighlight %}

To integrate redux we will need two packages [redux](https://github.com/reactjs/redux) and [react-redux](https://github.com/reactjs/react-redux). Lest install them

{% highlight sh %}
npm install --save redux
npm install --save react-redux
{% endhighlight %}

To understand react redux integration first we will have to understand the idea of separating presentational and container components. Read it [here](http://redux.js.org/docs/basics/UsageWithReact.html#presentational-and-container-components) first.
So presentational components will be normal react components and their purpose will be how things look and they are not aware of redux. Container components on the other hand will subscribe to redux state and will do data fetching, state updates etc.

Lets build a presentation component first and then we will build a container component.
{% highlight sh %}
import React, { Component } from 'react';

class Search extends Component {
  render() {
    return (
      <div>
        Search music artist <input type='text' onChange={(e) => this.props.fetchPosts(e.target.value)}/>
      </div>
    );
  }
}

export default Search;
{% endhighlight %}

Container component
{% highlight sh %}
import React, { Component } from 'react';
import { connect } from 'react-redux'
import Search from './Search.js'
import {fetchPosts, fetchPostsSuccess} from '../actions/index.js';

const mapDispatchToProps = (dispatch) => {
  return {
    fetchPosts: (query) => {
      dispatch(fetchPosts(query)).then((response) => {
        dispatch(fetchPostsSuccess(response.payload.data))
      });
    }
  }
}

const SearchContainer = connect(
  null,
  mapDispatchToProps
)(Search)

export default SearchContainer;
{% endhighlight %}

So here ```connect``` function provided by [react-redux](https://github.com/reactjs/react-redux) creates a new component that wraps around our existing search component.
This pattern is called [Higher-Order Components](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750#.f8xlk87pc).
To use connect we also need to define two special functions called ```mapStateToProps``` and ```mapDispatchToProps```.
```mapStateToProps``` transforms the current Redux store state into the props that we want to pass to our presentational component. And ```mapDispatchToProps``` receives the ```dispatch()``` methods and return callback props that we want to inject into our presentational component.

After integrating redux our index.js file will look like this
{% highlight sh %}
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux'
import { createStore, applyMiddleware } from 'redux'
import App from './App';
import './index.css';
import redditApp from './reducers/index.js'
import promise from 'redux-promise';

let store = createStore(redditApp, applyMiddleware(promise))

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

{% endhighlight %}

So lets see what has changed here. There is something new called as Provider ```<Provider store={store}></Provider>``` which we have imported from react-redux.
And this provider takes store created from ```createStore``` of redux as a prop. So what is happening here?
All container components need access to the Redux store so they can subscribe to it. To do this we use special ```Provider``` component which makes the store available to all container components.

To make async data fetching calls we are using promise based [axios](https://github.com/mzabriskie/axios) library. But to dispatch the resolved value of the promise we will need to use [redux-promise](https://github.com/acdlite/redux-promise) [middleware](http://redux.js.org/docs/api/applyMiddleware.html).

Next - See the source [code](https://github.com/vaibhavkhl/reddit-react-redux) for this app.
Read official [react](https://facebook.github.io/react/docs/hello-world.html) and [redux](http://redux.js.org/docs/introduction/) docs.
