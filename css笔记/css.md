### 单行文字溢出省略 ###
	overflow: hidden;
	text-overflow:ellipsis;
	white-space: nowrap;
### 多行省略 ###  
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 3;//行数控制
	overflow: hidden;
### 移动端禁止长按事件 ###
	*{
		-webkit-touch-callout:none;
  		-webkit-user-select:none;
  		-khtml-user-select:none;
  		-moz-user-select:none;
  		-ms-user-select:none;
  		user-select:none;
	}
    