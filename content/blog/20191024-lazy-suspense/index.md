---
title: React.lazy and Suspense
date: "2019-10-24T18:30:48.169Z"
description: React.lazy and Suspense
---

## Code-Splitting and Lazy Loading in React ##

A few years ago, the trend was for JavaScript developers to concatenate and minify all their JavaScript code. This was deemed the best way to include code into a web-based JavaScript application because it was the most efficient and delivered better performance. The world of front end development moves quickly though and nowadays code-splitting and lazy loading are becoming more and more common. This article will explain why.

### Bundlers ###

If you have been working with React for a little while, you have probably heard of **bundlers** such as Webpack, Rollup, Parcel or Browserify. You may not have dealt with them directly though, because it isn't necessary to know a lot about bundlers to work with React. The commonly used `create-react-app` sets up a bundler behind the scenes (it uses Webpack, in case you were curious) and unless something really specific is required for an app, there is rarely a need to manually edit its default configuration.

All you need to know for now is that when you build a production copy of your application, a bundler is responsible for concatenating (and processing, if necessary) your code to create a 'bundle' which includes all the code required to run your app.

#### Example ####

Here is a very basic example to illustrate the sort of thing bundlers will do (in reality things get much more complicated).

Here's our code for our app:

App.js:

```JavaScript
import { getFullName } from './Utils.js';

console.log(getFullName('Ash', 'Ketchum')); // Ash Ketchum
```

Utils.js:

```JavaScript
export function getFullName(firstName, lastName) {
    return `${firstName} ${lastName}`;
}
```

And here's the final result from the bundler:

```JavaScript
function getFullName(firstName, lastName) {
    return `${firstName} ${lastName}`;
}

console.log(getFullName('Ash', 'Ketchum')); // Ash Ketchum
```

A pretty contrived example, but hopefully it helps you visualise what bundlers do.

### Code-Splitting ###

As applications have increased in complexity, bundle sizes have also increased and including one single large file has become less feasible, because applications are taking too long to load. Add to that the fact that accessing the web on mobiles has become commonplace (meaning poor network speeds need to be accommodated for) and performance has become even more important for web apps.

Not all the code included in a bundle is required to be run at all times. For example, say you are writing an application that requires a user to log in before they are able to look at a list of their favourite books. On the login screen, you won't need the code that is responsible for talking to the book API and grabbing the information for display. If only there was a way to separate this code off and load it later...

This is where **code-splitting** comes in handy: it is a way of telling the bundler "this code can be loaded asynchronously" and as a result, it will produce code split into bite-size logical chunks that can be loaded in as-and-when required.

#### import() ####

In React, code splitting is done using the dynamic `import()` function.

Import statements usually look something like this:

```JavaScript
import { getBookList } from ./BookUtils.js';

console.log(getBookList());
```

With code-splitting, this becomes:

```JavaScript
import('./Book.js').then(bookUtils => {
    console.log(bookUtils.getBookList());
});
```

The `import()` function is not available as standard just yet, so you might need to do some setup to get your bundler to use it. However, if you are using `create-react-app`, Webpack will be set up correctly out of the box so you can use it without any tweaking.

### Lazy Loading ###

Once code-splitting has been added to our application, we can go one step further and use 'lazy loading' to load modules on demand. Components can be loaded asynchronously, improving performance and making sure users don't have to wait long before they see something appear on screen. 

We can even specify fallbacks to show loading spinners, giving immediate feedback on items that are still loading and letting users know content is on its way, even if they can't see it straight away.

#### React.lazy ####

In React, the `React.lazy` function enables lazy loading. It's very simple to use and makes use of the dynamic `import()` statement we saw earlier.

Normally, component import statements in React look like this:

```JavaScript
import AuthorComponent from './AuthorComponent';
```

Using lazy loading, they are transformed to:

```JavaScript
const AuthorComponent = React.lazy(() => import('./AuthorComponent'));
```

And like magic, components are now being loaded in lazily! There's just one more thing we need to do to get this to work in our apps.

#### Suspense ####

In order to load components asynchronously, we need to use the `<Suspense>` component, which also allows us to specify a fallback component to be shown while its child components are being being loaded in.

```JavaScript
import React, { Suspense } from 'react';
const AuthorComponent = React.lazy(() => import('./AuthorComponent'));

function App(props) {
    return (
        <Suspense fallback={<div>Loading...</div>}>
            {props.route === 'author' && <AuthorComponent />}
        </Suspense>
    );
}
```

`React.lazy` generates a promise, which is how `Suspense` is able to display a fallback before rendering the contents of the component upon loading.

### Arguments for Using Suspense Today ###

Suspense is still a pretty new feature in React and if you Google it, you'll notice a lot of people mentioning that nothing is set in stone and that the API for Suspense may change. Is that a good enough reason to hold off using it?

That largely depends on your use case. If you are working on a large established codebase where implementing code-splitting and lazy loading with Suspense is going to take a lot of time and effort, you may want to hold off until the good people at React decide where this is going.

However, if you are starting a new project, you should totally implement all these cool new features. If things change further down the line, you may have to update your code, but the increase in performance will hopefully be well worth the hassle.
