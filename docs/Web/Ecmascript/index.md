#### 简述

> 这里只对 ` ECMASCRIPT ` 规范新出的API做一些整理，开发也许会用到;

### ES5中几个实用的小技巧

#### 数组 API

##### 1 instanceof

`````javascript

//  检测一个对象是否是数组；（用来对付复杂数据类型）
//  简单数据类型 typeof

A instanceof B    //  A 是不是 B 生的

// eg:

var arr = [1, 2, 3];

console.log( arr instanceof Array); // arr属不属于（Array）数组的类型；

`````

##### 2 Array.isArray

`````javascript

//  Array.isArray(参数) // 判断参数是不是数组，返回布尔值；

// eg: 

var arr = [1, 2, 3];
var num = 123;

console.log( Array.isArray(arr)); // true;
console.log( Array.isArray(num)); // false;

`````

##### 3 toString()

`````javascript

//  数组.toString(); 把数组变成了字符串， 去除了[]，内容用逗号连接；

// eg: 

var arr = ["aa", "bb", "cc"];

console.log( arr.toString()); // "aa", "bb", "cc";

`````

##### 4 valueOf()

`````javascript

//  数组.valueOf(); 返回数组本身；

// eg: 

var arr = ["aa", "bb", "cc"];

console.log( arr.valueOf()); // 返回数组本身 ["aa", "bb", "cc"];

`````

##### 5 join(参数)

`````javascript

//  数组.join(参数); 数组中的元素可以按照参数进行连接变成一个字符串

// eg: 
console.log(arr.join()); // 和toString()一样用 , 连接;
console.log(arr.join('|')); // 用 | 连接;
console.log(arr.join(' ')); // 用 空格 连接;
console.log(arr.join('')); // 无缝连接;

`````

#### 数组的添加和删除

##### 1. push() 和 pop()

`````javascript

// 1. 数组.push();  //需要参数； 在数组的末尾添加元素
// 2. 数组.pop();   //不需要参数；在数组的末尾删除一项

//eg: 
var arr = [1,2,3];
var a = arr.push(4);
console.log(arr); // [1,2,3,4];
console.log(a);   // 数组的长度
a = arr.pop;      // [1,2,3];

`````

##### 2. unshift() 和 shift()

`````javascript
// 1. 数组.unshift();  //需要参数； 在数组的首位添加元素
// 2. 数组.shift();   //不需要参数；在数组的首位删除一项

//eg: 
var arr = [1,2,3];
var a = arr.unshift(0);
console.log(arr); // [0,1,2,3];
console.log(a);   // 数组的长度
a = arr.shift;      // [1,2,3]; a是被删除的那一项

`````

#### 数组元素的排序

##### 1. reverse()

`````javascript
reverse() //翻转数组

//eg 
var arr = [1,2,3,4,5];

var a = arr.reverse();  // [5,4,3,2,1]

`````

##### 2. sort()

`````javascript
sort() //数组中元素排序（默认从小到大）
//默认：按照首个字符的Unicode编码排序；如果第一个相同那么就比较第二个...

//eg 
var arr = [2,5,1,3,9,4,8,0,7,6];

var a = arr.sort();

console.log(a);          //  [0,1,2,3,4,5,6,7,8,9]
console.log(a === arr);  //  true 原数组被排序了（冒泡排序）

var arr2 = ['d','e','b','c','a'];

var b = arr2.sort();

console.log(b);          //  ['a','b','c','d','e'];
console.log(b === arr2);  //  true 原数组被排序了（冒泡排序）

`````

`````javascript
sort() //数组中数值排序方法需要借助回调函数
//默认：按照首个字符的Unicode编码排序；如果第一个相同那么就比较第二个...

//eg 
var arr = [2,5,1,3,9,4,8,0,7,6];

var a = arr.sort((a, b)=> a - b);  //升序
var a = arr.sort((a, b)=> b - a);  //降序

`````

##### 3. 冒泡排序

`````javascript

// 没有对循环次数做处理的循环

let arr = [2, 5, 1, 3, 9, 4, 8, 0, 7, 6];

function mode_1(arr, num) {
    const LEN = arr.length;
    for (let i = 0; i < LEN - 1; i++) {
        for (let j = 0; j < LEN - 1; j++) {
            //循环出81次  第0次就是 0 * 0 第1次 1 * 1 第2次 2 * 2 ... 第9次 9 * 9  所以是81次
            if (arr[j] > arr[j + 1]) {
                let temp = arr[j + 1];
                arr[j + 1] = arr[j];
                arr[j] = temp;
            }
            num++;
        }
    }
    console.log(arr, num);
    return arr;
}

mode_1(arr, 0);

`````

`````javascript

// 优化的 for 循环

let arr = [2, 5, 1, 3, 9, 4, 8, 0, 7, 6];

function mode_1(arr, num) {
    const LEN = arr.length;
    for (let i = 0; i < LEN - 1; i++) {
        for (let j = 0; j < LEN - 1 - i; j++) {
            //循环出45 也就是 第0次循环 0+0 第1次 0+1; 第2次  0 + 1 + 2； 第3次 0 + 1 + 2+ 3 ...以此类推
            if (arr[j] > arr[j + 1]) {
                let temp = arr[j + 1];
                arr[j + 1] = arr[j];
                arr[j] = temp;
            }
            num++;
        }
    }
    console.log(arr, num);
    return arr;
}

mode_1(arr, 0);

`````

`````javascript

// 优化的 while 循环

let arr1 = [2, 5, 1, 3, 9, 6, 4, 8, 0, 7];

function mode_2(arr, num) {
    let LEN = arr.length;
    while (LEN > 0) { // 如果 大于 0就执行 执行到 不大于0的时候
        for (let i = 0; i < LEN - 1; i++) {
            if (arr[i] > arr[i + 1]) {
                var temp = arr[i];
                arr[i] = arr[i + 1];
                arr[i + 1] = temp;
            }
            num++;
        }
        LEN--; //不写这个 或者一直加 内存就泄漏了
    }
    console.log(arr, num);
    return arr;
}
mode_2(arr1, 0);

`````

##### 选择排序

>不稳定的排序方法

`````javascript

let arr = [2, 5, 1, 3, 9, 6, 4, 8, 0, 7];

function mode_1(arr, num) {
    const LEN = arr.length;  // 首先存一个长度变量

    for (let i = 0; i < LEN - 1; i++) {
        let minIndex = i; //从第一位开始比较 默认是从第0位开始找
        for (let j = i + 1; j < LEN; j++) { //比较0的下一位
            if (arr[j] < arr[minIndex]) { //找到比第0位小的 就存起来；
                minIndex = j; //就是选择一个最小的数
            }
            num++;
        }
        //一下换位置
        let temp = arr[i]; 
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }

    console.log(arr, "循环了" + num + "次");
    return arr;
}
mode_1(arr, 0);

`````

`````javascript

let arr = [2, 5, 1, 3, 9, 6, 4, 8, 0, 7];

// while

function mode_2(arr, num) {
    let i = 0, LEN = arr.length;
    while (i < LEN) {
        let minIndex = i;
        for (let j = 0; j < LEN - 1 - i; j++) {
            if (arr[minIndex] < arr[i]) {
                minIndex = j;
            }
            num++;
        }
        let temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
        i++;
    }
    console.log(arr, "冒泡循环了" + num + "次");
    return arr;
}
mode_2(arr, 0);

`````

##### 插入排序

>稳定的排序方法

`````javascript

let arr = [2, 5, 1, 3, 9, 6, 4, 8, 0, 7];

//直接插入排序方法
function mode_1(arr, num) {
    const LEN = arr.length;
    for (let i = 0; i < LEN; i++) {
        if (arr[i] < arr[i - 1]) {
            let temp = arr[i];
            let prevIndex = i - 1;
            for (let j = prevIndex; j >= 0 && temp < arr[j]; j--) {
                arr[j + 1] = arr[j];
                prevIndex--;
                num++;
            }
            arr[prevIndex+1] = temp;
        }
    }
    console.log(arr, "直接插入循环了" + num + "次");
    return arr;
}
mode_1(arr, 0);

`````






#### 数组元素的操作

##### 1. concat()

`````javascript

// 数组1.concat(数组2);       //连接两个数组
// eg: 
var arr1 = [1, 2, 3];
var arr2 = ["a", "b", "c"];
var arr3 = arr1.concat(arr2);

console.log(arr3);  // [1, 2, 3, "a", "b", "c"];
`````
##### 2. slice()

`````javascript

// 数组.slice(开始索引， 结束索引);       //数组截取
// eg: 
var arr = [1, 2, 3, "a", "b", "c"];

console.log(arr.slice(3));     // ["a", "b", "c"];
console.log(arr.slice(0, 3));  // [1, 2, 3];
console.log(arr.slice(-2));    // ["b", "c"];
console.log(arr.slice(3, 0));  // [];
console.log(arr);              // 不改变数组
````` 

##### 3. splice()

`````javascript

// 数组.splice(开始索引， 删除几个， 替换内容1， 替换内容2， ...);       //数组替换和删除
// eg: 
var arr = [1, 2, 3, "a", "b", "c"];

console.log(arr.splice(3));     // 从索引值为3到最后;
console.log(arr);               // [1, 2, 3, "a"]
console.log(arr.slice(1, 2));   // 从索引1开始往后删2个
console.log(arr);               // [1, 2, "b", "c"]

````` 

##### 4. indexOf() / lastIndexOf()

`````javascript

// 数组.indexOf(元素)；    //给元素，查索引（从前往后）
// 数组.lastIndexOf(元素)；//给元素，查索引（从后往前）

var arr = [1, 2, 3, "a", "b", "c"];

console.log(arr.indexOf(1));        //0
console.log(arr.lastIndexOf(2));    //4
console.log(arr.lastIndexOf(0));    //-1

````` 

#### 数组的迭代

##### 1. every()

`````javascript

// 对数组中每一项都运行回调函数，如果都返回true, every返回true，如果有一项返回false，则停止遍历返回false；
// eg: 

var arr = [1,2,3,4,5];

arr.every(function(element, index, self){
    console.log(element);    // 元素
    console.log(index);      // 索引值
    console.log(self);       // 数组本身

    self[index] = 6          // 有的时候无法赋值
    return true;
});

// every返回的是boolean值 全部是 true 结果是 true  一个为 false 结果 false

var result = arr.every(function(element, index, self){
    // 判断我们定义的元素都大于5

    if(element > 6){
        return true;
    }else{
        return false;
    }
});

alert(result);   // false

````` 

##### 2. filter()

`````javascript

var arr = [1, 2, 3, 4, 5];

var result = arr.every(function(element, index, self){
    // 判断我们定义的元素都大于5

    if(element % 2 === 0){
        return true;
    }else{
        return false;
    }
});

console.log(result);   // [2, 4]
````` 

##### 3. forEach()

`````javascript

var arr = [1, 2, 3, 4, 5];

var sum = 0;

var result = arr.every(function(element, index, self){
    // 判断我们定义的元素都大于5

    sum += element;
});

console.log(result);   // undefined  不需解释 
console.log(sum);      // 数组中元素求和
````` 

##### 4. map()

`````javascript

//  对数组中每一项运行回调函数，返回该函数的结果组成新的数组  对数组的二次加工

var arr = [1, 2, 3, 4, 5];

var sum = 0;

var result = arr.every(function(element, index, self){
    
    if(index === 2){
        return element; // 3
    }
    return element * 100; // 返回乘以100的值
});

console.log(result);   // [100, 200, 3, 400, 500];
````` 

##### 5. some()

`````javascript

//  对数组中每一项运行回调函数，如果该函数对某一项返回 true 则 some 返回 true;

var arr = [1, 2, 3, 4, 5];

var result = arr.every(function(element, index, self){
    
    if(element % 2 === 0){
        return true;
    }
    return false;
});

console.log(result);   // true 有一个成功就是 true


````` 

#### 清空数组

`````javascript

arr.length = 0;  ('伪数组不能清空');
arr.splice(0);  ('伪数组没有此方法');
arr = [];       ('这个最好！')

`````

#### 数组案例

##### 数组的去重

`````javascript

var arr = [1, 2, 3, 4, 5, 5, 4, 2 ,9];

// 遍历老数组 新数组没有老数组的元素就添加，否则不加
var newArr = [];  

arr.forEach(function(ele, index, array){
    newArr.indexOf(ele) === -1 && newArr.push(ele);
});

console.log(newArr); // [1, 2, 3, 4, 5, 9];

// ES6中最常用
function method_1 (arr) {
    return Array.from(new Set(arr))
} 
console.log(method_1(arr)); // [1, 2, 3, 4, 5, 9];

// splice的用法
function method_2(arr){
    for(let i=0;i<arr.length;i++){ //先遍历数组获得当前位置
        for(let j = i+1; j<arr.length;j++){ //在遍历数组的下一位置
            if(arr[i] === arr[j]){ //如果数组的当前等于下一位
                arr.splice(j--, 1); //就删除这一位然后位置递减
            }
        }
    }
    return arr; //返回值
}
console.log(method_2(arr)); // [1, 2, 3, 4, 5, 9];

// sort
function result_3(arr) {
    arr = arr.sort(); // 首先按大小排列 是两次对比的位置一致
    let newArr = [arr[0]]; // 如果下面从1开始遍历就会把第一位丢失，所以手动添加一个
    let newArr = []; 
    /*
    * 对比从第一位开始 不是从0 如果从0开始 arr[i-1]的第一位会出现undefined 因为arr的长度是9
    * 如果起始是0，就会出现一个的第一位会出现undefined
    * 如果起始是1，就会出现一个的末位会出现undefined
    */ 
    for (let i = 1; i < arr.length; i++) { 
        if(arr[i] !== arr[i-1]){ //如果不对等就添
            newArr.push(arr[i]);
        }
    }
    return newArr;
}
console.log(result_3(arr));

// includes
function result_4(arr) {
    let newArr = [];
    for(let i=0;i<arr.length;i++){
        if(!newArr.includes(arr[i])){
            newArr.push(arr[i]);
        }
    }
    return newArr;
}
console.log(result_4(arr));

//优化数组排序
function method_5(arr){
    var newArr = [];
    for(let i=0;i<arr.length;i++){
        for(let j= i + 1; j < arr.length; j++){
            if(arr[i] === arr[j]){
                i++;
                j=i;
            }
        }
        newArr.push(arr[i]);
    }
    return newArr;
}
console.log(method_5(arr));

// new Set()
    var arr = [1, 2, 3, 4, 5, 5, 4, 2, 9];
    console.log(   [...new Set(arr)] );

`````



### ES6中几个实用的小技巧

#### 1.交换元素

`````javascript

// 利用数组结构来实现交换元素

let a = 'world', b = 'hello';

[a,b] = [b, a];

console.log(a); // hello
console.log(b)  // world 

`````
#### 2.单条语句

>es6操作数组的语句更加紧凑

`````javascript

// 寻找数组中最大的元素

const max = (arr) = > Math.max(...arr); 

max([1, 2, 4, 66, 90, 210])   //210

// 计算数组中总和

const sum = (arr) => arr.reduce((a, b) => (a + b), 0);

sum([1,2,3,4,5,6,7,8,9,10]); // 55

`````

#### 数组拼接

`````javascript

// 展开运算符代替 concat

let arr1 = [1,2,3];
let arr2 = [4,5,6];
let arr3 = [7,8,9];

console.log([...arr1,...arr2,...arr3]);             // [1,2,3,4,5,6,7,8,9];

`````

#### 制作副本

>我们很容易实现数组和对象的浅拷贝

`````javascript
let obj = {...oldobj};
let arr = [...oldarr];
`````

#### 类与继承

`````javascript
// Javascript 中的类继承是基于原型链和构造函数，es6中没有推出 class类的时候，我们的写法：

function Person(name, age){
    this.name = name;
    this.age = age;
}

Person.prototype.Hello = function(){
    alert('Hello' + this.name + this.age);
}

var p = new Person('小明', 23);

// 如果用 es6的语法 class 来书写，我们的写法：

class Person {
    constructor(name, age){
        this.name = name;
        this.age = age;
    }

    Hello(){
        alert('name': this.name + 'age': this.age);
    }
}
`````

>比较一下我们就会发现，class 的定义包含了构造函数 constructor 和定义在原型对象上的函数 Hello()(没有关键字function)，这样就避免了 Person.prototype.Hello = function(){}这样分散的代码；class 继承：用 class 定义对象的一个好处就是继承更加的方便了，想一想我们从 Person 派生一个 Student 需要编写的代码量，现在，原型继承的中间对象，原型对象的构造函数等等都不需要考虑了，直接从 extends 来实现；

`````javascript
class Student extends Person {
    constructor(name, sex){
        super(name);
        this.sex = sex;
    }

    Say(){
        alert('name': this.name + 'age': this.age);
    }
}
`````

### ES7(2016)中几个实用的小技巧

#### 1.Array.prototype.includes()

`````javascript

Array.prototype.includes() //参数可以是 数字 也可以是字符串 返回值是 true 或者 false 基本上代替了很早之前的 indexOf 可传两参 默认索引为0 后面的参是索引 

`````


#### 2.查看对象的属性描述

`````javascript

Array.prototype.Object.getOwnPropertyDescriptor(obj, prop) // 第一个参数是对象源 第二个参数是想要查看的属性 返回结果是对象或者 undefined 对象是 可配 可写 值 可枚举 应用场景基本也就是查看数据具不具备某些属性，根据这种是否具备某些条件而进行某些操作

`````


#### 3.查看对象的属性描述

`````javascript

Array.prototype.Object.getOwnPropertyDescriptor(obj, prop) // 第一个参数是对象源 第二个参数是想要查看的属性 返回结果是对象或者 undefined 对象是 可配 可写 值 可枚举 应用场景基本也就是查看数据具不具备某些属性，根据这种是否具备某些条件而进行某些操作

`````


#### 4.async/await 

- [大木讲堂](https://me.csdn.net/weixin_43921436) 我的伙伴大木老师在这里会做详细的解说


#### 5.从头填充字符串

`````javascript

let str = '123456789'

let nStr =str.padStart(10, 'a')

console.log(nStr); // a123456789  基本用不到 填充字符串的方法 如果不足就在头部补充

`````


#### 6.从尾填充字符串 

`````javascript

let str = '123456789'

let nStr =str.padStart(10, 'a')

console.log(nStr); // 123456789a  基本用不到 填充字符串的方法 如果不足就在尾部补充

`````


#### 7.深度拷贝对象属性

`````javascript

const obj = {
    set name(val){
        console.log(val);
    }
}

const obj1 = {}

Object.assgin(obj1, obj) // 如果obj是已经赋值的 obj={name: '张三'} 再使用 Object.assgin 就看不出什么问题 如果是以上那种待赋值的对象 Object.assgin就不会深度拷贝其属性的描述对象
Object.getOwnPropertyDescriptors(obj)

console.log(Object.getOwnPropertyDescriptors(obj)); // 通过这个方法 就能把属性的描述对象也拷贝过来了

// 一步完整的操作

Object.defineProperties(obj1, Object.getOwnPropertyDescriptors(obj)) // 让obj1获取到obj身上所有的属性 该属性必须是对象自己定义的 不能是从原型链继承来的
Object.getOwnPropertyDescriptors(obj1) // obj1 拥有 obj 所有的技能

`````



### ES8(2017)中几个实用的小技巧

#### 并行

`````javascript

let a = ()=>{
    return new Promise((resolve,reject) => {
        setTimeout(()=>{
            resolve(100);
        },500);
    });
}

let b = ()=>{
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            resolve([1,2,3]);
        },500);
    });
}

let c = async ()=>{
    let d = await Promise.all([a(),b()]);
    return d;
}

console.log(c) // [ 100, [ 1, 2, 3 ] ]  同时输出

`````

#### 2.Promise.finally()

`````javascript

var aa = 0;
b().then(res=>{
    aa = res
}).catch(err=>{
}).finally(()=>{
    console.log(aa); // [1,2,3]
})

`````

#### ... 扩展运算符

> 不仅仅可以运用于数组，对象也可以这样用


### ES9(2018)中几个实用的小技巧

#### 对象保存成多维数组

`````javascript

const obj = {
    a: 1,
    b: 2
};
const entries = Object.entries(obj);

console.log(entries); // [["a", 1], ["b", 2]]

`````

#### 多维数组保存成对象

`````javascript

const obj = [["a", 1], ["b", 2]];

const entries = Object.fromEntries(obj);

console.log(entries); // {a: 1, b: 2};

`````

#### 去掉前空格

`````javascript

const description = "  Unlocks Secret Codes ";
console.log(description.trimStart()); // "Unlocks Secret Codes "

`````

#### 去掉后空格

`````javascript

const category = " JavaScript ";
console.log(category.trimEnd()); // " JavaScript"

`````

#### 数组扁平化

>Array.prototype flat 和 flatMap flat方法创建一个新数组，所有子数组元素以递归方式连接到指定的深度。 默认情况下，深度为 1，使数组上第一层嵌套数组变平。

`````javascript

const arr = [1, 2, [3, 4, [5, 6]]];

var newArr = arr.flat(1);  // 可以是 infinite 一直到彻底扁平

console.log(newArr); // [1, 2, 3, 4, [5, 6]]

`````


### 开发中的理念和规范

#### API 模块化

> 本人做项目的时候 都有一个习惯 就是分块管理 便于任务的分发和交接管理 以下举例说明：
> 1 新建一个文件夹叫 API 顾名思义 就是放置接口API的目录

`````javascript

// 新建 index.js

import common from './common'   // 张三管理
import message from './message' // 李四管理
import other from './other'     // 王五管理

const api = {
  common,
  message,
  other
};

export default api;

`````

`````javascript

// 新建 common.js

const common = [
  {
    methodsName: 'login',
    method: 'POST',
    desc: '用户登录',
    path: '/userLogin/login',
    params: {
      userName: '',
      password: ''
    }
  },
  {
    methodsName: 'getSystemConfig',
    method: 'GET',
    desc: '获取系统配置',
    path: '/config',
    mockPath: '',
    params: {
    }
  },
  {
    methodsName: 'touristLogin',
    method: 'GET',
    desc: '游客登录',
    path: '/csutomerInvitationController/phone/add',
    params: {
      code: '',
      machineCode: '',
      promotionUrl: ''
    }
  },
  ...
]

export default common;


`````









