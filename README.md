## 判断数据类型
```js
let typeOf=(data)=>{
    let res=Object.prototype.toString.call(data).split(' ')[1]
    return res.substring(0,res.length-1).toLowerCase()
}

console.log(typeOf({})) //object
console.log(typeOf([])) //array
console.log(typeOf(()=>{})) //function
console.log(typeOf(null)) //null
console.log(typeOf(undefined))  //undefined
console.log(typeOf(1))  //number
console.log(typeOf(true))   //boolean
console.log(typeOf(''))   //string
console.log(typeOf(new Date()))   //date
```

##  数组去重
```js
let unique=(arr)=>{
    let newArr=[]
    arr.forEach(item=>{
        if(newArr.indexOf(item)==-1) newArr.push(item)
    })
    return newArr
}

let res=unique([1,2,34,1,4,8,67,null,null,'q','q',34,undefined,undefined,true,true])
console.log(res)
```

## 数组扁平化
```js
let flatArr=(arr,newArr=[])=>{
    arr.forEach(item=>{
        Array.isArray(item)?flatArr(item,newArr):newArr.push(item)
    })
    return newArr
}

let res=flatArr([1,[23,['qwerty',[null,[undefined,[true]]]]]])
console.log(res)
```

## 深拷贝（简单实现）
```js
let isObj=(data)=>typeof data=='object'

let deepClone=(data)=>{
    if(!isObj(data)) return
    let newData=Array.isArray(data)?[]:{}
    for(let key in data){
        if(data.hasOwnProperty(key)){
            if(isObj(data[key])){
                newData[key]=deepClone(data[key])
            }else{
                newData[key]=data[key]
            }
        }
    }
    return newData
}
```

## 事件总线
```js
class EventEmitter{
    constructor(){
        this.cache={}
    }
    on(name,fn){
        if(this.cache.hasOwnProperty(name)){
            this.cache.push(fn)
        }else{
            this.cache[name]=[fn]
        }
    }
    off(name,fn){
        if(this.cache[name]&&this.cache[name].indexOf(fn)>=0){
            this.cache[name]=this.cache[name].filter(f=>f!=fn)
        }
    }
    emit(name,fn){
        if(!this.cache[name]) return `Zero function to excute!`
        let tasks=this.cache[name].slice()
        for(let f of tasks){
            if(f==fn) f()
        }
    }
}
```

## 解析url参数为对象
```js
let parseParam=(url)=>{
    let index=url.indexOf('?')
    let paramStr=url.substr(index+1)
    let paramArr=paramStr.split('&')
    let paramObj={}

    paramArr.forEach((param,index)=>{
        if(param.indexOf('=')>=0){
            let [key,value]=paramArr[index].split('=')
            value=decodeURIComponent(value)
            value=isNaN(Number(value))?value:parseFloat(value)
            if(paramObj[key]){
                Array.isArray(paramObj[key])?paramObj[key].push(value):paramObj[key]=[paramObj[key],value]
            }else{
                paramObj[key]=value
            }
        }else{
            paramObj[param]=null
        }
    })
    return paramObj
}

let res=parseParam('www.baidu.com?name=baidu&password=123&link&to=nothing&name=netease')
console.log(res)
```

## 防抖函数 节流函数
```js
//  防抖函数
let debounce=(func,wait)=>{
    let timeID
    return function(){
        if(timeID) clearTimeout(timeID)
        timeID=setTimeout(func,wait)
    }
}

//  节流函数
let throttle=(func,wait)=>{
    let pre=0
    return function(){
        let now=+new Date()
        if(now-pre>=wait) func()
        pre=+new Date()
    }
}
```

`未完待续`
