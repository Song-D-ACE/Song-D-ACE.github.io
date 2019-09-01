#### 简述

> 这里只对 ` JS ` 项目开发中遇到的一些问题做一个记录, 以免再次遇到的时候踩到同样的坑和一些思路;


### 开发中的理念和规范

#### 1. API 模块化

> 本人做项目的时候 都有一个习惯 就是分块管理 便于任务的分发和交接管理 以下举例说明：
> 1 新建一个文件夹叫 API 顾名思义 就是放置接口API的目录

`````javascript

// 新建 index.js

import common from './common'   // 张三管理
import message from './message' // 李四管理
import other from './other'     // 王五管理

const api = {
	common,
	message,
	other
};

export default api;

`````

`````javascript

// 新建 common.js

const common = [
	{
		methodsName: 'login',
		method: 'POST',
		desc: '用户登录',
		path: '/userLogin/login',
		params: {
			userName: '',
			password: ''
		}
	},
		{
			methodsName: 'getSystemConfig',
			method: 'GET',
			desc: '获取系统配置',
			path: '/config',
			mockPath: '',
			params: {
		}
	},
	{
		methodsName: 'touristLogin',
		method: 'GET',
		desc: '游客登录',
		path: '/csutomerInvitationController/phone/add',
		params: {
		code: '',
		machineCode: '',
		promotionUrl: ''
		}
	}
  ...
]

export default common;
`````

#### 2. 图片压缩 

- [tinypng](https://tinypng.com/) 图片压缩


#### 3. 样式继承 @mixin @includes 和 Sprites（不作具体演示）

>通常用来定义在整个样式表中重复使用的样式，而避免了无语义的类；大大减少了代码的重复率

`````css
@minin clearfix{
  	display: inline-block;
	&::after {  /*为啥用双冒号 因为它是伪元素 不是伪类*/
		content: "";
		display: block;
		height: 0;
		clear: both;
		visibility: hidden;
	}
}

.class-select {
	@include clearfix /*继承样式*/
}
`````

>@minxin 支持参数和默认参数

`````css
@minin border(&bc: #000, $side: all, $br: 0, $bs: solid){
	position: relative;
	&::after {
		position: absolute;
		content: "";
		top: 0;
		left: 0;
		box-sizing: border-box;
		width: 100%;
		height: 100%;
		@if $side == all {
			border: 1px $bs &bc
		}
		@else {
			$size: length($side);
			@for $i from 1 through $size {
				border-#{nth($side, $i)}: 1px $bs $bc
			}
		} 
	}
}

.class-select {
	@include border(#ccc, top);
}

`````

####  4. 自动化过滤无用文件 （也可以用webpack 但是我webpack不是特别6）

>用node的fs模块来进行文件的过滤删除处理 （给项目减肥） 当 npm run build的时候就启用该文件

`````javascript
var fs = require('fs');    // 引入文件模块 node自带的
var path = require('path') // 引入路径模块 node自带的
var rm = require('rimraf') // 下载 rimraf 模块 npm i rimraf
var proRoot = path.resolve(__dirname, '../') // __dirname全局属性 找到项目文件的名称 
var rmFolder = path.join(proRoot, 'dist/static'); // 例如你要删除 dist/static 目录下所有的东西 就把第二个参数指向 dist/static 拼上文件名称

var blackList = { // 文件黑名单
	"a": "1",
	"b": "2"
}

module.exports = () => {
	fs.readFile(proRoot + '系统配置文件', 'utf8', (err, str) => {
        var lib = str.replace(/\s+/g, ''); // 首先把这个文件内容去掉空格
        var theme = lib.substr(lib.indexOf('THEME_') + 9, 2); //然后读去配置的名称 这个名称是 文件黑名单里的内容之一
        var fsFolder = blackList[theme];  // 会找到你要留下的文件名称
        var arr = Object.values(blackList);   // 找到文件黑名单 whiteList 所有的值  "1"  "2"
        delete blackList[theme]; // 然后从黑名单删除这个属性 

        Object.keys(blackList).forEach(ele => { //然后遍历这个黑名单
            rm(path.join(rmFolder, ele), (err) => { //然后调用删除这个模块来删除文件
                //console.log(err);
            })
        })

        //这里一定要  continue 不然会把几个站点公用的文件删掉
        for (let i = 0; i < arr.length; i++) { 
            if (arr[i] == fsFolder) continue; //把配置文件里面需要留下的文件要跳过
            rm(path.join(rmFolder, arr[i]), (err) => { // 然后删除文件里面的每一项
                //console.log(err);
            })
        }
	})

}
`````

####  5. 组件懒加载

>按需引入组件

`````javascript

// 创建param.js

var lib = { "a": "1" }

// 在组件里创建 a.vue  也就是当配置文件名称为 a 的时候 只让 a 组件显示

<template>
	<component /*:is='name'*/></component> 
</template>	

	
<script>
	export default {
		data(){
			return {
				name: "",
				lib: lib.a
			}
		},
		created(){
			this.name = ()=> import(/*文件路径*/ `${this.lib}.vue`);  //按需懒加载的模式让文件更轻量
		}
	}
<script>
`````

#### 6 屏蔽sourceMap gzip压缩

>  需要对项目开发环节的开发提示信息以及错误信息进行屏蔽和防止vue文件泄漏，一方面可以减少上线代码包的大小

`````javascript
'use strict'
// Template version: 1.3.1
// see http://vuejs-templates.github.io/webpack for documentation.

const path = require('path')
module.exports = {
  dev: {
    cssSourceMap: true
  },

  build: {
    // Template for index.html
    /**
     * Source Maps
     */

    productionSourceMap: false,  // 主要是这个
    // https://webpack.js.org/configuration/devtool/#production
    devtool: '#source-map',

    // Gzip off by default as many popular static hosts such as
    // Surge or Netlify already gzip all static assets for you.
    // Before setting to `true`, make sure to:
    // npm install --save-dev compression-webpack-plugin
    productionGzip: true, // gzip压缩
    productionGzipExtensions: ['js', 'css','svg'],

    // Run the build command with an extra argument to
    // View the bundle analyzer report after build finishes:
    // `npm run build --report`
    // Set to `true` or `false` to always turn it on or off
  }
}
`````


### 一些小工具函数

#### 检测包含特殊字符

`````javascript
var testString = {
	filterString(value) {
		var blackList = new RegExp("[`~%!$@#^=''?~！@#￥……&——‘”“'？*()（），,。.、]"); //[]内输入你要过滤的字符
		var rs = ""; // 将要把筛选出来的字符串放在里面
		for (var i = 0; i < value.length; i++) {
			rs += value.substr(i, 1).replace(blackList, "");  //把黑名单的字符串替换掉 
		}
		return rs;
	},
	checksString(newName) {
		var blackListEn = /[`!@#$%^&*()_+<>?:"{},.\/;'[\]]/im,
		blackListCn = /[·！#￥（——）：；“”‘、，|《。》？、【】[\]]/im;
		if (blackListEn.test(newName) || blackListCn.test(newName)) { // 如果 blackListEn 或者 blackListCn 包含 黑名单里的项 就返回 false
			return false;
		}
		return true;
	}
}
`````
