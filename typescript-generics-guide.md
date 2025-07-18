# TypeScript 泛型类型完全指南

> **文档信息**  
> 创建时间: 2025-07-18 07:55:29 UTC  
> 作者: zwd609258057  
> 更新时间: 2025-07-18

## 📋 目录

- [什么是泛型类型](#什么是泛型类型)
- [基础泛型语法](#基础泛型语法)
- [泛型函数](#泛型函数)
- [泛型接口](#泛型接口)
- [泛型类](#泛型类)
- [泛型约束](#泛型约束)
- [条件类型](#条件类型)
- [映射类型](#映射类型)
- [实际应用场景](#实际应用场景)
- [最佳实践](#最佳实践)

## 🎯 什么是泛型类型

### 基本概念

**泛型**就是**类型的参数化**，可以理解为"类型的变量"或"类型的模板"。它允许我们在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型。

### 生活类比

想象泛型就像一个**万能模具**：

```typescript
// 就像制作不同口味的饼干
// T 就是"口味"这个参数
function makeCookie<T>(flavor: T): T {
    return flavor;
}

const chocolateCookie = makeCookie<"巧克力">("巧克力");
const strawberryCookie = makeCookie<"草莓">("草莓");
const numberCookie = makeCookie<number>(123); // 甚至可以是数字！
```

### 为什么需要泛型？

```typescript
// 不使用泛型 - 每种类型都要写一遍
function getStringValue(value: string): string {
    return value;
}

function getNumberValue(value: number): number {
    return value;
}

function getBooleanValue(value: boolean): boolean {
    return value;
}

// 使用泛型 - 一个函数处理所有类型
function getValue<T>(value: T): T {
    return value;
}
```

## 🔧 基础泛型语法

### 泛型参数命名约定

```typescript
// 常用的泛型参数名
T    // Type - 最常用的泛型参数
K    // Key - 通常用于对象键类型
V    // Value - 通常用于值类型
E    // Element - 通常用于数组元素类型
U    // 第二个类型参数
R    // Return - 返回值类型

// 示例
function example<T, K, V>(type: T, key: K, value: V): void {
    // 实现逻辑
}
```

### 泛型的声明位置

```typescript
// 函数泛型
function func<T>(param: T): T { return param; }

// 接口泛型
interface Container<T> { value: T; }

// 类泛型
class Storage<T> { private data: T; }

// 类型别名泛型
type Wrapper<T> = { data: T; };

// 箭头函数泛型
const arrowFunc = <T>(param: T): T => param;
```

## 🚀 泛型函数

### 基础泛型函数

```typescript
// 基础泛型函数
function identity<T>(arg: T): T {
    return arg;
}

// 使用方式1：显式指定类型
const result1 = identity<string>("hello");    // result1: string
const result2 = identity<number>(42);         // result2: number

// 使用方式2：类型推断（推荐）
const result3 = identity("hello");           // 自动推断为 string
const result4 = identity(42);                // 自动推断为 number
```

### 多个泛型参数

```typescript
// 多个泛型参数
function pair<T, U>(first: T, second: U): [T, U] {
    return [first, second];
}

const nameAge = pair("张三", 25);            // [string, number]
const coords = pair(10, 20);                 // [number, number]

// 交换两个值
function swap<T>(a: T, b: T): [T, T] {
    return [b, a];
}

const swapped = swap(1, 2);                  // [2, 1]
const swappedStr = swap("hello", "world");   // ["world", "hello"]
```

### 泛型函数重载

```typescript
// 泛型函数重载
function process<T>(value: T): T;
function process<T>(value: T[]): T[];
function process<T>(value: T | T[]): T | T[] {
    if (Array.isArray(value)) {
        return value.map(item => item);
    }
    return value;
}

const singleResult = process("hello");       // string
const arrayResult = process(["a", "b"]);     // string[]
```

## 🏗️ 泛型接口

### 基础泛型接口

```typescript
// 泛型接口定义
interface Container<T> {
    value: T;
    getValue(): T;
    setValue(value: T): void;
}

// 实现泛型接口
class StringContainer implements Container<string> {
    constructor(public value: string) {}
    
    getValue(): string {
        return this.value;
    }
    
    setValue(value: string): void {
        this.value = value;
    }
}

class NumberContainer implements Container<number> {
    constructor(public value: number) {}
    
    getValue(): number {
        return this.value;
    }
    
    setValue(value: number): void {
        this.value = value;
    }
}
```

### 复杂泛型接口

```typescript
// API 响应接口
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
    timestamp: Date;
    meta?: {
        total?: number;
        page?: number;
        limit?: number;
    };
}

// 分页接口
interface Paginated<T> {
    items: T[];
    pagination: {
        current: number;
        total: number;
        pageSize: number;
        hasNext: boolean;
        hasPrev: boolean;
    };
}

// 仓储模式接口
interface Repository<T, ID = string> {
    findById(id: ID): Promise<T | null>;
    findAll(): Promise<T[]>;
    save(entity: T): Promise<T>;
    update(id: ID, entity: Partial<T>): Promise<T>;
    delete(id: ID): Promise<void>;
}
```

### 函数类型的泛型接口

```typescript
// 事件处理器接口
interface EventHandler<T> {
    (event: T): void;
}

// 比较器接口
interface Comparator<T> {
    (a: T, b: T): number;
}

// 转换器接口
interface Transformer<T, U> {
    (input: T): U;
}

// 使用示例
const stringComparator: Comparator<string> = (a, b) => a.localeCompare(b);
const numberTransformer: Transformer<string, number> = str => parseInt(str);
```

## 🏛️ 泛型类

### 基础泛型类

```typescript
// 泛型类定义
class DataStore<T> {
    private data: T[] = [];
    
    add(item: T): void {
        this.data.push(item);
    }
    
    get(index: number): T | undefined {
        return this.data[index];
    }
    
    getAll(): T[] {
        return [...this.data];
    }
    
    find(predicate: (item: T) => boolean): T | undefined {
        return this.data.find(predicate);
    }
    
    filter(predicate: (item: T) => boolean): T[] {
        return this.data.filter(predicate);
    }
    
    map<U>(mapper: (item: T) => U): U[] {
        return this.data.map(mapper);
    }
    
    get length(): number {
        return this.data.length;
    }
}

// 使用泛型类
const stringStore = new DataStore<string>();
stringStore.add("apple");
stringStore.add("banana");
console.log(stringStore.getAll()); // ["apple", "banana"]

const userStore = new DataStore<{id: number, name: string}>();
userStore.add({id: 1, name: "张三"});
userStore.add({id: 2, name: "李四"});
console.log(userStore.find(user => user.id === 1)); // {id: 1, name: "张三"}
```

### 继承中的泛型

```typescript
// 基础泛型类
abstract class BaseRepository<T, ID = string> {
    protected abstract tableName: string;
    
    abstract findById(id: ID): Promise<T | null>;
    abstract save(entity: T): Promise<T>;
    
    protected log(message: string): void {
        console.log(`[${this.tableName}] ${message}`);
    }
}

// 继承泛型类
class UserRepository extends BaseRepository<User, number> {
    protected tableName = "users";
    
    async findById(id: number): Promise<User | null> {
        this.log(`Finding user with id: ${id}`);
        // 实现查找逻辑
        return null;
    }
    
    async save(user: User): Promise<User> {
        this.log(`Saving user: ${user.name}`);
        // 实现保存逻辑
        return user;
    }
    
    async findByEmail(email: string): Promise<User | null> {
        this.log(`Finding user with email: ${email}`);
        // 特定于用户的方法
        return null;
    }
}

interface User {
    id: number;
    name: string;
    email: string;
}
```

## 🔒 泛型约束

### 基础约束

```typescript
// 约束泛型必须有某些属性
interface Lengthwise {
    length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
    console.log(`Length: ${arg.length}`); // 现在可以安全访问 length 属性
    return arg;
}

logLength("hello");        // ✅ string 有 length 属性
logLength([1, 2, 3]);      // ✅ array 有 length 属性
logLength({length: 10});   // ✅ 对象有 length 属性
// logLength(123);         // ❌ number 没有 length 属性
```

### keyof 约束

```typescript
// 约束泛型是对象的键
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const person = {name: "张三", age: 25, city: "北京"};
const name = getProperty(person, "name");     // string
const age = getProperty(person, "age");       // number
// const invalid = getProperty(person, "salary"); // ❌ 'salary' 不存在于 person 类型中
```

### 多重约束

```typescript
// 多重约束
interface Serializable {
    serialize(): string;
}

interface Timestamped {
    timestamp: Date;
}

function processData<T extends Serializable & Timestamped>(data: T): string {
    console.log(`Processing data from: ${data.timestamp}`);
    return data.serialize();
}

// 必须同时实现 Serializable 和 Timestamped
class LogEntry implements Serializable, Timestamped {
    constructor(
        public message: string,
        public timestamp: Date = new Date()
    ) {}
    
    serialize(): string {
        return JSON.stringify({
            message: this.message,
            timestamp: this.timestamp.toISOString()
        });
    }
}

const entry = new LogEntry("System started");
const serialized = processData(entry);
```

### 条件约束

```typescript
// 条件约束
type NonFunctionPropertyNames<T> = {
    [K in keyof T]: T[K] extends Function ? never : K;
}[keyof T];

type NonFunctionProperties<T> = Pick<T, NonFunctionPropertyNames<T>>;

interface Example {
    name: string;
    age: number;
    greet(): void;
    calculate(x: number): number;
}

type ExampleData = NonFunctionProperties<Example>;
// 结果: { name: string; age: number; }
```

## 🔄 条件类型

### 基础条件类型

```typescript
// 条件类型语法: T extends U ? X : Y
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>;   // true
type Test2 = IsString<number>;   // false
type Test3 = IsString<"hello">;  // true

// 实用的条件类型
type NonNullable<T> = T extends null | undefined ? never : T;

type A = NonNullable<string | null>;      // string
type B = NonNullable<number | undefined>; // number
type C = NonNullable<boolean | null | undefined>; // boolean
```

### infer 关键字

```typescript
// 使用 infer 推断类型
type ReturnType<T extends (...args: any) => any> = 
    T extends (...args: any) => infer R ? R : any;

function getString(): string { return "hello"; }
function getNumber(): number { return 42; }
function getUser(): {id: number, name: string} { return {id: 1, name: "张三"}; }

type StringReturn = ReturnType<typeof getString>; // string
type NumberReturn = ReturnType<typeof getNumber>; // number
type UserReturn = ReturnType<typeof getUser>;     // {id: number, name: string}

// 提取数组元素类型
type ArrayElement<T> = T extends (infer U)[] ? U : never;

type StringElement = ArrayElement<string[]>;     // string
type NumberElement = ArrayElement<number[]>;     // number
type ObjectElement = ArrayElement<{id: number}[]>; // {id: number}

// 提取 Promise 的值类型
type PromiseValue<T> = T extends Promise<infer U> ? U : never;

type AsyncStringValue = PromiseValue<Promise<string>>; // string
type AsyncUserValue = PromiseValue<Promise<User>>;     // User
```

### 分布式条件类型

```typescript
// 分布式条件类型
type ToArray<T> = T extends any ? T[] : never;

type StringOrNumberArray = ToArray<string | number>;
// 结果: string[] | number[] (不是 (string | number)[])

// 排除特定类型
type Exclude<T, U> = T extends U ? never : T;

type WithoutString = Exclude<string | number | boolean, string>;
// 结果: number | boolean

// 提取特定类型
type Extract<T, U> = T extends U ? T : never;

type OnlyString = Extract<string | number | boolean, string>;
// 结果: string
```

## 🗺️ 映射类型

### 基础映射类型

```typescript
// 基础映射类型语法
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

type Partial<T> = {
    [P in keyof T]?: T[P];
};

type Required<T> = {
    [P in keyof T]-?: T[P];
};

// 使用示例
interface User {
    id: number;
    name: string;
    email?: string;
}

type ReadonlyUser = Readonly<User>;
// 结果: { readonly id: number; readonly name: string; readonly email?: string; }

type PartialUser = Partial<User>;
// 结果: { id?: number; name?: string; email?: string; }

type RequiredUser = Required<User>;
// 结果: { id: number; name: string; email: string; }
```

### 高级映射类型

```typescript
// 键名转换
type Getters<T> = {
    [K in keyof T as `get${Capitalize<K & string>}`]: () => T[K];
};

type Setters<T> = {
    [K in keyof T as `set${Capitalize<K & string>}`]: (value: T[K]) => void;
};

interface Person {
    name: string;
    age: number;
    email: string;
}

type PersonGetters = Getters<Person>;
// 结果: {
//   getName: () => string;
//   getAge: () => number;
//   getEmail: () => string;
// }

type PersonSetters
