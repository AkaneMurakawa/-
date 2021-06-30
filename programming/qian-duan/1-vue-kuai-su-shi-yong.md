# 1 vue快速使用

## 一、杂谈

* 声明式编程
* 命令式编程
* 函数式编程（参数为函数）

## 二、与其他JS框架的区别

作者：尤以溪

angular的模板和数据绑定

react的组件化和虚拟DOM

## 三、vue扩展插件

* vue-cli脚手架
* vue-resource（axios）ajax请求
* vue-router路由
* vuex状态管理
* element-ui UI组件库\(PC端\)、mint-ui（手机端）
* vue-lazyload图片懒加载
* vue-scroller页面滑动开关

## 四、MVVM

Model + View + ViewModel

* model模型，数据对象data
* view视图，模板页面
* viewModel：v-model双向数据绑定\(dom监听，数据绑定\)

el指定根选择器，data初始化数据

## 五、语法

```text
{{双写大括号表达式}}
```

指令一，强制数据绑定

```text
原src，现   v-bind:src
     简写   :src
```

指令二，绑定事件监听

```text
v-on:click="test“

@click强制绑定监听
```

配置

```text
methods函数

computed计算属性

created

componet

mounted

watch （get/set）
v-if
v-else
v-show  只是隐藏不显示
```

