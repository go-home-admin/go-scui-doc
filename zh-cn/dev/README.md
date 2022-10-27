# 自定义组件开发

所有组件都必须嵌套 `*base.Render` 或者完整实现接口 `base.RenderBase`


### 小组件

任意一个实现接口 `base.RenderBase`的`struct`都是一个组件。
````go
type Div struct {
    *base.Render
}

func NewDiv() *Div {
	return &Div{Render: base.NewRender("<div>123456</div>")}
}
````

上面就是一个固定在页面输出123456的组件。正常开发肯定不满足固定内容, 一般会定义一些插槽, 或者占位符, 方便动态替换内容。

`base.NewRender("<div>123456</div>")` 还可以改成使用vue文件加载成go组件。

创建文件 `gui/base/views/demo.vue`。内容如下:

````vue
<template>
  <div @click="callFun">
    <slot id="context"/>
  </div>
</template>

<script>

export default {
  data() {
    return {
      "demo": "123"
    }
  },
  methods: {
    callFun: function () {
      alert(this.demo)
    }
  }
}
</script>
````
demo.go

````go
type Div struct {
    *base.Render
}

func NewDiv() *Div {
    d := &Div{Render: base.LoadVue("demo.vue")}
    d.AddRender(base.NewRender("<p>把内容嵌套进入demo.vue里的插槽id=context</p>"), "context")
    return d
}
````

上面demo组件就会变成显示子组件的文字, 点击她就会弹出内容。
你还可以添加其他函数, 获取覆盖vue文件里的函数

````go
d.AddMethods("funame", `function () { alert("动态新增函数"); }`)
````



