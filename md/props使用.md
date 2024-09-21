# props使用
# 一，defineProps接收

props最主要的作用就是接收父组件的传参，在Vue2里直接使用props接收参数即可，而在vue3需要依赖defineProps来接参。

 这里举一个简单的例子，父组件传一个数组给子组件渲染出来。

父组件Person.vue：

```vue
<template>
  <div class="person">
    <Child :list="personList"></Child>
  </div>
</template>

<script lang="ts" setup name="Person">
  import Child from './Child.vue'

  let personList = [
    {id:'asyud7asfd01',name:'张三',age:60},
    {id:'asyud7asfd02',name:'李四',age:18},
    {id:'asyud7asfd03',name:'王五',age:5}
  ]
</script>
```

子组件Child.vue：

```vue
<template>
  <div class="child">
    <ul>
      <li v-for="p in list" :key="p.id">
        {{ p.name }} -- {{ p.age }}
      </li>
    </ul>
  </div>
</template>
```

Vue3的接参方式有点不同，它需要依赖defineProps函数。它里面传入一个数组，里面的每一项是接参的参数名。

```vue
<script lang="ts" setup>
  defineProps(['list'])
</script>
```

# 二，使用props参数

Vue3的不能直接使用props的参数，必须先用一个变量把props存储起来，然后才能使用。

```vue
<script lang="ts" setup>
  const propParam = defineProps(['list'])
  console.log(propParam)
</script>
```

打印出来结果，我们可以得知他其实就是包裹成一个对象，我们要使用就以键值对的形式读取就行。

# 三，限制props参数类型

vue3的类型限制是通过defineProps的泛型来实现

```vue
<script lang="ts" setup>
  const propParam = defineProps<{list: Array<object>}>();
</script>
```

> 注意：使用了泛型来限制参数类型之后，defineProps方法里就不要传数组了，因为它通过泛型就已经知道你要接收的参数了。例如没有添加限制的写法是defineProps(['list'])，添加类型限制后就写成defineProps<{list: Persons}>()

# 四，限制是否必传

 vue3分两种情况：第一种是没有限制类型，把数组传入到defineProps里，比如defineProps(['list'])，那么它默认是非必传的；第二种是使用泛型限制参数类型，比如defineProps<{list: Persons}>()，则它默认是必传的。如果想设置它为非必传，则在限制类型的冒号:前加一个?，写成?:，这也是ts的一种语法。

```vue
<script lang="ts" setup>
  import {type Persons} from '@/types'
  const propParam = defineProps<{list?: Persons}>()
</script>
```

# 五，设置默认值

vue3设置默认值就会比vue我相对繁琐很多

Vue3的默认值需要依赖withDefaults函数，withDefaults函数需要传入两个参数，第一个参数把整个defineProps的内容传进去，第二个参数用来设置默认值，它是一个对象，通过键值对的方式对每个属性进行设置。

```vue
<script lang="ts" setup name="Person">
  import {type Persons} from '@/types'
  
  const propParam = withDefaults(defineProps<{list?:Persons}>(), {
    list: () => [{id:'ausydgyu01',name:'康师傅·王麻子·特仑苏',age:19}]
  })
</script>
```

