## 泛型相关

### 常见工具泛型

1、Partial

	- 将类型定义的所有属性都修改为可选
	- 适用与一些对类型结构需求不明确，需要灵活处理的场景

```typescript
// 实现
type Partial<T> = {
  [P in keyof T]?: T[P]
}

// eg:
type Coord = Partial<Record<'x' | 'y', number>>

// 等同于
type Coord = {
  x?: number,
  y?: number
}
```

2、Require

	- 与Partial相反，将类型定义的所有属性修改成必填
	- - 表示逻辑取反，比如 -?，表示非可选
	- -?是映射条件的装饰符，去掉所有可选项代表的?

```typescript
// 实现
type Require<T> = {
  [P in keyof T]-?: T[P]
}

// eg:
type Coord = Required<{ x: number, y?:number }>

// 等同于
type Coord = {
	x: number;
	y: number;
}
```

3、Readonly

	- 将所有属性定义为只读，若修改就会报错

```typescript
// 实现
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}
```

4、Record 

	- 由联合类型的 key 当作新类型的 key
	- 以 typeof 格式快速创建一个类型，包含一组指定的属性且都是必填

```typescript
type Record<K extends keyof any, T> = {
  [P in K]: T
}

// eg:
type Coord = Record<'x' | 'y', number>

// 等同于
type Coord = {
  x: number,
  y: number
}
```

5、Exclude

	- 排除一个联合类型中指定的子类型, 返回匹配之外的所有类型（排除相同的）
  
```typescript
// 主要是基于 extends 条件类型的解析推迟的特性，返回了匹配之外的所有 候选类型，配合 never 类型 的空值特性，实现了这一高级类型。
type Exclude<T, U> = T extends U ? never : T

// eg:
type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'a' | 'c'
type T1 = Exclude<string | number | boolean, boolean> // string | number
```

6、Extract

	- 从一个联合类型中排除掉属于另一个联合类型的子集（留下相同的）
 
```typescript
type Extract<T, U> = T extends U ? T : nerver

// eg:
type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'b'
type T1 = Exclude<string | number | boolean, boolean> // boolean

```

7、Pick

	- 从类型定义的属性中，选取指定一组属性，返回一个新的类型定义

```typescript
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}

// eg:
type Coord = Record<'x' | 'y', number>
type CoordX = Pick<Coord, 'x'>
// 等同于
type CoordX = {
  x: number
}

// 额外：实现让 pick 返回的类型也是 props 可选
type PickWithPartial<T, K extends keyof T> = {
  [P in K]?: T[P]
}
```

8、Omit

	- 排除接口中指定的属性

```typescript
// 先使用 Exclude 排除掉属于 K 的子集，再用 Pick 获取剩下的属性
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>
```

9、NonNullable

	- 排除 null / undefined 类型
  
```typescript
type NonNullable<T> = T extends null | undefined ? nerver : T
```

10、ReturnType

	- 返回函数的返回结果类型

```typescript
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => inter R ? R : any

// eg
type resType = ReturnType<(arg: { name: string }) => string>
// 等同于
type resType = string

```