# Using Gulp with WordPress to Improve Development Workflow 

## Introduction

I think the most common things as web developers do is to reload the browser after each change in our code, what if we are testing in more than browser and also on mobile, we will have to hist the refresh button on every app, what if something is responsible for doing this for us, another thing is image compression, what if we setup a tool to auto compress images for us once we put it in the images folder, think about automation in all the stuff we do everyday by hand, Sass compile, file concatenation.

Today we will introduce Gulp, how to use it, and how to integrate it with WordPress to automate and enhance the development workflow process

We will automate the development WorkFlow process as much as we can

### Why I need to automate my WorkFlow 

* Remove repetitive and boring tasks, replace them with tools, so tools will take care of all the process.
* Save a lot of time for doing actual development

It will help us with things like CSS prefixes, minifying, image optimizations, browser reload.

Code organizing in partials, variables, ....

we need a tool for doing theses tasks, we will use Gulp

## Assumptions of this article

I assume you have a working knowledge of WordPress and have a working WordPress installation. If this is the first time to use Gulp, don't worry, we will go through it at first. Also a basic knowledge of using the command line is a plus. Also I assume your system have [Nodejs](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) are installed.

## Introduction to Gulp

We need to install [Gulp](http://gulpjs.com/) in our system:

```
npm install --global gulp
```

## Environment Setup

We will use the [underscore](http://underscores.me/) theme as our initial theme. Please navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name then download it to the WordPress themes directory and activate it from the dashboard. 

I have downloaded my theme with the name as: `gulp-wordpress` and let's set this name our theme name from now.

From the command line, navigate to the theme directory where you have added the theme, for example:

```
cd aspire/wp-content/themes/gulp-wordpress
```

Create a `gulpfile.js` at the root of your project:

```
var gulp = require('gulp');

gulp.task('default', function() {
  // place code for your default task here
});
```

## Structuring a WordPress Project with Gulp
## Speeding up development with Gulp tasks

**Sass**

prefixes

**javaScript**

**Images**

We can setup to watch an image directory, and every time we drag a new image there it will compress the new image automatically for us.
**Browser refresh**

**Error Handler**

[gulp-plumber](https://www.npmjs.com/package/gulp-plumber)

[gulp-util](https://github.com/gulpjs/gulp-util)

```
npm install --save-dev gulp-plumber gulp-util
npm install --save-dev gulp-util
```

```js
var plumber = require('gulp-plumber');
var gutil   = require('gulp-util');

var onError = function( err ) {
  console.log('An error occurred:', gutil.colors.magenta(err.message));
  gutil.beep();
  this.emit('end');
}

gulp.task('sass', function () {
  return gulp.src('./assets/sass/*.scss')
  .pipe(plumber({ errorHandler: onError }))
  .pipe(gulp.dest('./assets/css'));
});
```

**Browsersync**

```
npm install browser-sync gulp --save-dev
```

## Switching between Development & Production environments
## Resources
## Conclusion

As we have seen, working with automation tools has become very important for the development process, things like Sass compile, browser reload after each action, production and environment mode, image optimization, all of theses tools should be automated from the start with one command, so we can focus on the the core development ideas.
