# Using Gulp with WordPress

## Introduction

We will automate the development WorkFlow process as much as we can 

We will talk about Gulp, how to use it and how to integrate it with WordPress.

### Why I need to automate my WorkFlow 

* Remove repetition steps.
* Replace all annoying things with tools, so tools will take care of all the process.

it will help us with things like CSS prefixes, minifying, image optimizations, browser reload.

Code organizing in partials, variables, ....

we need a tool for doing theses tasks, we will use Gulp

## Assumptions of this article
## Introduction to Gulp
## Set up an environment to use Gulp with WordPress
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

## Switching between Development & Production environments
## Resources
## Conclusion