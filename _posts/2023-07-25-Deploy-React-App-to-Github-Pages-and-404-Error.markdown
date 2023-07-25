---
layout: post
title:  "Deploy React App to Github Pages and 404 Error"
date:   2023-07-25 12:54:59 +1000
categories: git
---
I am learning how to deploy web apps to github pages.

For those simply structured web apps which consists of just a .html, a .js, and a .css, just go to Settings/Pages/Branch, choose a branch->Save. After two minutes, the site address will show up after I refresh the page. And they work perfectly.

But for react apps, I guess it is because there are too many files for github pages to look up, so it cannot simply generate a domain. And yes, that is [what BUILD means](https://www.freecodecamp.org/chinese/news/making-sense-of-front-end-build-tools/). To publish a not simply structured web app, we need to build it first. And **gh-pages** package helps run the files through a build process.

# 1. Based on:
- I have a react app that has been connected to a Github repository
- The app doesn't rely on back end or database

# 2. Install gh-pages and add scripts
gh-pages package helps run the files through a build process
```
npm install -D gh-pages
```
Add publishing scripts in package.json
```
"scripts": {
	// ...
	"predeploy": "npm run build",
	"deploy": "gh-pages -d build"
}
```
```predploy``` will automatically run before ```deploy```

# 3. Add homepage prop for building for relative path
Add a field called **homepage** in package.json, at the same level as dependencies. 
This is because by default, Create React App produces a build assuming your app is hosted at the server root. To override this, specify the homepage, in my case [https://dianaleo.github.io/words_learning_game](https://dianaleo.github.io/words_learning_game) .
```
{
  // ...
  "homepage": "https://{username}.github.io/{repo-name}",
  "dependencies": {
    // ...
  },
  "scripts": {
    // ...
  },
  //...
}
```
But GitHub Pages doesn’t support routers that use the HTML5 ```pushState``` history API under the hood (for example, React Router using ```browserHistory```).

It assumes the project is being hosted at ```/words_learning_game```(my repo name). But on localhost, the project is not being hosted at ```/words_learning_game```. So the paths being requested for js and css are messed up.

When there is a fresh page load for a url like [https://dianaleo.github.io/words_learning_game/...](https://dianaleo.github.io/words_learning_game/), where ```/words_learning_game/...``` is a frontend route, the GitHub Pages server **returns 404** because it knows nothing about ```/words_learning_game/...```.

#### 3.1 Serving Apps with Client-Side Routing
If you use routers that use ```browserHistory```, many static file servers will fail. If you used React Router with a route for /todos/42, the development server will respond to localhost:3000/todos/42 properly, but a production build will not.

solution: 
- run
 ```export PUBLIC_URL="/words_learning_game"``` 
in the terminal
- Add a basename to your parent ```Router``` element, setting basename to the name of your repo – like so: ```Router basename="/words_learning_game"```

![Add basename for client side routing.png](https://dianaleo.github.io/assets/images/25-07-2023/Add-basename-for-client-side-routing.png)

reference:
[https://create-react-app.dev/docs/deployment/](https://create-react-app.dev/docs/deployment/)
[https://dev.to/kathryngrayson/deploying-your-cra-react-app-on-github-pages-2614](https://dev.to/kathryngrayson/deploying-your-cra-react-app-on-github-pages-2614)


# 4. Build and push
Run ``` npm run build ```. 
It creates a ``` /build ``` directory with a production build of my app.

Add, commit, and push to github.

# 5. Generate a gh-pages branch
After pushing, there is a ```./build``` folder in ```master/main``` branch of github repo.
Run
 ```git subtree push --prefix=build origin gh-pages```
to copy the ```./build``` folder to a new branch ```gh-pages```.
After 2 minutes, the website is available to visit.

![Change branch to gh-pages.png](https://dianaleo.github.io/assets/images/25-07-2023/Change-branch-to-gh-pages.png)

From now, everytime I modify and push it, I have to delete the gh-pages branch
```git push origin --delete gh-pages```

and regenerate it again using
```git subtree push --prefix=build origin gh-pages```.

reference:
[https://github.com/vortesnail/blog/issues/8](https://github.com/vortesnail/blog/issues/8)


