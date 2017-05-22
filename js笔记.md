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

    div.addEventListener('click', callback, true);
    p.addEventListener('click', callback, true);
    div.addEventListener('click', callback, false);
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
 ### event.target与event.currentTarget的区别
 
 target属性返回触发事件的那个节点，即事件最初发生的节点
 
    ![html结构示意图](https://github.com/shiwanqiong/webNotes/blob/master/div.png)  
    如果最初点击到的区域是P区域，那么，event.target.tagName='P'  
    如果最初点击的区域是div-p的区域，那么event.target.tagName='DIV'  
    故，event.target即为“目标阶段”的节点  
 currentTarget属性返回时间当前所在的节点，即正在执行的监听函数所绑定的节点  
    如果监听函数在捕获阶段和冒泡阶段触发，那么currentTarget!=target  
 
 ### 事件代理  
 由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听事件定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件代理。  
 eg:  
 
    var ul = document.querySelector('ul');

    ul.addEventListener('click', function(event) {
        if (event.target.tagName.toLowerCase() === 'li') {
        // some code
        }
    });
 
 
 
 
 
 
 
 
 
