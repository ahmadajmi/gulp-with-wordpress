# Using Gulp with WordPress to Improve Development Workflow 

## Introduction

I think the most common thing as web developers do is to reload the browser after each change in our code, what if we are testing in more than a browser, and also on mobile, we will have to hit the refresh button on every single screen, what if something is responsible for doing this for us. Another thing is image compression, what if we setup a tool to auto compressing images for us once we put it in the images folder, think about automation in all the stuff we do everyday by hand, Sass compile, file concatenation.

Today we will introduce Gulp, how to use it, and how to integrate it with WordPress to automate and enhance our development workflow process.

## Why I Need to Automate My Workflow

* Remove repetitive and boring tasks, replace them with tools, so tools will take care of all the process.
* Save a lot of time for doing actual development.
* Optimize website for performance by minifying and concatenating and optimizing all our assets.

## Assumptions of This Article

- WordPress knowledge.
- WordPress installed in your machine.
- Your machine have [Nodejs](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) installed.
- If this is the first time to use Gulp, don't worry, we will go through it at first.
- Also a basic knowledge of using the command line is a plus.

## Introduction to Gulp

Gulp is a JavaScript build system that will help us automate a time-consuming tasks like CSS compressing, Sass compile, live reloading.

We need to install [Gulp](http://gulpjs.com/) globally in our system:

```
npm install --global gulp
```

## Theme Setup

We will use the [underscore](http://underscores.me/) theme as our initial theme. We can navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name like `gulp-wordpress`, download it to the WordPress themes directory, then activate it from the dashboard.

From the command line, we can navigate to the theme directory where we have added the theme, for example:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

To prepare the theme wo work with Gulp, we need to run the command `npm init` and follow a few simple steps to create a `package.json` file which will include some information about the theme and how to manage Node.js modules.

After finishing up the steps, we will have a starting file that looks similar to this:

```js
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "Using Gulp with WordPress to Improve Development Workflow",
  "main": "gulpfile.js",
  "author": "Ahmad Ajmi"
}
```

Next, we’ll install Gulp within the theme:

```
npm install gulp --save-dev
```

For the future tasks to work properly, some Gulp tasks like [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) require ES6-style Promises support, so we can install the [es6-promise](https://github.com/jakearchibald/es6-promise) polyfill, require it at the top of the `gulpfile.js` file as we will do next.

```
npm install es6-promise --save-dev
```

We have to create an empty `gulpfile.js` configuration file within the theme directory. It’ll be used to define our Gulp tasks such as JavaScript and Sass.

Our `gulpfile.js` starter file will be like this:

```js
require('es6-promise').polyfill();

var gulp = require('gulp');

gulp.task('default');
```

What we have done above is:

- We required the `es6-promise` polyfill on top of the file, the `require` function is a to include the ....
- Require Gulp itself.
- Add a Gulp task named `default`, and this is the the default task.

To make sure that Gulp is running and everything is done perfectly, we can run the default Gulp task in the command line inside the theme directory:

```
gulp
```

Writing the `gulp` command above will execute the `default` task we added in the `gulpfile.js`, we will how to create a new tasks in the next sections.

The output should be similar to:

```
[16:33:13] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

At this point, our theme is now ready for new tasks, let's add some new tasks that are required for everyday working.

## Speeding up Development with Gulp Tasks

Now it's time to go through some common tasks that we can use in development everyday.

### Sass

For working with Sass, we will need to install some packages to compile Sass to regular CSS code, and to make writing Sass easier we will autoprefix our code.

The next step is to install:

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

The above command will install `gulp-sass` and `gulp-autoprefixer` in the `devDependencies` object inside our `package.json` file. 

The next step is to create a sass directory with a basic structure inside out theme contains a `style.scss` file inside.

```
├── sass
│   └── style.scss
```

What we need to do, is to write a task that will do two things:

- Compile Sass.
- Autoprefix Sass.
- Build a `style.css` file, which will be used by WordPress.

To do this, we will create a Gulp `sass` task as:

```js
require('es6-promise').polyfill();

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

The `style.scss` is the main and a starting point file, you are free to create your Sass architecture and import other components, modules and functions inside it based on your preference, we will no got through this as it's out of the scope, but if you want to read more about Sass and how to structure it, you can read:

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)
[Architecture for a Sass Project](Architecture for a Sass Project)

### Watching Files

Instead of doing `gulp sass` every time we make a change to compile Sass, we need to automate this by adding a task to watch our code changes and then to the compile.

So the watch task is used to watch the changed that are made in our files, and once that file is changed we need to to some action, in our case when a Sass file changes, we run the `sass` task.

```js
gulp.task('watch', function(){
  gulp.watch('./sass/**/*.scss', ['sass']);
})

gulp.task('default', ['sass', 'watch']);
```

Now we can run `gulp` to run the default gulp task and also run the sass and watch tasks.

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)

### JavaScript

For working with JavaScript, there are different tasks that are required to speed up and improve JavaScript development.

```
// To lint files
npm install jshint gulp-jshint --save-dev

// To minify files
npm install gulp-uglify --save-dev

// To concatenate files
npm install gulp-concat --save-dev

// To rename the final file
npm install gulp-rename --save-dev
```

```js
var jshint = require('gulp-jshint');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');
```

```js
// JavaScript

gulp.task('js', function(){
  return gulp.src(['./js/*.js'])
    .pipe(jshint())
    .pipe(concat('app.js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
});
```

After we run `gulp js` as a task command from the command line, a new files called `app.min.js` and this file will be the final and compress file that we will add it to our theme.

The task above will concatenate every single file inside the `/js` directory, and in the `_underscore` themes, theses files are `customizer.js`, `navigation.js` and `skip-link-focus-fix.js`. If we just need to include specific files, we can add them inside `gulp.src` array as:

```js
// JavaScript

gulp.task('js', function(){
  return gulp.src([
    'js/navigation.js', 
    'js/skip-link-focus-fix.js'])
  //
  //
});
```

This will just do all the operations on those two files, if we need to add another new file we can only append it to the array.

We can also update the watch task to watch changes inside any JavaScipt file and then run the `js` task, and also update the default task to include the `js` task as:

```js

gulp.task('watch', function(){
  gulp.watch('./sass/**/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['js']);
});

gulp.task('default', ['sass', 'js', 'watch']);
```

Inside `functions.php` file, we can enqueue this file as:

```php
wp_enqueue_script( 'app-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20120206', true );
```

We can remove other files enqueue as we already concatenated them using `gulp concat` task above. Including one file will speed up our website and improve performance as we should always do instead of loading too many files whether they are CSS or JavaScipt files.

We can end up with the `gulp_wordpress_scripts` function that is looks like this:

```php
/**
 * Enqueue scripts and styles.
 */
function gulp_wordpress_scripts() {
  wp_enqueue_style( 'gulp-wordpress-style', get_stylesheet_uri() );

  wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20120206', true );

  if ( is_singular() && comments_open() && get_option( 'thread_comments' ) ) {
    wp_enqueue_script( 'comment-reply' );
  }
}
add_action( 'wp_enqueue_scripts', 'gulp_wordpress_scripts' );
```

### Images

Now with images, most of images not are so much big, especially if we are using some images from websites like [unsplash](https://unsplash.com/) which sometimes reach more than 5 MB in size, how can we automate image compression in Gulp, that's what we will discover next.

To minify images we need to install the [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) to minify PNG, JPEG, GIF and SVG images.

```
npm install gulp-imagemin --save-dev
```

We can create two folders:

* The first for the original images
* The second for the optimized images

And the task that we will write will see images in the first folder, then optimize them and move them to the optimized folder.

```js
var imagemin = require('gulp-imagemin');
var pngquant = require('imagemin-pngquant');

// Images

gulp.task('images', function() {
  return gulp.src('./images/*')
    .pipe(imagemin({
      optimizationLevel: 7,
      progressive: true,
      use: [pngquant()]
    }))
    .pipe(gulp.dest('./dist/images'));
});
```

We can setup to watch an image directory, and every time we drag a new image there it will compress the new image automatically for us.

```js
gulp.task('watch', function(){
  gulp.watch('./images/*', ['images']);
});
```

[Image Optimizing with Gulp](http://diezjietal.be/blog/2015/02/18/image-optimizers.html)

### Browser Refresh with BrowserSync

First, we'll need to install Browsersync as development dependency.

```
npm install browser-sync --save-dev
```

Then we will require Browsersync within our `gulpfile.js` file:

```js
var browserSync = require('browser-sync').create();
var reload      = browserSync.reload;
```

The next step is to add the Browsersync to the watch task as:

```js
gulp.task('watch', function() {
  browserSync.init({
    files: ['./**/*.php'],
    proxy: 'http://localhost:8888/wordpress/',
  });
  gulp.watch('./sass/**/*.scss', ['sass', reload]);
  gulp.watch('./js/*.js', ['js', reload]);
  gulp.watch('./images/*', ['images', reload]);
});
```

We can now run `gulp` and it will open a new tab in the browser to the localhost, and this local host could be used in any device connected to the same network, so in every change browserSync will reload all the tabs.

We also have updated the `sass`, `js` and `images` watch tasks to reload the page if any changes happened to the files or we changed the content of the images folder.

Notice that we will need to update the [proxy](https://www.browsersync.io/docs/options/#option-proxy) option to our local development URL. For example, if our local development URL is `http://localhost:8888/wordpress/`, we would update the proxy value above with it.

We need to specify the host name

### Error Handler

[gulp-plumber](https://www.npmjs.com/package/gulp-plumber)

Prevent pipe breaking caused by errors from gulp plugins.

We need to install `gulp-plumber` as a development dependency:

```
npm install gulp-plumber --save-dev
```

Next, we will add another plugin for adding a simple sound and add colors to the error message, we will need to install [gulp-util](https://github.com/gulpjs/gulp-util) package

```
npm install gulp-util --save-dev
```

```js
var plumber = require('gulp-plumber');
var gutil   = require('gulp-util');

var onError = function( err ) {
  console.log('An error occurred:', gutil.colors.magenta(err.message));
  gutil.beep();
  this.emit('end');
};

gulp.task('sass', function () {
  return gulp.src('./sass/*.scss')
  .pipe(plumber({ errorHandler: onError }))
  //
  //
});
```

## Switching between Development & Production Environments

## Resources

## Conclusion

As we have seen, working with automation tools has become very important for the development process, things like Sass compile, browser reload after each action, production and environment mode, image optimization, all of theses tools should be automated from the start with one command, so we can focus on the the core development ideas.
