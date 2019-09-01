#### 简述

> 这里就不对 ` Vue ` 做详细的介绍了, 具体可以参考 [Vue](https://cn.vuejs.org/index.html) 官方网站;

这里只对 ` Vue ` 项目开发中遇到的一些问题做一个记录, 以免再次遇到的时候踩到同样的坑;


### 初始化项目

` Vue ` 官方网站为我们提供了一个 ` Vue-cli ` [Vue-cli 中文文档](https://cli.vuejs.org/zh/) 脚手架工具;

不过, 这里需要注意的是 ` vue-cli@3x ` 和 ` vue-cli@2x ` 的安装使用方法是不一样的 ; 

> 安装和创建项目:

`````bash
npm install vue-cli -g

vue create my-project               // `vue-cli@3x `

vue init webpack my-project         // `vue-cli@2x `

`````
安装的时候, ` bash ` 终端会有提示是否要使用 ` vuex ` , ` e2e ` , ` Eslint ` 等插件工具, 这里我们可以通过项目的复杂程度判断是否需要安装 ; 

### 路由配置

` vue ` 官方有为我们提供了一个强大易用的路由库 ` vue-router `[vue-router 官方文档](https://router.vuejs.org/zh/) ;

- 支持路由嵌套
- 支持模块化的, 基于组件的路由配置
- 支持路由参数, 查询, 通配
- 支持H5 history 模式和 hash 模式
- 支持自定义滚动行为

#### 路由懒加载

初始化项目的时候, ` vue-cli ` 会为我们创建一个 router 的文件夹, 并会生成 index.js 文件, 配置如下:

`````javascript
import Home from '@/components/Home'
export default new Router({
    mode: 'history', // hash 默认的就是这个 hash 哈希模式
    routes: [
        {
            path: '/',
            name: 'Home',
            component: Home
        }
    ]
})
`````
当我们打包构建应用时候, js 包会变的非常大, 影响页面的首次加载速度, 如果我们能够把不同的路由对应的页面拆分成不同的代码块;

然后, 当每一个路由被访问时候才加载对应的组件, 这样就很大程度上提高了加载效率;

可以看到, 这里的路由并有没使用懒加载的模式, 我们可以对路由文件稍作修改;

`````javascript
export default new Router({
    routes: [
        {
            path: '/',
            name: 'Home',
            meta: {
                title: "首页"
            },
            component: resolve => require(["@/components/Home"], resolve) // commonjs的规范
            /*
            *   还有另一种 ecmascript 的规范
            *   component: () => import("@/components/Home")   
            *   注意： 这种写法在vue-cli@2x 的版本中会报错 Module build failed: SyntaxError: Unexpected token
            *   因为import这儿报错了，这就需要babel的插件了，vue-router官网上有一段提示：如果您使用的是 Babel，你将需要添加 syntax-dynamic-import 插件，才能使 Babel 可以正确地解析语法。
            *   1：npm i babel-plugin-syntax-dynamic-import --save-dev
            *   2：修改 webpack.base.conf.js module对象下面的js部分 增加一个选项                 
            *   options: {
            *        plugins: ['syntax-dynamic-import']
            *   }, 
            *   至此，问题全部解决了。
            *   提示：如果是vue-cli@3x 就不用这么麻烦了      
            */
        }
    ]
})
`````
上面 , 我们把路由更换成了懒加载模式, 并且, 还给路由添加了一个 ` meta ` 属性, 将页面的 title 通过 ` meta ` 属性传递到组件内;

在 ` Home ` 这个组件内我们就可以通过 ` $route.meta.title ` 来使用页面的 ` title ` 字段值了;

#### 路由嵌套

在大型的项目中, 路由嵌套也是很常用的功能 ;

` vue ` 的路由嵌套, 有了 ` vue-router `这个路由库, 实现起来也是非常简单的, 就拿上面的 ` Home ` 来说, 如果我们想在 Home 页里面嵌套一个子路由;

要在 ` Home `组件中添加一个 `<router-view> `, 然后在 ` Router ` 中使用 `children ` 参数:

`````html
<div class="Home">
    <router-view></router-view>
</div>
`````
`````javascript
export default new Router({
    routes: [
        {
            path: '/',
            name: 'Home',
            meta: {
                title: "首页"
            },
            component: resolve => require(["@/components/Home"], resolve),
            children:[{
                path: '/Profile',
                name: 'Profile',
                component: resolve => require(["@/components/Profile"], resolve),
            }]
        }
    ]
})   
`````
#### 过渡动画

> 让 webapp 拥有类似 iOS 页面切换左右滑动的动画, 用到的是 ` vue ` 自带的过渡动画 ` <transition></transition> `; 


##### 实现方式

`````javascript
//修改router/index.js文件,并在Router的原型链上扩展一个方法
Router.prototype.goBack = function () {
    this.isBack = true
    this.go(-1)
    // 提示：也可以用传参的形式 这样可以动态前进几步或者后退几步，当然router还有push、replace的方法都可以进行封装 在这里就不一一展示
}
`````
`````javascript
//在带有<router-view/>的页面内监听路由的update钩子函数做相应的操作
beforeRouteUpdate (to, from, next) {
    // 如果isBack为true时，证明是用户点击了回退，执行slide-right动画
    let isBack = this.$router.isBack
    if (isBack) {
        this.transitionName = 'slide-right'
    } else {
        this.transitionName = 'slide-left'
    }
    // 做完回退动画后，要设置成前进动画，否则下次打开页面动画将还是回退
    this.$router.isBack = false
    next()
}
`````
`````html
<!-- 通过vue自带的 transition 做过渡动画  -->
<transition :name="transitionName">
    <router-view class="child-view"></router-view>
</transition>
`````
`````css
/* 
* css transition 进入和移出的动画
*/
.child-view {
    position: absolute;
    width: 100%;
    transition: all .45s cubic-bezier(.55, 0, .1, 1);
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: #f1f1f1;
    -webkit-backface-visibility: hidden;
    -webkit-transform-style: preserve-3d;
    z-index: 10;
}
.slide-left-enter,
.slide-right-leave-active {
    opacity: 1;
    -webkit-transform: translate3d(100%, 0, 0);
    transform: translate3d(100%, 0, 0);
    z-index: 1000;
}
.slide-left-leave-active,
.slide-right-enter {
    opacity: 1;
    -webkit-transform: translate3d(-100%, 0, 0);
    transform: translate3d(-100%, 0, 0);
}
`````

#### 路由组件化管理

> 1 在router.js 编写主配置  

`````javascript
import Vue from 'vue' // 导入 VUE 模块
import Router from 'vue-router' // 导入 vue-router模块
import routes from './routes'; // 导入子路由
Vue.use(Router)
const router = new Router({
  mode: 'history',            // 这里可以定义路由模式
  base: process.env.BASE_URL, //基础路由
  routes: [
    ...routes                 //分解子路由数组
  ]
})

export default router

`````

> 2 新建一个文件夹为 routes 里面新建几个js文件 目的就是模块化路由组件方便管理

`````javascript
import a from './a'
import b from './b'
import c from './c'

const routes = [
    ...a,
    ...b,
    ...c
];
export default routes; // 导出这个路由数组

`````

> 3 新建路由子组件 a.js b.js c.js

`````javascript
const a = [
    {
      path: '/a',
      name: 'a',
      component: () => import(/* webpackChunkName: "a" */ '@/views/a/index.vue'),
    },
    {
      path: '/aa',
      name: 'aa',
      component: () => import(/* webpackChunkName: "aa" */ '@/views/aa/index.vue'),
    }
  ]
  export default a

`````

> 路由分块管理更方便于维护 如果把整个项目的路由都集中在一个地方管理 即便逐行注释也会很乱，所以用此方法更方便于大项目的管理，如果给组员分任务，一个人维护一个模块就可以了也尽量避免了提交代码时候造成代码冲突;


#### 保存位置

>以下是后退页面显示在上次浏览的位置 如果页面数据需要请求加载有延迟的情况 页面直接出发滚动 页面数据再加载进来 DOM又重新渲染 滚动就废了 这是官方给的方法 因为这跟eventloop执行顺序有关系 就记住一句话 普通代码先执行 然后 Promise 执行 最后 时间函数执行 就那这个滚动来说，普通js先执行造成dom渲染了，然后延迟的数据也加载完了，dom又重新渲染一次，这就造成了滚动条计算错误。因为promise在任务队列的中间 就是为了跟数据进行平行执行，然后所有异步加载完毕，再执行时间函数，时间函数永远是所有的js代码执行完了它再执行，等所有数据加载完毕，再滚动条再重新进行计算，就不会错误了 

`````javascript

scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
        return savedPosition
    } else {
        return new Promise((resolve, reject)=>{
            setTimeout(() => {
                resolve({ x: 0, y: 0 })
            }, 500)
        })
    }
}

`````

#### 路由守卫管理

> 我就是举例说明 把事件函数整理到 config/router.js去管理 功能模块按需导出 after并不是守卫它也就是个钩子函数 beforeRouteLeave beforeRouteEnter 不能和一下两个方法同时使用

`````javascript

import {routerBeforeEachFn, routerAfterEachFn} from '@/config/router'

router.beforeEach(routerBeforeEachFn)

router.afterEach(routerAfterEachFn)

`````

`````javascript

export function routerBeforeEachFn(to, from, next){
    console.log(1)
}

export function routerAfterEachFn(to, from){
    console.log(2)
}

`````


### 多语言

>由于项目需要, 会接触到一些外资项目; 因此, 我们需要在项目里面添加多语言;

#### 实现方式

` vue ` 项目里面使用多语言也是比较方便的, , 只需要安装 `vue-i18n` 这个插件就可以实现网站多语言;
`````bash
npm install vue-i18n --save
`````
然后在我们的项目的 ` src/assets ` 文件夹下面新建一个 ` i18n.js ` 文件, 和 ` lang/index.js `文件夹, 以便于我们来管理好语言包;

*i18n.js*
`````javascript
import Vue from 'vue';
import VueI18n from 'vue-i18n';
import Language from './lang';
Vue.use(VueI18n);

export default new VueI18n({
	locale:'zh',
	messages:Language
})
`````
*lang/index.js*
`````javascript
import zh from './zh-CN';
import en from './en-US';
import ja from './ja-JP';
export default{
	zh,
	en,
	ja
}
`````
这样我们就通过 ` Vue.use() `方法将语言插件添加到 ` Vue.prototype `上了; ([Vue.use() 实现方式](https://cn.vuejs.org/v2/guide/plugins.html));

然后我们需要在 ` src/main.js `文件里面引用一下, 将插件挂载到 `vue` 实例下面;

`````javascript
import Vue from 'vue'
import App from './App'
import router from './router'
import i18n from './assets/i18n'
import store from './store';
Vue.config.productionTip = false
FastClick.attach(document.body);
/* eslint-disable no-new */
new Vue({
	el: '#app',
	router,
	components: {
		App
	},
	i18n,
	store,
	template: '<App/>'
})
`````

通过修改 ` VueI18n `的 ` locale `参数就可以替换语言包了; 我们可以在每一个组件里面调用 ` this.$i18n.locale = 'en' `,来动态切换语言了;

`````javascript
<template>
	<ul class="dropdown-menu">
        <li>
            <a href="javascript:;" /*@click="ChangeLanguage('zh')"*/>简体中文</a>
        </li>
        <li>
            <a href="javascript:;" /*@click="ChangeLanguage('en')"*/>English</a>
        </li>
        <li>
            <a href="javascript:;" /*@click="ChangeLanguage('ja')"*/>セーター</a>
        </li>
    </ul>
</template>
<script>
    export default {
        data(){
            return{}
        },
        methods:{
            ChangeLanguage(param){
                this.$i18n.locale = param;
            }
        }
    }
</script>
`````
#### 多语言使用

上面我们将 `i18n` 挂在到了 `vue`的实例上, 这样我们就可以在每一个 ` vue `组件内通过 ` $t` 来调用语言包了;[`i18n 源码`](https://github.com/kazupon/vue-i18n/blob/7c5fd95805dbcc6620cccf5343040f2cc515756c/dist/vue-i18n.js)

`````html
<template>
    <div class="Home">
        {{$t('nav.item-1')}}
    </div>
</template>
`````

#### 多语言持久化

上面的代码实现了页面切换多语言的功能, 但是这样有一个问题就是, 每次用户刷新浏览器之后页面上又恢复了 ` i18n.js `里面默认的 ` locale` 值;

所以我们需要将用户切换语言之后的 `locale` 值存到页面 `cookie`里面, 然后在网站入口去读取 `cookie` 里面的值, 这样就达到了持久化的目的;

*template*
`````javascript
<script>
    export default {
        data(){
            return{}
        },
        methods:{
            ChangeLanguage(param){
                this.$i18n.locale = param;
                var date = new Date();
                date.setDate(date.getDate() + 365);
                //设置一个过期时间为 365 天的 cookie; 或者存储在localstroage里进行无时间永久存储
                document.cookie = 'locale=' + param + ';expires=' + date;
            }
        }
    }
</script>
`````
*App.vue*
`````javascript
<script>
    export default {
        data(){
            return{}
        },
        beforeCreate(){
            this.$nextTick(() => {
                var arr = document.cookie.replace(/\s/g, "").split(';');
                var Language = null;
                for (var i = 0; i < arr.length; i++) {
                    var tempArr = arr[i].split('=');
                    if (tempArr[0] == name) {
                        Language = decodeURIComponent(tempArr[1]);
                    }
                }
                if (Language && Language != null && Language != "null") {
                    this.$i18n.locale = Language;
                } else {
                    this.$i18n.locale = "zh";
                }
            });
        }
    }
</script>
`````

### 组件封装

`vue` 开发项目中, 我们会使用到第三方的 UI 库, PC端当前比较流行的主流 UI 有 [`ElementUI`](https://element.eleme.cn/#/zh-CN), [`Ant-Aesign`](https://vue.ant.design/docs/vue/introduce/), 移动端也有一些比较常用的 UI 库[`vux`](https://doc.vux.li/zh-CN/), [`Mint-UI`](http://mint-ui.github.io/#!/zh-cn),练习中发现一个 UI 库也很好(国内滴滴出行的UI库)[`Cube-UI`](https://didi.github.io/cube-ui/#/zh-CN);

由于我们有一个移动端的项目, 需要打包成APP, 并且需要使用到IOS和Android的一些原生的功能, 所以选用的UI库是 [`MUI`](http://dev.dcloud.net.cn/mui/ui/),配合 `Dcloud`提供的 H5+方法使用;
但是 `MUI`里面提供的一些组件并不能满足我们的开发需求, 所以这时候就需要自己去开发一些组件了;

#### 键盘组件

由于项目里面有输入银行卡, 用户密码等敏感操作; 使用系统的键盘会有一定的安全风险, 并且, 在项目里面, 输入框如果固定在底部的话, 用系统自带的输入键盘会有各种各样的问题(别问我为什么会有兼容问题,  - -! 已经被万恶的 UC 整哭好多次了);所以, 后面我们是把所有系统输入数字的地方全都替换成自定义组件的键盘;

首先, 我们在 `src`下面新建一个 `plugin`文件夹, 后面这个文件夹里面就存放我们自定义的组件;

*plugin/Keyborad.vue*
`````html
<template>
    <transition name="fade">
        <div id="pluginKeyborad" 
             :style="onShow ? {'z-index':'1000'} : {'z-index':'100'}" 
             v-show="onShow" 
             @touchend="showBox()">
            <transition name="fade">
                <div id="keyborad-mask" v-if="onShow"></div>
            </transition>
            <transition name="action">
                <div class="keyborad" v-if="onShow"  @touchend.stop>
                    <table class="table-number" cellpadding="0" cellspacing="0" border="0">
                        <tr>
                            <td colspan="4" class="before-none"><p>{{result}}</p></td>
                        </tr>
                        <tr v-for="(arr,i) in values" :key="i">
                            <td v-for="(num,index) in arr" 
                                :key="index" @touchend="onKeyTouch(num)">
                                <span :style="num == 'C' ? {'color':'red'} : {}">{{num}}</span>
                            </td>
                        </tr>
                    </table>
                </div>
            </transition>
        </div>
    </transition>
</template>
<script>
	/*
	 *  onShow     控制小键盘显示或者隐藏
	 *  isNumber   当前键盘点击，如果不是纯数字的话那就执行加法运算，如果是数字的话就返回数字组合
	 * */
	export default{
		props:{
			onShow:{
				type:Boolean,
				default:false
			},
			isNumber:{
				type:Boolean,
				default:false
			}
		},
		data(){
			return{
				values:[
					[1,2,3],
					[4,5,6],
					[7,8,9],
					['C',0,'X']
				],
				result:'',
                count:0,
                isMaxNum : false
			}
		},
		methods:{
            showBox() {
                this.$emit('updateShow', '1',this.result);
                this.result = '';
				this.count = 0;
            },
			onKeyTouch(num){
                var len = this.result.toString().length+1;
                if(len>22){
                    return false
                }
				if(num == 'X'){
					this.result = this.result + '';
					try{
						this.result = this.result.substr(0,this.result.length -1);
					}catch(e){}
					if(this.result == ''){
						this.result = '';
						this.count = 0;
					}
					return;
				}
				
				if(!this.isNumber){
					this.result += num;
				}else{
					this.count += num;
					this.result = this.count;
				}
				
				if(num == 'C'){
					this.result = '';
					this.count = 0;
                }
			}
        },
	}
</script>
<style scoped="scoped" lang='scss'>
	#pluginKeyborad{
		position: fixed;
		left: 0;
		bottom: 0;
		backface-visibility: hidden;
		z-index: -1;
		width: 100%;
		height: 100%;
		background:transparent;
	}
	#keyborad-mask{
		position: fixed;
		z-index: 1000;
		width: 100%;
		height: 100%;
		top: 0;
		right: 0;
		left: 0;
		bottom: 0;
		background: rgba(0, 0, 0, .5); 
	}
	.keyborad{
		position: fixed;
		left: 0;
		bottom: 0;
		transform: translate(0, 0);
		backface-visibility: hidden;
		z-index: 5000;
		width: 100%;
		transition: transform .35s;
	}

	.fade-enter,
	.fade-leave-active{
	  opacity: 0;
	}
	.fade-leave-active,
	.fade-enter-active {
	  transition: opacity 300ms!important;
	}
	
	.action-enter,
	.action-leave-active{
	  transform: translate(0, 100%);
	}
	.action-leave-active,
	.action-enter-active {
	  transition: transform 300ms!important;
	}
	.table-number{
		width: 100%;
		background: #fff;
		float: left;
		cursor: pointer;
        overflow: hidden;
	}
	.table-number tr{
		height: 1rem;
	}
	.table-number tr td{
		position: relative;
		width: 33.3333%;
        font-weight: 700;
	}
	.table-number tr td:active{
		background: #f1f1f1;
	}
	.table-number tr td:after {
		content: "";
		display: block;
		border-bottom: 1px solid #f5f5f5;
		width: 100%;
		background: #ccc;
		position: absolute;
		bottom: 0;
		left: 0;
		transform-origin: 0 0;
		transform: scaleY(0.5);
	}
	.table-number tr td:before{
		display: block;
		content: "";
		width: 1px;
		height: 100%;
		background: #ccc;
		position: absolute;
		right: 0;
		top: 0;
		transform: scaleX(0.5);
	}
    .before-none{
        overflow: hidden;
        word-break: break-all;
        p{
            white-space:nowrap;
            overflow: hidden;
            width: 100%;
            font-size: 0.28rem;
            font-weight: 700;
            color: #304356;
            padding: 0 0.24rem;
            box-sizing: border-box;
            text-align: left;
        }
    }
	.table-number tr .before-none:before{
		display: none;
	}
</style>
`````
*使用方法*
`````

`````
这样, 一个简单的键盘输入组件就完成了, 这里涉及到我们可以在其他的 `<template>` 组件里面通过 `import` 的方式加载这个键盘组件, 然后传入`props`;

这里涉及到了一些功能点 `vue`组件之间的通信`props`, `$emit`等, 详见 [vue props](https://cn.vuejs.org/v2/guide/components-props.html);

这里只是项目中实现的一个简单的键盘组件, 其他更多复杂的组件可以去[我的 github](https://github.com/Scorpio-nan/H5-CSS3/tree/master/Vue-Components) vue 组件库;

### 常见问题

> 这里列举一些开发过程中遇到一些问题, 并且附带解决方案, 以便后面再次遇到此类问题的时候, 会马上想到解决方案 ; 

#### 组件复用

实际开发过程中, 经常会遇到这样的需求, 就是页面级组件复用的问题; 如果同一个组件被不同的页面引用的时候, 组件的生命周期不会重新实例化;

`````javascript
{ path : 'creted' , component : { PostCreate , name : ' 发表文章' }} ,
{ path : 'edit/ : id ( \\d+)' , component : { PostCreate , name : ' 编辑文章' }}
`````
真实的业务场景中，编辑的页面使用的是同一个 ` component `(如上，发表文章和编辑文章用的是同一个.vue 文件);

默认情况下当这两个页面切换时并不会触发 ` vue ` 的 ` created ` 或者 ` mounted `钩子;
` Vue ` 官方的文档里面 是建议 ` watch ` ` $router `的变化来处理, 类似于这样的:

`````javascript
watch: { 
    '$route '( to, from ,next){
        this.$router.replace('/undefined');
        setTimeout(() => {
            this.$router.push({path : ' creted ' });                
        }            
    }        
}
`````
先让路由跳转到一个` undefined `的空页面, 然后在跳转回来, 这样做可以解决问题, 但是会有一个新的问题, 就是 `history`按钮会退回到空白页;

后来发现其实可以简单的在 ` <router-view>` 上加上一个唯一的` key `, 来保证路由切换时都会重新渲染触发钩子了.

`````javascript
<router-view :key="key"></router-view>
computed: {
    key() {
        return this.$route.name !== undefined? this.$route.name + +new Date(): this.$route + +new Date()
    }
}
`````
#### 一键换肤

使用手机APP应用的时候, 一些APP里面会有一个一键换肤的功能; 

要先写好样式表 想要多少个主题颜色就需要建立多少个样式表
`````css

:root {
    --theme-color: #000;
    --theme-bg-color: blue;
}

`````
在主页里面引入

`````html
<link rel="stylesheet" href="static/css/Skin/blue.css">
`````

在vue-的初始化数据代理里面设置初始化数据

`````javascript
Skin_theme: [
    {
        theme: "大红色", //主题颜色名字
        color: "red"    //颜色
    {
        theme: "大绿色",
        color: "green"
    },
    {
        theme: "大蓝色",
        color: "blue"
    }
]
`````

模版里面加入事件

`````javascript
<template>
  <div id="app">
    <img src="./assets/logo.png" />
    <div>
      <button
        v-for="(item, index) in Skin_theme"
        :key="index"
        :style="{'background-color': item.color }"
        @click="change_color(item.color)"
      >{{item.theme}}</button>
    </div>
    <router-view />
  </div>
</template>
`````
逻辑实现

`````javascript
change_color(color){
    const link = document.querySelectorAll('link'); //首先找到link的标签集合
    const head = document.querySelector('head'); //因为link标签写在了<head>标签里所以要找到这个节点

    for(let i=0;i<link.length;i++){
        if(link[i].href.includes('red') || link[i].href.includes('green') || link[i].href.includes('blue')){ //如果标签里面包含数据这几个文字
            const createLink = document.createElement('link'); //新建link标签
            createLink.href = `static/css/Skin/${color}.css`; // 复制属性
            createLink.rel = "stylesheet" // 复制属性
            head.removeChild(link[i]); //删掉不要的节点（为了优化性能）
            head.append(createLink); //生成新节点
        }
    }
}
`````

#### 插件注入

>不要把大段的引入插件写在main入口文件里面

`````javascript
import a from 'a'
import b from 'b'
import c from 'c'
...


export default {
    install: (Vue, options)=> {
        Vue.prototype.$a = a;
        Vue.prototype.$b = b;
        Vue.prototype.$c = c;
        ... //哪些插件需要引入一一写在这里
    }
}

// 最后在入口文件里引入
// 挂载插件
import mount from '@/core/mount';
Vue.use(mount);

`````

#### Vue SEO 优化

##### seo优化之预渲染prerender-spa-plugin

平常我们开发过程中都是使用 ` vue-cli ` 来开发项目, 但是这种 spa 应用是非常不利于 seo 优化的;而 Vue 针对 seo 优化也有自己的解决方案; `vue ssr`[Vue服务器端](https://ssr.vuejs.org/zh/) 和 [NUXT](https://zh.nuxtjs.org/guide/installation); 这些都是 vue 的服务端渲染方案;

预渲染, 相对于 SSR 比较简单, 并且可以极大的提高网页的性能; 而且, 配合一些 meta 插件, 基本上能完全满足 SEO 需求;

安装
`````bash
npm install prerender-spa-plugin vue-meta-info --save
`````

`prerender-spa-plugin` 安装过程中, 会为我们下载一个 chromeium 的浏览器; 原理就是将我们页面的路由全部在浏览器中打开一次, 然后爬出页面相对应的内容, 再输出为 html 静态文件; `vue-meta-info` 这个插件的作用是为我们每一个打包出来的静态页面添加一个 title , content, discripetion , 也就是我们 seo 优化内容里面的关键词;

> 需要注意的是, 使用这个插件, 路由就需要改成同步加载的方式;

配置

***webpack.prod.conf.js***
`````javascript
const PrerenderSPAPlugin = require('prerender-spa-plugin')   //引用插件
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;
//webpack的plugin 下面添加一个插件
...
plugins:[
	// 配置PrerenderSPAPlugin
	new PrerenderSPAPlugin({
		// 生成文件的路径，也可以与webpakc打包的一致。
		staticDir: path.join(__dirname, '../dist'),

		// 对应自己的路由文件，比如index有参数，就需要写成 /index/param1。
		routes: ['/', '/allLottery','/openResult','/DaohanZhandian','/caiba','/Home'],

		// 这个很重要，如果没有配置这段，也不会进行预编译
		// renderer: new Renderer({
		// 	inject: {
		// 	  foo: 'bar'
		// 	},
		// 	headless: false,
		// 	// 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
		// 	//renderAfterDocumentEvent: 'render-event'
		// })
		
		//一开始是用上面那种写法, 但是编译会报错;
		renderer: new PrerenderSPAPlugin.PuppeteerRenderer({//这样写renderAfterTime生效了
			renderAfterTime: 5000
		})
	}),
]
`````
修改完 webpack 配置之后, 我们还需要在 main.js 里面添加一行配置

***main.js***
`````javascript
new Vue({
    el: '#app',
    router,
    store,
    components: { App },
    template: '<App/>',
	mounted () {
        document.dispatchEvent(new Event('render-event'))
        //document.dispatchEvent(new Event('custom-render-trigger'))
	}
})
`````
这样, 就配置完成了, 再次运行一下 `npm run build` 会看到 dist 目录下面, 我们的代码全都被打包成了一个个的 .html文件;

##### 头部标签插件vue-meta-info
上面, 我们已经安装好了 `vue-meta-info` 插件, 再次修改一下 main.js, 在 main 里面添加;

***main.js***
`````javascript
import MetaInfo from 'vue-meta-info';
Vue.use(MetaInfo);
`````
***template.vue***
`````javascript
export default {
	metaInfo: {
		title: 'We Inc',
		meta: [
			{
				name: 'keywords',
				content: '关键字1,关键字2,关键字3'
			},
			{
				name: 'description',
				content: '这是一段网页的描述'
			}
		]
	}
}
`````
这样, 就可以将关键字预渲染到 .html 页面中去了;
