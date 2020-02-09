# 基础类型-入门
## 介绍
TypeScript包含的最简单的数据单元有：数字，字符串，布尔值，Null 和 Undefined等。TypeScript支持与JavaScript几乎相同的数据类型，此外还提供了实用的枚举类型方便我们使用。本节介绍基础类型的布尔值、数字、字符串、数组、元组、枚举、any 和 void 等，其他几种基础类型详见 [基础类型-高级](/advanced/basic-types.html)。

## 布尔值
最基本的数据类型就是简单的`true/false`值，在JavaScript和TypeScript里叫做`boolean`。
```
let isDone: boolean = false
```

## 数字
和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是`number`。 
```
let decLiteral: number = 6
```

## 字符串
TypeScript像其它语言里一样，使用`string`表示文本数据类型。 和JavaScript一样，可以使用双引号（"）或单引号（'）表示字符串。
```
let from: string = "千锋教育"
from = "好程序员"
```

也使用模版字符串，定义多行文本和内嵌表达式。 这种字符串是被反引号包围（`），并且以${ expr }这种形式嵌入表达式。

```
let surname: string = `Felix`
let age: number = 37
let sentence: string = `Hello, my name is ${ surname }.

I'll be ${ age + 1 } years old next month.`
```

## 数组
TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在元素类型后面接上 `[]`，表示由此类型元素组成的一个数组：
```
let list: number[] = [1, 2, 3]
```
第二种方式是使用数组泛型，Array<元素类型>：
```
let list: Array<number> = [1, 2, 3]
```

## 元组 Tuple
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。
```
// 声明一个元组类型 x
let x: [string, number]
// 初始化 x
x = ['hello', 10] // OK
// 无效的初始值
x = [10, 'hello'] // Error
```
当访问一个已知索引的元素，会得到正确的类型：
```
console.log(x[0].substr(1)) // OK
console.log(x[1].substr(1)) // Error, 'number' 不存在 'substr' 方法
```
当访问一个越界的元素，会出现错误：
```
x[3] = "world" // Error, '[string, number]' 未定义第 3 个元素的类型.
console.log(x[5].toString()) // Error, '[string, number]' 未定义第 5 个元素的类型.
```

## 枚举
enum类型是对JavaScript标准数据类型的一个补充。 使用枚举类型可以为一组数值赋予友好的名字。
```
enum Color {Red, Green, Blue}
let c: Color = Color.Green
```

默认情况下，从 0 开始为元素编号。 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从 1 开始编号：
```
enum Color {Red = 1, Green, Blue}
let c: Color = Color.Green
```

或者，全部都采用手动赋值：
```
enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green
```

枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为2，但是不确定它映射到Color里的哪个名字，我们可以查找相应的名字：
```
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2]
console.log(colorName)  // 'Green'
```

## any
有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用`any`类型来标记这些变量：
```
let notSure: any = 4
notSure = "maybe a string instead" // OK 赋值了一个字符串
notSure = false // OK 赋值了一个布尔值
```

在对现有代码进行改写的时候，`any` 类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。
```
let notSure: any = 4
notSure.ifItExists() // okay, ifItExists函数在运行时可能存在
notSure.toFixed() // okay, toFixed 函数存在 (在编译时不做检查)
```

当你只知道一部分数据的类型时，`any` 类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据：
```
let list: any[] = [1, true, "free"]
list[1] = 100
```

## void
某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 `void`：
```
function echo(): void {
  console.log('做真实的自己，用良心做教育')
}
```
声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null：
```
let unusable: void = undefined
let greeting: void = 'hello world' // void 类型不能赋值为字符串
```
