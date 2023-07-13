---
title: TS手册
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2021-12-03 15:22:09
password:
summary:
tags:
- 前端
- Js
- Ts
categories:
- [前端, Ts]
id: 1
---
学习一下 [TypeScript]{.red},顺便记录一下。
<!-- more -->
# 基础类型
## 布尔值
* true/false
```js codes
//
let isDone: boolean = false;
```
## 数字
* 和 JavaScript 一样，TypeScript 里的所有数字都是浮点数。这些浮点数的类型是 number。除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。
```js codes
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```
## 字符串
* 和 JavaScript 一样，可以使用双引号（"）或单引号（'）表示字符串。
```js codes

  let name: string = 'zhangsan';
```
* 还可以使用 模板字符串。 这种字符串是被反引号包围（`），并且以${ expr }这种形式嵌入表达式
```js codes

  let name: string = 'zhangsan';
  let age: number = '22';
  let text: string = `my name is &{name}, my age is ${age};`
```
## 数组
* 有两种方式可以定义数组。 
* 第一种，可以在元素类型后面接上 []，表示由此类型元素组成的一个数组：
```js codes
// 
  let list: number[] = [1, 2, 3]
```
* 第二种方式是使用数组泛型，Array<元素类型>：
```js codes
  let list: Array<number> = [1, 2, 3]
```
## 元组 Tuple
* 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。
```js codes
  let x: [string, number];
  x = ['zhangsan', 22]; // OK
  x = [22, 'zhangsan']; // error
```
## 枚举
* enum 类型是对 JavaScript 标准数据类型的一个补充。
```js codes
  enum Color {Red, Green, Blue}
  let color: Color = Color.Red;
```
* 默认情况下，从0开始为元素编号。 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从 1开始编号
```js codes
  enum Color {Red = 1, Green, Blue = 4}
  let color: Color = Color.Red;
```
* 枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为2，但是不确定它映射到Color里的哪个名字，我们可以查找相应的名字：
```js codes
  enum Color {Red = 1, Green, Blue}
  let colorName: string = Color[2];
  console.log(colorName); // Green
```
## 函数类型
* 为函数定义类型。我们可以给每个参数添加类型之后再为函数本身添加返回值类型
```js codes
function func1 (a: number,b: number): string {
  return 'func1'
}
func1(1, 2) // 参数的个数必须要完全相同

function func2 (a: number,b?: number): string {
  return 'func2'
  // 设置可选参数必须放最后面
}
func2(1) // ok b为可选参数

function func3 (a: number,b: number = 10): string {
  return 'func3'
  // 设置参数b的默认值 （设置默认值的参数必须放最后面）
}

function func4 (a: number,b: number, ...rest: number[]): string {
  return 'func4'
  // 设置接收任意数量的参数
}
```
* 使用函数表达式
```js codes
  const func1: (a: number, b: number) => string = function (a: number, b: number): string {
    return 'func1'
  }
```
## Any
* 有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型,那么我们可以使用 any类型来标记这些变量
```js codes
  let a: any = false;
  a = 'zhangsan'; // ok
  a = 22; // ok
```
* 当你只知道一部分数据的类型时，any类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据：
```js codes
  let list: any[] = [1, true, 'name'];
  list[1] = 100;
```
## Void
* void 类型像是与 any 类型相反，它表示没有任何类型。只能赋予它 undefined 和 null。
```js codes
  let unusable: void = undefined;
```
## Null 和 Undefined
* TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。默认情况下null和undefined是所有类型的子类型。和void相似，它们的本身的类型用处不是很大：
```js codes
  let u: undefined = undefined;
  let n: null = null;
```
## Never
* never类型表示的是那些永不存在的值的类型。
## Object
* Object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。
# 类型断言
* 绕过编译器的类型推断，手动指定一个值的类型
* 有两种方式。第一种：<类型>变量
```js codes 
let someValue: any = 'zhangsan';
let strLength: number = (<string>someValue).length;
```
* 第二种：变量 as 类型
```js codes 
let someValue: any = 'zhangsan';
let strLength: number = (someValue as string).length;
```
# 接口  Interface
* 可以理解为一种规范，约束对象的结构。
```js codes 
interface Params {
  title: string
  content: string
}

function submit (params: Params) {
  console.log(params.title)
  console.log(params.content)
}
submit({
  title: '213',
  content: '334'
})

```
## 可选属性
* 接口里的属性不全都是必需的。(属性名?: 类型)
```js codes 
interface Params {
  title: string
  content: string
  subTitle?: string
}
```
## 只读属性
* 一些对象属性只能在对象刚刚创建的时候修改其值。属性名之前加 readonly 来指定只读属性。
```js codes 
interface Params {
  title: string
  content: string
  subTitle?: string
  readonly author: string
}
```
## 动态成员
* 有时候不知道具体的字段名，是动态的,例如 缓存
```js codes 
interface Params {
  [key: string]: string
  // key不是固定的，可以用任意名称代替 如props
}
```
[:heart:筱何仔]{.label .danger}