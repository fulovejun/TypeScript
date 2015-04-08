# TypeScript手册

[TypeScript Handbook](http://www.typescriptlang.org/Handbook)

## 基本类型

为了写出有用的程序, 我们需要有能力去处理简单的数据单位: 数字, 字符串, 结构, 布尔值等. 在TypeScript里, 包含了与JavaScript中几乎相同的数据类型, 此外还有便于我们操作的枚举类型.

### Boolean

最基本的数据类型就是true/false值, 在JavaScript和TypeScript里叫做布尔值.

```typescript
var isDone: boolean = false;
```

### Number

与JavaScript一样, 所有的数字在TypeScript里都是浮点数. 它们的类型是'number'.

```typescript
var height: number = 6;
```

### String

像其它语言里一样, 我们使用'string'表示文本数据类型. 与JavaScript里相同, 可以使用双引号(")或单引号(')表示字符串.

```typescript
var name: string = "bob";
name = 'smith';
```

### Array

TypeScript像JavaScript一样, 允许你操作数组数据. 可以用两种方式定义数组. 第一种, 可以在元素类型后面接'[]', 表示由此此类元素构成的一个数组:

```typescript
var list:number[] = [1, 2 ,3];
```

第二种方式是使用数组泛型, Array<元素类型>:

```typescript
var list:Array<number> = [1, 2, 3];
```

### Enum

'enum'类型是对标准JavaScript数据类型的一个补充. 像其它语言, 如C#, 使用枚举类型可以为一组数值赋予友好的名字.

```typescript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

默认情况下, 从0开始为元素编号. 你也可以手动的指定成员的数值. 例如, 我们将上面的例子改成从1开始编号:

```typescript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

或者, 全部都采用手动赋值:

```typescript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

枚举类型一个便利的特点是, 你可以从枚举的值取出它的名字. 例如, 我们知道数值2, 但是不确认它映射到Color里的哪个名字, 我们可以查找相应的名字:

```typescript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

### Any

有时, 我们可能会为暂时还不清楚的变量指定类型. 这些值可能来自于动态的内容, 比如第三方程序库. 这种情况下, 我们不希望类型检查器对这些值进行检查或者说让它们直接通过编译阶段的检查. 这时, 我们可以使用'any'类型来标记这些变量:

```typescript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

在对现有代码进行改写的时候, 'any'类型是十分有用的, 它允许你在编译时可选择地包含或移除类型检查.

当你仅仅知道一部分数据的类型时, 'any'类型也是很有用的. 比如, 你有一个数组, 它包含了不同的数据类型:

```typescript
var list:any[] = [1, true, "free"];

list[1] = 100;
```

### Void

某种程度上来说, 'void'类型与'any'类型是相反的, 表示没有任何类型. 当一个函数不返回任何值是, 你通常会见到其返回值类型是'void':

```typescript
function warnUser(): void {
    alert("This is my warning message");
}
```

## 接口

TypeScript的核心原则之一是对值所具有的'shape'进行类型检查. 它有时被称做鸭子类型或结构性子类型. 在TypeScript里, 接口负责为这样的类型命名, 它可以为你的代码或第三方代码定义契约.

### 第一个接口例子

下面来看一个很简单的例子:

```typescript
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

类型检查器查看'printLabel'的调用. 'printLabel'有一个参数, 这个参数应该是一个对象, 并包含一个名字是'label'类型为'string'的属性. 需要注意的是, 我们传入的对象可能有很多个属性, 但是编译器要求至少'label'属性是存在的, 并且其类型是'string':

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

LabelledValue接口可以用来描述上面例子里的契约: 应该具有一个类型是'string'的名字是'label'的属性. 需要注意的是, 在这里我们并不能像在其它语言里一样, 说labelledObj实现了LabelledValue接口. 我们关注的只是值的'shape'. 只要传入的对象满足上面提到的必须条件, 那么就通过检查.

### 可选属性

接口里的属性并不是全部是必须的. 有些是在某些条件下存在, 而有的则根本不需要. 这在使用'option bags'模式时很有用, 即给函数传入的对象中仅存在一部分属性.

下面是应用了'option bags'的例子:

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

带有可选属性的接口与普通的接口定义差不多, 只是在可选属性名字定义的后面加一个'?'符号.

可选属性的好处之一是可以对可能存在的属性进行预定义, 好处之二是可以捕获出现了不存在的属性时的错误. 比如, 我们故意错误的拼写color(collor), 传入'createSquare'的属性名, 我们会得到一个错误提示.

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```
