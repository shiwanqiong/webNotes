一、EVENT的三个阶段
================
第一阶段 “捕获阶段（capture phase）”
------------------------------------
第二阶段 “目标阶段（target phase）”
------------------------------------
第三阶段 “冒泡阶段（bubbling phase）”
------------------------------------
用户点击网页的时候，浏览器总是假定click事件的目标节点是点击位置的嵌套最深的那个节点  
eg:  
html:  

    <div>
      <p>Click Me</p>
    </div>  
js:  

    var phases = {
      1: 'capture',
      2: 'target',
      3: 'bubble'
    };

    var div = document.querySelector('div');
    var p = document.querySelector('p');
	div.addEventListener('click', callback, true);//定义了捕获阶段的处理程序时才会触发，否则不会有执行  
	p.addEventListener('click', callback, true); 
	div.addEventListener('click', callback, false);//定义了冒泡阶段的处理程序时才会触发，否则不会执行  
	p.addEventListener('click', callback, false); 
    function callback(event) {
      var tag = event.currentTarget.tagName;
      var phase = phases[event.eventPhase];
      console.log("Tag: '" + tag + "'. EventPhase: '" + phase + "'");
    }  
 
 	
	点击以后的结果：  
	//Tag: 'DIV'. EventPhase: 'capture'  
	//Tag: 'P'. EventPhase: 'target' 
	//Tag: 'P'. EventPhase: 'target' 
	//Tag: 'DIV'. EventPhase: 'bubble'  
 
###在捕获阶段或者冒泡阶段执行顺序按照DOM事件流的顺序，而在目标阶段时，事件执行顺序按照事件绑定顺序###

###event.target与event.currentTarget的区别### 
 
target属性返回触发事件的那个节点，即事件最初发生的节点   
 
![html结构示意图](https://github.com/shiwanqiong/webNotes/blob/master/div.png)  
	
	如果最初点击到的区域是P区域，那么，event.target.tagName='P'  
    如果最初点击的区域是div-p的区域，那么event.target.tagName='DIV'  
    故，event.target即为“目标阶段”的节点  
    
currentTarget属性返回事件当前所在的节点，即正在执行的监听函数所绑定的节点  
如果监听函数在捕获阶段和冒泡阶段触发，那么currentTarget!=target        
 
###事件代理###  

由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听事件定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件代理。  
 eg:  
 
    var ul = document.querySelector('ul');

    ul.addEventListener('click', function(event) {
        if (event.target.tagName.toLowerCase() === 'li') {
        // some code
        }  
	});

###事件绑定的三种方法 ###  

a>  
    
    <div onclick="callback(e)"></div>  //DOM0
b>  

    div.onclick=funcion(){...}  //DOM0
c>  

    div.addEventListener('click',callBack,false);  //DOM2
    IE:div.attachEvent('onclick',callback);

### addEventListener与attachEvent的区别 ###
- 支持的浏览器不同。addEventListener是W3C DOM规范中提供的注册事件监听器的方法，而attachEvent是针对IE9之前的浏览器，必须使用attachEvent注册监听事件
- 参数个数不同，addEventListener接收三个参数，事件名，处理函数，（‘click’，func,useCapture）useCapture用来指定触发阶段，可选捕获或者冒泡；而attachEvent接收两个参数（‘onClick’，func）注意到第一个参数不同（‘on-’），且不能指定触发阶段
- this指向不同，使用addEventListener为一个元素注册事件的时候，句柄中的this值为该元素的引用，而使用attachevent注册事件时，this指向全局window对象
- addEventListener允许给同一个事件注册多个listener
    
## 二、AJAX ## 
### 1、XMLhttpRequest对象(XMLHttpRequest对象用来在浏览器与服务器之间传送数据)###  

#### XMLhttpRequest对象的典型用法XMLhttpRequest对象的典型用法 ####

       var xhr=new XMLHttpRequest();//声明XMLhttpRequest对象  
        xhr.onreadystatechange=function(){  //定义回调函数
            if(xhr.readystate===4){
                if(xhr.status===200){
                    console.log(xhr.responseText);
                }
                else{
                    console.error(xhr.statusText);
                }
            }
          }
		xhr.onerror=function(){...} //定义error事件函数  
		xhr.open('GRT','/endpoint',true);  //指定发送HTTP请求的参数(method,url,async,user,password)  
		xhr.send(null) //发送HTTP请求   
## 三、Debounce 和 Throttle 的原理及实现 ## 
### 链接如下： ###  
[Debounce 和 throttle 的原理及实现](http://hackll.com/2015/11/19/debounce-and-throttle/)
