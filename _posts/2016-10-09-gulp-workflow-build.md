---
layout: post
title: "Gulp工作流的简单搭建"
categories: Workflow
tags: 自动化 项目构建
---

* content
{:toc}

刚刚看了一下 `Gulp` ，总体来说比较简洁，就五个API，其基于 **流** 的构建相比 `Grunt` 效率更高，构建方式还需深入研究，但是知道了那几个API就可以自己搭个简单的工作流了。




## 准备工作

为了演示需要，我们建一个项目，目录结构是这样的：

```ruby
    |-- src
        |-- tpl
            |-- index.swig
        |-- sass
            |-- index.scss
        |-- js
            |-- index.js
    |-- dist
    |-- gulpfile.js
    |-- package.json
```

我们需要做的是，编译压缩相应文件并输出到对应目录，实际效果应该如这样：

```ruby
    |-- src
        |-- tpl
            |-- index.swig
        |-- sass
            |-- index.scss
        |-- js
            |-- index.js
    |-- dist
        |-- static
            |-- index.css
            |-- index.js
        |-- index.html
    |-- gulpfile.js
    |-- package.json
```

编译 `swig`、`sass` 分别需要的插件是 `gulp-swig`、`gulp-sass`，启用 `npm` 一步安装：

```ruby
    $ npm install gulp-swig gulp-sass --save-dev
```

## 编写任务

`gulpfile.js` 配置文件中引入相应插件：

```js
    var gulp = require('gulp'),
        swig = reuire('gulp-swig'),
        sass = require('gulp-sass');
```

下面分别编写相应任务：

```js
    //swig编译输出
    gulp.task('tpl', function() {
        gulp.src('src/tpl/*.swig')
            .pipe(swig({
                defaults: {
                    cache: false
                }
            }))
            .pipe(gulp.dest('dist'));
    });
    //sass编译压缩输出
    gulp.task('sass', function() {
        gulp.src('src/sass/*.scss')
            .pipe(sass({
                outputStlye: 'compressed'
            }))
            .pipe(gulp.dest('dist/static'));
    });
    //js输出
    gulp.task('js', function() {
        gulp.src('src/js/*.js')
            .pipe(gulp.dest('dist/static'));
    });
```

进行任务合并：

```js
    gulp.task('build', ['tpl', 'sass', 'js']);
```

此时，运行命令：

```ruby
    $ gulp build    
```

便可以看到正常输出

## 浏览器自动刷新

这里我们需要一个插件 `browser-sync` ，安装之后引入：

```js
    var browserSync = require('browser-sync').create(); 
    var reload = browserSync.reload;
```

编写相应任务：

```js
    //各自任务
    gulp.task('tpl:dev', function() {
        return gulp.src('src/tpl/*.swig')
                   .pipe(swig({                
                            defaults: {
                                cache: false
                            }
                       }
                   ))
                   .pipe(gulp.dest('dist'))
                   .pipe(reload({
                            stream: true
                       }
                   ));
    });

    gulp.task('sass:dev', function() {
        return gulp.src('src/sass/*.scss')
                   .pipe(sass())
                   .pipe(gulp.dest('dist/static'))
                   .pipe(reload({
                       stream: true
                   }))
    });

    gulp.task('js:dev', function() {
        return gulp.src('src/js/*.js')
                   .pipe(gulp.dest('dist/static'))
                   .pipe(reload({
                           stream: true
                       }
                   ));
    });

    //合并任务、初始化服务器
    gulp.task('dev', ['js:dev', 'sass:dev', 'tpl:dev'], function() {
        browserSync.init({
            server: {
                baseDir: './dist'
            },
            notify: false
        });
    });

    //监听任务改动
    gulp.watch('src/js/*.js', ['js:dev']);
    gulp.watch('src/sass/*.scss', ['sass:dev']);
    gulp.watch('src/tpl/*.swig', ['tpl:dev']);
```

这样一个监听本地改动，浏览器自动刷新的任务就编写好了

## 一个简单的Gulp工作流

如果把以上两部分合起来，任务 `build`是打包任务，任务 `dev` 是开发任务，这样就可以构建成一个比较完整的Gulp工作流：

```js
    var gulp = require('gulp'),
        swig = reuire('gulp-swig'),
        sass = require('gulp-sass'),
        browserSync = require('browser-sync').create(),
        reload = browserSync.reload;  

    /* 开发任务 */
    gulp.task('tpl', function() {
        gulp.src('src/tpl/*.swig')
            .pipe(swig({
                defaults: {
                    cache: false
                }
            }))
            .pipe(gulp.dest('dist'));
    });

    gulp.task('sass', function() {
        gulp.src('src/sass/*.scss')
            .pipe(sass({
                outputStlye: 'compressed'
            }))
            .pipe(gulp.dest('dist/static'));
    });

    gulp.task('js', function() {
        gulp.src('src/js/*.js')
            .pipe(gulp.dest('dist/static'));
    });

    gulp.task('build', ['tpl', 'sass', 'js']);

    /* 打包任务 */
    gulp.task('tpl:dev', function() {
        return gulp.src('src/tpl/*.swig')
                   .pipe(swig({                
                            defaults: {
                                cache: false
                            }
                       }
                   ))
                   .pipe(gulp.dest('dist'))
                   .pipe(reload({
                            stream: true
                       }
                   ));
    });

    gulp.task('sass:dev', function() {
        return gulp.src('src/sass/*.scss')
                   .pipe(sass())
                   .pipe(gulp.dest('dist/static'))
                   .pipe(reload({
                       stream: true
                   }))
    });

    gulp.task('js:dev', function() {
        return gulp.src('src/js/*.js')
                   .pipe(gulp.dest('dist/static'))
                   .pipe(reload({
                           stream: true
                       }
                   ));
    });

    gulp.task('dev', ['js:dev', 'sass:dev', 'tpl:dev'], function() {
        browserSync.init({
            server: {
                baseDir: './dist'
            },
            notify: false
        });
    });

    gulp.watch('src/js/*.js', ['js:dev']);
    gulp.watch('src/sass/*.scss', ['sass:dev']);
    gulp.watch('src/tpl/*.swig', ['tpl:dev']);
```

此时，为了编译方便，可以向 `package.json` 中添加相应的命令：

```js
    "scripts"：{
        "build": "gulp build",
        "dev": "gulp dev"
    }
```

于是，开发环境时就用：

```js
    $ npm run dev
```

打包环境就用：

```js
    $ npm run build
```

## 总结

这只是用几个简单的API搭建的工作流，Gulp还有很多其他方面需要研究的，帮助我们改进自动化流程，研究仍在继续，一起加油！