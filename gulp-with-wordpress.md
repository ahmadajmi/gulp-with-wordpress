# WordPress Theme Development Automation with Gulp

I think the most common thing as web developers do is to reload the browser after each change in our code. What if we are testing in more than one browser or mobile devices, we will have to hit the refresh button on every single screen. What if something is responsible for doing this for us. 

Another thing is image compression, what if we setup a tool to auto compressing images for us once we put it in the images folder, think about automating everything we do everyday by hand, Sass compile, file concatenation.

In this tutorial, I’ll introduce [Gulp](http://gulpjs.com/), and how to integrate it with [WordPress](https://wordpress.org/) to automate and enhance your theme development process by putting together an automated workflow.

## Why You Need to Automate Your Development Workflow

* Remove repetitive and boring tasks, replace them with tools.
* Save a lot of time for doing other important core development.
* Optimize website for performance by minifying and optimizing all assets.

## Assumptions of This Article

- WordPress installed in your development machine.
- [Nodejs](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) are installed.
- Command line basic knowledge.

## Introduction to Gulp

Gulp is a JavaScript task runner that will help us automate a time consuming tasks like CSS compressing, Sass compile, image optimization, or browser refresh.

Gulp is a tool we can teach it to do some actions after something is happened, for example consider the following scenarios:

- Every time I change a Sass file, compile Sass and output a minified CSS file.
- When I add a new Image in the images folder, optimize this image and move it to a new dedicated folder for optimized images.
- When I change a PHP file, automatically refresh the browser.

First, you need to install [Gulp](http://gulpjs.com/) globally in your system, and later we will see how to install it as a package inside your theme.

Assuming Node.js is installed, open the command line tool, then you can install Gulp using npm as: 

```
npm install --global gulp
```

Once the installation is done, you can test the Gulp version by writing `gulp -v` and you can get a response like:

```
➜  ~ gulp -v
[17:19:46] CLI version 3.9.1
[17:19:46] Local version 3.9.1
```

## Theme Setup

In this tutorial I will use the [underscore](http://underscores.me/) as the base theme. You can navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name like `gulp-wordpress`, download it to the WordPress themes directory, then activate it from the dashboard.

From the command line, navigate to the `gulp-wordpress` theme directory where you have added the theme, for example in my case:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

Next, run the `npm init` command and follow a few simple steps to create a `package.json` file which will include some information about the theme and the packages that will be installed later.

After finishing up the steps, you will have a starting file that looks similar to this:

```js
{
  "name": "gulp-wordpress",
  "version": "1.0",
  "description": "Automate WordPress Theme Development Workflow with Gulp",
  "main": "gulpfile.js",
  "author": "Your Awesome Name"
}
```

Next, install Gulp as `devDependencies`:

```
npm install gulp --save-dev
```

A `node_modules` directory is now created inside the theme contains all the Gulp source files, and `package.json` file has been updated to include Gulp as a development dependency.

Some Gulp tasks like [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) require ES6-style Promises support, so you can install the [es6-promise](https://github.com/jakearchibald/es6-promise) polyfill, and then require it at the top of the `gulpfile.js` as we will do next.

```
npm install es6-promise --save-dev
```

The last step to configure Gulp is to create an empty `gulpfile.js` configuration file, which will be used to define Gulp tasks such as JavaScript and Sass.

The `gulpfile.js` starter file will be like this:

```js
require('es6-promise').polyfill();

var gulp = require('gulp');

// default task
gulp.task('default');
```

What we have done above is:

- Required the `es6-promise` polyfill on top of the file, then gulp is required.
- Create a `default` task.

To make sure that Gulp is running and everything is done perfectly, run `gulp` in the command line to execute the `default` task created in the `gulpfile.js` file, and the output should be similar to:

```
[16:33:13] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

## Speeding up Development with Gulp Tasks

At this point, the theme is ready for new tasks, and it's time to go through some common tasks that could be used to speed up development.

### Sass

If you are using Sass to write CSS, two things needed to be automated, the first one is to compile Sass to CSS, the second is to use autoprefixer. Also note that I'm Sass as an example, if you prefer another tool like Less for example you will find a Gulp plugin for it.

First, install [`gulp-sass`](https://www.npmjs.com/package/gulp-sass) and [`gulp-autoprefixer`](https://www.npmjs.com/package/gulp-autoprefixer).

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

The next step is to create a Sass directory with a basic structure.

```
├── sass
│   └── style.scss
```

The `style.scss` is the main starting point file, you are free to create your Sass architecture and import other components, modules, functions inside it based on your preference. 

The first few lines will be the stylesheet header required by WordPress.

```css
/*
  Theme Name: gulp-wordpress
  Theme URI: http://example.com/
  Author: Your Awesome Name
  Author URI: http://example.com/
  Description: Your Awesome Name Description
  Version: 1.0
  License:
  License URI:
  Text Domain: gulp-wordpress
*/

// @import 'element/all'
// @import 'components/all'

body {
  color: #333;
  background-color: #fff;
}
```

The next step is to create a `sass` task that will do the following things:

- Compile and autoprefix Sass.
- Build a `style.css` file, which is the final CSS file used by WordPress.

```js
require('es6-promise').polyfill();
var gulp          = require('gulp');
var sass          = require('gulp-sass');
var autoprefixer  = require('gulp-autoprefixer');

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))
});

gulp.task('default', ['sass']);
```

Now, run `gulp sass` task directly into the command line, this will compile `style.scss` file, and build a new `style.css` in the theme root. 

Another way to run the `sass` task, is to pass the task name as a second parameter to the `default` task as I did above, so by running `gulp`, the `sass` task will be executed.

A good WordPress practice is to include the CSS table of content to the final CSS file at the top just after the stylesheet header, then add a CSS comment before any code or import related to the section. 

Note that the comment is a normal CSS style comment like (`/*----- 1.0 Normalize -----*/`) and not a Sass comment like (`//1.0 Normalize`) and this is important because we need this comment to exist in the final CSS file, but with a Sass style comment it will be hidden by the Sass compiler. Also note that this is used in the stylesheet header, and the table of contents.

```css
/*
  Stylesheet Header ...
*/

/*--------------------
>>> TABLE OF CONTENTS:
----------------------
1.0 Normalize
2.0 Typography
3.0 Icons
4.0 Components
--------------------*/

/*----- 1.0 Normalize -----*/
@import 'normalize';

/*----- 2.0 Typography -----*/
@import 'typography';

```

To automate generating the `rtl.css` file automatically, [`gulp-rtlcss`](https://www.npmjs.com/package/gulp-rtlcss) plugin could be used to auto convert LTR CSS to RTL, so you write Sass in one file then gulp will generate the `style.css` file then generate `rtl.css` file based on it.

The idea behind `gulp-rtlcss` is to convert all the CSS properties like floats, text align, text direction, and other properties from left to right.

The second plugin is the [`gulp-rename`](https://www.npmjs.com/package/gulp-rename) which will rename the file to `rtl.css` automatically.

```
npm install gulp-rtlcss gulp-rename --save-dev
```

The next step is to included the newly installed plugins and modify the `sass` task to use `rtlcss` and in this step the plugin will convert all the CSS properties like floats and text direction from left to right.

```js
var rtlcss       = require('gulp-rtlcss');
var rename       = require('gulp-rename');

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))              // Output LTR stylesheets (style.css)

  .pipe(rtlcss())                     // Convert to RTL
  .pipe(rename({ basename: 'rtl' }))  // Rename to rtl.css
  .pipe(gulp.dest('./'));             // Output RTL stylesheets (rtl.css)
});
```

Note that I added the `rtlcss` just after the `style.css` is generated then renamed the file to `rtl.css`, and the final step is to output it.

Run `gulp sass`, and you will have `style.css` and `rtl.css` files generated.

To read more about Sass structure, and how to use it with Gulp you can read:

[Architecture for a Sass Project](Architecture for a Sass Project)

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)

### Watching Files

Instead of running `gulp sass` every time when you change a Sass file, a new task is required to do this automatically for you.

So the watch task will be used to watch any changes made to a file, once that file is changed do some action, for example when a Sass file changes, run the `sass` task.

Inside `gulpfile.js` file add a new `watch` task that will watch any changes in the `/sass` directory, then run the `sass` task. The next step is to update the `default` task with the watch task.

```js
// Watch any file with `.scss` extension inside the sass directory.
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
})

// Add watch task to Gulp default task.
gulp.task('default', ['sass', 'watch']);
```

Now you can run `gulp` in the command line to execute `sass` task first then the `watch` task will continue working to watch changes.

### JavaScript

For working with JavaScript, there are different tools required to speed up and improve JavaScript development workflow like:

- Code validation with jshint.
- Concatenate files.
- Minify code.

You can install theses plugins as following:

```
// To validation JavaScript
npm install jshint gulp-jshint --save-dev

// To concatenate files
npm install gulp-concat --save-dev

// To minify files
npm install gulp-uglify --save-dev
```

Next, in the we will require all the newly installed packages, then we can create a `js` task as:

Next, inside `gulpfile.js` file require the newly installed plugins, add a new `js` task.

```js
var jshint = require('gulp-jshint');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');

gulp.task('js', function() {
  return gulp.src(['./js/*.js'])
    .pipe(jshint())
    .pipe(concat('app.js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
});
```

Now, run `gulp js` from the command line, a new `app.min.js` file will be generated which will be used in the theme.

The task above will concatenate every single file inside the `/js` directory. In the `_underscore` themes, theses files are `customizer.js`, `navigation.js`, and `skip-link-focus-fix.js`. 

If you just need to include a specific files, you can add them inside `gulp.src` array as:

```js
// JavaScript

gulp.task('js', function() {
  return gulp.src([
    'js/navigation.js', 
    'js/skip-link-focus-fix.js'
  ])
  //
  //
});
```

This will just do all the operations on those two files, if you need to add another new file you only need to append it to the array.

You can also update the `watch` task to watch changes inside any JavaScipt file, then run the `js` task.

```js
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['js']);
});

gulp.task('default', ['sass', 'js', 'watch']);
```

Inside `functions.php` theme file, enqueue the final JavaSript file (`app.min.js`) as:

```php
wp_enqueue_script( 'app-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20120206', true );
```

We can remove other enqueued files as we already concatenated them using `gulp concat` task above. Including one file will speed up and improve website performance instead of loading too many files.

The enqueue function inside `functions.php` will end up to looks like this:

```php
/**
 * Enqueue scripts and styles.
 */
function gulp_wordpress_scripts() {
  wp_enqueue_style( 'gulp-wordpress-style', get_stylesheet_uri() );

  wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20120206', true );
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

1. `/images`: Source folder contains the original images.
2. `/dest/images`: Destination folder contains the optimized images.

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

The next is to watch the `/images`, and every time we drag a new image there it will compress the new image automatically for us.

```js
gulp.task('watch', function() {
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

## Conclusion

As we have seen, working with automation tools has become very important for the development process, things like Sass compile, browser reload after each action, production and environment mode, image optimization, all of theses tools should be automated from the start with one command, so we can focus on the the core development ideas.