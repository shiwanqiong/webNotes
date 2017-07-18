## onkeypress ##
	
	<input type="text" onkeypress="return isNumberKey(event)">
     function isNumberKey(evt){
            var charCode = (evt.which) ? evt.which : event.keyCode;
            var char=String.fromCharCode(charCode);
            var reg=/^[\d|\-]/g
            return reg.test(char);
        }