### Debounce 防抖

```
//Debounce 防抖

function debounce(fn,wait,immediate){
    let timer
    if(immediate){
      fn.apply(this,arguments)
    }
    if(timer){
       clearTimeout(timer)
    }
    timer = setTimeout(()=>{
       fn.apply(this,arguments)
    },wait)
}
  ```

### Throttle 节流

```
//时间戳版本

	function throttle(fn,wait){
		let prev = new Date()
		return function(){
			let args = arguments
			let now = new Date()
			if(now - prev > wait){
				fn.apply(this,args)
				prev = new Date()
			}
		}
	}
  
  
	//定时器版本
  
	function throttle(fn,wait){
		let timer = null
		return function(){
			let args = arguments
			if(!timer){
				timer = setTimeout(()=>{
					fn.apply(this,args)
					timer = null
				},wait)
			}
		}
	}
  
  
	//时间戳&定时器版本
  
	function throttle(fn,wait,isDebounce){
		let timer
		let lastCall = 0
		return function(...args){
			if(isDebounce){
				if(timer){
					clearTimeout(timer)
					timer = setTimeout(()=>{
						fn.apply(...args)
					},wait)
				}
			}else{
				const now = new Date().getTime()
				if(now - lastCall < wait){
					return
				}
				lastCall = now
				fn(...args)
			}
		}
	}
  ```
  ### deepClone
  
  ```
  //一般对象和数组的深度克隆
  
	function deepClone(obj){
		var newObj = obj instanceof Array ? [] : {};
		for(var i in obj){
			newObj[i] = typeof obj[i] == "object" ? deepClone(obj[i]) : obj[i]; 
		}
		return newObj;
	}
	var arr = [1,2,3];
	console.log(deepClone(arr) == arr);


	//对于原始值或者包装类
  
	function baseClone(base){
		return base.valueOf();
	}
	
	//Number
	var num = new Number(1);
	var newNum = baseClone(num);
	console.log(newNum);
	console.log(newNum === num);
	
	//String
	var str = new String('蜡笔小新')
	var newStr = baseClone(str);
	console.log(newStr);
	console.log(newStr === str);
	
	//Boolean
	var bol = new Boolean(true);
	var newBol = baseClone(bol);
	console.log(newBol);
	console.log(newBol === bol);

	//Date类型
  
	Date.prototype.clone = function(){
		return new Date(this.valueOf());
	}
	var date = new Date('2019');
	var newDate = date.clone();
	console.log(newDate);
	console.log(newDate === date);


	//RegExp 类型
  
	RegExp.prototype.clone = function(){
		var pattern = this.valueOf();
		var flags = '';
		flags += pattern.global ? 'g' : '';
		flags += pattern.ignoreCase ? 'i' : '';
		flags += pattern.multiline ? 'm' : '';
		return new RegExp(pattern.source, flags);
	};
	var reg = new RegExp('/111/');
	var newReg = reg.clone();
	console.log(newReg);
	console.log(newReg === reg);

  ```
  ### instanceOf实现
  
  ```
  function instance_of(L,R){
		var O = R.prototype
		L = L.__proto__
		while(true){
			if (L === null) {
				return false
			}
			if (O === L) {
				return true
				L = L.__proto__
			}
		}
	}
  ```
  
  ### orginBind
  
  ```
  Function.prototype.orginBind = function(context){
		let self = this;
		let args1 = Array.prototype.slice.call(arguments,1);
		return function(){
			let args2 = [].prototype.slice.call(arguments);
			let args = args1.concat(args2);
			return self.apply(context,args);
		}
	}
  ```
  
  
  ### promise封装Ajax
  
  ```
  function ajaxMise(url, method, data, async, timeout) {
    var xhr = new XMLHttpRequest()
    return new Promise(function (resolve, reject) {
        xhr.open(method, url, async);
        xhr.timeout = options.timeout;
        xhr.onloadend = function () {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304)
                resolve(xhr);
            else
                reject({
                    errorType: 'status_error',
                    xhr: xhr
                })
        }
        xhr.send(data);
        //错误处理
        xhr.onabort = function () {
            reject(new Error({
                errorType: 'abort_error',
                xhr: xhr
            }));
        }
        xhr.ontimeout = function () {
            reject({
                errorType: 'timeout_error',
                xhr: xhr
            });
        }
        xhr.onerror = function () {
            reject({
                errorType: 'onerror',
                xhr: xhr
            })
        }
    })
}
  ```
  ### 数组扁平化的三种方法
```
function flatten(arr){
    return [].concat(
      ...arr.map(x => Array.isArray(x)? flatten(x):x)
    )
  }
  
  function flatten(arr){
    return arr.reduce(function(prev,item){
      return prev.concat(Array.isArray(item)?flatten(item):item)
    },[])
  }
  
  function flatten(arr){
    let stack = [...arr]
    let res = []
    while(stack.length){
      const next = stack.pop()
      if(Array.isArray(next)){
        stack.push(...next)
      }else{
        res.push(next)
      }
    }
    return res.reverse()
  }
```
