# vue-interview

## v-if和v-for谁的优先级更高，应该如何使用避免性能问题

源码中找答案compiler/codegen/index.js
- 同级情况下
```html
<div v-for="child in children" v-for="isFile">{{ child.file }}</div>
```

```javascript
//打印render函数
// 先执行v-for循环,再执行v-if
ƒ anonymous(
) {
with(this){return _c('div',{attrs:{"id":"app"}},_l((children),function(child){return (isFile)?_c('div',[_v(_s(child.file))]):_e()}),0)}
}
```

- 不同级情况下
```html
<template v-if="isFile">
    <div v-for="child in children">{{ child.file }}</div>
</template>
```

```javascript
// 打印render函数
// 先执行v-if,再执行v-for循环
ƒ anonymous(
) {
with(this){return _c('div',{attrs:{"id":"app"}},[(isFile)?_l((children),function(child){return _c('div',[_v(_s(child.file))])}):_e()],2)}
}
```

***结论***

1. v-for优先级高于v-if
2. 如果同时出现，每次渲染都会先执行循环在判断条件，无论如何循环都不可避免，浪费性能
3. 要避免这种情况，则在外层嵌套template，在这一层进行v-if判断，然后在内部进行v-for循环
4. 如果渲染的数据里有可以判断的字段，可以在计算属性中先处理数据
```javascript
children: [
    {file: "foo"},
    {file: "baz"},
    {file: "bus", isShow: false}
]
```