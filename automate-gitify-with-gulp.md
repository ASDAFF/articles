
# Overclocking Gitify workflow with Gulp

If you don't use Gitify for your MODX project yet – **you must**! Seriously! 

Okay. What is Gitify?

Gitify is bunch of tools (commands) that make life easier. With Gitify you can install MODX, install packages (all or specified), make backup, restore backup and of course extract whole site to files and build whole site from files. Sounds like magic. And it is, I checked! :)

Here official repository of project: https://github.com/modmore/Gitify

More information about usage you can find on [wiki](https://github.com/modmore/Gitify/wiki). I'll just say that I use Gitify in all my latest MODX projects.

## Useful, but not fast

Gitify awesome tool, but written on PHP, that not allow use `watch` command, as it do nodejs (or I just was banned by Google). And after rapid work with gulp on the layouts and styles, work with Gitify looks slowly for me.

So, usually I make this steps in my workflow:

1. fix some files
2. run `gitify build`
3. check result
4. go to 1

Not difficult, but take time. You should open IDE, fix files, then open terminal, run command, then open browser and check result. Extra useless actions. It whould be nice if changes could applied by self, after save. I solved this problem and below will tell how.

## Preparing environment

For solving problem I did use nodejs, npm, Gulp, Gitify and MODX (of course).

### Install nodejs and npm

At first you should install nodejs and npm. We will use watch command and tasks for run gitify commands.
How it do you can find on [official page of nodejs](https://nodejs.org/)

### Create `package.json` on root folder of project

Usually I install node_modules to `assets/theme` folder for build by gulp all styles and scripts, but now we use gulp only for build MODX with Gitify.

Let's create package.json for install all dependencies that will need.

```json
{
    "name": "sitename.com",
    "version": "1.0.0",
    "devDependencies": {
        "gulp": "*",
        "gulp-shell": "*",
        "gulp-tap": "*",
        "gulp-watch": "*"
    }
}
```

- gulp - [Gulp](http://gulpjs.com/)
- gulp-shell - for run command from shell
- gulp-tap - plugin for tap specified files to pipeline
- gulp-watch - watch command for check MODX files updates

### Install gulp and dependencies

Now we need install Gulp globally and install local dependencies.

`npm install -g gulp`

And now just run `npm install` for installing all dependencies.

Okay, dependencies installed. Now we can start write main gulp config - gulpfile.js.

### Gulpfile.js

First lines. We need to require used modules.

```javascript
'use strict';
 
console.time('Loading plugins');
 
var gulp = require('gulp'),
    watch = require('gulp-watch'),    
    shell = require('gulp-shell'),
    tap = require('gulp-tap');
 
console.timeEnd('Loading plugins');
```

Next we need define pathes to MODX. Section `watch` matched all files in "\_data" folder for check changes. Section `modx` keep pathes to MODX root folder and to Gitify data folder.

```javascript
var path = {
    watch: {
        modx: '_data/**/*.*'
    },    
    modx: {
        root: '.',
        data: '_data/'
    }
};
```

For use our gulp config we need to create some tasks. After run `gulp` we need build whole MODX from files and then run `watch` for checking changes. Let's create default task.

```javascript
gulp.task('default', ['modx:build', 'watch']);
```

Okay. Gitify can run build for specified category of elements only, which corresponds to folder. Example: `gitify build templates` - it will build only templates. It's works faster than build whole site. So we need create task that can run this command.

But gulp pipeline not give current file name for use in function and we need create a few tasks for each of types.

```javascript
function modxTaskCreator(type) {
    gulp.task('modx:build:' + type, function () {
        gulp.src(path.modx.data + type) 
            .pipe(shell([
                //'gitify build ' + type + ' --skip-clear-cache'
                'gitify build ' + type
            ], {cwd: path.modx.root}));
        });
}
```

This function create small tasks for build specified type of element. 

Now we need create this tasks for all types of elements.

```javascript
gulp.task('modx:init', function() {
    gulp.src(path.modx.data + '*')
        .pipe(tap(function (file, t) {            
            modxTaskCreator(file.path.split('/').pop());
        }));
});
 
gulp.start('modx:init');
```

We created task for collecting all folders from "\_data" and creating task for each and then we should run this task. Now we need build all site (i.e. to run build task for each type).

```javascript
gulp.task('modx:build', function() {
    gulp.src(path.modx.data + '*')
        .pipe(tap(function (file, t) {
            gulp.start('modx:build:' + file.path.split('/').pop());
        }));
});
```

And last command - watch. We catch changes from filesystem, then determine type of element and run `build` for this type.

```javascript
gulp.task('watch', function() {
    watch([path.watch.modx], function(event, cb) {
        var path = event.path.split('/');
        var type = path[path.length - 2];        
        gulp.start('modx:build:' + type);
    });
});
```

This all. :)

### Full listing of gulpfile.js

```javascript
'use strict';
 
console.time('Loading plugins');
 
var gulp = require('gulp'),
    watch = require('gulp-watch'),    
    shell = require('gulp-shell'),
    tap = require('gulp-tap');
 
console.timeEnd('Loading plugins');
 
var path = {
    watch: {
        modx: '_data/**/*.*'
    },    
    modx: {
        root: '.',
        data: '_data/'
    }
};
 
function modxTaskCreator(type) {
    gulp.task('modx:build:' + type, function () {
        gulp.src(path.modx.data + type) 
            .pipe(shell([
                //'gitify build ' + type + ' --skip-clear-cache'
                'gitify build ' + type
            ], {cwd: path.modx.root}));
        });
}
 
gulp.task('modx:init', function() {
    gulp.src(path.modx.data + '*')
        .pipe(tap(function (file, t) {            
            modxTaskCreator(file.path.split('/').pop());
        }));
});
 
gulp.start('modx:init');
 
gulp.task('modx:build', function() {
    gulp.src(path.modx.data + '*')
        .pipe(tap(function (file, t) {
            gulp.start('modx:build:' + file.path.split('/').pop());
        }));
});
 
gulp.task('watch', function(){
    watch([path.watch.modx], function(event, cb) {
        var path = event.path.split('/');
        var type = path[path.length - 2];        
        gulp.start('modx:build:' + type);
    });
});
 
gulp.task('default', ['modx:build', 'watch']);
```

## Usage

For use this tools you can just run `gulp` in root folder and write code in IDE.

NOTE:
It works fine for work from IDE only. If you make changes via admin panel and then change file, you can lost changes made in panel. Be careful!

I hope it was useful article and now you can develop MODX sites faster and easier.

P.S.
I collected all files for usage in Gist here https://gist.github.com/Alroniks/1466b67a648949a1bbea. Feel free to give me feedback.

Thank you.