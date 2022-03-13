# 简述

TypeScript 是 JavaScript 的超集，支持ES6标准

TypeScript 是一种静态类型语言，主要为了应用于大型项目开发中，支持静态类型检查，将类型检查从运行时提前到编译时，消除在代码中隐藏的类型安全问题。
确保对某一个类型只使用该类型支持的操作。

## 适用场景

## 基础类型

TypeScript 作为 JavaScript 的超集，沿用了 JavaScript 的基础类型、包装类型等，同时添加了一些额外的类型。

- 基础类型：number、boolean、string、symbol、undefined、null、bigInt、object

- 包装类型：Number、Boolean、String、Symbol、Object

- 其他类型：元组（Tuple）、接口（interface）、枚举（Enum）

- 特殊类型

  - void：代表空，可以是 null / undefined

  - any：任意类型，可以赋值给任意类型，任意类型也可以赋值给它

  - unknown：未知类型，不可以赋值给任意类型，但任意类型可以赋值给它

  - never：不可达，不可达类型，例如函数抛出异常时为 never 类型

## 复合类型

### 元组（Tuple）

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同

```typescript
  type Tuple = [string, number, boolean]
```

[X] 元组和数组的区别

### 接口（interface）

TypeScript 原则之一是为了对值所具有的的结构进行类型检查<font color="#aaa" >（鸭子辨型法、结构性子类型化）</font>，而接口可以用来描述 **函数**、**构造器**、**引用类型** 的结构。

### 枚举

枚举可以表示一系列带名字的常量，可以用于表达一组有区别的用例。

```typescript
  // 不完全初始化器：此时JavaScript值默认为0，TypeScript值默认为1，后续若还有枚举类型依次递增。
  enum Language {
    JavaScript,
    TypeScript,
  }

  // 若给JavaScript值初始化为1则TypeScript会从2开始递增
  // enum Language {
  //   JavaScript = 1,
  //   TypeScript,
  // }

  // 也可以初始化为自定义的值
  // enum Language {
  //   JavaScript = 'JavaScript',
  //   TypeScript = 'TypeScript',
  // }


  const userLanguage = Language.TypeScript
```