---
private: false
title: TypeScript的面向对象
authors:
  - admin
tags:
  - typescript
  - 前端
categories:
  - study
summary: TypeScript的面向对象
---
{{< toc mobile_only=true is_open=true >}}

在 TypeScript（及面向对象编程）中，子类继承父类后，核心拓展方向围绕 **“复用父类能力+新增/重写自身特性”** 展开，具体可从以下 7 个关键维度进行拓展，结合 TS 特性（类型约束、装饰器、泛型等）说明：


### 一、新增类成员（属性/方法）
这是最基础的拓展：子类在继承父类所有非私有（`public`/`protected`）成员后，可新增专属的属性或方法，实现父类没有的功能。

#### 核心要点：
- 新增属性需符合 TS 类型约束（显式声明类型或通过初始化推导）；
- 新增方法可访问父类的 `public`/`protected` 成员，也可调用自身新增成员。

#### 示例：
```typescript
class Animal {
  protected name: string; // 父类受保护属性（子类可访问）
  constructor(name: string) {
    this.name = name;
  }
  public eat() {
    console.log(`${this.name} 吃食物`);
  }
}

// 子类 Dog 拓展：新增属性 + 新增方法
class Dog extends Animal {
  // 新增专属属性（显式类型声明）
  private breed: string; 
  // 新增静态属性（子类共享）
  static category = "哺乳动物";

  constructor(name: string, breed: string) {
    super(name); // 必须先调用父类构造器
    this.breed = breed; // 初始化新增属性
  }

  // 新增专属实例方法
  public bark() {
    console.log(`${this.name}（${this.breed}）汪汪叫`);
  }

  // 新增静态方法（子类直接调用）
  static isDog(obj: unknown): obj is Dog {
    return obj instanceof Dog;
  }
}

const dog = new Dog("旺财", "中华田园犬");
dog.eat(); // 继承父类方法：旺财 吃食物
dog.bark(); // 子类新增方法：旺财（中华田园犬）汪汪叫
console.log(Dog.category); // 子类静态属性：哺乳动物
```


### 二、重写父类方法（方法覆盖）
子类可重写父类的 `public`/`protected` 方法，修改其实现逻辑（但需保持**方法签名兼容**，TS 会强制校验），实现“多态”特性。

#### 核心要点：
- 重写方法的**参数类型、返回值类型**需与父类兼容（可更严格，符合“协变/逆变”规则）；
- 不可重写父类的 `private` 方法（子类无法访问，本质是“隐藏”而非“重写”）；
- 可通过 `super.方法名()` 调用父类原实现，避免重复逻辑。

#### 示例：
```typescript
class Cat extends Animal {
  constructor(name: string) {
    super(name);
  }

  // 重写父类 eat 方法（签名兼容，逻辑修改）
  public eat() {
    super.eat(); // 调用父类原实现
    console.log(`${this.name} 只吃小鱼干`); // 新增子类逻辑
  }

  // 重写 + 更严格的返回值（父类无返回值，子类返回 void 兼容）
  public sleep(): void {
    console.log(`${this.name} 蜷缩着睡`);
  }
}

const cat = new Cat("咪酱");
cat.eat(); 
// 输出：咪酱 吃食物 → 咪酱 只吃小鱼干（父类逻辑+子类拓展）
```


### 三、拓展构造器（初始化逻辑增强）
子类构造器需通过 `super()` 调用父类构造器（必须是子类构造器的第一条语句），在此基础上可**新增子类专属的初始化逻辑**（如初始化新增属性、接收额外参数）。

#### 核心要点：
- 子类构造器参数可比父类多（拓展入参），但需保证父类构造器的参数被正确传递；
- 可通过构造器参数修饰符（`public`/`private`/`protected`）简化属性声明。

#### 示例：
```typescript
class Bird extends Animal {
  // 构造器参数 + 修饰符：直接声明并初始化属性（简化代码）
  constructor(name: string, public wingSpan: number) {
    super(name); // 必传父类构造器参数
    // 新增子类初始化逻辑
    console.log(`${this.name} 的翼展：${this.wingSpan}cm`);
  }
}

const bird = new Bird("麻雀", 15); 
// 输出：麻雀 的翼展：15cm（子类构造器拓展逻辑）
```


### 四、实现/拓展接口（多态能力增强）
子类继承父类的同时，可实现一个或多个接口（`implements`），强制子类满足接口约束，进一步拓展“多态”能力（父类负责基础能力，接口负责行为规范）。

#### 核心要点：
- 接口仅约束“结构”（属性/方法签名），无具体实现，子类需实现接口的所有成员；
- 可结合父类方法重写，让子类同时满足父类和接口的约束。

#### 示例：
```typescript
// 定义接口（行为规范）
interface Flyable {
  fly(height: number): void;
}

// 子类：继承 Animal + 实现 Flyable 接口
class Eagle extends Animal implements Flyable {
  constructor(name: string) {
    super(name);
  }

  // 必须实现接口的 fly 方法
  public fly(height: number) {
    console.log(`${this.name} 飞到了 ${height} 米高空`);
  }

  // 重写父类方法，同时符合接口无约束的自由拓展
  public eat() {
    console.log(`${this.name} 吃肉类`);
  }
}

const eagle = new Eagle("雄鹰");
eagle.fly(1000); // 接口要求的方法：雄鹰 飞到了 1000 米高空
eagle.eat(); // 重写父类方法：雄鹰 吃肉类
```


### 五、泛型拓展（类型灵活性增强）
若父类是泛型类（`class Parent<T>`），子类可通过**指定具体类型**或**拓展泛型约束**，让子类的类型更精准或更灵活。

#### 核心要点：
- 子类可固定父类泛型的类型（如 `class Child extends Parent<string>`）；
- 子类可新增泛型参数，或对父类泛型添加更严格的约束。

#### 示例：
```typescript
// 父类：泛型类（存储任意类型的数据）
class Storage<T> {
  protected data: T[] = [];

  public add(item: T) {
    this.data.push(item);
  }

  public get(): T[] {
    return this.data;
  }
}

// 子类 1：固定父类泛型类型（仅存储字符串）
class StringStorage extends Storage<string> {
  // 新增方法：仅处理字符串
  public join(separator: string): string {
    return this.get().join(separator);
  }
}

// 子类 2：拓展泛型约束（仅存储可排序的类型）
class SortableStorage<T extends number | string> extends Storage<T> {
  // 新增方法：排序（依赖 T 可比较）
  public sort(): T[] {
    return [...this.get()].sort((a, b) => (a < b ? -1 : 1));
  }
}

const strStorage = new StringStorage();
strStorage.add("a");
strStorage.add("b");
console.log(strStorage.join("-")); // a-b（子类泛型固定后的拓展）

const numStorage = new SortableStorage<number>();
numStorage.add(3);
numStorage.add(1);
console.log(numStorage.sort()); // [1,3]（子类泛型约束后的拓展）
```


### 六、装饰器拓展（元编程能力）
利用 TS 装饰器（`Decorator`），可在不修改父类/子类源码的前提下，为子类新增**元数据、日志、缓存、权限校验**等横切逻辑，是“无侵入式拓展”的核心方式。

#### 核心要点：
- 需开启 TS 配置 `experimentalDecorators: true`；
- 支持类装饰器、方法装饰器、属性装饰器等，可作用于子类本身或其成员。

#### 示例：
```typescript
// 类装饰器：为子类添加日志功能
function LogClass(constructor: Function) {
  const originalConstructor = constructor;
  // 替换构造器，添加日志逻辑
  const newConstructor = function (...args: any[]) {
    console.log(`创建 ${constructor.name} 实例，参数：${JSON.stringify(args)}`);
    return new originalConstructor(...args);
  };
  return newConstructor as any;
}

// 方法装饰器：为子类方法添加执行时间日志
function LogMethod(target: any, methodName: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    const start = Date.now();
    const result = originalMethod.apply(this, args);
    const end = Date.now();
    console.log(`${methodName} 执行耗时：${end - start}ms`);
    return result;
  };
  return descriptor;
}

// 子类：继承 Animal + 应用装饰器
@LogClass // 类装饰器
class Tiger extends Animal {
  constructor(name: string) {
    super(name);
  }

  @LogMethod // 方法装饰器
  public hunt() {
    console.log(`${this.name} 在捕猎`);
  }
}

const tiger = new Tiger("东北虎"); 
// 输出：创建 Tiger 实例，参数：["东北虎"]（类装饰器生效）
tiger.hunt(); 
// 输出：东北虎 在捕猎 → hunt 执行耗时：0ms（方法装饰器生效）
```


### 七、访问控制拓展（封装性优化）
子类可通过 `public`/`protected`/`private` 调整父类成员的访问权限（**只能更严格，不能更宽松**），或新增私有成员，优化类的封装性。

#### 核心规则：
- 父类 `public` → 子类可声明为 `public`/`protected`（不能是 `private`，会破坏继承兼容性）；
- 父类 `protected` → 子类可声明为 `protected`/`private`（不能是 `public`，会扩大访问范围）；
- 父类 `private` → 子类无法访问，只能新增同名私有成员（本质是子类自己的成员，与父类无关）。

#### 示例：
```typescript
class Person {
  public name: string; // 父类公开属性
  protected age: number; // 父类受保护属性
  private idCard: string; // 父类私有属性（子类不可访问）

  constructor(name: string, age: number, idCard: string) {
    this.name = name;
    this.age = age;
    this.idCard = idCard;
  }
}

class Student extends Person {
  // 父类 public → 子类 protected（权限收紧：外部无法直接访问）
  protected name: string;
  // 父类 protected → 子类 private（权限收紧：仅子类内部访问）
  private age: number;
  // 子类新增私有属性
  private studentId: string;

  constructor(name: string, age: number, idCard: string, studentId: string) {
    super(name, age, idCard);
    this.name = name; // 子类重定义的 protected 属性
    this.age = age; // 子类重定义的 private 属性
    this.studentId = studentId;
  }

  public getInfo() {
    console.log(`姓名：${this.name}，学号：${this.studentId}`);
    // console.log(this.idCard); // 报错：父类 private 属性子类无法访问
  }
}

const student = new Student("小明", 18, "123456", "S1001");
// console.log(student.name); // 报错：子类 name 是 protected，外部不可访问
student.getInfo(); // 正常：姓名：小明，学号：S1001
```


### 总结：拓展的核心原则
1. **兼容性**：子类拓展不能破坏父类的接口契约（如重写方法签名需兼容、访问权限不能宽松）；
2. **复用性**：优先通过 `super` 复用父类逻辑，避免重复代码；
3. **封装性**：通过访问控制、私有成员隐藏内部实现，仅暴露必要接口；
4. **灵活性**：结合泛型、接口、装饰器，让子类适配更多场景（多态、元编程等）。

以上维度可单独使用，也可组合（如“泛型子类 + 接口实现 + 装饰器”），覆盖绝大多数 TS 面向对象的拓展需求。



# 合并类
在 TypeScript 中合并两个类（Class Merging）主要有 **3 种核心场景**，分别对应不同的需求（复用逻辑、组合实例、扩展静态成员），下面结合具体场景和代码示例详细说明：


## 一、场景 1：复用类逻辑（继承 + 混入 Mixin）
如果需要让一个类「继承多个类的实例方法/属性」（TS 不支持多继承，但可通过 **混入（Mixin）** 实现），这是最常见的类合并场景。

### 核心原理
通过函数动态将多个类的原型方法“拷贝”到目标类，实现多类逻辑复用。

### 实现步骤
1. 定义基础类（被合并的类）；
2. 编写混入工具函数（处理类型和原型拷贝）；
3. 目标类通过混入继承多个基础类的逻辑。

### 代码示例
```typescript
// 1. 定义被合并的基础类（无构造函数依赖）
class Walkable {
  walk() {
    console.log("正在行走");
  }
  speed = 5; // 实例属性
}

class Swimmable {
  swim() {
    console.log("正在游泳");
  }
  depth = 10; // 实例属性
}

// 2. 混入工具函数（通用实现，可复用）
type Constructor = new (...args: any[]) => {}; // 基础构造函数类型

// 接收多个类，返回一个新的构造函数（合并所有类的原型）
function mixin<T extends Constructor[]>(...baseClasses: T) {
  // 中间类，用于承载合并后的逻辑
  class MixedClass {}

  // 遍历所有基础类，拷贝原型方法/属性到 MixedClass
  baseClasses.forEach((base) => {
    Object.getOwnPropertyNames(base.prototype).forEach((prop) => {
      if (prop !== "constructor") {
        MixedClass.prototype[prop] = base.prototype[prop];
      }
    });

    // 拷贝静态属性/方法（如果需要）
    Object.getOwnPropertyNames(base).forEach((prop) => {
      if (prop !== "prototype" && prop !== "constructor") {
        (MixedClass as any)[prop] = (base as any)[prop];
      }
    });
  });

  return MixedClass as unknown as Constructor & UnionToIntersection<InstanceType<T[number]>>;
}

// 辅助类型：将联合类型转为交叉类型（获取所有类的实例类型合并）
type UnionToIntersection<U> = (U extends any ? (k: U) => void : never) extends (k: infer I) => void ? I : never;

// 3. 目标类：通过混入合并 Walkable 和 Swimmable
class Person extends mixin(Walkable, Swimmable) {
  name: string;
  constructor(name: string) {
    super(); // 必须调用 super()（混入类的构造函数）
    this.name = name;
  }
}

// 使用示例
const p = new Person("小明");
p.walk(); // 继承 Walkable：正在行走
p.swim(); // 继承 Swimmable：正在游泳
console.log(p.speed); // 5（继承实例属性）
console.log(p.depth); // 10（继承实例属性）
```

### 注意事项
- 基础类**尽量避免复杂构造函数**（如果有构造函数参数，需要在混入时统一处理）；
- 若多个基础类有同名方法，**后面的类会覆盖前面的类**（按混入顺序优先级）；
- 混入本质是“原型拷贝”，不是真正的多继承，不影响原型链层级。


## 二、场景 2：组合两个类的实例（对象合并）
如果已有两个类的实例，需要将它们的**实例属性/方法合并为一个新对象**（不修改原实例），可使用 `Object.assign` 或扩展运算符 `...`。

### 代码示例
```typescript
class A {
  a = 1;
  methodA() {
    console.log("A 的方法");
  }
}

class B {
  b = 2;
  methodB() {
    console.log("B 的方法");
  }
}

// 方式 1：Object.assign（浅拷贝，合并实例）
const instanceA = new A();
const instanceB = new B();
const mergedInstance = Object.assign({}, instanceA, instanceB);

mergedInstance.methodA(); // A 的方法
mergedInstance.methodB(); // B 的方法
console.log(mergedInstance.a); // 1
console.log(mergedInstance.b); // 2

// 方式 2：扩展运算符（更简洁，同样浅拷贝）
const mergedInstance2 = { ...instanceA, ...instanceB };
```

### 注意事项
- 仅合并**实例的自身属性/方法**（不包含原型链上的属性，除非显式挂载在实例上）；
- 同名属性/方法会被**后面的实例覆盖**；
- 是浅拷贝，若属性是引用类型（如对象、数组），会共享引用。


## 三、场景 3：合并类的静态成员（Static Merging）
如果需要合并两个类的**静态属性/方法**（无需实例化即可调用），可直接通过赋值或工具函数实现。

### 代码示例
```typescript
// 定义两个带静态成员的类
class MathUtil {
  static add(a: number, b: number) {
    return a + b;
  }
  static PI = 3.14;
}

class StringUtil {
  static upperCase(str: string) {
    return str.toUpperCase();
  }
  static EMPTY = "";
}

// 方式 1：直接赋值合并静态成员
class CombinedUtil {
  // 拷贝 MathUtil 的静态成员
  static add = MathUtil.add;
  static PI = MathUtil.PI;
  // 拷贝 StringUtil 的静态成员
  static upperCase = StringUtil.upperCase;
  static EMPTY = StringUtil.EMPTY;
}

// 方式 2：工具函数批量合并（更通用）
function mergeStatic<T extends Record<string, any>, U extends Record<string, any>>(
  target: T,
  ...sources: U[]
): T & U {
  sources.forEach((source) => {
    Object.getOwnPropertyNames(source).forEach((key) => {
      if (key !== "prototype" && key !== "constructor") {
        (target as any)[key] = (source as any)[key];
      }
    });
  });
  return target as T & U;
}

// 使用工具函数合并
const MergedUtil = mergeStatic({}, MathUtil, StringUtil);

// 调用合并后的静态成员
console.log(CombinedUtil.add(1, 2)); // 3
console.log(CombinedUtil.upperCase("hello")); // HELLO
console.log(MergedUtil.PI); // 3.14
console.log(MergedUtil.EMPTY); // ""
```


## 四、关键总结
| 场景 | 实现方式 | 核心特点 |
|------|----------|----------|
| 复用多类实例逻辑 | 混入（Mixin） | 目标类继承多个类的原型方法/属性，类型安全 |
| 合并已有实例 | Object.assign / 扩展运算符 | 浅拷贝实例属性，不修改原实例 |
| 合并静态成员 | 直接赋值 / 工具函数 | 无需实例化，共享静态方法/属性 |

### 选型建议
- 若需长期复用多类逻辑 → 优先用 **混入（Mixin）**；
- 若仅需临时合并两个实例 → 用 **Object.assign / 扩展运算符**；
- 若需共享静态工具方法 → 用 **静态成员合并**。