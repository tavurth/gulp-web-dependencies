# gulp-web-dependencies
Parse your HTML/JS file and copy bower/npm dependencies to your destination directory

[![Build Status](https://travis-ci.org/demsking/gulp-web-dependencies.svg?branch=master)](https://travis-ci.org/demsking/gulp-web-dependencies)

## Install

`npm install --save-dev gulp-web-dependencies`

## Usage
The project structure:
```
project/
├── bower_components
│   ├── bootstrap
│   │   └── dist
│   │       ├── css
│   │       │   ├── bootstrap.min.css
│   │       │   ├── bootstrap-theme.min.css
│   │       │   └── ...
│   │       └── js
│   │           ├── bootstrap.min.js
│   │           └── ...
│   ├── require1k
│   │   └── require1k.min.js
│   └── jquery
│       └── dist
│           ├── jquery.min.js
│           └── ...
├── node_modules
│   ├── angular
│   │   └── angular.min.js
│   └── firebase
│       ├── app.js
│       ├── auth.js
│       └── ...
├── src
│   ├── index.html
│   └── app.js
└── gulpfile.js
```

Use relative paths for NPM/Bower dependencies:

The `index.html`:

```html
<!doctype html>
<html>
    <head>
        <title>Test file</title>
        <meta charset="utf-8">
        <link rel="stylesheet" href="../bower_components/bootstrap/dist/css/bootstrap.min.css">
        <link rel="stylesheet" href="../bower_components/bootstrap/dist/css/bootstrap-theme.min.css">
    </head>
    <body>
        <h1>Page title</h1>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur tempus enim leo, ac lacinia purus accumsan sit amet. In ultrices sagittis nulla, ut dapibus.</p>
        
        <script src="../bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
        <script src="../bower_components/jquery/dist/jquery.min.js"></script>
        <script src="../node_modules/angular/angular.min.js"></script>
        <script src="../bower_components/require1k/require1k.min.js" data-main="./app"></script>
    </body>
</html>
```

The `app.js`:

```js
var _app = require('../node_modules/firebase/app');
var _auth = require('../node_modules/firebase/auth');

// ...
```

In your `gulpfile.js`, add the task:

```js
var gulp = require('gulp')
  , dependencies = require('gulp-web-dependencies');

var path_dest = 'dist';

gulp.task('dependencies', function() {
    return gulp.src('src/index.html')
        .pipe(dependencies({
            dest: path_dest,    // The basedir of your application. default: path.dirname(file.path)
            prefix: '/vendor',  // The URL prefix. Default "/"
        }))
        .pipe(gulp.dest(path_dest));
});

// or using a template preprocessing (pug)
gulp.task('dependencies-jade', function() {
    return gulp.src('src/app.js')
        .pipe(pug())
        .pipe(dependencies({
            dest: path_dest, 
            prefix: '/vendor',
        }))
        .pipe(gulp.dest(path_dest));
});

// parsing js files
gulp.task('dependencies-js', function() {
    return gulp.src('src/app.js')
        .pipe(dependencies({
            dest: path_dest
        }))
        .pipe(gulp.dest(path_dest));
});

// Gulp Default Task
gulp.task('default', ['dependencies', 'dependencies-jade', 'dependencies-js']);

```

After the build, get:

```
project/
├── bower_components
│   └── ...
├── dist
│   ├── index.html
│   ├── products.html
│   ├── node_modules
│   │   └── firebase
│   │       ├── app.js
│   │       └── auth.js
│   └── vendor
│       ├── angular
│       │   └── angular.min.js
│       ├── bootstrap
│       │   └── dist
│       │       ├── css
│       │       │   ├── bootstrap.min.css
│       │       │   └── bootstrap-theme.min.css
│       │       └── js
│       │           └── bootstrap.min.js
│       └── jquery
│           └── dist
│               └── jquery.min.js
├── node_modules
│   └── ...
├── src
│   ├── index.html
│   └── app.js
└── gulpfile.js
```

`index.html`:

```html
<!doctype html>
<html>
    <head>
        <title>Test file</title>
        <meta charset="utf-8">
        <link rel="stylesheet" href="/vendor/bootstrap/dist/css/bootstrap.min.css">
        <link rel="stylesheet" href="/vendor/bootstrap/dist/css/bootstrap-theme.min.css">
    </head>
    <body>
        <h1>Page title</h1>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
        
        <script src="/vendor/bootstrap/dist/js/bootstrap.min.js"></script>
        <script src="/vendor/jquery/dist/jquery.min.js"></script>
        <script src="/vendor/angular/angular.min.js"></script>
        <script src="plugins.js"></script>
    </body>
</html>

```

`app.js`:

```js
var _app = require('firebase/app');
var _auth = require('firebase/auth');

// ...
```

## Use the flat option


```js
gulp.task('dependencies', function() {
    return gulp.src('src/**/*.pug')
        .pipe(pug())
        .pipe(dependencies({
            dest: 'dist',
            prefix: '/vendor',
            flat: true
        }))
        .pipe(gulp.dest('dist'));
});


```

After the build, get:

```
project/
├── ...
├── dist
│   ├── ..
│   └── vendor
│       ├── angular.min.js
│       ├── bootstrap.min.css
│       ├── bootstrap.min.js
│       ├── bootstrap-theme.min.css
│       └── jquery.min.js
├── ...
```

```html
<!doctype html>
<html>
    <head>
        <title>Test file</title>
        <meta charset="utf-8">
        <link rel="stylesheet" href="/vendor/bootstrap.min.css">
        <link rel="stylesheet" href="/vendor/bootstrap-theme.min.css">
    </head>
    <body>
        <h1>Page title</h1>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
        
        <script src="/vendor/bootstrap.min.js"></script>
        <script src="/vendor/jquery.min.js"></script>
        <script src="/vendor/angular/angular.min.js"></script>
        <script src="plugins.js"></script>
    </body>
</html>

```

## License

Under the MIT license. See [LICENSE](https://github.com/demsking/gulp-web-dependencies/blob/master/LICENSE) file for more details.
