# Vue属性方式
# 一，computed计算属性

## I，computed概述

​    计算属性是 Vue 实例的一个选项，可以用来定义一个属性，该属性的值是经过计算后的结果。它的特点是：

​    1、依赖于其他的响应式属性

​    2、会进行缓存，只有在依赖发生变化时，才会重新计算

​    3、计算属性可以定义为一个函数，也可以定义为一个对象，包含 getter 和 setter

## II，computed的使用

这里使用拼接姓名作为例子，输入姓和名，最终拼接成完整的姓名，实例如下

```vue
<template>
  <div class="person">
    姓：<input type="text" v-model="firstName"> <br>
    名：<input type="text" v-model="lastName"> <br>
    全名：<span>{{firstName}}-{{lastName}}</span> <br>
  </div>
</template>
<script lang="ts" setup name="Person">
  import {ref} from 'vue'

  let firstName = ref('zhang')
  let lastName = ref('san')
</script>  
```

这样写固然简单，但是倘若加入一些规则，比如我们需要让首字母大写，这时候我们就需要用到computed计算属性，改动如下

```vue
<template>
  <div class="person">
    姓：<input type="text" v-model="firstName"> <br>
    名：<input type="text" v-model="lastName"> <br>
    全名：<span>{{firstName.slice(0,1).toUpperCase() + firstName.slice(1)}}-{{ lastName }}</span> <br>
  </div>
</template>
```

这样写也固然可以，但是违背了Vue官方文档的原则，即是让模板写的语句尽可能的简单，易读。所以我们这时候就可以用上computed属性

```vue
<template>
  <div class="person">
    姓：<input type="text" v-model="firstName"> <br>
    名：<input type="text" v-model="lastName"> <br>
    全名：<span>{{fullName}}</span> <br>
  </div>
</template>
<script lang="ts" setup>
  import {ref,computed} from 'vue'

  let firstName = ref('zhang')
  let lastName = ref('san')

  let fullName = computed(() => {
    return firstName.value.slice(0,1).toUpperCase() + firstName.value.slice(1) + '-' + lastName.value
  })
</script>  

```

这样写业务逻辑也会更加清楚

## III，computed的缓存特性

computed属性无论使用多少次，它只会计算一次，只要它依赖的变量没有发生变化，它就不会重新计算。

```vue
<template>
  <div class="person">
    姓：<input type="text" v-model="firstName"> <br>
    名：<input type="text" v-model="lastName"> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
    全名：<span>{{fullName}}</span> <br>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {ref,computed} from 'vue'

  let firstName = ref('zhang')
  let lastName = ref('san')

  // fullName是一个计算属性，且是只读的
  let fullName = computed(()=>{
    console.log('执行computed计算')
    return firstName.value.slice(0,1).toUpperCase() + firstName.value.slice(1) + '-' + lastName.value
  })
</script>  
```

这里计算属性依赖的变量没有改变所以只会计算一次，其他的使用缓存

## IV，computed的get和set

computed计算属性如果传入回调函数，那么它是一个只读属性，不能修改。但是可以通过get和set方法进行修改。

```vue
<template>
  <div class="person">
    姓：<input type="text" v-model="firstName"> <br>
    名：<input type="text" v-model="lastName"> <br>
    全名：<span>{{fullName}}</span> <br>
    <button @click="changeFullName">将全名改为li-si</button> <br>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {ref,computed} from 'vue'

  let firstName = ref('zhang')
  let lastName = ref('san')

  // fullName是一个计算属性，可读可写
  let fullName = computed({
    // 当fullName被读取时，get调用
    get(){
      return firstName.value.slice(0,1).toUpperCase() + firstName.value.slice(1) + '-' + lastName.value
    },
    // 当fullName被修改时，set调用，且会收到修改的值
    set(val){
      const [str1,str2] = val.split('-')
      firstName.value = str1
      lastName.value = str2
    }
  })

  function changeFullName(){
    fullName.value = 'li-si'
  }
</script>
```

### 1，get方法

调用get方法就是显示在模板里的内容，也是就是我们return的内容

### 2，set方法

set方法在你修改这个计算属性的时候它就会执行，它会把最新的值接收回来。我们知道，想让computed重新计算，实际上是让它所依赖的变量发生改变，那么我们只需要在set方法里把它依赖的变量重新赋值，即可重新计算最新值。

# 二，watch监听属性

## I，监听数据

Vue3中watch只能监听以下四种数据

> 1. `ref`定义的数据。
> 2. `reactive`定义的数据。
> 3. 函数返回一个值（`getter`函数）。
> 4. 一个包含上述内容的数组。

## II，监听ref类型

### 1，ref基本类型

首先watch由三个部分构成，依次是监听对象，回调函数和配置规则，其中回调函数中会有两个值，依次是新的改变量和旧的改变量。

```vue
import {watch} from 'vue'
watch(sum,(newValue,oldValue)=>{

})
```

举个例子，点击按钮让数字加一，这里的数字属于基本类型，符合监听数据的范围

```vue
<template>
  <div class="person">
    <h2>当前求和为：{{sum}}</h2>
    <button @click="changeSum">点我sum+1</button>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {ref,watch} from 'vue'
  // 数据
  let sum = ref(0)
  // 方法
  function changeSum(){
    sum.value += 1
  }
  watch(sum,(newValue,oldValue)=>{
    console.log('sum变化了', newValue, oldValue)
  })
</script>
```

### 2，ref对象类型

这里监听对象的实质就是看监听的对象的地址是否有变化，比如前面用到的Object.assign

对整个对象类型进行替换，这个实质是把对应的值赋给原有对象的值，并没有新创建出一个地址，所以他们的地址没有变化，这时候就不会触发监听

```vue
<template>
  <div class="person">
    <h2>姓名：{{ person.name }}</h2>
    <h2>年龄：{{ person.age }}</h2>
    <button @click="changeName">修改名字</button>
    <button @click="changeAge">修改年龄</button>
    <button @click="changePerson">修改整个人</button>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {ref,watch} from 'vue'
  // 数据
  let person = ref({
    name:'张三',
    age:18
  })
  // 方法
  function changeName(){
    person.value.name += '~'
  }
  function changeAge(){
    person.value.age += 1
  }
  function changePerson(){
    person.value = {name:'李四',age:90}
  }
  watch(person,(newValue,oldValue)=>{
    console.log('person变化了',newValue,oldValue)
  })
</script>
```

 我们发现，直接把对象放进去watch进行监听，它是无法监听内部属性的变化的，它只能监听整个对象有没有被重新赋值，也就是它的地址有没有改变。

![watch](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/watch.gif) 


### 3，监听对象属性

在实际的开发中，我们更多的是监听对象里的属性变化。此时需要手动开启深度监听，也就是设置deep: true。

​      在Vue3中，它同样是设置deep:true，区别就在于它是在watch的第三个参数里设置，第三个参数是一个对象，用来设置watch的一些配置项，比如{deep: true}。

```js
watch(person,(newValue,oldValue)=>{
  console.log('person变化了',newValue,oldValue)
}, {
  deep: true
})
```

我们发现，设置了deep:true深度监听之后，不管是属性值发生变化，还是整个对象发生变化，它一样能监听得到。

​      当然除了deep:true之外，还有immediate:true也是比较常用，这个是立即监听的意思，它是先执行监听的函数，因此在初始化数据的时候，它就会立即调用一次watch监听。它的作用跟Vue2是完全一致的，在这里就不再举例详述。

> 注意：我们在监听对象的时候，如果是整个对象发生改变，那么它是可以识别新对象与旧对象的。但如果对象不变，只是内部的属性发生改变，那么它的newValue与oldValue是一致的。

## III，监听reactive对象类型

### 1，reactive与ref的监听对比

 reactive只能定义对象类型，它不像ref那样可以定义基本和对象两种类型。reactive与ref在监听的时候，主要有以下两点区别：

​    1、reactive类型是不允许改变整个对象的，也就是说不能给它赋值一个新对象，因此它就不会出现监听整个对象发生变化的情况

​    2、默认开启深度监听

### 2，监听reactive

其实监听reactive跟ref是一样的，也是把对象传进去watch里进行监听，不过它比ref更加方便，直接就可以监听到里面的属性变化，不需要设置deep:true。准确来说是Vue3强行设置深度监听，哪怕你设置deep:false也是没用的。

```vue
<template>
  <div class="person">
    <h2>姓名：{{ person.name }}</h2>
    <h2>年龄：{{ person.age }}</h2>
    <button @click="changeName">修改名字</button>
    <button @click="changeAge">修改年龄</button>
    <button @click="changePerson">修改整个人</button>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {reactive,watch} from 'vue'
  // 数据
  let person = reactive({
    name:'张三',
    age:18
  })
  // 方法
  function changeName(){
    person.name += '~'
  }
  function changeAge(){
    person.age += 1
  }
  function changePerson(){
    Object.assign(person,{name:'李四',age:80})
  }
  
  watch(person,(newValue,oldValue)=>{
    console.log('person变化了',newValue,oldValue)
  })
</script>
```

![reactive](https://envysage.oss-cn-guangzhou.aliyuncs.com/md/img/reactive.gif) 

> 注意：示例中修改整个人(整个对象)，它实际上是使用了Object.assign()方法，这个方法实际上并没有改变对象地址，它只不过是批量改变对象的各个属性而已。

### 3，监听对象属性

  在Vue3监听一个对象，是直接把对象放进去watch里监听即可。现在要监听对象里的属性，我们的第一反应就会把对象.属性放进去监听。

​    例如在例子中想监听对象的名字，很容易就会写成watch(person.name, ()=>{})，如下所示：

```vue
<template>
  <div class="person">
    <h2>姓名：{{ person.name }}</h2>
    <h2>年龄：{{ person.age }}</h2>
    <h2>汽车：{{ person.car.c1 }}、{{ person.car.c2 }}</h2>
    <button @click="changeName">修改名字</button>
    <button @click="changeAge">修改年龄</button>
    <button @click="changeC1">修改第一台车</button>
    <button @click="changeC2">修改第二台车</button>
    <button @click="changeCar">修改整个车</button>
  </div>
</template>

<script lang="ts" setup name="Person">
  import {reactive,watch} from 'vue'

  // 数据
  let person = reactive({
    name:'张三',
    age:18,
    car:{
      c1:'奔驰',
      c2:'宝马'
    }
  })
  // 方法
  function changeName(){
    person.name += '~'
  }
  ......
  watch(person.name, (newValue,oldValue) => {
    console.log('person.name变化了',newValue,oldValue)
  })
</script>
```

   我们发现，我们直接以对象.属性的方式放进去监听，Vue3是不允许的，它会发出警告：这是一个无效的监听源，它只允许以下四种类型：getter函数、ref类型、reactive类型、以上3种类型组合的数组。

所以我们可以修改成这样

```js
watch(() => person.name, (newValue,oldValue) => {
  console.log('person.name变化了',newValue,oldValue)
})
```

这样就只会监听对象的name属性了。**所以我们一般推荐用箭头函数包裹对象**

但是包裹了箭头函数之后，它反而无法监听里面的属性变化，它只关注对象的地址是否发生改变

**如果想既监听内部属性，也监听对象地址，只需要再加上深度监听属性即可，即设置deep:true**。

### 4，监听多个数据

  Vue3它可以同时监听多个数据，不需要分开写多个watch，可以写在一个watch里，将需要监听的属性放进数组里传入watch监听即可。

​    这里还是举上述的例子，这次监听的是名字和汽车这两个属性，但不监听年龄属性：

```javascript
let person = reactive({
  name:'张三',
  age:18,
  car:{
    c1:'奔驰',
    c2:'宝马'
  }
})
function changeC1(){
  person.car.c1 = '奥迪'
}
function changeC2(){
  person.car.c2 = '大众'
}
function changeCar(){
  person.car = {c1:'雅迪',c2:'爱玛'}
}
watch([()=>person.name,person.car],(newValue,oldValue)=>{
  console.log('person.car变化了',newValue,oldValue)
})

```

> 注意：因为你是同时监听多个属性，因此里面只要某一个属性发生了变化，它都会触发。你传入的是数组，因此它也会返回整个数组，里面就是各个属性的值。

# 三，watchEffect

## I，watch和watchEffect的区别

  1、都能监听响应式数据的变化，不同的是监听数据变化的方式不同

​    2、watch：要明确指出监听的数据，它是一个惰性监听，你不指定它就不监听

​    3、watchEffect：不用明确指出监听的数据（函数中用到哪些属性，那就监听哪些属性）。

## II，两者实现方式

### 1，watch实现方式

 watch有多种监听方式，可以对水温和水位分别监听，也可以同时监听。这里我们使用最快捷的方式，使用数组同时监听俩属性：

```javascript
watch([temp,height],(value)=>{
  // 从value中获取最新的水温(newTemp)、最新的水位(newHeight)
  let [newTemp,newHeight] = value
  // 逻辑
  if(newTemp >= 60 || newHeight >= 80){
    console.log('给服务器发请求')
  }
})
```

在这个需求里，涉及到的变量总共是两个，分别是水温和水位，因此watch需要监听这两个属性才能实现。如果需求很复杂，涉及的变量有10个，那么你也只能老老实实的给watch指定这10个变量的监听。

### 2，watchEffect实现方式

 watchEffect它不需要你去指定具体监听的属性，你只需要关注业务逻辑，只要代码涉及到的变量，它会帮你自动监听。

​      如果用watchEffect来实现上面的需求，将会省去所有监听的属性，变得更加简洁和灵活。

```JavaScript
watchEffect(()=>{
  if(temp.value >= 60 || height.value >= 80){
    console.log('给服务器发请求')
  }
}
```

## III，watchEffect的瑕疵

 如果在if条件里使用了或运算符||，它会按照条件顺序进行监听，一旦前面的条件符合，则后续的条件将不会监听。

​      继续用上面的例子：当水温达到60度，或水位达到80cm时，给服务器发请求。我们的代码写成if(temp.value >= 60 || height.value >= 80)，按照我们的理解，无论是temp还是height变量，只要哪一个条件符合它都会触发。

  但watchEffect的机制并非如此，它认为第一个条件是temp是否大于60，如果符合，则height的值无论是多少它都不关心。它只有在第一个条件不符合的情况下，它才会关注第二个条件。

​      因此大家在使用watchEffect写逻辑的时候，要小心使用||运算符，如果实在想在watchEffect实现if的或运算符，可以多写一个if来巧妙的绕开或运算符。

```vue
watchEffect(()=>{
  if (temp.value >= 60) {
    console.log('给服务器发请求')
  }
  if (height.value >= 80 && temp.value < 60) {
    console.log('给服务器发请求')
  }
})
```

 上面的代码将if(temp.value >= 60 || height.value >= 80)拆分成两段，如果水温temp大于60就发请求，如果水位超过80且水温不到60也发请求（因为水温到60就不用考虑水位了）。这样也同样能完美实现我们的需求。

## IV，watchEffect的注意事项

  1、watchEffect是立即监听的，相当于watch配置了immediate为true一样

  2、watchEffect无论是基本类型还是对象类型，它都可以监听到

  3、watchEffect的解除监听方式跟watch是一样的