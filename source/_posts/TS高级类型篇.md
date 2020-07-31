---
title: TS高级类型篇
date: 2020-07-23 17:57:51
categories: javascript
tags: TypeScript
---


TypeScript 高级类型

<!-- more -->

## 交叉类型
使用符号 & 合并类型
```ts
interface Product {
  id: number
  name: string
}

interface Shape {
  width: number
  height: number
}

type IPhone = Product & Shape
```

## 联合类型
用符号竖线 | 分割每个类型。
```ts
interface Fish {
  name: string
  swim: Function
}
interface Bird {
  name: string
  fly: Function
}
type Pet = Fish | Bird

function petShow(pet: Pet) {
  if ('swim' in pet) {
    pet.swim()
  } else {
    pet.fly()
  }
}
```
因为是多种类型的并集，所以使用的时候需要 进行类型保护和区分，以确保能正确访问

## 类型守卫
- in
    ```ts
    function petShow(pet: Pet) {
    if ('swim' in pet) {
        pet.swim()
    } else {
        pet.fly()
    }
    }
    ```

- 自定义类型保护

    类型谓词
    ```ts
    function isFish(pet: Pet): pet is Fish {
        return (<Fish>pet).swim !== undefined;
    }
    function petShow(pet: Pet) {
    if (isFish(pet)) {
        pet.swim()
    } else {
        pet.fly()
    }
    }
    ```

- typeof
    ```ts
    function padLeft(value: string, padding: string | number) {
        if (typeof padding === "number") {
            return Array(padding + 1).join(" ") + value;
        }
        if (typeof padding === "string") {
            return padding + value;
        }
        throw new Error(`Expected string or number, got '${padding}'.`);
    }
    ```
    这些* typeof类型保护*只有两种形式能被识别： typeof v === "typename"和 typeof v !== "typename"， "typename"必须是 "number"， "string"， "boolean"或 "symbol"。 但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。

- instanceof
    ```ts
    interface Padder {
        getPaddingString(): string
    }

    class SpaceRepeatingPadder implements Padder {
        constructor(private numSpaces: number) { }
        getPaddingString() {
            return Array(this.numSpaces + 1).join(" ");
        }
    }

    class StringPadder implements Padder {
        constructor(private value: string) { }
        getPaddingString() {
            return this.value;
        }
    }

    function getRandomPadder() {
        return Math.random() < 0.5 ?
            new SpaceRepeatingPadder(4) :
            new StringPadder("  ");
    }

    // 类型为SpaceRepeatingPadder | StringPadder
    let padder: Padder = getRandomPadder();

    if (padder instanceof SpaceRepeatingPadder) {
        padder; // 类型细化为'SpaceRepeatingPadder'
    }
    if (padder instanceof StringPadder) {
        padder; // 类型细化为'StringPadder'
    }
    ```

## 类型别名
给类型起个新名字
```ts
type Name = string
type NameResolver = () => string
type NameOrResolver = Name | NameResolver
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n
    }
    else {
        return n()
    }
}
```

## 索引类型
目的：编译器就能够检查使用了动态属性名的代码

先简单了解一下，泛型会详解

例如，一个常见的JavaScript模式是从对象中选取属性的子集。
```js
function pluck(o, names) {
    return names.map(n => o[n]);
}
```
下面是如何在TypeScript里使用此函数，通过 **索引类型查询**和 **索引访问操作符**：
```ts
function pluck<T, K extends keyof T>(o: T, names: K[]): T[K][] {
  return names.map(n => o[n]);
}

interface Person {
    name: string;
    age: number;
}
let person: Person = {
    name: 'Jarid',
    age: 35
};
let strings: string[] = pluck(person, ['name']); // ok, string[]
```

编译器会检查 name是否真的是 Person的一个属性。 本例还引入了几个新的类型操作符。 首先是 **keyof T**， **索引类型查询操作符**。 对于任何类型 T， keyof T的结果为 T上已知的公共属性名的联合。 例如：
```ts
let personProps: keyof Person; // 'name' | 'age'
```
keyof Person是完全可以与 'name' | 'age'互相替换的。 不同的是如果你添加了其它的属性到 Person，例如 address: string，那么 keyof Person会自动变为 'name' | 'age' | 'address'。 你可以在像 pluck函数这类上下文里使用 keyof，因为在使用之前你并不清楚可能出现的属性名。


第二个操作符是 T[K]， **索引访问操作符**。 在这里，类型语法反映了表达式语法。 这意味着 person['name']具有类型 Person['name'] — 在我们的例子里则为 string类型。 然而，就像索引类型查询一样，你可以在普通的上下文里使用 T[K]，这正是它的强大所在。 你只要确保类型变量 K extends keyof T就可以了。 例如下面 getProperty函数的例子：
```ts
function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
    return o[name]; // o[name] is of type T[K]
}
```

getProperty里的 o: T和 name: K，意味着 o[name]: T[K]。 当你返回 T[K]的结果，编译器会实例化键的真实类型，因此 getProperty的返回值类型会随着你需要的属性改变。
```ts
let name: string = getProperty(person, 'name');
let age: number = getProperty(person, 'age');
let unknown = getProperty(person, 'unknown'); // error, 'unknown' is not in 'name' | 'age'
```

### 索引类型和字符串索引签名
keyof和 T[K]与字符串索引签名进行交互。 如果你有一个带有字符串索引签名的类型，那么 keyof T会是 string。 并且 T[string]为索引签名的类型：
```ts
interface Map<T> {
    [key: string]: T;
}
let keys: keyof Map<number>; // string
let value: Map<number>['foo']; // number
```

### 映射类型
一个常见的任务是将一个已知的类型每个属性都变为可选的：

```ts
interface PersonPartial {
    name?: string;
    age?: number;
}
```
这在JavaScript里经常出现，TypeScript提供了从旧类型中创建新类型的一种方式 — 映射类型。 在映射类型里，新类型以相同的形式去转换旧类型里每个属性。 
```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
}

type PersonPartial = Partial<Person>;
```

下面的例子辅助理解一下 in 语法:
```ts
type Keys = 'option1' | 'option2';
type Flags = { [K in Keys]: boolean };
```
它的语法与索引签名的语法类型，内部使用了 for .. in。 具有三个部分：

- 类型变量 K，它会依次绑定到每个属性。
- 字符串字面量联合的 Keys，它包含了要迭代的属性名的集合。
- 属性的结果类型。
在这个简单的例子里， Keys是硬编码的的属性名列表并且属性类型永远是 boolean，因此这个映射类型等同于
```ts
type Flags = {
    option1: boolean;
    option2: boolean;
}
```

TypeScrpt内置了很多映射类型，后续泛型介绍
