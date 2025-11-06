---
private: false
title: TypeScript的类型
authors:
  - admin
tags:
  - typescript
  - 前端
categories:
  - study
summary: TypeScript的类型
---
{{< toc mobile_only=true is_open=true >}}

## 类型注解

```typescript
let age: number = 18
```

## 原始类型

1. number
2. string
3. boolean
4. null
5. undefined
6. symbol
## 对象类型

### 1. 数组类型

```typescript
let numbers: number[] = [1,2,3]
```
### 2. 对象类型
属性为 属性名:属性
方法为 方法名():返回值类型
```typescript
let person: {name:string;age:number:sayHi():void} = {
	name: 'jack',
	age: 19,
	sayHi(){}
}
```
### 3. 元组类型
数组可以做到元组的效果，但元组在确切知道元素数量和类型的时候更严谨
```typescript
let position: [number,number] = [39.5427,116.2317]
```
## 接口（interface)
### 接口的定义
定义
```typescript
interface IPerson {
	name: string
	age: number
	sayHi():void
}
```
使用
```typescript
let person: IPerson = {
	name: 'jack',
	age: 19,
	sayHi(){}
}
```
### 接口的继承
```typescript
interface Point2D {x:number;y:number}
interface Point3D {x:number;y:number;z:number}
```
更好的方式：
```typescript
interface Point2D {x:number;y:number}
interface Point3D extends Point2D {z:number}
```
## 联合类型
```typescript
let sets: (number | string)[] = [1,"abc",2,"def"]
```
## 类型别名
```typescript
type CustomArray = (number|string)[]
let sets: CustomArray = [1,"abc",2,"def"]
```
类型别名可以实现类似接口的效果
```typescript
type IPerson = {
	name: string
	age: number
	sayHi():void
}

```
## 函数类型

即为**函数参数**和**返回值**指定类型
### 单独指定
```typescript
function add(num1: number,num2: number): number {
	return num1+num2
}
const add = (num1: number,num2: number): number =>{
	return num1+num2
}
```
 第二个箭头表达式类似lamda表达式
### 同时指定
```typescript
const add: (num1: number, num2:number) => number = (num1,num2) => {
	return num1+num2
}
```
对变量add进行类型注解，类型为冒号后的`(num1: number, num2:number) => number`，值为`(num1,num2) => {return num1+num2}`

### 可选参数
在参数名后加`?`
```typescript
function add(num1?: number,num2?: number): number {
	return num1+num2
}
```
## 类型推论
以下2种常见场景会触发类型推论，届时无需手动写类型：
1. 声明变量并初始化时
2. 决定函数返回值时
## 类型断言
有时我们比ts更清楚变量的类型，可以使用as对类型进行指定
```typescript
const aLink = document.getElementById('link') as HTMLAnchorElement
```

## 字面量类型
任意js字面量（字符串，对象，数字等）都可以作为类型使用
```typescript
const str2 = 'Hello TS'
```
以上的常量类型会推断为字面量`'Hello TS'`

字面量通常搭配联合类型一起使用，达到**枚举**的效果
```typescript
function changeDirection(direction: 'up'|'down'|'left'|'right' ){}
```

## 枚举
```typescript
enum Direction{UP,DOWN,LEFT,RIGHT}
function changeDirection(direction: Direction){}
changeDirection(Direction.UP)
```

