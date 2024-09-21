# Typescript
# 一，TypeScript概述

TypeScript是由微软开发自由和开源的编程语言，它是JavaScript的一个超集。由于JavaScript属于弱类型语言，在编码过程中的常见拼写、类型等错误都不能预先检查。TypeScript的最初目标是成为JavaScript程序的静态类型检查器，但随着其发展，TypeScript的目标更多是开发大型应用，同时其代码可以编译生成纯JavaScript代码，并可运行在任何浏览器上。

由于TypeScript知识点非常庞大，在这里不能全部展开一一细说，只能了解一下我们在实战项目上比较常用的知识点。这里主要介绍一下ts的接口、泛型以及自定义类型，以及在Vue3上使用ts的流程。

# 二，TS接口

## I，定义接口

​    假设定义一个Person对象，里面包含id、name、age这三个属性，它们分别是字符串、字符串和数字类型。对于这种确定对象属性以及属性的类型，我们可以使用接口对它进行约束。

​    在ts中，使用interface关键字来定义接口：

```typescript
interface PersonInter {
  id:string,
  name:string,
  age:number
}
```

> 注意：定义属性的类型，用的是小写，不要用大写。例如字符串使用string，不要写成String。

## II，使用接口

定义好接口之后，别忘记要把它导出出去，不然外部就无法引入。这里使用最直接的export把接口暴露出去。

```typescript
export interface PersonInter {
  id:string,
  name:string,
  age:number
}
```

接下来就是导入ts文件，但是导入时需要注意加上type的关键词，因为你可能导入的是一个值或者一个类型，关键词的目的就是为了避免混淆

```typescript
import {type PersonInter} from "@/types"
```

## III，TS泛型、

假设我们一有一个persons的数组，他的每一个项都是person对象，这时候我们就可以用到泛型去规定数组里面放什么数据内容，在数组中使用泛型可以让元素严格保持规范。

```vue
<script lang="ts" setup>
  import {type PersonInter} from '@/types'
  
  let personList:Array<PersonInter> = [
    {id:'asyud7asfd01',name:'张三',age:60},
    {id:'asyud7asfd02',name:'李四',age:18},
    {id:'asyud7asfd03',name:'王五',age:5}
  ]
</script>
```

这样写就能保证数组每一项都能符合personInter的接口规范

## IV，TS自定义类型

有时候一个数组里可以包含多个类型，例如它可以写成Array<PersonInter, aaa, bbb, ccc, ...>，类似这种情况看起来就会显得比较臃肿，要是有多个地方出现这种情况，写起来感觉特别难受。

这时候我们就可以用到type去定义一个类型，然后根据自己的需求去组合，像前面的例子里的personList既要符合数组类型又要求每一项都符合person接口

```typescript
export interface PersonInter {
  id:string,
  name:string,
  age:number
}

/* 一个自定义类型 */

// 第一种写法
// export type Persons = Array<PersonInter>

// 第二种写法
export type Persons = PersonInter[]；
```

自定义类型之后，在使用的时候就显得比较简洁了：

```vue
<script lang="ts" setup>
  import {type Persons} from '@/types'
  
  let personList:Persons = [
    {id:'asyud7asfd01',name:'张三',age:60},
    {id:'asyud7asfd02',name:'李四',age:18},
    {id:'asyud7asfd03',name:'王五',age:5}
  ]
</script>
```

## V，响应式数据使用TS

### 1，常规使用

在变量名后使用ts添加接口类型

```vue
<script lang="ts" setup>
  import {reactive} from 'vue'
  import {type Persons} from '@/types'
  
  let personList:Persons = reactive([
    {id:'asyud7asfd01',name:'张三',age:60},
    {id:'asyud7asfd02',name:'李四',age:18},
    {id:'asyud7asfd03',name:'王五',age:5}
  ])
</script>

```

### 2，推荐使用

我们还可以与泛型结合实现效果

```vue
<script lang="ts" setup>
  import {reactive} from 'vue'
  import {type Persons} from '@/types'
  
  let personList = reactive<Persons>([
    {id:'asyud7asfd01',name:'张三',age:60},
    {id:'asyud7asfd02',name:'李四',age:18},
    {id:'asyud7asfd03',name:'王五',age:5}
  ])
</script>
```

