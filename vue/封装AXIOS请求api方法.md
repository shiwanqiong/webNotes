### axios请求方法封装  
	import axios from 'axios'
	let root= process.env.API_ROOT
	
	function apiAxios(method,url,params,success,failure){
		axios({
			method:method,
			url:url,
			data:method=='POST'||method=='PUT'?params:null,
			params:method=='GET'||method=='DELETE'?params:null,
			baseURL:root,
			widthCredentials:false
		})
		.then(function(res)
		{
			if(res.data.code==0){
				if(success){
					success(res.data);
				}
			}
			else{
				if(failure){
					failure(res.data);
				}
				else{
					alert('err:'+JSON.stringify(res.data));
				}
			}
		})
		.catch(function(err){
			let res=err.response;
			if(err){
				alert(err.status);
			}
		})
	}
	
	export default{
		get:function(url,params,success,failure){
			return apiAxios('GET',url,params,success,failure);
		},
		post:function(url,params,success,failure){
			return apiAxios('POST',url,params,success,failure);
		},
		put:function(url,params,success,failure){
			return apiAxios('PUT',url,params,success,failure);
		},
		delete:function(url,params,success,failure){
			return apiAxios('DELETE',url,params,success,failure);
		}
	}
	
	
	//在main.js里注册vue的属性$api
	
	import api from './api/index.js'//即上面的方法所在文件路径
	Vue.prototype.$api=api
	
	//调用：
	this.$api.get('topics',null,res=>{
		console.log(res);
	})
	
	
	
	