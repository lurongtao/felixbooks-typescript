# 装饰器
## 介绍
> 装饰器是一种特殊类型的声明，它能够附加到类、类的函数、类属性、类函数的参数上，以达到修改类的行为。

### 装饰器的种类
#### 根据装饰器的位置
* 类装饰器
* 类函数装饰器
* 类属性装饰器
* 类函数参数装饰器

#### 根据装饰器是否有参数
* 无参装饰器(一般装饰器)
* 有参装饰器(装饰器工厂)

### 类的装饰器
* 类装饰器的写法

```
function desc(target) {
  console.log(target) // 输出 [Function: Person]表示当前装饰的类
}

@desc
class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
}
```

此处 target 类型会隐式定义为any, 这样会引发一个TS问题，解决方案：设置tsconfig.json 

1、"noImplicitAny": false

或者

2、"strict": false

* 使用类的装饰器扩展类的属性和方法
interface Class {
  new(...args: any[]): {}
}

function desc<T extends Class>(Target: T) {
  console.log(Target)
  return class extends Target {
    gender = '男'
    say() {
      console.log(this.gender)
    }
  }
}

@desc
class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  say() {
    console.log(this.name, this.age)
  }
}

let p = new Person('Felix', 20)
console.log(p)
p.say()
```

* 使用装饰器修改类的构造函数(构造函数的重载、方法重载)

```
function desc(target) {
  return class extends target{
    name = 'Felixlu'
    age = 18
    sayHell() {
      console.log('我是重载后的', this.name)
    }
  }
}

@desc
class Person {
  public name: string | undefined
  public age: number | 0

  constructor() {
    this.name = 'Felix'
    this.age = 20
  }

  sayHell() {
    console.log('hello word', this.name)
  }
}

let p = new Person()
console.log(p)
p.sayHell()
```

* 装饰器工厂的写法

```
function desc(params: string) {
  return function (target: any) {
    console.log('params', params)
    console.log('target', target)
    // 直接在原型上扩展一个属性
    target.prototype.apiUrl = params
  }
}

@desc('好程序员')
class P {
  say() {
    console.log('说话')
  }
}

let p: any = new P()
console.log(p.apiUrl)
```

### 类函数装饰器

> 它应用到方法上，可以用来监视、修改、替换该方法

* 基本使用

```
function desc(target, key, descriptor) {
  console.log('target', target) // Person { say: [Function] } 表示类的原型
  console.log('key', key) // 被装饰的函数名
  console.log('descriptor', descriptor) // 被装饰的函数的对象属性
}

class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  @desc
  say() {
    console.log('说的方法')
  }
}
```

* 在装饰器中添加类的原型属性和原型方法

```
function desc(target, key, descriptor) {
  target.gender = '男'
  target.foo = function () {
    console.log('我是原型上的方法')
  }
}

class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  @desc
  say() {
    console.log('说的方法')
  }
}

// 测试代码
let p: any = new Person('Felixlu', 20)
console.log(p)
console.log(Person.prototype)
p.say()
console.log(p.gender); // 使用p原型链上的属性
p.foo() // 调用了p原型链上的方法
```

* 使用装饰器拦截函数的调用（替换）

```
function desc(params: string) {
  return function (target: any, key: string, descriptor: {[propsName: string]: any}) {
    // 修改被装饰的函数
    descriptor.value = function (...args: Array<any>) {
      args = args.map(it => String(it))
      console.log(args)
    }
  }
}

class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  @desc('装饰器上的参数')
  say() {
    console.log('说的方法')
  }
}


let p: any = new Person('Felixlu', 20)
console.log(p)
p.say(123, 23, '你好')
```

* 使用装饰器拦截函数的调用(附加新的功能)

```
function desc(params: string) {
  return function (target: any, key: string, descriptor: {[propsName: string]: any}) {
    // 修改被装饰的函数的
    let method = descriptor.value
    descriptor.value = function (...args: Array<any>) {
      args = args.map(it => String(it))
      console.log(args)
      method.apply(this, args)
    }
  }
}
class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  @desc('装饰器上的参数')
  say(...args) {
    console.log('说的方法', args)
  }
}


let p = new Person('Felixlu', 20)
console.log(p)
p.say(123, 23, '你好')
```

### 类属性装饰器

* 基本用法

```
function desc(target, name) {
  console.log('target', target, target.constructor) // 表示类的原型
  console.log('name', name) // 表示被装饰属性名
}

class Person {
  public name: string | undefined
  public age: number | 0

  @desc
  private gender: string | undefined

  constructor(name, age) {
    this.name = name
    this.age = age
  }
}

let p = new Person('Felixlu', 20)
console.log(p)
```

* 在装饰器中修改属性值

```
function desc(target, name) {
  target[name] = '女'
}


class Person {
  public name: string | undefined
  public age: number | 0

  @desc
  public gender: string | undefined

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  say() {
    console.log(this.name, this.age, this.gender)
  }
}

let p = new Person('Felixlu', 20)
console.log(p)
p.say()
```

* 类函数参数的装饰器

> 参数装饰器表达式会在运行时候当做函数被调用，以使用参数装饰器为类的原型上附加一些元数据

* 基本用法

```
function desc(params: string) {
  return function (target: any, key, index) {
    console.log(target); // 类的原型
    console.log(key); // 被装饰的名字
    console.log(index); // 序列化
  } 
}
class Person {
  public name: string | undefined
  public age: number | 0

  constructor(name, age) {
    this.name = name
    this.age = age
  }

  say(@desc('参数装饰器') age: number) {
    console.log('说的方法')
  }
}

let p = new Person('Felixlu', 20)
console.log(p)
p.say(20)
```

* 为类的原型上添加一些东西

```
function desc(params: string) {
  return function (target: any, key, index) {
    console.log(target); // 类的原型
    console.log(key); // 被装饰的名字
    console.log(index); // 序列化
    target.message = params;
  } 
}
class Person {
  public name: string | undefined;
  public age: number | 0;

  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  say(@desc('参数装饰器') age: number) {
    console.log('说的方法')
  }
}


let p: any = new Person('哈哈', 20);
console.log(p);
p.say(20);
console.log(p.message)
```

### 几种装饰器的执行顺序

```
function logCls(params: string) {
  return function (target: any) {
    console.log('4.类的装饰器');
  }
}

function logMethod(params: string) {
  return function (target: any, key: string, descriptor: {[propsName: string]: any}) {
    console.log('3.类的函数装饰器');
  }
}

function logParams(params: string) {
  return function (target: any, name: string) {
    console.log('1.类属性装饰器');
  }
}

function logQuery(params: string) {
  return function (target: any, key: string, index: number) {
    console.log('2.函数参数装饰器');
  }
}

@logCls('类的装饰器')
class Person{
  @logParams('属性装饰器')
  public name: string | undefined;

  @logMehod('函数装饰器')
  getData(@logQuery('函数参数装饰器') age: number, @logQuery('函数参数装饰器') gender: string) {
    console.log('----');
  }
}
```