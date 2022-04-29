# Webpack

[TOC]

# 1. optimization.runtimeChunk

runtimeChunk，直观翻译是运行时的chunk文件，作用是把chunks（一些异步组件等需异步加载的js）的映射关系从app.js（总入口）单独提取出来。试想一下不把这部分代码提取出来，每次修改其中一个异步组件，app.js的hash值都会改变，用户都需要重新下载，其实只有chunks的映射关系改变而已。所以runtimeChunk就是用来优化浏览器缓存的，让用户下载最少的代码就能享受到更新。

### 何为运行时代码？

