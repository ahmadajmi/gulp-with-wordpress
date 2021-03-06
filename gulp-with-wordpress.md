# WordPress Theme Development Automation with Gulp

As websites become more complicated in the way we write code, and the steps we do every time doing repetitive tasks to optimize the assets for example. There should be a better way for an efficient development process.

In this tutorial, I’ll introduce [Gulp](http://gulpjs.com/), and how to integrate it with [WordPress](https://wordpress.org/) to automate and enhance theme development process by putting together an automated workflow.

## Why You Need to Automate Your Development Workflow

* Remove repetitive and boring tasks, replace them with custom tools.
* Save a lot of time for doing other important core development.
* Optimize website for performance by minifying and optimizing all assets.

## Assumptions of This Article

- WordPress installed on your development machine.
- [Nodejs](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) are installed.
- Command line basic knowledge.

## Introduction to Gulp

Gulp is a JavaScript task runner that will help automate time-consuming tasks like CSS compressing, Sass compile, image optimization, or browser reload.

Gulp is giving you the tools to do some actions (tasks) for a particular need after something happens. For example, consider the following scenarios:

- Every time you save a Sass file, Gulp will compile Sass and output a minified CSS file.
- When you add a new image to a folder, Gulp will optimize this image and move it to a new dedicated folder.
- When you save a PHP or a Sass file, Gulp will automatically reload the browser.

### Gulp Setup

First, you need to install [Gulp](http://gulpjs.com/) globally in your system, and later I will show you how to install it as a package inside your theme.

Assuming Node.js is installed, open the command line tool, then install Gulp using npm as:

```
npm install --global gulp
```

Now, run `gulp -v` (Gulp version) to test that Gulp is installed correctly, and you should get output similar to:

```
➜  ~ gulp -v
[09:33:59] CLI version 3.9.1
```

## Theme Setup

In this tutorial, I will use the [underscore](http://underscores.me/) as the base theme. To download it, navigate to [underscores.me](http://underscores.me/), generate a new theme and give it a name like `gulp-wordpress`, download it to the WordPress themes directory, then activate it from the dashboard.

From the command line, navigate to the `gulp-wordpress` directory where you have added the theme, for example in my case:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

Next, run the `npm init` command and follow a few simple steps to create a `package.json` file which will include some information about the theme and the packages that will be installed later.

After finishing up the steps, you will have a starting file that looks similar to this:

```js
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "WordPress Theme Development Automation with Gulp",
  "author": "Name"
}
```

Next, install Gulp as a development dependency:

```
npm install gulp --save-dev
```

A `node_modules` directory is now created, contains Gulp package source files, and `package.json` file has been updated to include Gulp as a development dependency.

```js
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "WordPress Theme Development Automation with Gulp",
  "author": "Author Name",
  "devDependencies": {
    "gulp": "^3.9.1"
  }
}
```

Some Gulp tasks like [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) require ES6-style Promises support so that you can install the [es6-promise](https://github.com/jakearchibald/es6-promise) polyfill, and then require it at the top of the `gulpfile.js` as we will do next.

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
[09:48:23] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

## Speeding up Development with Gulp Tasks

At this point, the theme is ready for new tasks, and it's time to go through some common tasks that you can use to speed up your theme development.

### Working with CSS (Sass)

If you are using [Sass](http://sass-lang.com/) to write CSS, two main things needed to be automated, the first one is to compile Sass to CSS, the second is to use autoprefixer to add vendor prefixes to your CSS. Also note that I'm using Sass as an example, if you prefer another tool like [Less](http://lesscss.org/) for example, you will find a Gulp plugin for it.

First, install [`gulp-sass`](https://www.npmjs.com/package/gulp-sass) and [`gulp-autoprefixer`](https://www.npmjs.com/package/gulp-autoprefixer).

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

The next step is to create a Sass directory with a basic structure.

```
├── sass
│   └── style.scss
```

The `style.scss` is the main starting point file, you are free to create your Sass architecture and import other components, modules, functions inside it based on your preference. 

The first few lines will be the [*stylesheet header*](https://codex.wordpress.org/Theme_Development#Theme_Stylesheet) required by WordPress.

```css
/*
  Theme Name: Gulp WordPress
  Theme URI: http://example.com/
  Author: Author Name
  Author URI: http://example.com/
  Description: Description
  Version: 1.0.0
  License:
  License URI:
  Text Domain: gulp-wordpress
*/

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
  return gulp.src('./sass/**/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))
});

gulp.task('default', ['sass']);
```

Now, run `gulp sass` task directly from the command line, this will compile `style.scss` file, and build a new `style.css` file in the theme root. 

Another way to run the `sass`, is to pass the task name as a second parameter to the `default` task as I did above, so by running `gulp`, the `sass` task will be executed.

A good WordPress practice is to include the CSS table of content to the final CSS file at the top just after the *stylesheet header*, then add a CSS comment before any code or import related to the section. 

Note that the comment is a standard CSS style comment like (`/*----- 1.0 Normalize -----*/`), and not a Sass comment like (`//----- 1.0 Normalize -----`). This is important because this comment needs to be to exist in the final CSS file, but with a Sass style comment it will be hidden by the Sass compiler. Also, note that this is used in the *stylesheet header*, and the table of contents.

The following is an example of the `style.scss` file containing the table of content, and some imports to external sass files.

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

To automate generating the `rtl.css` file automatically, [`gulp-rtlcss`](https://www.npmjs.com/package/gulp-rtlcss) plugin could be used to auto convert LTR (left to right) CSS to RTL (right to left), so you can write Sass in one file then Gulp will generate two CSS files, the first is `style.css` file, and the second is `rtl.css` file.

The idea behind `gulp-rtlcss` is to convert all the CSS properties like floats, text-align, text direction, and other properties from left to right.

The second plugin is [`gulp-rename`](https://www.npmjs.com/package/gulp-rename) which will rename the file to `rtl.css` automatically.

```
npm install gulp-rtlcss gulp-rename --save-dev
```

The next step is to include the newly installed plugins at the top of `gulpfile.js` file, and modify the `sass` task to use `rtlcss()` after the `style.css` is generated to do the conversion. 

In this step, the plugin will convert all the CSS properties like floats and text direction from left to right, then rename the file to `rtl.css`, and then output the file to the theme root.

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

Run `gulp sass`, and you will have `style.css` and `rtl.css` files generated.

To read more about Sass structure, and how to use it with Gulp you can read:

[Architecture for a Sass Project](Architecture for a Sass Project)

[A Simple Gulp’y Workflow For Sass](http://www.sitepoint.com/simple-gulpy-workflow-sass/)

### Watching Files

Instead of running `gulp sass` whenever you change a Sass file, a new task is required to do this automatically for you.

The `watch` task will be used to view any changes made to a file, so once that file changed, do another action. For example, when you save a Sass file, then `sass` task should run automatically.

Inside `gulpfile.js` file add a new `watch` task to watch any changes in the `/sass` directory, then run the `sass` task. The next step is to update the `default` task with the watch task.

```js
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
});

gulp.task('default', ['sass', 'watch']);
```

Now you can run `gulp` in the command line to execute `sass` task first then the `watch` task will continue working.

### Error Handling

Sometimes while you are writing code, and write undefined Sass variable, and in the middle of work while you are watching files, Gulp will break because the particular task can't compile that variable, this is annoying because you have to start Gulp again to continue working. 

You can fix this by using [gulp-plumber](https://www.npmjs.com/package/gulp-plumber) plugin, which will prevent Gulp breaking caused by errors.

To improve error handling, install [gulp-util](https://github.com/gulpjs/gulp-util) utility functions package to customize the error message, add beep sound once the error occurred, plus adding colors to the error message which is useful identifying the error.

```
npm install gulp-plumber gulp-util --save-dev
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

What have been done above:

- Add `onError` function to log the error message, and create a beep sound.
- Update the `sass` task to use the `plumber` function, then pass the `onError` function to the `errorHandler` object property. 

By doing this you can know exactly what's happening on errors, in which file, and also Gulp will not stop working.

An example of undefined Sass variable `$color`.

![Sass Error Handling](https://cloud.githubusercontent.com/assets/626005/15383185/d304f5d4-1d99-11e6-9382-c336574acfd7.png)

### JavaScript

For working with JavaScript, there are different tools required to speed up and improve JavaScript development workflow, for example:

- Concatenate many JavaScript files into a single file.
- Check code errors with JSHint.
- Minify code to get a much smaller file size.

You can install these plugins as following:

To concatenate files

```
npm install gulp-concat --save-dev
```

To validate JavaScript

```
npm install gulp-jshint --save-dev
```

To minify code

```
npm install gulp-uglify --save-dev
```

Next, inside `gulpfile.js` file require the newly installed plugins, add a new `js` task.

```js
var concat = require('gulp-concat');
var jshint = require('gulp-jshint');
var uglify = require('gulp-uglify');

gulp.task('js', function() {
  return gulp.src(['./js/*.js'])
    .pipe(jshint())
    .pipe(jshint.reporter('default'))
    .pipe(concat('app.js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
});
```

This task takes any file that ends with `.js` extension inside the `./js` directory, check for code errors with jshint, then concatenate them to `app.js`, and as we need a minified output; it's time to rename the file to `app.min.js`, then minify the code, and in the last step, output the file to the `./js` directory.

At this point, create a `.jshintrc` configuration file in the theme root, which is a simple JSON file that specifies which JSHint options to turn on or off, for example:

```js
{
  "undef": true,
  "unused": true,
  "browser": true
}
```

- `undef`: Warns when you use a variable that was not declared.
- `unused`: Warns when defining a variable and never use it.
- `browser`: Defines globals exposed by modern browsers, like `navigator` and `document`.

JSHint comes with a set of [options](http://jshint.com/docs/options/) that you can use based on your preference.

Now, run `gulp js` from the command line, a new `app.min.js` file will be generated which will be used later in the theme.

By default, the `_underscore` theme includes `customizer.js`, `navigation.js`, and `skip-link-focus-fix.js` files under the `/js` directory.

If you only need to include specific files, you can add them inside `gulp.src` array as:

```js
gulp.task('js', function() {
  return gulp.src([
    './js/navigation.js', 
    './js/skip-link-focus-fix.js'
  ])
  //
  //
});
```

The code above will do all the operations on those two files. If you need to add another new file you can append it to the array.

You can also update the `watch` task to watch changes to any JavaScipt file, and also update the `default` task to run the `js` actions automatically when running `gulp`, just as we did in the with Sass.

```js
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['js']);
});

gulp.task('default', ['sass', 'js', 'watch']);
```

Inside `functions.php` theme file, enqueue the generated `app.min.js` file as:

```php
wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20151215', true );
```

You can remove other enqueued JavaScript files as they already concatenated and minified into a single file. Including one file will speed up and improve website performance instead of loading too many files.

The enqueue scripts function inside `functions.php` file will end up to look similar to:

```php
/**
 * Enqueue scripts and styles.
 */
function gulp_wordpress_scripts() {
  wp_enqueue_style( 'gulp-wordpress-style', get_stylesheet_uri() );

  wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20151215', true );
}
add_action( 'wp_enqueue_scripts', 'gulp_wordpress_scripts' );
```

### Images

Let's now try to optimize images with Gulp, by doing this we will ensure that all the used pictures in the theme are automatically optimized for speed. To make this automation easier, you can set up a Gulp task to watch the images directory, and once you drag an image there, gulp will optimize it and move it to another folder for optimized and ready to use images.

Create two folders:

1. `/images/src`: Source folder contains the original images.
2. `/images/dest`: Destination folder contains the optimized images.

Install [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) to minify PNG, JPEG, GIF, and SVG images.

```
npm install gulp-imagemin --save-dev
```

Create a new task (`images`), that we will watch images located in the source folder (`/images/src`), optimize them, and move them to the optimized folder (`/images/dest`).

```js
var imagemin = require('gulp-imagemin');

gulp.task('images', function() {
  return gulp.src('./images/src/*')
    .pipe(plumber({ errorHandler: onError }))
    .pipe(imagemin({ optimizationLevel: 7, progressive: true }))
    .pipe(gulp.dest('./images/dist'));
});
```

You can also watch the `/images/src` folder, so every time you drag a new image there, the `images` task will run. Also, update the `default` task to run the `images` action.

```js
gulp.task('watch', function() {
  //
  gulp.watch('images/src/*', ['images']);
});

gulp.task('default', ['sass', 'js', 'images', 'watch']);
```

### Browser Refresh with BrowserSync

What if you want to refresh the browser after any code changes whether the code is PHP, Sass, or JavaScript, and what if you are testing with more than one browser or mobile devices, you will have to hit the refresh button on every single screen. [BrowserSync](https://www.browsersync.io/) will help in doing this effectively.

First, you'll need to install Browsersync as development dependencies.

```
npm install browser-sync --save-dev
```

Next, require Browsersync inside `gulpfile.js` file, and update the `watch` task to include Browsersync as:

```js
var browserSync = require('browser-sync').create();
var reload = browserSync.reload;

gulp.task('watch', function() {
  browserSync.init({
    files: ['./**/*.php'],
    proxy: 'http://localhost:8888/wordpress/',
  });
  gulp.watch('./sass/**/*.scss', ['sass', reload]);
  gulp.watch('./js/*.js', ['js', reload]);
  gulp.watch('images/src/*', ['images', reload]);
});
```

Notice that you will need to update the [proxy](https://www.browsersync.io/docs/options/#option-proxy) option to the local development URL. For example, if your local URL is `localhost:8888/wordpress`, update the proxy value above with it. 

As Browsersync can watch your files as you work, for example, I configured the task to watch any changes to PHP files. 

Also updated the `sass`, `js`, and `images` watch tasks to reload the page if any changes happened to the files, or you changed the content of the images folder.

Now, you can run `gulp`, and a new tab will be opened automatically in the browser to the localhost URL, and your console will looks something like this:

```
[BS] Access URLs:
 --------------------------------------------
       Local: http://localhost:3000/wordpress/
    External: http://192.168.1.2:3000/wordpress/
 --------------------------------------------
          UI: http://localhost:3001
 UI External: http://192.168.1.2:3001
 --------------------------------------------
```

You can use the *External* URL in any device connected to the same network, so in every change browserSync will reload all the browsers.

The *UI External* URL used for Browsersync control panel, which allows you to change sync options, manage devices.

## Conclusion

As you’ve seen, working with automation tools becomes very necessary to improve and speed the development process. There are lots of [Gulp plugins](http://gulpjs.com/plugins/) out there you can try and use based on your requirements, so that if anything is manually done should be automated to save time, and to make the development process more efficiency.

I’ve created a GitHub repo for this article WordPress theme and [you can check out the code here](https://github.com/ahmadajmi/underscore-wordpress-with-gulp). Let me know if you use any Gulp plugins in your development process that makes your life easier.