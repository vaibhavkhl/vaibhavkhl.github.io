---
layout: post
title: Rails Api & AngularJS
---

I love using [Rails Api](https://github.com/rails-api/rails-api) with [AngularJS](https://angularjs.org/) and i think it is the best combination for rapid development.

We will learn how to integrate them in this post.

###Folder Structure

We will have separate directories for server(rails api) and client(angular) codebase.
{% highlight sh %}
--client
--server
--README.md
{% endhighlight %}

So lets begin and create a new rails application.

```rails-api new server --skip-sprockets
```

We will use yeoman to generate our angular app. If you haven't used it, first go checkout [yeoman](http://yeoman.io/), [grunt](http://gruntjs.com/) & [bower](http://bower.io/).

```yo angular client
```

Run this command inside client folder.

Run rails server and grunt serve to check everything is working fine.

### Serving angular app through rails
Before building any thing first lets see how we will serve our frontend code through rails.

Run ```grunt build``` command inside client folder to build our static assests.
This grunt task runs multiple task internally and prepares our code to be served and deployed.
I suggest checking Gruntfile.js to see how this task works.

This builds a dist folder. Copy the contents of this folder and paste it in public folder inside server directory.

If you head over to [localhost:3000]() now you will see this-
![](assets/rails-angular-blog.png)

This shows that our angular app is getting served through rails server.

You can create a rake task or grunt task to automate this process.

### Deploying to heroku
If you are to deploying this to heroku, set

```config.serve_static_assets = true```

in ```server/config/environments/production.rb``` file.

Deploy using this command

```git subtree push --prefix server heroku master```

as we want to push only server folder instead of complete root directory.

#### Its not complete yet. I will update it where we will create a api and consume it using angular.





