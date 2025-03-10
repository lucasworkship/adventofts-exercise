# 10. [Day Ten](https://www.adventofts.com/events/2023/10)

目标: 判断`A` `B` 两个字符串`A`是否与`B`结尾

```ts
import { Expect, Equal } from 'type-testing';

type test_0_actual = StreetSuffixTester<'Candy Cane Way', 'Way'>;
//   ^?
type test_0_expected = true;
type test_0 = Expect<Equal<test_0_expected, test_0_actual>>;

type test_1_actual = StreetSuffixTester<'Chocalate Drive', 'Drive'>;
//   ^?
type test_1_expected = true;
type test_1 = Expect<Equal<test_1_expected, test_1_actual>>;

type test_2_actual = StreetSuffixTester<'Sugar Lane', 'Drive'>;
//   ^?
type test_2_expected = false;
type test_2 = Expect<Equal<test_2_expected, test_2_actual>>;

type test_3_actual = StreetSuffixTester<'Fifth Dimensional Nebulo 9', 'invalid'>;
//   ^?
type test_3_expected = false;
type test_3 = Expect<Equal<test_3_expected, test_3_actual>>;


```

>解决方案: 使用infer推断字符前面，并且严格要求结尾

```ts

type StreetSuffixTester<T extends string,K extends string> = T extends `${infer _}${K}`? true:false

```
> 方案2: `Exclude 方案` 

```ts

type StreetSuffixTester<T extends string,K extends string> = Exclude<T,`${string}${K}`> extends never ? true: false

```


# 11.[Day Eleven](https://www.adventofts.com/events/2023/11)


目的: 将对象 T 递归地 变为 readonly，并将数组转换为 readonly 元组。

```ts

type test_1_actual = SantaListProtector<{
  //   ^?
  theo: () => 'naughty';
  prime: string;
  netflix: {
    isChill: boolean;
  };
}>;
type test_1_expected = {
  readonly theo: () => 'naughty';
  readonly prime: string;
  readonly netflix: {
    readonly isChill: boolean;
  };
};
type test_1 = Expect<Equal<test_1_expected, test_1_actual>>;

```

> 解决思路: 挨个类型进行判断，并且递归调用有child 的对象或者数组


```ts

type SantaListProtector<T> = T extends (...args: any) => any
  ? T
	:T extends Object
	? {readonly [K in keyof T]:SantaListProtector<T[K]>}:
	T extends Array<infer U>
	? ReadonlyArray <SantaListProtector<U>>
	:T
	

```