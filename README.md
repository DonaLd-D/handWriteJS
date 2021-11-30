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
console.log(typeOf(new Promise((resolve,reject)=>{})))  //promise
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
    if(!isObj(data)) return data
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

## Promise
- Promise.all()
- Promise.race()
- Promise.allSettled()
- Promise.any()
- Promise.resolve()
- Promise.reject()
- Promise.try()
```js
class Promise{
    constructor(executor){
        this.state='pending'
        this.value=undefined
        this.reason=undefined
        this.onResolvedCallbacks=[]
        this.onRejectedCallbacks=[]

        let resolve=(value)=>{
            if(this.state=='pending'){
                this.state='fulfilled'
                this.value=value
                this.onResolvedCallbacks.forEach(fn=>fn())
            }
        }
        let reject=(reason)=>{
            if(this.state=='pending'){
                this.state='rejected'
                this.reason=reason
                this.onRejectedCallbacks.forEach(fn=>fn())
            }
        }

        try{
            executor(resolve,reject)
        }catch(err){
            reject(err)
        }
    }

    then(onFulfilled,onRejected){
        onFulfilled=typeof onFulfilled=='function'?onFulfilled:value=>value
        onRejected=typeof onRejected=='function'?onRejected:err=>{throw err}

        let promise2=new Promise((resolve,reject)=>{
            if(this.state=='fulfilled'){
                setTimeout(()=>{
                    try{
                        let x=onFulfilled(this.value)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(err){
                        reject(err)
                    }
                },0)
            }
            if(this.state=='rejected'){
                setTimeout(()=>{
                    try{
                        let x=onRejected(this.reason)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(err){
                        reject(err)
                    }
                },0)
            }
            if(this.state=='pending'){
                this.onResolvedCallbacks.push(()=>{
                    setTimeout(()=>{
                        try{
                            let x=onFulfilled(this.value)
                            resolvePromise(promise2,x,resolve,reject)
                        }catch(err){
                            reject(err)
                        }
                    },0)
                })
                this.onRejectedCallbacks.push(()=>{
                    setTimeout(()=>{
                        try{
                            let x=onRejected(this.reason)
                            resolvePromise(promise2,x,resolve,reject)
                        }catch(err){
                            reject(err)
                        }
                    },0)
                })
            }
        })
        return promise2
    }

    catch(fn){
        this.then(null,fn)
    }
}

function resolvePromise(promise2,x,resolve,reject){
    if(x==promise2){
        return reject(new TypeError('Chaining cycle detected for promise'))
    }

    let called
    if (x != null && (typeof x === 'object' || typeof x === 'function')) {
        try {
            let then = x.then
            if (typeof then === 'function') { 
                then.call(x, y => {
                    if (called) return
                    called = true
                    resolvePromise(promise2, y, resolve, reject)
                }, err => {
                    if (called) return
                    called = true
                    reject(err)
                })
            } else {
                resolve(x)
            }
        } catch (e) {
            if (called) return
            called = true
            reject(e)
        }
    } else {
        resolve(x)
    }
}
```

## Promise.solve()
```js
Promise.solve=function(value){
    return new Promise((resolve,reject)=>{
        resolve(value)
    })
}
```

## Promise.reject()
```js
Promise.reject=function(reason){
    return new Promise((resolve,reject)=>{
        reject(reason)
    })
}
```

## Promise.race()
```js
Promise.race = function(promises){
  return new Promise((resolve,reject)=>{
    for(let i=0;i<promises.length;i++){
      promises[i].then(resolve,reject)
    }
  })
}
```

## Promise.all()
```js
Promise.all = function(promises){
  let arr = []
  let i = 0
  function processData(index,data){
    arr[index] = data
    i++
    if(i == promises.length){
      resolve(arr)
    }
  }
  return new Promise((resolve,reject)=>{
    for(let i=0;i<promises.length;i++){
      promises[i].then(data=>{
        processData(i,data)
      },reject)
    }
  })
}
```


`未完待续`
