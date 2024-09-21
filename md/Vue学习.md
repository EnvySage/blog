# Vue学习
# 一，创建Vue项目

在有node.js的环境下，才可以使用npm指令

```npm
npm create vue@latest
```

进行基于vite的vue项目启动

![1](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/1.png)

## I，OptionsAPI选项式配置项

 `Options`类型的 `API`，数据、方法、计算属性等，是分散在：`data`、`methods`、`computed`中的，若想新增或者修改一个需求，就需要分别修改：`data`、`methods`、`computed`，不便于维护和复用。

![OptionAPI](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/OptionAPI.gif)  ![OptionAPI2](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/OptionAPI2.gif) 

## II，CompositionAPI组合式配置项

可以用函数的方式，更加优雅的组织代码，让相关功能的代码更加有序的组织在一起。

​    所谓组合式，其实就是将相同功能的代码组合在一起，不像选项式那样拆散，如果想加功能或者维护，只需要在对应的功能函数体内添加即可。

<img src="https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/CompositionAPI.gif" alt="CompositionAPI" style="zoom:80%;" />  
<img src="https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/CompositionAPI2.gif" alt="CompositionAPI2" style="zoom: 80%;" />

[^资料来源：大帅老猿]: 

# 二，setup

## I，setup概述

etup是Vue3中一个新的配置项，值是一个函数，它是Composition API表演的舞台，组件中所用到的：数据、方法、计算属性、监视......等等，均配置在setup中。

## II，setup执行时机

在Vue2中，在所有生命周期中最早执行的是beforeCreate，而setup执行时机比beforeCreat更快。

因为setup的时机比beforecreate更早，所以如果同时用了vue2和vue3的写法的情况下，vue2能拿到vue3里的数据

```xml
<template>
  <div class="person">
    ......
    <h2>测试1：{{a}}</h2>
    <h2>测试2：{{c}}</h2>
    <button @click="b">测试</button>
  </div>
</template>

<script lang="ts">
  export default {
    name:'Person',
    data(){
      return {
        a:100,
        c:this.name
      }
    },
    methods:{
      b(){
        console.log('b')
      }
    },
    setup(){
      let name = '张三'
      let age = 18
      let tel = '13888888888'
      
      ......
    }
  }
</script>
```

## III，setup语法糖

### 1，setup函数写法

```xml
<script lang="ts">
    setup(){
      let a = 2222
      return {a}
    }
  }
</script>
```

这里需要写出setup方法和return导出，才能使用

### 2，script里写setup

```xml
<script setup>
  let a = 666
</script>
```

这里则只申明变量也会有默认导出的操作，换而言之就是你不在需要自己导出和写setup方法

## IV，重复导出报错

前面说到，在setup语法糖中会自动参配return的导出。所以如果在此基础上再加上export default等导出方式就会报错

<img src="https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/export.png" alt="export" style="zoom: 80%;" />

# 三，ref响应式对象

 Vue2是把数据放在data里，在data里的数据，全都是响应式数据。而在Vue3是通过ref和reactive来实现响应式，这个ref跟标签的ref是不相同的，在后面会详细讲解标签ref。Vue3的理念是，它并不会把所有数据都设置成响应式，而是你需要哪个数据是响应式，就在哪个数据包裹一下ref()，让它变成响应式。

## I，ref基本响应式

### 1，导入ref

```js
import {ref} from 'vue'
```

### 2，ref包裹对象

```js
<template>
  <div class="person">
    <h2>姓名：{{name}}</h2>
    <h2>年龄：{{age}}</h2>
    <h2>地址：{{address}}</h2>
    <button @click="changeName">修改名字</button>
    <button @click="changeAge">修改年龄</button>
  </div>
</template>

<script lang="ts" setup name="Person">
    import {ref} from 'vue'

    let name = ref('张三')
    let age = ref(18)
    let tel = '12345657'
    // 方法
    function changeName() {
        name = 'zhang-san'
    }
    function changeAge() {
        age += 1
    }
</script>
```

这里就是把那么和age包裹成响应式对象

![ref](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/ref.png)

## II，reactive对象类响应式

ref主要是针对基本类型的响应式，如果是对象类型我们可以使用reactive，用法跟ref一样，也是使用reactive把对象进行包裹。

### 1，深层次的包裹

```html
<template>
  <div>
    <h1>{{ obj.a.b.c }}</h1>
  </div>
</template>

<script lang="ts" setup>
import {reactive} from 'vue'
let obj = reactive({
    a:{
        b:{
            c:222
        }
    }
})
</script>
```

## III，ref对比reactive

###   1、宏观角度

​      (1)ref用来定义：基本类型数据、对象类型数据

​      (2)reactive用来定义：对象类型数据

###     2、区别

​      (1)ref创建的变量必须使用`.value`（可以使用`volar`插件自动添加`.value`）。

​      (2)reactive重新分配一个新对象，会`失去响应式`（可以使用`Object.assign`去整体替换）。（局限性）

###     3、使用原则

​      (1)若需要一个基本类型的响应式数据，必须使用`ref`。

​      (2)若需要一个响应式对象，层级不深，`ref`、`reactive`都可以。

​      (3)若需要一个响应式对象，且层级较深，推荐使用`reactive`。

### 4，实现原理

虽然ref能干reactive的活，但是ref底层也是调用reactive来包裹响应式对象，所以reactive并不可能消失

# 四，toRefs与toRef

## I，toRefs

   我们用reactive包裹了对象之后，它就变成了响应式了，假设此时我们使用解构赋值，要注意的一点是，解构赋值的变量，并不是响应式的，它只是一个普通的变量而已。

```js
<script lang="ts" setup name="Person">
  let person = reactive({
    name:'张三',
    age:18
  })
  let {name,age} = person
  //这里相当于是
  let name = person.name
  let age = person.age
  //但是相适应对象是person，所以重新解构的name不是响应对象
</script>  
  
```

此时toRefs就可以派上用场了，它的作用就是可以让对象的每一个属性解构出来都变成响应式：

```js
<script lang="ts" setup name="Person">
  let person = reactive({
    name:'张三',
    age:18
  })
  let {name,age} = toRefs(person)
</script>  
```

​    包裹了toRefs()之后，整个对象里的各个属性值都变成了ref对象，我们可以通过输出toRefs(person)进行观察：

![toRefs](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/toRefs.png) 

## II，toRef

见名知意，就是toRefs的单数，他会把响应式对象里的其中一项改为响应式数据

# 五，ref标签

## I，使用ref标签

vue3在模板上的使用跟vue2是一样的，但获取ref元素的时候，它需要用ref类型先把ref标签保存起来，然后再使用。

```vue
<template>
  <div class="person">
    <h1>中国</h1>
    <h2 ref="title2">北京</h2>
    <h3>尚硅谷</h3>
    <button @click="showLog">点我输出h2这个元素</button>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {ref} from 'vue'

  // 创建一个title2，用于存储ref标记的内容
  let title2 = ref()

  function showLog(){
    console.log(title2.value)
  }
</script>
```

## II， defineExpose使用

 ref标签不只是用在普通的html元素，它还可以应用在组件标签上。假设这里存在一对父子组件，在父组件里对子组件标签使用ref的话，它获取的是子组件的实例。

父组件：

```vue
<template>
  <button @click="showLog">测试</button>
  <Person ref="child"/>
</template>

<script lang="ts" setup name="App">
  import Person from './components/Person.vue'
  import {ref} from 'vue'

  let child = ref()

  function showLog(){
    console.log(child.value)
  }
</script>
```

子组件：

```vue
<template>
  <div class="person">
    ......
  </div>
</template>

<script lang="ts" setup name="Person">
  let a = ref(0)
  let b = ref(1)
  let c = ref(2)
</script>
```

这里的ref就只能拿到person整个页面的实例，但是拿不到person里面abc实例的数据，这时候就需要defineExpose登场了

```vue
<script lang="ts" setup name="Person">
  import {ref} from 'vue'

  // 创建一个title2，用于存储ref标记的内容
  let title2 = ref()
  let a = ref(0)
  let b = ref(1)
  let c = ref(2)

  function showLog(){
    console.log(title2.value)
  }

  defineExpose({a,b,c})
</script>
```

将想要给父类看到的东西defineExpose出来，父类才能看到子类的数据