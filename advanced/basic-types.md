# 基础类型-高级
## 类型推断
如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。
### 什么是类型推断
以下代码虽然没有指定类型，但是会在编译的时候报错：
```
let lunarDay = '初一'
lunarDay = 1
// Type '1' is not assignable to type 'string'.
```
事实上，它等价于：
```
let lunarDay: string = '初一'
lunarDay = 1
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。
如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查：
let myFavoriteNumber
myFavoriteNumber = 'seven'
myFavoriteNumber = 7

let someValue: any = "this is a string"

let strLength: number = (<string>someValue).length
另一个为as语法：

let someValue: any = "this is a string"

let strLength: number = (someValue as string).length
两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在TypeScript里使用JSX时，只有 as语法断言是被允许的。

## 联合类型
联合类型（Union Types）表示取值可以为多种类型中的一种。
### 简单的例子
```
let lunarDay: string | number
lunarDay = '初一'
lunarDay = 1
```
联合类型使用 | 分隔每个类型。

这里的` let lunarDay: string | number `的含义是，允许 lunarDay 的类型是 string 或者 number，但是不能是其他类型。

### 访问联合类型的属性或方法
当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：
```
function getLength(something: string | number): number {
  return something.length
}
// Property 'length' does not exist on type 'string | number'.
// Property 'length' does not exist on type 'number'. 
```

上例中，length 不是 string 和 number 的共有属性，所以会报错。
访问 string 和 number 的共有属性是没问题的：
```
function getString(something: string | number): string {
  return something.toString()
}
```
### 联合类型赋值的类型推断
联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：
```
let lunarDay: string | number
lunarDay = '初一'
console.log(lunarDay.length) // 2
lunarDay = 1
console.log(lunarDay.length) // 编译时报错
```
上例中，第二行的 lunarDay 被推断成了 string，访问它的 length 属性不会报错。
而第四行的 lunarDay 被推断成了 number，访问它的 length 属性时就报错了。

## Null 和 Undefined
`null` 是一个只有一个值的特殊类型。表示一个空对象引用。用 typeof 检测 null 返回是 `object`。
typeof 一个没有值的变量会返回 `undefined`。

null 和 Undefined 是其他任何类型（包括 void）的子类型，可以赋值给其它类型，如数字类型，此时，赋值后的类型会变成 null 或 undefined。

在TypeScript中启用严格的空校验（--strictNullChecks）特性，使得 `null` 和 `undefined` 只能被赋值给 `void` 或本身对应的类型

在 tsconfig.json 中启用 --strictNullChecks
```
let x: number
x = 1 // 运行正确
x = undefined // 运行错误
x = null // 运行错误
```

在 tsconfig.json 中启用 --strictNullChecks，需要将x赋值为联合类型
```
let x: number | null | undefined //本身对应的类型
x = 1 // 运行正确
x = undefined // 运行正确
x = null // 运行正确
```

## Never
never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。

never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。

下面是一些返回never类型的函数：
```
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
  throw new Error(message)
}

// 推断的返回值类型为never
function fail() {
  return error("Something failed")
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
  while (true) {
  }
}
```