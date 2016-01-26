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

Gulp is a a JavaScript build system that will hep us automate a time-consuming tasks like CSS compressing, Sass compile, live reloading and much more.

We need to install [Gulp](http://gulpjs.com/) globally in our system:

```
npm install --global gulp
```

## Environment Setup

We will use the [underscore](http://underscores.me/) theme as our initial theme. Please navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name then download it to the WordPress themes directory then activate it from the dashboard.

I have downloaded my theme with the name as: `gulp-wordpress` and let's set this name our theme name from now.

From the command line, navigate to the theme directory where you have added the theme, for example:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

Next we need to run the command `npm init` and follow a few simple steps to create a `package.json` file which will include some information about the theme and how to manage Node.js modules. After finishing up the steps, we will have a starting file that looks similar to:

```js
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "Using Gulp with WordPress to Improve Development Workflow",
  "main": "gulpfile.js",
  "author": "Ahmad Ajmi"
}
```

As we will use Git as a version control system, it's recommended to add `.ignore` file to include the filed that we want to be ignored bit Git. As a starting point I have added `node_modules` directory to be ignored, this directory will include all the packed files required for the development and not part of the theme files and could be downloaded by any one in the future by running `npm install` to install the `package.json` packages.

```
node_modules
```

Next, we’ll install Gulp within the theme:

```
npm install gulp --save-dev
```

We have to create an empty `gulpfile.js` configuration file within the theme directory. It’ll be used to define our Gulp tasks such as JavaScript and Sass.

Our `gulpfile.js` starter file will be like this:

```js
var gulp = require('gulp');

gulp.task('default');
```

To make sure that Gulp is running and everything is done perfectly, we can run the default Gulp task in the command line inside the theme directory:

```
gulp
```

The output should be:

```
[16:33:13] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

At this point, our theme is now ready for new tasks, let's add some new tasks that are required for everyday working.

## Speeding up development with Gulp tasks

**Sass**

For working with Sass, we will need to install some packages to compile.

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

The above command will install `gulp-sass` and `gulp-autoprefixer` in the `devDependencies` object inside our `package.json` file. 

The next step is to create a simple Sass folder structure inside our theme, we will create a sass directory and add a simple style.scss file inside.

```
├── sass
│   └── style.scss
├── screenshot.png
├── style.css
```

What we need to do, is to write a task that will compile, autoprefix Sass and then build a `style.css` file.

To do this, we will create a Gulp `sass` task to compile and autoprefix Sass as:

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var autoprefixer = require('gulp-autoprefixer');

// Sass
gulp.task('sass', function () {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))
});

gulp.task('default', ['sass']);
```

Now we can run `gulp sass` and this will compile our `style.scss` file and build a new `style.css` file which will be used by WordPress. Another way to run the `sass` task, is to pass the task name as a second parameter to the `default`, so this will let us run `gulp` and to run the sass task or any other tasks inside an array of tasks to be executed and completed, as we will setup the watch task next.

The `style.scss` is the main and a starting point file, you are free to create your CSS architecture and import other components, modules and functions inside it based on your preference.

**Watch files**

Instead of doing `gulp sass` every time we make a change to compile Sass, we need to automate this by adding a task to watch our code changes and then to the compile.

So the watch task simply it to watch files and do something when a file changes, in our case when a Sass file changes, we run the `sass` task.

```js
gulp.task('watch', function(){
  gulp.watch('./sass/**/*.scss', ['sass']);
})

gulp.task('default', ['sass', 'watch']);
```

Now we can run `gulp` to run the default gulp task and also run the sass and watch tasks.

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)

**JavaScript**

**Images**

We can setup to watch an image directory, and every time we drag a new image there it will compress the new image automatically for us.
**Browser refresh**

**Error Handler**

[gulp-plumber](https://www.npmjs.com/package/gulp-plumber)

[gulp-util](https://github.com/gulpjs/gulp-util)

```
npm install --save-dev gulp-plumber gulp-util
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

## Structuring a WordPress Project with Gulp
## Switching between Development & Production environments
## Resources
## Conclusion

As we have seen, working with automation tools has become very important for the development process, things like Sass compile, browser reload after each action, production and environment mode, image optimization, all of theses tools should be automated from the start with one command, so we can focus on the the core development ideas.
