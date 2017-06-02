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

//生成新数组的迭代器

//map() 与forEach类似，对数组中的每个元素使用某个函数，两者的区别是map() 返回一个新的数组，该数组的元素是对原有元素应用某个函数得到的结果
function curve(grade){
	return grade += 5
}
var grades = [77, 54, 23]
var newgrades = grade.map(curve)

function first(word){
	return word[0]
}

var words = ['for','you','information']
var acronym = words.map(first)
acronym.join('') //传入空参处理数组中的逗号

//filter() 与every类似，传入一个返回值为布尔类型的参数，返回一个新数组包含应用该函数后结果为true的元素
function isEven(num){
	return num % 2 === 0
}

var nums = [1,2,3,4,5]
var even = nums.filter(isEven)

function after(str){
	if(str.indexOf('cle') > -1){
		return truen
	}
	return false
}

var words = ['sdfcew','clessdfs','regrecle']
var misspelled = words.filter(after)


// 创建二维数组

// 如果直接在数组内部新增新的数组，那么数组中的每个元素都是undefined的，最好的方式遵照JavaScript:The Good Parts一书中的例子。
通过扩展JavaScript数组对象，增加一个新方法，根据传入数组行数，列数，和初始值。（构建矩阵数组）

Array.matrix = function(numrows, numcols, initial){
	var arr = []
	for( var i=0; i<numrows; i++ ){
		var columns = []
		for( var j=0; j<numcols; j++ ){
			columns[j] = initial
		}
		arr[i] = columns
	}
	return arr
}

// 对于小规模的数据，这是创建二维数组的最简单方法

// 处理二维数组的元素

var grades = [[87, 77, 45],[42,245,78],[67,34,22]]
var total = 0
var average = 0.0
for(var row = 0; row < grades.length; row++){
	for(var col = 0; col < grades[row].length; col++){
		total += grades[row][col]
	}
	//外层对应行，内层对应列
	average = total / grades[row].length
	console.log(average)
	average = 0.0
	total = 0
}

var grades = [[87, 77, 45],[42,245,78],[67,34,22]]
var total = 0
var average = 0.0
for(var col = 0; col < grades.length; col++){
	for(var row = 0; row < grades[row].length; row++){
		total += grades[row][col]
	}
	//外层对应列，内层对应行，相当于将原数组旋转90度
	average = total / grades[col].length
	console.log(average)
	average = 0.0
	total = 0
}

//一般可应用于矩阵的数据读取操作
//以上操作对于参差不齐的数组同样适用

// 对象中的数组

function weekTemps() {
	this.dataStore = []
	this.add = add
	this.average = average
}

function add(temp){
	this.dataStore.push(temp)
}

function average(){
	var total = 0.0
	for(var i=0; i<this.dataStore.length; i++){
		total += this.dataStore[i]
	}
	return total / this.dataStore.length
}

var thisWeek = new weekTemps()
thisWeek.add(1)
thisWeek.add(2)
console.log(thisWeek.average())

```

# 列表

列表的抽象数据类型定义
* listSize(property) 列表元素的个数
* pos(property) 列表当前位置
* length(property) 返回列表中元素的个数
* clear(method) 清空列表中的所有元素
* toString(method) 返回列表的字符串形式
* getElement(method) 返回当前位置的元素
* insert(method) 在现有元素后当中插入新元素
* append(method) 在列表的末尾添加新元素
* remove(method) 从列表中删除元素
* front(method) 将列表的当前位置设移动到第一个元素
* end(method) 将列表的当前位置移动到最后一个元素
* prev(method) 将当前位置后移一位
* next(method) 将当前位置前移一位
* currPos(method) 返回列表的当前位置
* moveTo(method) 将当前位置移动到指定位置

实例：

```
function List() {
	this.listSize = 0
	this.pos = 0
	this.dataStore = [] //初始化一个空数组来保存列表元素
	this.clear = clear
	this.find = find
	this.toString = toString
	
	...等定义的方法

}

//以下为基础方法实现

function append(element){
	this.dataStore[this.listSize++] = element
}

function find(element){
	for(var i=0;i<this.dataStore.length; i++){
		if(this.dataStore[i] ==  element){
			return i;
		}
	}
}

```
# 栈

栈的操作主要有两种：
1. 将一个元素压入栈（push）和将一个元素弹出栈(pop)
2. 预览栈顶元素（peek）

```

function Stack(){
	this.dataStore = [];
	this.top = 0;
	this.push = push;
	this.pop = pop;
	this.peek = peek;
}

function push(element){
	this.dataStore[this.top++] = element;
}

function pop(){
	return this.dataStore[--this.top];
}

function peek(){
	return this.dataStore[this.top-1];
}

// 返回其长度
function length(){
	return this.top;
}

function clear(){
	return this.top = 0;
}

```


实例1：将数字转换为2进制和8进制

```
function mulBase(num, base){
	var s = new Stack();
	do = {
		s.push(num % base);
		num = Method.floor(number /= base);
	}while( num >0 )
	var converted = '';
	while.(s.length() > 0){
	converted += s.pop;
	return conve
}
}

```
实例2: 判断规定字符串是否是回文

```
function isPalinddrome(word){
	var s = new Stack();
	for(var = 0;i <  word.length; i++){
		s.push(word[i])
	}
	var rword = '';
	while(s.length() > 0){
		rword += s.pop;
	}
	if(word == reword){
		return truel
	}else{
		return false;
	}
}

```

实例3： 递归演示
```
// 原递归, 计算任何数字的阶乘
function factorial(n){
	if(n === 0){
		return 0;
	}else{
		return n * factorial(n-1)
	}
}

// 栈模拟
function fact(n) {
	var s = new Stack();
	while( n > 1 ){
		s.push(n--);
	}
	var product = 1;
	while( s.length() > 1 ){
		product *= s.pop();
	}
	return product;
}

```
# 队列

对列表的操作主要有两种：
1. 向队列中插入新元素和删除队列中的元素，插入叫做入队，删除叫做出队
2. 读取队头元素（peek）

```
function Queue(){
	this.dataStore = [];
	this.enqueue = enqueue;
	this.dequeue = dequeue;
	this.front = front;
	this.back = back;
	this.toString = toString;
	this.empty = empty;
}

function enquequ(element){
	this.dataStore.push(element);
}

function dequeue(){
	return this.dataStore.shift;
}

function front(){
	return this.dataStore[0]
}

function back(){
	return this.dataStore[this.dataStore.length - 1]
}

function toString(){
	...打印所有结果
}

```
实例1：

```

```






