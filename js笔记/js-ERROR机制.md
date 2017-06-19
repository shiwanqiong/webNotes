### js的错误处理机制 ###
#### ERROR对象 ####
一、属性：  
- message：错误提示信息  
- name：错误名称（非标准属性）  
- stack：错误的堆栈（非标准属性）  
二、js原生错误类型  
 
- **SyntaxError**  
 解析代码时发生的语法错误（eg：变量名规则错误，缺少括号，分号或者其他符号）
    
- **ReferenceError**  
 引用一个不存在的变量时发生错误  
 将一个值分配给无法分配的对象，比如对函数的运行结果或者this赋值  
eg:  
    console.log()=1    referenceError  
    this=1        referenceError  

- **RangeError**  
 当一个值超出有效范围时发生的错误，主要有以下情况：  
   a>数组的长度为负  
   b>Number对象的方法参数超出范围  
   c>函数堆栈超过最大值  

- **TypeError**
  变量活参数不是语气类型时发生的错误

- **URIError**
  URI相关函数的参数不正确时抛出的错误，eg:  
    encodeURI()、decodeURI()、encodeURIComponent()、
	decodeURIComponent()、escape()和unescape()  
- **EvalError**  
 eval函数没有被正确执行时会抛出的错误
	
 
#### tyr->catch->finally ####
以下例子充分反映了try...catch...finally之间的执行顺序  
    
    function f(){
    	try{
			console.log(0);
			throw 'bug';
    	}catch(e){
			console.log(1);
			return true;
			console.log(2);//不会执行
		}finally{
			console.log(3);
			return false;
			console.log(4);//不会执行
		}
		console.log(5);//不会执行
	}
	var result=f();
	//0
	//1
	//3
	result
	//false  
try代码块一抛出错误，js引擎就立即把代码的执行转到catch代码块。catch代码块捕获错误之后，程序不会中断，会按照正常程序继续执行下去  
上面代码中，catch代码结束执行之前，会先执行finally代码块。从catch转入finally的标志，**不仅有return语句，还有throw语句**。

    var count=0;
	function countUp(){
		try{
			return count;		
		}finally{
			count++;
		}
	}
	countUp();   //0
	count        //1
	
	**注：**以上代码说明，return语句的count的值，是在finally代码块运行之前就获取完成了。只是等finally代码执行完毕后才返回。  

	function cleanUp(){
		try{
			throw new Error('出错了...');
			console.log('此行不会执行');
		}finally{
			console,log('完成清理工作');
		}
	}
	cleanUp() 
	//完成清理工作
	//Error:出错了
	
**注：** 由于没有catch代码块，所以错误没有捕获。(throw语句标志转入到finally)执行finally代码块以后。程序就中断在错误抛出的地方