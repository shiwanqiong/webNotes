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
 
 
