### Array.sort ###
#### 语法 ####
- arr.sort()  
- arr.sort(compareFunction)
#### 参数 ####
- compareFunction-----可选
#### 说明 ####
- 若未指明参数compareFunction，则元素按照转换为字符串的诸个字符的unicode位点进行排序  
- 如果指明了compareFunction，那么数组会按照调用该函数的返回值排序。（a,b是两个将要被比较的元素)  
#### eg: #### 
	a)如果 compareFunction(a,b)<0.则a排在b之前  
	b)如果 compareFunction(a,b)>0 则b排在a之前  
	c)如果 compareFunction(a,b)=0 则a和b的相对位置不变

    function compare(a,b){
		return a-b
	}
    //以上表示比较数字，且数组升序排列
	