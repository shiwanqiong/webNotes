### 全局注册filter ###
    vue.filter(id,definition);
	eg:	 
	Vue.filter('my-filter',function(value){
	
	})

### scoped的作用于 ###
question：为什么父组件添加scoped后不能影响子组件
   
解决方法：you can	include both scoped and non-scoped styles in the same component  

故可以将父组件的样式的作用域定义为scoped,将要作用于子组件的样式定义为全局

注：A child component's **root** node will be affected by both the parent's scoped css and the child's scoped css(这里指的是子组件的根元素会受父组件scoped作用域的css的影响)

### $el: ###

### $event: ###