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

###五、 keep-alive与router-view结合缓存组件 ###
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


	
	
