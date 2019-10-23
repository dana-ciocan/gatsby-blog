---
title: React.lazy and Suspense
date: "2019-10-23T13:30:32.169Z"
description: React.lazy and Suspense
---

## Code-Splitting and Lazy Loading in React ##

A few years ago, JavaScript developers would tend to concatenate and minify all their JavaScript code. This was deemed the best way to include code into a web-based JavaScript application. The world of front end development moves quickly though and nowadays code-splitting and lazy loading are becoming important. This article will explain why.

### Bundlers ###

If you have been working with React for a little while, you have probably heard of **bundlers** such as Webpack, Rollup, Parcel or Browserify. You may not have dealt with them directly, because it is not necessary to know a lot about bundlers to work with React. The ever-helpful `create-react-app` does most of the bundler setup for you (it uses Webpack, in case you were curious) and unless you want something hugely specific, there is rarely a need to manually edit its default configuration.

All you need to know for now is that when you build a production copy of your application, a bundler is responsible for concatenating (and processing, if necessary) your code to create one big file called, surprise surprise, a 'bundle'. This allows the HTML page that is generated to reference just one JavaScript file rather than lots of little ones and can improve performance, as long as your app meets certain criteria.

#### Example ####

Here is a very basic example to illustrate the sort of thing bundlers will do (in reality things get much more complicated).

Here's our code for our app:

app.js:

    import { concat } from ./utils.js';

    console.log(concat(['hello', 'there']));

utils.js:

    export function concat(array) {
        return array.join(' ');
    }

And here's the result from our bundler:

    function concat(array) {
        return array.join(' ');
    }

    console.log(concat(['hello', 'there']));

### Code-Splitting ###

As applications have increased in size, bundle sizes have also increased and including one single large file has become less worthwhile, because loading times multiply significantly with bundle size. Add to that the fact that more and more users are accessing the internet on mobiles, and thus with potentially poor network connections, performance has become extremely important.

Given that not all the code included in a bundle needs to be available when the application first loads, it might be useful to split the code up in an automated way. For example, say you are writing an application that requires a user to log in before they are able to look at a list of their favourite books. On the login screen, you won't need the code that is responsible for talking to the book API and grabbing the information for display, so it can be separated off and loaded later.

This is where **code-splitting** comes in: it is a process whereby instead of producing one giant bundle, code is split into bite-size logical chunks.

#### import() ####

The best way to split code in React is using the `import()` function.

So, from our previous example:

    import { concat } from ./utils.js';

    console.log(concat(['hello', 'there']));

With code-splitting, this becomes:

    import('./utils.js').then(utils => {
        console.log(utils.concat(['hello', 'there']));
    });

The `import()` function is not yet part of the JavaScript standard, but bundlers will be able to code-split if they are set up correctly (if you are using `create-react-app`, Webpack will be set up correctly out of the box).

### Lazy Loading ###

Once code-splitting has been implemented, we can go one step further and use 'lazy loading' to load modules on demand. Components can be loaded asynchronously, improving performance even further and making sure users don't have to wait long before they see something appear on screen. Fallbacks can be specified to give users feedback on items that are still loading.

#### React.lazy ####

The `React.lazy` function lets you lazy load a component. Here is an example:

Before:

    import SomeComponent from './SomeComponent';

After:

    const SomeComponent = React.lazy(() => import('./SomeComponent'));

#### Suspense ####

Finally, using suspense allows us to specify a fallback for when items are loading.