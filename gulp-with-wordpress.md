# WordPress Theme Development Automation with Gulp

I think the most common thing as web developers do is to reload the browser after each change in our code, what if we are testing in more than a browser, and also on mobile, we will have to hit the refresh button on every single screen, what if something is responsible for doing this for us. Another thing is image compression, what if we setup a tool to auto compressing images for us once we put it in the images folder, think about automation in all the stuff we do everyday by hand, Sass compile, file concatenation.

Today we will introduce [Gulp](http://gulpjs.com/), how to use it, and how to integrate it with [WordPress](https://wordpress.org/) to automate and enhance our theme development process by putting together an automated workflow.

## Why I Need to Automate Development Workflow

* Remove repetitive and boring tasks, replace them with tools, which will take care of all the process.
* Save a lot of time for doing the core development.
* Refresh the browser across multiple devices for testing once a file is changed.
* Optimize website for performance by minifying and optimizing all our assets.

## Assumptions of This Article

- WordPress installed in your development machine.
- [Nodejs](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) are installed.
- Basic knowledge working with the command line.

## Introduction to Gulp

Gulp is a JavaScript task runner that will help us automate a time consuming tasks like CSS compressing, Sass compile, image optimization, and other tasks.

We need to install [Gulp](http://gulpjs.com/) globally in our system, and later we will see how to install it as a package inside out theme. Assuming Node.js is installed in our machine, we can install Gulp using npm like: 

```
npm install --global gulp
```

## Theme Setup

We will use the [underscore](http://underscores.me/) as our working theme. We can navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name like `gulp-wordpress`, download it to the WordPress themes directory, then activate it from the dashboard.

From the command line, we can navigate to the `gulp-wordpress` theme directory where we have added the theme, for example in my case:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

To prepare the theme to work with Gulp, we need to install it inside our theme, but first we need to run the command `npm init` and follow a few simple steps to create a `package.json` file which will include information about the theme and what are the packages we need in our project.

After finishing up the steps, we will have a starting file that looks similar to this:

```js
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "Automate WordPress Theme Development Workflow with Gulp",
  "main": "gulpfile.js",
  "author": "Ahmad Ajmi"
}
```

Next, we’ll install Gulp at the theme project as `devDependencies`:

```
npm install gulp --save-dev
```

A `node_modules` directory is now created in the theme directory contains all the Gulp source files, and `package.json` file has been updated to include Gulp as a dev dependency.

Some Gulp tasks like [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) require ES6-style Promises support, so we can install the [es6-promise](https://github.com/jakearchibald/es6-promise) polyfill, and then require it at the top of the `gulpfile.js` as we will do next.

```
npm install es6-promise --save-dev
```

Let's create an empty `gulpfile.js` configuration file within the theme root, which will be used to define our Gulp tasks such as JavaScript and Sass.

Our `gulpfile.js` starter file will be like this:

```js
require('es6-promise').polyfill();

var gulp = require('gulp');

// default task
gulp.task('default');
```

What we have done above is:

- We required the `es6-promise` polyfill on top of the file, then we required gulp.
- Create a `default` task.

To make sure that Gulp is running and everything is done perfectly, we can run the `gulp` in the command line to execute the `default` task we created in the `gulpfile.js` file, and the output should be similar to:

```
[16:33:13] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

At this point, our theme is now ready for new tasks, and it's time to go through some common tasks that we can use in development everyday.

## Speeding up Development with Gulp Tasks

### Sass

We will need to install some packages to compile [Sass](http://sass-lang.com/) to regular CSS code, and to make writing Sass easier we will autoprefix our code.

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

The above command will install [`gulp-sass`](https://www.npmjs.com/package/gulp-sass) and [`gulp-autoprefixer`](https://www.npmjs.com/package/gulp-autoprefixer) as `devDependencies` inside `package.json` file. 

The next step is to create a sass directory with a basic structure inside our theme, with a main `style.scss` file.

```
├── sass
│   └── style.scss
```

The `style.scss` is the main and a starting point file, you are free to create your Sass architecture and import other components, modules, functions inside it based on your preference. The first few lines will be the stylesheet header required by WordPress to provide details about the the theme, then we can write or import other Sass files.

```css
/*
Theme Name: gulp-wordpress
Theme URI: http://underscores.me/
Author: Underscores.me
Author URI: http://underscores.me/
Description: Description
Version: 1.0.0
License: GNU General Public License v2 or later
License URI: http://www.gnu.org/licenses/gpl-2.0.html
Text Domain: gulp-wordpress
*/

body {
  color: #333;
  background-color: #fff;
}
```

Next, we will write a task that will do the following things:

- Compile and autoprefix Sass.
- Build a `style.css` file, which will be used by WordPress.

To do this, we will create a Gulp `sass` task as:

```js
require('es6-promise').polyfill();

var gulp = require('gulp');
var sass = require('gulp-sass');
var autoprefixer = require('gulp-autoprefixer');

// Sass
gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))
});

gulp.task('default', ['sass']);
```

Now we can run `gulp sass` task directly, this will compile our `style.scss` file and build a new `style.css` in the theme root. 

Another way to run the `sass` task, is to pass the task name as a second parameter to the `default` task, and by running `gulp`, the `sass` task will be executed.

**RTL Style**

```js
var rename       = require('gulp-rename');
var rtlcss       = require('gulp-rtlcss');

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))              // Output LTR stylesheets.
  .pipe(rtlcss())                     // Convert to RTL.
  .pipe(rename({ basename: 'rtl' }))  // Append "-rtl" to the filename.
  .pipe(gulp.dest('./'));             // Output RTL stylesheets.
});
```

To read more about Sass structure it, and how to use it with Gulp you can read:

[Architecture for a Sass Project](Architecture for a Sass Project)

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)

### Watching Files

Instead of doing `gulp sass` every time we make a change to a Sass file, we need to automate this by adding a new task to watch changes to any file.

So the watch task is used to watch the changes that are made in our files, and once that file is changed we need to do some action, in our case when a Sass file changes, run the `sass` task.

```js
// Watch any file with `.scss` extension inside the sass directory.
gulp.task('watch', function(){
  gulp.watch('./sass/**/*.scss', ['sass']);
})

// Add watch task to Gulp default task.
gulp.task('default', ['sass', 'watch']);
```

Now we can run `gulp` to run the `sass`, and `watch` tasks.

### JavaScript

For working with JavaScript, there are different tasks that are required to speed up and improve JavaScript development.

```
// To lint JavaScript files
npm install jshint gulp-jshint --save-dev

// To minify files
npm install gulp-uglify --save-dev

// To concatenate files
npm install gulp-concat --save-dev

// To rename the final file
npm install gulp-rename --save-dev
```

Next, we will require all the newly installed packages, then we can create a `js` task as:

```js

var jshint = require('gulp-jshint');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');

// JavaScript Task

gulp.task('js', function() {
  return gulp.src(['./js/*.js'])
    .pipe(jshint())
    .pipe(concat('app.js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
});
```

After we run `gulp js` as a task command from the command line, a new file called `app.min.js`, and this file will be the final and the compressed file that we will add it to our theme.

The task above will concatenate every single file inside the `/js` directory, and in the `_underscore` themes, theses files are `customizer.js`, `navigation.js`, and `skip-link-focus-fix.js`. 

If we just need to include a specific files, we can add them inside `gulp.src` array as:

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

This will just do all the operations on those two files, if we need to add another new file we need to only append it to the array.

We can also update the `watch` task to watch changes inside any JavaScipt file and then run the `js` task.

```js

gulp.task('watch', function(){
  gulp.watch('./sass/**/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['js']);
});

gulp.task('default', ['sass', 'js', 'watch']);
```

Inside `functions.php` file, we can enqueue the final JavaSript file (`app.min.js`) as:

```php
wp_enqueue_script( 'app-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20120206', true );
```

We can remove other files enqueue as we already concatenated them using `gulp concat` task above. Including one file will speed up our website and improve performance as we should always do instead of loading too many files whether they are CSS or JavaScipt files.

We can end up with the `gulp_wordpress_scripts` function inside `functions.php` that is looks like this:

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

Now with images, most of images are so much big, especially if we are using some images from websites like [unsplash](https://unsplash.com/) which sometimes reach more than 5 MB in size, how can we automate image compression in Gulp, that's what we will do next.

To minify images we need to install the [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) to minify PNG, JPEG, GIF and SVG images.

```
npm install gulp-imagemin --save-dev
```

We can create two folders:

- The first is the source folder (`./images`) for the original images.
- The second is the destination folder (`./dest/images`) for the optimized images.

And the task that we will write will see images in the first folder, then optimize them and move them to the optimized folder.

```js
var imagemin = require('gulp-imagemin');
var pngquant = require('imagemin-pngquant');

// Images

gulp.task('images', function() {
  return gulp.src('./images/*')
    .pipe(plumber({ errorHandler: onError }))
    .pipe(imagemin({
      optimizationLevel: 7,
      progressive: true,
      use: [pngquant()]
    }))
    .pipe(gulp.dest('./dest/images'));
});
```

The next is to watch the `./images`, and every time we drag a new image there it will compress the new image automatically for us.

```js
gulp.task('watch', function() {
  //
  //
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

We can now run `gulp` and it will open a new tab in the browser to the localhost, and this localhost could be used in any device connected to the same network, so in every change browserSync will reload all the browsers.

We also have updated the `sass`, `js` and `images` watch tasks to reload the page if any changes happened to the files or we changed the content of the images folder.

Notice that we will need to update the [proxy](https://www.browsersync.io/docs/options/#option-proxy) option to our local development URL. For example, if our local development URL is `localhost:8888/wordpress`, we would update the proxy value above with it.

### Error Handler

Sometimes while we are writing code we might write something wrong, say we mistypes undefined Sass variable, and while we are watching files, Gulp will break because the specific task can't compile that variable. This is annoying because we have to retype `gulp` again to start working. Aw can fix this issue by modifying our tasks to give a notification in the command line to that specific error, so we can know exactly what's happening and in which file, and also Gulp will not stop working.

We will use [gulp-plumber](https://www.npmjs.com/package/gulp-plumber) plugin that will prevent Gulp breaking caused by errors from gulp plugins.

We need to install `gulp-plumber` as a development dependency:

```
npm install gulp-plumber --save-dev
```

Next, we will add another plugin for adding a simple sound, and to add colors to the error message, we will need to install [gulp-util](https://github.com/gulpjs/gulp-util) plugin.

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

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(plumber({ errorHandler: onError }))
  //
  //
});
```

What we have did above:

- Add a `onError` function to log an error message, and to create a beep sound.
- Update the `sass` task to use the `plumber` function and then pass the `onError` to it as a value to the `errorHandler` object property.

## Switching between Development & Production Environments

## Resources

## Conclusion

As we have seen, working with automation tools has become very important for the development process, things like Sass compile, browser reload after each action, production and environment mode, image optimization, all of theses tools should be automated from the start with one command, so we can focus on the the core development ideas.