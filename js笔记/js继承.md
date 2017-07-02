## 继承 ##


- 对于继承，js只有一种结构：对象。每个对象都有一个私有属性（prototype），它指向该对象的原型对象。该原型对象的prototype属性又指向该原型对象的原型对象，依次类推，直到某一个原型对象的prototype的值为null。  
- 当试图访问一个对象的属性时，不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依此层层向上搜索，直到找到一个名字匹配的属性或者到达原型链的末尾  

### 继承的实现 ###
#### 模式一：默认模式，将原型对象指向父对象 ####
eg:  

    function Parent(n){
		this.name= n || 'Adam';
	}
	Parent.prototype.say=function(){
		return this.name;
	}
	function Child(n){}

	Child.prototype=new Parent();
	var c1=new Child('Jack');
	console.log(c1.name);//Adam
	c1.say();       //Adam  

**缺点：**  


- 无法将子构造函数的参数给父构造函数
- 效率低，每创建一个子对象都会重复的创建父对象  

#### 模式二：借用构造函数 ####  
eg:  

    function Parent(n){
		this.name= n||'jack';
	}
	Parent.prototype.say=funciton(){
		return this.name
	}
	function Child(n){
		Parent.apply(this,arguments);
	}

	var c2=new Child('jay');
	console.log(c2.name);  //jay
	c2.say();   //error:say未定义

**缺点：**  

- Child只是借用了Parent的构造函数的实现，获得了一个Parent的副本。但Child和Parent是完全独立的。某种意义上讲，其实并不是继承，无法从Parent的prototype中获取任何东西  

####模式三： 借用和设置原型 ####
该模式是在模式二的基础上将Child的原型指向Parent对象（即模式一与模式二的合并）

    function Child(name){
		Parent.apply(this,arguments);
	}
	Child.prototype=new Parent();  

**缺点：**

- 与模式一的缺点二一样，效率低，每创建一个子对象就会重复创建父对象。
- 另外，子对象和父对象都存在name属性，如果delete子对象中的name属性，父对象的name属性会显现出来，可能出bug。

#### 模式四：共享原型 ####
该模式为了克服模式三中重复创建父对象的缺点，该模式不调用构造函数，即任何需要继承的的成员都放在原型里，而不是放在父构造函数的this中。等价于对象共享一个原型。

eg:  

    function Child(n){
		this.name=n;
	}
	Child.prototype=Parent.prototype;  

**缺点：**  

- 由于父子对象共享原型，因此如果双方中的一方修改到了原型里面的属性和方法都会影响到另一方，可能会造成不必要的麻烦。父子对象间耦合度较高。  

#### 模式五：临时构造函数 ####
该模式为了解决模式四中父子对象间耦合度较高的缺点，该模式断开父子对象间的原型的直接链接关系，但同时还能继续受益于原型链的好处。

eg:


    function Child(n){
		this.name=n;
	}
	var F=function(){}
	F.prototype=Parent.prototype;
	Child.prototype=new F();

**优点：**

- 效率高、实现父子对象解耦（子类原型上添加的新方法父类无法访问）
	

