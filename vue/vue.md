### 一、全局注册filter ###
    vue.filter(id,definition);
	eg:	 
	Vue.filter('my-filter',function(value){
	
	})

###二、 scoped的作用于 ###
question：为什么父组件添加scoped后不能影响子组件
   
解决方法：you can	include both scoped and non-scoped styles in the same component  

故可以将父组件的样式的作用域定义为scoped,将要作用于子组件的样式定义为全局

注：A child component's **root** node will be affected by both the parent's scoped css and the child's scoped css(这里指的是子组件的根元素会受父组件scoped作用域的css的影响)

###三、 $el: ###

###四、 $event: ###

###五、 缓存组件 ###
#### a、vue2.10之前的实现方式： keep-alive与router-view结合####
app.vue  

	<keep-alive>  
      <router-view v-if="$route.meta.keep_alive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keep_alive"></router-view>

index.js  
	
	routes: [
	    {
	      path: '/dormitoryManage',
	      name: 'DormitoryManage',
	      component: DormitoryManage,
	      meta:{keep_alive:true},
	      beforeEnter: (to, from, next) =>
	      {
	        hm.setTitle("宿舍管理", "","", true);
	        next();
	      }
	    }
	]
	//同过meta属性来配置组件是否缓存
	不是第一次进入keep-alive组建的话，钩子函数的调用顺序：
	beforeEach->beforeRouteEnter->actived->beforeUpdate->beforeRouteEnter的next函数，created函数，mounted函数不再执行

#### b、vue2.10版本之后keep-alive的include、exclude属性控制组件是否缓存####
include、exclude属性允许组件有条件地缓存，二者都可以用逗号分隔字符串，正则表达式或一个数组来表示，当使用正则表达式或者数组时，一定使用v-bind! 	

	<!-- 逗号分隔字符串，只有组件a与b被缓存。这样使用场景变得更有意义了 -->
	<keep-alive include="a,b">
  		<component :is="view"></component>
	</keep-alive>

	<!-- 正则表达式 (需要使用 v-bind，符合匹配规则的都会被缓存) -->
	<keep-alive :include="/a|b/">
	  <component :is="view"></component>
	</keep-alive>

	<!-- Array (需要使用 v-bind，被包含的都会被缓存) -->
	<keep-alive :include="['a', 'b']">
	  <component :is="view"></component>
	</keep-alive>
有了include之后，再与router-view一起使用就方便多了
#### 注意 ####
	1、<keep-alive> 先匹配被包含组件的 name 字段，如果 name 不可用，则匹配当前组件 componetns 配置中的注册名称。
	2、<keep-alive> 不会在函数式组件中正常工作，因为它们没有缓存实例。
	3、当匹配条件同时在 include 与 exclude 存在时，以 exclude 优先级最高(当前vue 2.4.2 version)。比如：包含于排除同时匹配到了组件A，那组件A不会被缓存。
	4、包含在 <keep-alive> 中，但符合 exclude ，不会调用activated 和 deactivated。

### 六、全局scss变量的使用 ###
问题：在项目启动初始，选择使用scss预编译工具，但是在我们的vue项目中发现公共变量并不能一次引入处处使用，需在每个需要公共变量的页面import包含公共变量的文件，这样并不能实现预期，万一公共变量的文件路径发生变化，则叫你欲哭无泪。通过搜索发现scss-resources-loader可以帮助我们解决该问题 
 
解决方法如下：  

	npm install -D scss-resources-loader  
首先找到项目中的build文件夹，找到util.js添加一下代码
	
	function resolveResouce(name) {
    	return path.resolve(__dirname, '../src/assets/sass/' + name);
  	}

  	function generateSassResourceLoader() {
	    var loaders = [
	      cssLoader,
	      // 'postcss-loader',
	      'sass-loader',
	      {
	        loader: 'sass-resources-loader',
	        options: {
	          // it need a absolute path
	          resources: [resolveResouce('base.scss')]
	        }
	      }
	    ];
	    if (options.extract) {
	      return ExtractTextPlugin.extract({
	        use: loaders,
	        fallback: 'vue-style-loader'
	      })
	    } else {
	      return ['vue-style-loader'].concat(loaders)
	    }
  	}
然后在当前文件夹找到下面代码：

	return {
	    css: generateLoaders(),
	    postcss: generateLoaders(),
	    less: generateLoaders('less'),
	   	// sass: generateLoaders('sass', { indentedSyntax: true }),
	    // scss: generateLoaders('sass'),
		用下面两句替换上面两句
	    sass: generateSassResourceLoader(),
	    scss: generateSassResourceLoader(),
		
	    stylus: generateLoaders('stylus'),
	    styl: generateLoaders('stylus')
	}

### 七、判断角色进入不同的首页 ###
问题描述：在main.js里面请求角色，然后根据角色类型跳转到不同的页面，使用router.push('...'),在内页调用原生的下拉刷新事件时会跳转到首页
  
解决办法：添加一个过度页null.vue，main.js里面只获取角色，并存储在localstorage中，将判断逻辑写在null中进行转跳，使用router.replace('...')


	
	
