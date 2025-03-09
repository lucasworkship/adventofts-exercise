# adventofts-exercise
### [adventofts](https://www.adventofts.com/) 
刷一刷记录一下练习过程



# 1. [The Reindeer Have Had Enough](https://www.adventofts.com/events/2024/1)

目标: 我们需要定义一个类型 Demand，使得它的类型与 number 一致。然后通过 Expect<Equal<t0_actual, t0_expected>> 来验证 Demand 是否正确。

```ts
import type { Expect, Equal } from 'type-testing';

type t0_actual = Demand;    // =>

/** This line shows what the expected result of `Demand` should be */
type t0_expected = number; // => 

/**
 * We compare the two results here with `Expect` and `Equal`
 * 
 * These are special TypeScript types that will cause a type error if the two types are not equal.
 * 
 * If every `Expect`/`Equal` line passes, then it means you've completed today's challenge!
 */
type t0 = Expect<Equal<t0_actual, t0_expected>>;

```
>解决方案：直接将 Demand 定义为 number 类型即可。

```ts
type Demand = number;
```




# 2. [Blitzen Fixes The Mess !](https://www.baidu,com)

目标：
我们需要将 Demand 定义为 900_000，而不是 number，因此不能直接使用 number。
然后，我们需要通过 Expect<Equal<t0_actual, t0_expected>> 来确保 t0_actual 和 t0_expected 是完全匹配的，即 900_000 类型。

```ts

import type { Expect, Equal } from 'type-testing';

type t0_actual = Demand;        // =>
type t0_expected = 900_000; // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>; 

/*
 * Sometimes, throughout Advent of TypeScript, you'll see a test that asserts there _is_ a type error, often to ensure that you're providing a correct implementation.
 * In this example, passing `number` isn't good enough, so we're highlighting that to you by showing that would cause a type error if `number` is the value of `Demand`.
 */

// @ts-expect-error
type e0 = Expect<Equal<Demand, number>>;

```

> 解决方案：在这个任务中，900_000 是一个字面量类型，不是一个常规的 number 类型。所以我们应该将 Demand 定义为字面量类型 900_000

```ts

type Demand = 900_000;

```

# 3. [Santa Wants Housing Data](https://www.adventofts.com/events/2024/3)

目标：
我们需要确保 survivalRatio 函数只能接受特定的有效输入，例如年份（如 2009 至 2023）并且年份必须是reportForSanta 里面有的值。


```ts
export const reportForSanta = {
  2009: survivalRatio(2009),
  2010: survivalRatio(2010),
  2011: survivalRatio(2011),
  2012: survivalRatio(2012),
  2013: survivalRatio(2013),
  ...
}

// @ts-expect-error
survivalRatio('1');

// @ts-expect-error
survivalRatio(true);

// @ts-expect-error
survivalRatio([1]);

// @ts-expect-error
survivalRatio({ 1: 1 });


```

>解决方案: 创建survivalRatio 函数，接受一个参数，对参数进行类型约束，最后返回计算结果，根据原数据，创建一个AnnualData 类型，根据数据中的年份创建对应的数据结构。

```ts

const survivalRatio = (input: keyof typeof annualData):number => {
  const data = annualData[input];
  if (!data) {
    throw new Error("Data not found");
  }
  return data.housingIndex / data.minimumWage;
}

//  元数据
const annualData = {
  2009: {
    housingIndex: 159.50891,
    minimumWage: 92.85234
  },
  2010: {
    housingIndex: 143.02079,
    minimumWage: 100.50286
  }
} as const; //  使用 `as const` 确保年份和数值是字面量类型



//声明 AnnualData 类型，它应该根据 annualData 中的年份键来创建每一年的数据结构。
type AnnualData = {
	[key in keyof typeof annualData]: {
		/** inflation corrected housing price index */
		housingIndex: number;

		/** inflation corrected North Pole minimum wage */
		minimumWage: number;
	};
};
```



# 4.[🎩Bernard Presents The Housing Data](https://www.adventofts.com/events/2024/4)


目标: 
1. 通过测试用例。
2. 跟第三题类似，数据结构稍有变化，我们在判断 `survivalRatio` 函数的参数的时候需要做出对应的更改，使得函数正确运行与返回.



```ts

const start = survivalRatio(2009);
type t0_actual = typeof start; // =>
type t0_expected = number;   // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

const now = survivalRatio(2024);
type t1_actual = typeof now; // =>
type t1_expected = number; // =>
type t1 = Expect<Equal<t1_actual, t1_expected>>;

// We can pass strings like `'2009 Q2'`:

const q1_2009 = survivalRatio('2009 Q1');
type t2_actual = typeof q1_2009; // =>
type t2_expected = number;     // =>
type t2 = Expect<Equal<t2_actual, t2_expected>>;

const q2_2024 = survivalRatio('2024 Q2');
type t3_actual = typeof q2_2024; // =>
type t3_expected = number;     // =>
type t3 = Expect<Equal<t3_actual, t3_expected>>;

// Other data types are not allowed by TypeScript:

// @ts-expect-error
survivalRatio(true);

```


>解决方案：对输入参数进行数字或者字符串的匹配判断。

```ts
const survivalRatio = (input: keyof typeof quarterlyData | number): number => {
  // 如果是数字，构建季度字符串（默认假设 Q1）
  const quarter = typeof input === 'number' ? `${input} Q1` : input;
  
  // 从 quarterlyData 中查找对应的季度数据
  const data = quarterlyData[quarter as keyof typeof quarterlyData];
  
  // 如果找不到数据，抛出错误
  if (!data) {
    throw new Error("Data not found");
  }

  // 返回生存比率（housingIndex / minimumWage）
  return data.housingIndex / data.minimumWage;
}

```


# 5. [Drafting This Year's Routes](https://www.adventofts.com/events/2024/5)

目标：实现`createRoute`函数，使得每个调用 `createRoute` 都返回一个类型，该类型是传入的第二个参数的类型。

```ts
import type { Expect, Equal } from 'type-testing';

const oneMill = createRoute('💨Dasher', 100_000_000);
type t0_actual = typeof oneMill;  // =>
type t0_expected = 100_000_000; // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

const two = createRoute('💃Dancer', 2);
type t1_actual = typeof two; // =>
type t1_expected = 2;      // =>
type t1 = Expect<Equal<t1_actual, t1_expected>>;

const three = createRoute('🦌Prancer', 2);
type t2_actual = typeof three; // =>
type t2_expected = 2;        // =>
type t2 = Expect<Equal<t2_actual, t2_expected>>;

const four = createRoute('🌟Vixen', '1');
type t3_actual = typeof four; // =>
type t3_expected = '1';     // =>
type t3 = Expect<Equal<t3_actual, t3_expected>>;

const five = createRoute('☄️Comet', true);
type t4_actual = typeof five; // =>
type t4_expected = true;    // =>
type t4 = Expect<Equal<t4_actual, t4_expected>>;

const six = createRoute('❤️Cupid', [1]);
type t5_actual = typeof six;   // =>
type t5_expected = number[]; // =>
type t5 = Expect<Equal<t5_actual, t5_expected>>;

const seven = createRoute('🌩️Donner', { 1: 1 });
type t6_actual = typeof seven;      // =>
type t6_expected = { 1: number }; // =>
type t6 = Expect<Equal<t6_actual, t6_expected>>;

const eight = createRoute('⚡Blitzen', Symbol('🔴 === evil'));
type t7_actual = typeof eight; // =>
type t7_expected = symbol;   // =>
type t7 = Expect<Equal<t7_actual, t7_expected>>;

```

> 解决方案：我们可以通过泛型来实现 createRoute，使得它的返回类型能够正确地继承传入的第二个参数的类型。


```ts
const createRoute<T>(author:string,route:T):T=>{
     return route;
}
```




# 6. [🎅Santa Refuses To Use Threads](https://www.adventofts.com/events/2024/6)

目标：同上，在参数上排除`any[]|boolean|object` 三种类型


```ts


// @ts-expect-error
createRoute('🌟Vixen', true);

// @ts-expect-error
createRoute('💃Dancer', [1]);

// @ts-expect-error
createRoute('☄️Comet', { 1: 1 });
```

> 解决方案：使用 `Exclude` 或者 `extends` 实现类型进一步的约束


```ts

// 使用 Exclude 类型 来排除不允许的类型
type notallowed = any[]|boolean|object;

const createRoute = <Route>(author: string, route: Exclude<Route,notallowed>) => {
  console.log(`[createRoute] route created by ${author} at ${Date.now()}`);
  return route
}


// 使用 extends 指定类型

const createRoute = <Route extends number|string>(author:string,route:Route)=>{
  console.log(`[createRoute] route created by ${author} at ${Date.now()}`);
	return route;
}


```


# 7. [💋Crystal Panics](https://www.adventofts.com/events/2024/7)

目标：我们需要确保 `createRoute` 函数能够处理一个字符串作为第一个参数，并且将第二个参数（一个字符串数组）包装在 `route` 属性中。这个 `route` 属性的类型必须是传入的数组类型，因此返回的类型需要推断出第二个参数的类型。


```ts
const oneMill = createRoute('💨Dasher', ['Atherton', "Scarsdale", "Cherry Hills Village"]).route;
type t0_actual = typeof oneMill; // =>
type t0_expected = [           // =>
    'Atherton',
    "Scarsdale",
    "Cherry Hills Village"
];
type t0 = Expect<Equal<t0_actual, t0_expected>>;

const two = createRoute('🌟Vixen', ['Detroit', "Cleveland", "Dayton"]).route;
type t1_actual = typeof two; // =>
type t1_expected = [       // =>
    'Detroit',
    "Cleveland",
    "Dayton"
];
type t1 = Expect<Equal<t1_actual, t1_expected>>;


```

> 解决方案：`createRoute` 接受一个 name（字符串类型）和一个 route（字符串数组）。createRoute 返回一个对象，包含 route 属性，其类型即为传入的数组类型。


```ts


function createRoute<const T extends string[]>(name: string, route: T) {
	return {
		name,
		route,
	};
}
```


# 8.[A Night With Mrs. Claus](https://www.adventofts.com/events/2024/8)

目标： 在`process.env`中 需要有三个字面量分别是 `MOOD_LIGHTS`, `BATH_TEMPERATURE`, 和 `STRAWBERRIES`

```ts


const { MOOD_LIGHTS } = process.env;
type t0_actual = typeof MOOD_LIGHTS; // =>
type t0_expected = 'true';         // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

const { BATH_TEMPERATURE } = process.env;
type t1_actual = typeof BATH_TEMPERATURE; // =>
type t1_expected = '327.59';            // =>
type t1 = Expect<Equal<t1_actual, t1_expected>>;

const { STRAWBERRIES } = process.env;
type t2_actual = typeof STRAWBERRIES; // =>
type t2_expected = 'chocolate';     // =>
type t2 = Expect<Equal<t2_actual, t2_expected>>;
```


解决方案：使用环境变量类型定义，为 process.env 提供自定义的类型定义

```ts

declare global {
  namespace NodeJS {
    interface ProcessEnv {
      MOOD_LIGHTS: 'true';
      BATH_TEMPERATURE: '327.59';
      STRAWBERRIES: 'chocolate';
      // 
    }
  }
}
```


# 9.[🌟Vixen Confronts 🪩Jamie](https://www.adventofts.com/events/2024/9)

目标：定义三个模块，Status，Child 和 List。并且给定特定的类型

```ts

type t0_actual = Status;                 // =>
type t0_expected = 'naughty' | 'nice'; // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

type t1_actual = Child; // =>
type t1_expected = {  // =>
    name: string,
    status: Status
};
type t1 = Expect<Equal<t1_actual, t1_expected>>;

type t2_actual = List;        // =>
type t2_expected = Child[]; // =>
type t2 = Expect<Equal<t2_actual, t2_expected>>;

```

>解决方案：定义三种类型，并且可以导出

```ts
// 全局声明
declare module "santas-special-list"{
	export type Status = 'naughty' | 'nice';

	export type Child = {
		name:string,
		status:Status
	};

	export type List = Child[];
}


```

# 10.The Reindeer Hatch A Plan

目标：定义类型通过测试用例


```ts
const test = <F extends Gift>(flag: F) => flag;

test<Gift.Coal>(0);
test<Gift.Train>(1);
test<Gift.Bicycle>(2);
test<Gift.SuccessorToTheNintendoSwitch>(4);
test<Gift.TikTokPremium>(8);
test<Gift.Vape>(16);
test<Gift.Traditional>(3);
test<Gift.OnTheMove>(26);
test<Gift.OnTheCouch>(28);

// @ts-expect-error
test<Gift.Coal>(10);
// @ts-expect-error
test<Gift.Train>(11);
// @ts-expect-error
test<Gift.Bicycle>(12);
// @ts-expect-error
test<Gift.SuccessorToTheNintendoSwitch>(14);
// @ts-expect-error
test<Gift.TikTokPremium>(18);
// @ts-expect-error
test<Gift.Vape>(116);
// @ts-expect-error
test<Gift.Traditional>(13);
// @ts-expect-error
test<Gift.OnTheMove>(126);
// @ts-expect-error
test<Gift.OnTheCouch>(124);

```


>解决方案：在枚举类型里面定义对应的值

```ts
enum Gift {
	Coal=0b00000,
	Train =0b00001,
	Bicycle=0b00010,
	SuccessorToTheNintendoSwitch =0b00100,
	TikTokPremium =0b01000,
	Vape = 0b10000,
	Traditional =0b00001 | 0b00010,
	OnTheMove = 0b00000 | 0b00010 | 0b01000 | 0b10000,
	OnTheCouch = 0b11010 & ~0b00010 | 0b00100
};

```


# 11. [Excuses, Excuses](https://www.adventofts.com/events/2024/11)

目标： `Excuse` 类型的泛型约束来构建出根据不同数据生成不同字符串的类型。

```ts

const existingExcuses = {
  karaoke: ['Kendrick Lamar, Opeth'],
  margarita: 'Peppermint-Jalapeño Mojito',
}

const helpingTheReindeer = {
  helping: 'the reindeer',
} as const;
declare const Excuse0: Excuse<typeof helpingTheReindeer>;
const excuse0 = new Excuse0({
  ...existingExcuses,
  ...helpingTheReindeer,
});
type t0_actual = typeof excuse0;              // =>
type t0_expected = "helping: the reindeer"; // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

const eatingFudge = {
  eating: 'fudge',
} as const;
declare const Excuse1: Excuse<typeof eatingFudge>;
const excuse1 = new Excuse1({
  ...existingExcuses,
  ...eatingFudge,
});
type t1_actual = typeof excuse1;      // =>
type t1_expected = "eating: fudge"; // =>
type t1 = Expect<Equal<t1_actual, t1_expected>>;


```

>实现方案：构建出一个对应的字符串类型，

```ts
type Excuse<T> = new (excuse: T) => `${keyof T & string}: ${T[keyof T] & string}`;

```

# 12.[🎩Bernard's Long List Of Names](https://www.adventofts.com/events/2024/12)

目标： 创建`FormatNames`

```ts
type t0_actual = FormatNames<Names>['length']; // =>
type t0_expected = 31682;                    // =>
type t0 = Expect<Equal<t0_actual, t0_expected>>;

type t1_actual = FormatNames<Names>[0]; // =>
type t1_expected = {                  // =>
  name: 'Liam',
  count: 20802,
  rating: 'naughty' // even number of characters in the name get 'naughty'
};
type t1 = Expect<Equal<t1_actual, t1_expected>>;

type t2_actual = FormatNames<Names>[11194]; // =>
type t2_expected = {                      // =>
  name: 'Yanni',
  count: 19,
  rating: 'nice' // odd number of characters in the name get 'nice'
};
type t2 = Expect<Equal<t2_actual, t2_expected>>;

type t3_actual = FormatNames<Names>[2761]; // =>
type t3_expected = {                     // =>
  name: 'Petra',
  count: 148,
  rating: 'nice'
};
type t3 = Expect<Equal<t3_actual, t3_expected>>;

type t4_actual = FormatNames<Names>[31680]; // =>
type t4_expected = {                      // =>
  name: 'Aala',
  count: 5,
  rating: 'naughty'
};
type t4 = Expect<Equal<t4_actual, t4_expected>>;

type t5_actual = FormatNames<Names>[31681]; // =>
type t5_expected = {                      // =>
  name: 'Aagya',
  count: 5,
  rating: 'nice'
};
type t5 = Expect<Equal<t5_actual, t5_expected>>;

/**
 * Note that since this is exported, you can import it to play around with it a bit
 *
 * data sourced from https://www.ssa.gov/oact/babynames/names.zip
 */
export type Names = [
  ["Liam", "M", "20802"],
  ["Noah", "M", "18995"],
  ["Oliver", "M", "14741"],
  ["Emma", "F", "13527"],
  ["Charlotte", "F", "12596"],
  ["Amelia", "F", "12311"],
  ["Sophia", "F", "11944"],
  ["James", "M", "11670"],
  ["Elijah", "M", "11452"]]

```


>解决思路：通过 `FormatNames<Names>` 生成一个新类型，在其中包含名字、计数以及根据名字的字符数来决定 "naughty" 或 "nice" 评级,偶数个字符的名字被评级为 "naughty"，奇数个字符的名字被评级为 "nice"


```ts

type NaughtyOrNice<N> = N extends `${any}0` ? "naughty" : "nice";

// type NaughtyOrNice<N extends number> = `${N}` extends `${infer _Rest}${infer Last}` // 获取数字最后一位
//   ? Last extends "0" | "2" | "4" | "6" | "8" // 判断最后一位是否是偶数
//     ? "naughty" // 偶数
//     : "nice"     // 奇数
//   : never; // 如果不能解析，返回 never（应该不可能）

type ParseInt<T> = T extends `${infer N extends number}` ? N : never;

type FormatNames<T extends [string, string, string][]> = {
	[K in keyof T]: {
		name: T[K][0];
		count: ParseInt<T[K][2]>;
		rating: NaughtyOrNice<K>;
	};
};
```




# 13.[The Reindeer Plan Their Attack](https://www.adventofts.com/events/2024/13)

目标：
1. Demand<unknown> 类型的对象能够接受任何类型的 demand 值
2. Demand<string> 类型的对象只能接受 demand 为 string 类型的值
3. Demand<'Immediate 4% Pay Increase'> 类型的对象只能接受 demand 为特定字面量 'Immediate 4% Pay Increase' 的值
4. Demand<'3 Days Paid Time Off Per Year'> 类型的对象只能接受 demand 为字面量 '3 Days Paid Time Off Per Year' 的值


```
import type { Equal, Expect } from 'type-testing';

declare let demand1: Demand<unknown>;
declare let demand2: Demand<string>;
declare let demand3: Demand<'Immediate 4% Pay Increase'>;
declare let demand4: Demand<'3 Days Paid Time Off Per Year'>;

type t1 = Expect<Equal<typeof demand1, { demand: unknown }>>
demand1 = demand1; // ✅
// @ts-expect-error
demand1 = demand2;
// @ts-expect-error
demand1 = demand3;
// @ts-expect-error
demand1 = demand4;

type t2 = Expect<Equal<typeof demand2, { demand: string }>>
// @ts-expect-error
demand2 = demand1;
demand2 = demand2; // ✅
// @ts-expect-error
demand2 = demand3;
// @ts-expect-error
demand2 = demand4;

type t3 = Expect<Equal<typeof demand3, { demand: 'Immediate 4% Pay Increase' }>>
// @ts-expect-error
demand3 = demand1;
// @ts-expect-error
demand3 = demand2;
demand3 = demand3; // ✅
// @ts-expect-error
demand3 = demand4;

type t4 = Expect<Equal<typeof demand4, { demand: '3 Days Paid Time Off Per Year' }>>
// @ts-expect-error
demand4 = demand1;
// @ts-expect-error
demand4 = demand2;
// @ts-expect-error
demand4 = demand3;
demand4 = demand4; // ✅

```

> 引用 [satohshi's Solution](https://www.adventofts.com/events/2024/13/solutions/2974) 

知识点补充: 
#### 1.什么是协变（Covariance）？
如果 A 是 B 的子类型（A extends B）,那么 X<A> 也是 X<B> 的子类型，这样就可以称为
`X<T>` 关于 T 是协变的
一句话概括：子类型可以传递。

*Example1:数组是协变的*
```ts
class Animal {
  name!: string;
}
class Dog extends Animal {
  breed!: string;
}

let animals: Animal[] = [new Animal()];
let dogs: Dog[] = [new Dog()];

animals = dogs; // ✅ 成功，数组是协变的

```

```ts
type Demand<in out T> = {
	demand: T;
};

```



# 14.[🎅Santa Hides Behind Perf Review](https://www.adventofts.com/events/2024/14)

目标：两个异步生成器函数 `numberAsyncGenerator``stringAsyncGenerator` 分别生成对应的字符串，定义两个类型


```ts

import type { Expect, Equal } from 'type-testing';

async function* numberAsyncGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
type t0_type = typeof numberAsyncGenerator;
type t0_actual = PerfReview<ReturnType<t0_type>>;
type t0_expected = 1 | 2 | 3;
type t0 = Expect<Equal<t0_actual, t0_expected>>;

async function* stringAsyncGenerator() {
  yield '1%';
  yield '2%';
}
type t1_type = typeof stringAsyncGenerator;
type t1_actual = PerfReview<ReturnType<t1_type>>;
type t1_expected = "1%" | "2%";
type t1 = Expect<Equal<t1_actual, t1_expected>>;



```




> 解决思路: 在 TypeScript 中，一个 async function* 返回的是 `AsyncGenerator<T, TReturn, TNext>` 类型，其中：
>1. T 是 yield 出去的类型（我们要获取的部分）。
>2. TReturn 是 return 语句返回的类型（通常不是我们关注的）。
>3. TNext 是 next() 方法可以接受的参数类型（通常也不是我们关注的）。

根据以上，我们需要获取T 并且需要推到了出类型，那我们使用`infer`关键字
```
type YieldedType<T> = T extends AsyncGenerator<infer U, any, any> ? U : never;

```
以上代码可以理解成： <b>如果T是AsyncGenerator 这种类型就提取它的第一个泛型参数 U 那就得到了我们的yieId，如果得到就返回U否则never<b>

这时我们得到了yieId,完整代码如下:


```ts
type YieldedType<T> = T extends AsyncGenerator<infer U, any, any> ? U : never;
```


# 15.
