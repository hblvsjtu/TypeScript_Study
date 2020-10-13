# TypeScript_Study
TypeScript学习

- [TypeScript_Study](#typescript_study)
  - [参考文献](#参考文献)
  - [一. 基础](#一-基础)
    - [1. 原始数据类型](#1-原始数据类型)
      - [``boolean``](#boolean)
      - [``number``](#number)
      - [``string``](#string)
      - [``void``](#void)
      - [``undefined``和``null``](#undefined和null)
    - [2. 类型推论](#2-类型推论)
    - [3. 联合类型](#3-联合类型)
    - [4. 接口——对象的类型](#4-接口对象的类型)
    - [5. 数组类型](#5-数组类型)
    - [6. 函数的类型](#6-函数的类型)
    - [7. 类型断言](#7-类型断言)
  - [二. 进阶](#二-进阶)
    - [1. 类型别名](#1-类型别名)
    - [2. 元组](#2-元组)
    - [3. 枚举](#3-枚举)
    - [4. 类](#4-类)
    - [5. 泛型](#5-泛型)
    - [6. 声明合并](#6-声明合并)
    - [7. 声明文件](#7-声明文件)


## 参考文献
> - [TypeScript 入门教程](https://ts.xcatliu.com/)
> - [TypeScript 官网](http://www.typescriptlang.org/)


## 一. 基础
### 1. 原始数据类型
#### ``boolean``
> - ``new Boolean`` 返回的是一个 ``Boolean`` 对象，所以会判断错误
> - 字面量或者``Boolean(1)``可以
#### ``number``
> - 二进制（0b）和八进制（0o）会被编译成十进制，十六进制却不会
#### ``string``
#### ``void``
> - 表示没有任何返回值的函数, 一般只用于函数返回值
#### ``undefined``和``null``
> - ``undefined`` 和 ``null`` 是所有类型的子类型，可以赋值给其他的类型，但是``void``不可以
### 2. 类型推论
> - ``TypeScript`` 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。
> - 变量定义时有赋值但没有指定类型，就会触发类型推论
> - 如果定义的时没有赋值也没有指定类型，不管之后有没有赋值，都会被推断成 ``any`` 类型而完全不被类型检查
### 3. 联合类型
> - 表示取值可以为多种类型中的一种, 使用 | 分隔每个类型, 例如：``let myFavoriteNumber: string | number``
### 4. 接口——对象的类型
> - 普通属性 利用接口声明变量时属性不能多也不能少也不能取其他的属性，必须一模一样
> - 可选属性 如果诉求是不完全匹配属性，可以用可选属性，表示该属性可以不选
> - 任意属性 一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
> - 只读属性 使用 ``readonly`` 标记，相当于``const``, 注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候
```ts
        interface Person {
            readonly id: number; // 只读属性
            name: string; // 普通属性
            age?: number; // 可选属性
            [propName: string]: any; // 任意属性
        }

        let tom: Person = {
            id: 9527,
            name: 'Tom',
            gender: 'male'
        };
```
### 5. 数组类型
> - 「类型 + 方括号」表示法 ``let ids: number[] = [1, 2, 3];``
> - 数组泛型 ``let ids:  Array<number> = [1, 2, 3];``
> - 用接口表示数组
```ts
        interface NumberArray {
            [index: number]: number;
        }
```
> - 类数组 类数组（Array-like Object）不是数组类型，需要用接口来描述, 事实上常用的类数组都有自己的接口定义，如 IArguments, NodeList, HTMLCollection
```ts
        function sum() {
            let args: {
                [index: number]: any;
                length: number;
                callee: Function;
            } = arguments;
        }
```
### 6. 函数的类型
> - 普通形式 ``(x: number, y: number) => number``, 注意不要混淆了 TypeScript 中的 => 和 ES6 中的 =>, 在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型
> - 接口形式 ``(source: string, subString: string): boolean;``
> - 可选参数 可选参数必须接在必需参数后面 ``(firstName: string, lastName?: string)``
> - 参数默认值 TypeScript 会将添加了默认值的参数识别为可选参数, 此时就不受「可选参数必须接在必需参数后面」的限制 ``(firstName: string = 'Tom', lastName: string)``
> - 剩余参数 可以用数组的类型来定义 rest 参数只能是最后一个参数 ``(array: any[], ...items: any[])``
> - 重载 可以选择联合类型输入输出不同的参数类型，但是不够精确。比较好的做法是多次定义函数，最后一次实现函数
```ts
        // 不够精确
        function reverse(x: number | string): number | string {
            if (typeof x === 'number') {
                return Number(x.toString().split('').reverse().join(''));
            } else if (typeof x === 'string') {
                return x.split('').reverse().join('');
            }
        }

        // 精确从上到下进行匹配
        function reverse(x: number): number;
        function reverse(x: string): string;
        function reverse(x: number | string): number | string {
            if (typeof x === 'number') {
                return Number(x.toString().split('').reverse().join(''));
            } else if (typeof x === 'string') {
                return x.split('').reverse().join('');
            }
        }
```
### 7. 类型断言
> - 语法 ``值 as 类型`` 或者 `` <类型>值``
> - 用途
>> - 将一个联合类型断言为其中一个类型
>> - 将一个父类断言为更加具体的子类, 比如在window上添加属性 ``(window as any).foo = 1;``
>> - 将任何一个类型断言为 any
>> - 将 any 断言为一个具体的类型 从而改善代码

## 二. 进阶
### 1. 类型别名
> - type定义类型 比如，``type NameOrResolver = Name | NameResolver;``
> - 字符串字面量类型 约束取值只能是某几个字符串中的一个 ``type EventNames = 'click' | 'scroll' | 'mousemove';``
### 2. 元组
> - 数组合并了相同类型的变量，而元组（Tuple）合并了不同类型的变量
> - 初始化赋值的时候需要提供所有的项
> - 越界的元素 被指定为元组中每个类型的联合类型
```ts
        let tom: [string, number];
        tom = ['Tom', 25];
```
### 3. 枚举
> - enum ``enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};``
> - 默认赋值0，1，2，3，4...
> - 手动赋值 ``enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};``
### 4. 类
> - TypeScript 可以使用三种访问修饰符，分别是 ``public``、``private`` 和 ``protected``。
> - 需要注意的是，TypeScript 编译之后的代码中，并没有限制 private 属性在外部的可访问性
> - 注意如果 readonly 和其他访问修饰符同时存在的话，需要写在其后面。
> - 抽象类 abstract 抽象类是不允许被实例化而且方法必须被子类实现
> - 类的类型
```ts
        class Animal {
            name: string;
            constructor(name: string) {
                this.name = name;
            }
            sayHi(): string {
                return `My name is ${this.name}`;
            }
        }

        let a: Animal = new Animal('Jack');
        console.log(a.sayHi()); // My name is Jack
```
### 5. 泛型
### 6. 声明合并
> - 如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型
> - 函数的重载
> - 接口的合并，需要注意的是合并的属性的类型必须是唯一的，重复的属性进行合并且类型不一致会报错
```ts
        interface Alarm {
            price: number;
            alert(s: string): string;
        }
        interface Alarm {
            weight: number;
            alert(s: string, n: number): string;
        }

        // 相当于
        interface Alarm {
            price: number;
            weight: number;
            alert(s: string): string;
            alert(s: string, n: number): string;
        }
```
> - 类的合并
### 7. 声明文件
> - 声明文件格式 ``.d.ts``, ``tsconfig.json`` 中的 ``files``、``include`` 和 ``exclude`` 配置，确保其包含了 ``.d.ts`` 文件。
> - 书写声明文件 
```ts
        declare var 声明全局变量
        declare function 声明全局方法
        declare class 声明全局类
        declare enum 声明全局枚举类型
        declare namespace 声明（含有子属性的）全局对象
        interface 和 type 声明全局类型
        export 导出变量
        export namespace 导出（含有子属性的）对象
        export default ES6 默认导出
        export = commonjs 导出模块
        export as namespace UMD 库声明全局变量
        declare global 扩展全局变量
        declare module 扩展模块
        /// <reference /> 三斜线指令
```
> - ``namespace`` 随着 ES6 的广泛应用，现在已经不建议再使用 ts 中的 ``namespace``，而推荐使用 ES6 的模块化方案了，故我们不再需要学习 ``namespace`` 的使用了。``namespace`` 被淘汰了，但是在声明文件中，``declare namespace`` 还是比较常用的，它用来表示全局变量是一个对象，包含很多子属性。
```ts
        // src/jQuery.d.ts

        declare namespace jQuery {
            function ajax(url: string, settings?: any): void;
            const version: number;
            class Event {
                blur(eventType: EventType): void
            }
            enum EventType {
                CustomClick
            }
        }
```
> - 除了全局变量之外，可能有一些类型我们也希望能暴露出来。在类型声明文件中，我们可以直接使用 ``interface`` 或 ``type`` 来声明一个全局的接口或类型：
```ts
        // src/jQuery.d.ts

        interface AjaxSettings {
            method?: 'GET' | 'POST'
            data?: any;
        }
        declare namespace jQuery {
            function ajax(url: string, settings?: AjaxSettings): void;
        }
```
> - 第三方依赖声明文件：
>> - ``package.json`` 中有 ``types`` 字段，或者有一个 index.d.ts 声明文件。这种模式不需要额外安装其他包，是最为推荐的，所以以后我们自己创建 npm 包的时候，最好也将声明文件与 npm 包绑定在一起
>> - 发布到 @types 里。我们只需要尝试安装一下对应的 @types 包就知道是否存在该声明文件
>> - 创建一个 node_modules/@types/foo/index.d.ts 文件，存放 foo 模块的声明文件。这种方式不需要额外的配置，但是 node_modules 目录不稳定，代码也没有被保存到仓库中，无法回溯版本，有不小心被删除的风险，故不太建议用这种方案，一般只用作临时测试。
>> - 创建一个 types 目录，专门用来管理自己写的声明文件，将 foo 的声明文件放到 types/foo/index.d.ts 中。这种方式需要配置下 tsconfig.json 中的 paths 和 baseUrl 字段。
```ts

        /path/to/project
        ├── src
        |  └── index.ts
        ├── types
        |  └── foo
        |     └── index.d.ts
        └── tsconfig.json

        // tsconfig.json
        {
            "compilerOptions": {
                "module": "commonjs",
                "baseUrl": "./",
                "paths": {
                    "*": ["types/*"]
                }
            }
        }
```
> - 自动生成声明文件 命令行中添加 --declaration（简写 -d），或者在 tsconfig.json 中添加 declaration 选项
```ts
        // 添加了 outDir 选项，将 ts 文件的编译结果输出到 lib 目录下，然后添加了 declaration 选项，设置为 true，表示将会由 ts 文件自动生成 .d.ts 声明文件，也会输出到 lib 目录下
        {
            "compilerOptions": {
                "module": "commonjs",
                "outDir": "lib",
                "declaration": true,
            }
        }
```
> - 模块插件``declare module`` 有时通过 import 导入一个模块插件，可以改变另一个原有模块的结构。此时如果原有模块已经有了类型声明文件，而插件模块没有类型声明文件，就会导致类型不完整，缺少插件部分的类型。ts 提供了一个语法 declare module，它可以用来扩展原有模块的类型。
```ts
        // types/moment-plugin/index.d.ts

        import * as moment from 'moment';

        declare module 'moment' {
            export function foo(): moment.CalendarKey;
        }
```
> - 三斜线指令 与 namespace 类似，三斜线指令也是 ts 在早期版本中为了描述模块之间的依赖关系而创造的语法。随着 ES6 的广泛应用，现在已经不建议再使用 ts 中的三斜线指令来声明模块之间的依赖关系了。
