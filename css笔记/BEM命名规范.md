### BEM命名方法

综‘-’、'_'、‘--’三种连接符为一体，举个例子：

	.static-man_head--big{}
	
我们来分析下BEM，B 即block，是个限定词；E 即element，是元素，也就是元素主体，M 即modifiers，是修饰符。结合上面的例子，static就是个限定词，限定man，就是“火柴人”,中间是用‘-’连接，head是man的一部分，也就是子元素，与父元素用‘_’连接，最后的big是修饰符，用来修饰当前主体，与主体用‘--’连接。更多例子如下：

	.static-man{}  //block
	.static-man--blue{}, //block--modifier
	.static-man--red{},   //block--modifier
	.static-man_head{},	  //block_element		
	.static-man_foot{},  //block_element
	.static-man_head--small{},//block_element--modifier
	.static-man_head--big{}, //block_element--modifier
	
	.person{} //block
	.person_hand{} //block-element
	.person--female{} //block--modifier
	.person--female_hand{} //block--modifier_element
	.person_hand--left{} //block_element--modifier