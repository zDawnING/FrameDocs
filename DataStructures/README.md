# 数组（array）

数组的标准定义：一个存储元素的线性集合，元素可以通过索引来任意存取，索引通常是数字，用来计算元素之间存储位置偏移量。

javascript中的数组是一种特殊的对象，用来表示偏移量的索引是该对象的属性，索引可能是整数。

创建数组：
```
var arr = []

var arr = new Array()

Array.isArray() // 可用于判断一个对象是否为数组

```

数组使用：
```JavaScript

// 由字符串生成数组
var str = 'aaa bbb ccc'
var arr = str.split(' ')

//对数组整体操作

//引用复制(浅复制)
var nums = [1,2]
var arr = nums

// 更好的方案是使用深复制，即将原数组的每一份元素都复制的新数组当中
// 还有一种是JSON.parse(JSON.stringify(nums)),暂未考虑过性能开销

//查找元素
//indexOf 返回第一个与参数相同的元素的索引（从左往右）
var nums = [1,2]
nums.indexOf(3) //找到则返回索引值，没有找到则返回-1
//lastIndexOf 返回相同元素中最后一个元素的索引（从右往左）

//数组的字符串表示
//join()和toString() 都可以数组转换为字符串，元素间用逗号分隔
var arr = [1,2]
var str = arr.join()
var str = arr.toString()
//当直接对数组使用print() ,系统会自动调用数组的toString()

//已有数组创建新数组
//concat() 合并多个数组创建一个新数组（谁合并谁）
var arr1 = [1,2]
var arr2 = [3,4]
var newArr = arr1.concat(arr2)
//splice() 截取一个数组的子集创建一个新数组（后续细则）
var arrF = [1,2,3,4,4,4]
var arrC = arrF.splice(3,2)

//可变函数
//push() 方法会将一个元素添加到数组末尾,这里会返回一个数组的新长度
//unshift() 方法可以将元素添加至数组的开头。可添加多个参数,这里会返回一个数组的新长度
//如果没有unshift()处理元素将会非常低效
var nums = [2,3,4]
var newNum = 1
var N = nums.length
for(var i=N; i>=0; --i){
	nums[i] = nums[i-1]
}
nums[0] = newNum
console.log(nums)

//pop() 删除数组末尾的元素，返回一个删除掉的元素
//shift() 删除数组第一个元素,返回一个删除掉的元素
//如果没有shift()处理元素将会非常低效
var nums = [9,2,3,4]
for(var i = 0; i<nums.length; i++){
	nums[i] = nums[i+1]
}
nums.pop() // nums.splice(nums.length-1,1)

//splice 可以为数组添加元素和删除元素
* 起始位置
* 需要删除的元素个数（添加时为0）
* 想要添加进数组的元素

//数组排序
//reverse() 将数组中元素顺序进行翻转
//sort() 该方法是按照字典顺序进行排序的，因此它假定元素都是字符串类型，即使元素是数字类型，也会被认为字符串类型。
为了让sort()方法也可以对数字进行排序，可以在调用时传入一个比较大小的函数，排序时，sort()方法将会根据该函数比较数组
中两个元素的大小，从而决定整个数组的顺序[另外这个方法的原理建议参考一下API]
数字之间的比较，可以采取相减的办法来获取比较结果，小则为负，0为相等，大为正
function compare(num1,num2){
	return num1 - num2
}
var nums = [2,4,7,4,8]
nums.sort(compare())

//迭代器方法
//forEach() 该方法接受一个函数作为参数，对数组中的每个元素使用该函数
function square(num){
	console.log(num, num * num)
}
var nums = [1,2,3,4]
nums.forEach(square)

//every() 接受一个返回值为布尔类型的函数，对数组中的每个元素使用该函数。如果对于所有的元素，该函数均返回true,则该方法返回true
function isEven(num){
	return num % 2 == 0
}
var nums = [2,4,6,8,10]
var even = nums.every(isEven)
even ? alert('even') : alert('not even')

//some() 接受一个返回值为布尔类型的函数，只要有一个元素使得该函数返回true，该函数均返回true,则该方法返回true

//reduce() 接受一个函数，返回一个值，该方法会从一个累加值开始，不断对累加值和数组中的后续元素调用该函数，直到数组中的最后一个元素，
最后返回得到累加值(从左往右)
function add(runningTotal, currentValue){
	return runningTotal + currentValue
}
var nums = [1,2,3,4,5]
var sum = nums.reduce(add)

//reduceRight() 与reduce()类似，但是顺序相反（从右往左）

```