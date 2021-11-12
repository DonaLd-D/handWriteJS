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
