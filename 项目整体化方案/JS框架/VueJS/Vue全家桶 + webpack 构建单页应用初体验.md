# 文章指南

### 主题
&emsp; 承接这上一篇[Vue + Webpack 构建模块化开发框架详解](https://blog.csdn.net/AngelLover2017/article/details/85052389)，我们知道了如何使用webpack对vue进行打包，从而开始我们的前端模块化开发之路，这一篇在上一篇的基础上讲解 `Vue全家桶(vue+vuex+vue-router+axios)` + `webpack` 构建一个单页应用Demo 
### 前提
&emsp; 阅读本篇内容之前，除了需要掌握上一篇内容中的前提部分的知识，还需要了解以下内容👇
- Vue全家桶系列，掌握vuex，vue-router以及axios ，不了解请移步官方教程[axios-npm](https://www.npmjs.com/package/axios)，[vuex](https://vuex.vuejs.org/zh/guide/actions.html)，[vue-router](https://router.vuejs.org/zh/guide/)

> <b>vue全家桶简单介绍</b>
> <code>vuex</code> : vuex是一种集中式状态管理模式，什么意思呢？我们在模块化开发过程中，我们以组件来作为模块单位，模块之间存在于不同的命名空间，作用域互不干预，这样保证了我们模块之间变量函数名称等不会冲突，但是有时候我们我们需要组件之间共享一些数据或者状态，我们通常的做法是传参，但是传参的做法至少有两个弊端，一是麻烦(尤其是当需要传递的参数很多时)，二是不好管理且冗余(给多个组件传参就需要多份参数列表，而且容易出错)。vuex提供的集中式管理就解决了这个问题，通过把要共享的数据或状态集中起来管理，别的组件需要时就去访问变更，大大提高了可维护性和开发效率
>
> <code>vue-router</code> : vue-router是一个前端路由管理器，这个和后端常听说的路由有些不同(个人觉得)，这里的路由管理器更像是一个组件注册器，vue-router为分散的组件注册一个路由或者叫地址也未尝不可，以方便我们控制组件的层级嵌套关系以及隐藏还是显示，这样我们可以很方便高效的构建单页应用
>
> <code>axios</code> : axios 和 jquery.ajax/vue-resource一样 ， 都是HTTP异步请求的工具，axios和vue-resource的API很像，但是个人觉得，axios的API更丰富一些

# 正文
<hr>
&emsp;本文的小Demo大概功能就是一个登陆的功能，我们这里app.vue封装了一个登陆组件，success.vue封装了一个提示面板，通过vuex来集中管理登陆状态，用vue-router来路由提示面板，用axios来异步提交到一个跨域的服务器(这里后台服务是nginx+php提供，所以在dev-server中要设置一个反向代理，也就是nginx来代理我们的dev-server的请求从而解决axios跨域问题)
<hr>

### 先来看看我们的项目结构 [自定义的]

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181220122147711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)
这里的目录设置，并不是一成不变的金科玉律，也不一定是最好的，读者可以根据自己的想法设定文件结构，了解如何配置单入口单出口的webpack.config.js，可以看我的上一篇文章(开头提到了)，或者移步webpack官方网站。本篇内容，不再讲解过多webpack配置，和上一篇是基本相同的，下面主要讲解 Vue全家桶相关内容

### index.js
```javascript
import Vue from 'vue';
import Router from './routers/index-router';
import Store from './stores/index-store';
import App from './components/App.vue';

var app = new Vue({     //创建一个Vue实例
	router : Router,   //加入路由配置
	store : Store,     //加入状态管理
	components : {App} //加入App组件
}).$mount('#app');     //挂载节点

```
需要注意的是，路由和状态都需要加入一个vue实例中去才有意义。这里有一点很有意思，就是在路由router中注册的组件(success.vue)依然可以访问到状态store实例，而官方教程上并没有特别强调这一点，在下面我们可以看到这个有趣的事情
### stores/index-store.js
```javascript
import Vuex from 'vuex';
import Vue from 'vue';
import axios from 'axios';

Vue.use(Vuex); //在vue中加入Vuex插件
const Store = new Vuex.Store({ //实例化一个Store
	state : {   //这里是我们需要集中管理的登陆状态
		account : "ads",
		password : "123456",
		islogin : null
	},
	mutations : {   // 这是唯一可以变更state的途径，需要通过一个提交
		updateAccount(state,payload){  //具有载荷的mutation
			state.account = payload.account
		},
		updatePassword(state,payload){
			state.password = payload.password
		},
		islogin(state,payload){
			if(payload == 1){
				state.islogin = true
			}else if(payload == 0){
				state.islogin = false
			}
		}
	},
	actions : {   //这是可以支持异步执行提交的actions
		login (context,payload){
			//这里是去访问我们的反向代理服务器上的一个php文件
			axios.get('/index.php',{
					params : {
						account : payload.account,
						password : payload.password
					}
				}).then(function(response){
					if(response.data.code == 'success'){
						//变更store状态
						context.commit('islogin',1) //这里做了在异步逻辑中的状态提交
					}else{
						context.commit('islogin',0)
					}
				}).catch(function(error){
					console.log(error)
					alert('fail')
				})
		}
	}
})

export default Store
```
我们这里的Store有三个属性，`state`是我们需要集中管理的状态或者数据， `mutations`是维护变更我们状态的一个方式， `actions`是为了在异步逻辑中提交状态的一个中转站，需要注意的是：
> 在实例化Store之前，必须先 Vue.use(Vuex) ，先在vue中加入Vuex插件

这里需要说一下的是，如何开启dev-server的proxy，让nginx来代理axios的请求，也就是跨域访问了(因为dev-server跑的是8080端口，nginx跑的是80端口，不设置代理的话浏览器将拒绝跨域访问)，感觉很深奥，其实很简单，我们需要修改一下webpack.config.js中devServer的配置 ： 

webpack.config.js
```javascript
	devServer : {
		contentBase : './dist',
		watchContentBase : true,
		compress : true,
		port : 8080,
		hot : true,
		inline : true, //开启页面自动刷新
		open : true,
		proxy : {
			'/index.php' : {
				target : 'http://localhost:80/phpinfo.php',
				secure : false
			}
		}
	}
```
然后，我们的axios在配置url时使用 `/index.php`，就可以相当于访问`http://localhost:80/phpinfo.php`
### routers/index-router.js
```javascript
import VueRouter from 'vue-router';
import Vue from 'vue';
import success from '../components/success.vue';

// 组件注册路由，这里success组件注册路由是'/app'
var routes = [
	{
		path : '/app',component : success
	}
]
Vue.use(VueRouter);   //在vue中加入VueRouter插件
var Router = new VueRouter({   //实例化一个router
	routes   //这里的写法相当于 routes:routes
})


export default Router;
```
这里的路由只注册了一个组件，vue-router的能力远不止如此，还可以嵌套定义组件的层级关系，更多到官方文档了解 ，路由器和状态管理器一样都是vue官方核心插件，使用前都需要先 `Vue.use(VueRouter)` ，我们其实可以看到，这里把路由和状态分别从vue实例中抽离出来，单独管理，通过暴露实例`Store` `Router`，加入到vue实例中去，这样可以方便debug和后期维护

### components/App.vue   和  components/success.vue

App.vue
```html
<template>
	<div>
		<input type="text" v-model="account">
		<input type="password" v-model="password">
		<button type="button" @click="login">show</button>
	</div>
</template>
<script>
 	export default {
 		data : function(){
 			return {
 				account : "",
 				password : ""
 			}
 		},
 		created : function(){
 			this.account = this.$store.state.account
 			this.password = this.$store.state.password
 		},
 		watch : {  //侦听属性
 			account : function(){ //当accout改变就立即提交状态
 				this.$store.commit('updateAccount',{
 					account : this.account
 				})
 			},
 			// 当password改变就立即提交状态
 			password : function(){
 				this.$store.commit('updatePassword',{
 					password : this.password
 				})
 			}
 		},
		methods : {
			login : function(){ //通过分发到action，来异步请求登录服务，并变更状态
				this.$store.dispatch('login',{
					account : this.account,
					password : this.password
				})
				this.$router.push('/app');//同时显示登陆状态
			}
		}
 	}
</script>
<style scoped>
	input {
		height:  40px;
		width:   300px;
		border: 1px solid blue;
		box-shadow: none;
	}
</style>
```
success.vue
```html
<template>
	<div>
		<p>{{islogin}}</p>
	</div>
</template>
<script>
 	export default {
 		computed : { //计算属性
 			//动态响应状态的变更
 			islogin : function(){
 				var islogin = this.$store.state.islogin
 				if(islogin == null){
 					return 'No option'
 				}else if(islogin == true){
 					return 'Login Success'
 				}else if(islogin == false){
 					return 'Login Fail'
 				}
 			}
 		}
 	}
</script>
<style scoped>
	p {
		height:  40px;
		text-align: center;
	}
</style>
```
### templates/index.html
```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>index</title>
</head>
<body>
	<div id="app">
	   <!-- App组件渲染出口 -->
		<App></App>
		<!-- 注册路由的组件渲染出口 -->
		<router-view></router-view>
	</div>
</body>
</html>
```
### 总结
好了，到此为止，代码基本完成，我们来理一理，vue+vuex+vue-router以及组件之间是如何配合工作的？

用户在`App.vue`组件中输入，`v-model`双向绑定了`App`组件中的`data`，App组件中的侦听属性发现data改动，立即向`vuex`的`Store`实例提交了状态变更，当用户点击`show`，App组件中`methods.login`事件被触发，它向Store实例发起一个分发，并导航到`/app`，`Store`的`action`收到分发调用`axios`去异步请求位于`localhost:80`下的`php`后台服务，得到的登陆状态立即提交，与此同时，`Router`去渲染`/app`路由对应的组件到模板中去，于是我们就可以看到一个动态响应式的状态提示了