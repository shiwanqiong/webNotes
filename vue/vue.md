<h3 id="1">一、[全局注册filter]</h3>
    vue.filter(id,definition);
	eg:	 
	Vue.filter('my-filter',function(value){
	
	})

### 二、 scoped的作用于 ###
question：为什么父组件添加scoped后不能影响子组件
   
解决方法：you can	include both scoped and non-scoped styles in the same component  

故可以将父组件的样式的作用域定义为scoped,将要作用于子组件的样式定义为全局

注：A child component's **root** node will be affected by both the parent's scoped css and the child's scoped css(这里指的是子组件的根元素会受父组件scoped作用域的css的影响)

### 三、 $el: ###

### 四、 $event: ###

### 五、 缓存组件 ###
#### a、vue2.10之前的实现方式： keep-alive与router-view结合 ####
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

#### b、vue2.10版本之后keep-alive的include、exclude属性控制组件是否缓存 ####
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

### 八、beforeRouteEnter函数中获取this实例 ###

	beforeRouteEnter(to,from,next){
		next(vm=>{
			//通过'vm'访问组件实例
		})
	}
	//不能获取组件实例‘this’,因为当钩子执行前，组件实例还没有被创建

#### beforeRouteUpdate、 beforeRouteLeave中可以直接访问组件实例this ####

	beforeRouteUpdate (to, from, next) {
	    // 在当前路由改变，但是该组件被复用时调用
	    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
	    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
	    // 可以访问组件实例 `this`
  	},
  	beforeRouteLeave (to, from, next) {
	    // 导航离开该组件的对应路由时调用
	    // 可以访问组件实例 `this`
  	}  

### 九、注册全局变量 ###
	在main.js中import公共变量，然后使用Vue.property.name=variable注册

	import variable from '...'
	Vue.property.name=variable
	在组件中的使用:this.name.attr；
#### 注册全局类使用同样的方法 ####
	util.js
	class Hm{...}
	export default Hm

	main.js：
	import Hm from '.../util.js'
	Vue.prototype.HM=Hm;

	组件中：
	let hm=new this.HM();

### 十、watch监听路由$route,需要深度监听才能触发  ###

	watch:{
        $route:{
          handler:function(to,from){
            if(from.name=='ChooseTeacher'){
              if(sessionStorage.getItem('choosedTeacher')){
                this.teacher=sessionStorage.getItem('choosedTeacher');
              }
            }
          },
          deep:true
        }
      }
### 十一、vue组件中css中的background-image路径问题 ###
	
	{
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
      },
	limit是图片大小限制，当背景图大小大于limit时，webpack会将图片打包到dist/static/img文件夹中，而如果图片大小小于limit，会将其打包成base64形式打包在css文件中

	路径配置：在build下的util.js中的generateSassResourceLoader中添加publiscPath
	
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


        publicPath: '../../',//这里配置图片路径


        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
  }

### 十二、路由按需加载配置之后，路由参数为空的话，路由跳转错误 ###
业务场景：  
app的消息推送要链接到宿舍情况汇总页面，从推送进入到晚归详情页面需要显示截至“推送时间...”情况，如果是从应用入口进去的显示“时间1-时间2”的情况，所以从汇总页到详情页需要添加一个时间参数，且该时间参数可能为空。**将路由配置为懒加载时，首次从汇总页跳转到详情页时路由跳转到了首页，继而从首页进入汇总页再跳转至详情页时又正常**
	
	 <router-link :to="{name:'LateDetail',params:{id:item.dormitory_id,urlTime:urlTime}}">
        <p class="list-title">{{item.dormitory_name}}（{{item.headcount}}人）<span class="arr-right"></span></p>
        <div class="sum-des">
          ...
          <dl>
            <dt><span class="dot grey"></span>正常</dt>
            <dd>{{item.normal}}人</dd>
          </dl>
        </div>
     </router-link>
	
	路由配置如下：
	{
      path: '/lateDetail/:id/:urlTime'
      name: 'LateDetail',
      component: LateDetail,
      meta:{keep_alive:false},
      beforeEnter: (to, from, next) =>
      {
       ...
      }
    },
	当从应用进入详情页时urlTime值为空，会出现上述情形，故将默认参数值改为0就ok了

### 十三、宿舍管理应用中，出入详情页面tab切换，原生下拉刷新与better-scroll组件下拉刷新冲突的处理 ###

业务场景：  
1、两个tab公用一个视图，由于牵涉分页处理，故需要记录两个视图分别对应的page，item,lastLength(当上一次请求到的数据少于一页说明是最后一页，就不在执行上滑加载更多)。  
2、业务有下拉刷新页面的需求，另外bette-scroll插件在内容小于容器高度时不会触发滚动事件，所以此时要启动原生下拉刷新，而对于内容高于容器高度时，可以触发插件的下拉事件，该下拉与原生下拉有冲突（两个下拉同时启动时，插件的不起作用，且内容不能滚动下来）所以此时应禁用原生的下拉事件。

处理方法：在vue的updated钩子函数中获取列表高度，以及容器高度（屏幕高度-容器以外内容高度）当列表高度大于容器高度，禁用原生下拉刷新事件，当列表高度小于容器高度，启用原生下拉刷新事件
	
	let topHeight=this.$refs.top.clientHeight;//top部分高度
    let listHeight=this.$refs.itemUl.clientHeight;//列表高度
	let bodyHeight=document.documentElement.clientHeight;//body的高度
    if(listHeight>(bodyHeight-topHeight)){
	      hm.request({
	      command:'hm_window://init',
	      params:'{"refresh_enabled":false}'
    	});
    }

### 十四、 updated钩子函数  ###
	调用时机：由于数据更改导致的虚拟DOM重新渲染和打补丁，在这之后会调用该钩子
	
	由于该钩子调用时，组件DOM已经更新，所以可以执行依赖于DOM的操作。然而大多数情况下，应该避免在此期间更改状态。如果要相应状态改变，通常使用**计算属性 **或**watcher**取而代之

	注意：uodated不会承诺所有的子组件也都一起被重绘。如果希望等整个视图都重绘完毕。可以用vm.$nextTick()替换掉updated:

	uodated:function(){
		this.$nextTick(function(){
			// Code that will run only after the
    		// entire view has been re-rendered
		})
	}
### 十五、 Vue.nextTick() 全局api（vm.$nextTick()局部api，组件内调用）###
	1、在Vue的声明周期的created()钩子函数进行DOM操作一定要放在Vue.nextTick()的回调函数中，因为在created()钩子函数执行的时候DOM其实并未进行任何渲染，而此时进行DOM操作无异于徒劳，所以一定要讲DOM操作放在该函数的回调中，与之对应的mounted钩子函数，该钩子函数执行时所有的DOM挂载和渲染已经完成，此时在钩子函数中进行任何DOM操作都不会有问题
	
	2、在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的DOM结构时，这个操作都应该放在该函数的回调函数中

### 十六、checkbox全选的处理 ###
	<div class="check-all">
      <input type="checkbox" v-model="checkAll" @change="_checkAll()">
      <label for="">全选</label>
    </div>
	绑定了vue-model的checkbox的事件处理是用change事件，用click处理的话ios会有兼容性问题
### 十七、<input v-model.number="age" type="number"> ###
在type='number'时，html中输入的值也总是返回字符串类型

### 十八 开发阶段跨域请求接口解决方法 ###
在webpack的配置中配置开发的代理（config/index）  

	dev: {
    env: require('./dev.env'),
    port: process.env.PORT || 8080,
    autoOpenBrowser: true,
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',

    proxyTable: {
      '/api': {
        target: 'http://192.168.0.159:19100',//设置你调用的接口域名和端口号 别忘了加http
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''//这里理解成用‘/api’代替target里面的地址，后面组件中我们掉接口时直接用api代替 比如我要调用'http://40.00.100.100:3002/user/add'，直接写‘/api/user/add’即可
        }
      }
    },
	//在上面配置代理，仅在开发阶段有效

    cssSourceMap: false
  }
### 十九、判断json对象与json字符串的方法 ###  
	//判断是否为json对象
	
	if (typeof(obj.params) == "object" && Object.prototype.toString.call(obj.params).toLowerCase() == "[object object]" && !obj.params.length) {
            obj.params = JSON.stringify(obj.params);
        }  
	
	//判断是否为json字符串  
	
	if (Object.prototype.toString.call(resp) === "[object String]"){
		resp = JSON.parse(resp);
	}

### 二十、混合开发过程中与原生交互时数据传输问题 ###
通常前端通过原生与服务端交互时，传输使用json字符串，但是对于文本输入过程中的英文''或者其他符号会导致数据解析失败。原因是，服务端会对特殊符号进行转译，原生在接收到服务端的数据时会先解析一遍然后在封装成json字符串，最后传输给前端，原生在解析过程中会将转译符“丢掉”。所以前端解析接收到的数据时就会报错。

**解决办法**  
更改传输协议，原生在传输回来时直接传输json对象，前端接收到时不需要再解析，对于文本的渲染使用v-html  

### 二十一、根据名字的hash值计算随机的颜色（钉钉用户头像颜色生成原理） ###
	//获取hash值

	function hashCode (str) {
	    var hash = 0;
	    if (str.length == 0) return hash;
	    for (let i = 0; i < str.length; i++) {
	        let char = str.charCodeAt(i);
	        hash = ((hash << 5) - hash) + char;
	        hash = hash & hash; // Convert to 32bit integer
	    }
	    return hash;
	}
	
	//根据余数来设定hash对应的颜色值
	export function getColor(name) {
	    if(name!=''&&name!=undefined){
	        var hash = Math.abs(hashCode(name));
	        var colors = ['#F7980A', '#ED8299','#9997F2', '#6FCF86','#6DC6C7'];
	        var colorIndex = hash % (colors.length);
	        var color = colors[colorIndex];
	        return color;
	    }
	    else{
	      return
	    }
	}