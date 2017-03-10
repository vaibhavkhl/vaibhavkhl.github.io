---
layout: post
title: Building a react-redux app
comments: True
---

We will build a app which consumes lastfm api and allow users to search artists.

We will use official [create-react-app](https://github.com/facebookincubator/create-react-app) to scaffold our app.
This does all the configuration of babel and webpack with live reloading.

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

To understand react redux integration first we will have to understand the idea of separating presentational and container components. Read it [here](http://redux.js.org/docs/basics/UsageWithReact.html#presentational-and-container-components)
So presentational components will be normal react components and their purpose will be how things look and they are not aware of redux. Container components on the other hand will subscribe to redux state and will do data fetching, state updates etc.

Lets build a presentation component first and then we will build a container component
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

After integrating redux our index.js file will look like this
{% highlight sh %}
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import App from './App';
import './index.css';

let store = createStore()

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
{% endhighlight %}

So lets see what has changed here. There is something new called as Provider ```<Provider store={store}></Provider>``` which we have imported from react-redux.
And this provider takes store created from ```createStore```redux as a prop. So what is happening here?
