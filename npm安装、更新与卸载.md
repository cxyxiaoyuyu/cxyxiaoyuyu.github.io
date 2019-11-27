---
title: npm安装、更新与卸载
date: 2019-10-24 16:47:53
tags: npm
categories: npm
---

# npm 安装、更新与卸载

## 本地安装

    npm install <package-name>
    npm install <package-name> --save  
    npm install <package-name> --save-dev

**安装完安装包会出现在当前目录的node_modules下**

### --save 与 --save--dev的区别
> --save 保存在package.json 的 dependencies 字段
> --save-dev 保存在package.json 的 devDependencies 字段

#### 生产环境依赖包   
> "dependencies": These packages are required by your application in production.   

#### 测试环境依赖包   
> "devDependencies": These packages are only needed for development and testing.

## 更新本地安装包

    npm update

更新完成后 执行 `npm outdate` 没有任何输出

## 本地卸载

    npm uninstall <package-name>  

不加参数可自动卸载，也可添加参数指定卸载

    npm uninstall <package-name> --save
    npm uninstall <package-name> --save-dev


## 全局安装

    npm install -g <package-name>

**全局安装路径可通过 `npm config get prefix` 查看**

## 更新全局安装包

    npm update -g <package-name>   // 指定更新某个包
    npm outdated -g --depth=0      // 更新需要更新的
    npm update -g                  // 更新全局所有的

## 卸载全局安装的包

    npm uninstall -g <package-name>


## 更新npm

### 查看npm版本

    npm -v

#### 更新成为最新的
    
    npm install npm@latest -g

### 将要发布的下一版本
    
    npm install npm@next -g

