# WECH (Wechat Component Helper)

极轻量的微信小程序模块化组件开发helper，没有任何构建相关的骨架或者约束。只是通过getter、setter帮助开发者能够以“模块化”的方式将小程序页面剥离成一个个组件。

### 使用方式

1.引入widget.js

2.开发组件时，组件的config包一层之后再exports：

```

import wech from 'wech';

var yourConfig = {
    data: {
        district: '',
    },
    methods: {
        yourComponentMethod: function () {},
    },
    events: {
        yourComponentEvent: function () {},
    },
    onLoad: function () {}
};

module.exports = wech(yourConfig);

```

其中，可以通过 this.data.district / this.yourComponentMethod 访问组件内部的数据和方法，通过 this.setData 更新组件内部的数据，通过 this.$emit('eventName', data) 向外传递事件。模版中可以通过<view bindtap="{{ yourComponentEvent }}">来触发组件内部方法，防止全局事件名污染。

3.引入组件时，通过install／addTo方法。install用于将组件挂载到页面，addTo用于将组件作为子组件，挂载到另一个组件内部

```

var pageConfig = { 微信页面配置 };

child1.install(pageConfig, {
    scope: '这里的名字需要和<template is="component1" data="{{...scope1}}"></template>里面的scope1相符',
    static: {
        age: 25, // 传递给组件的静态参数，一般用于初始化、配置等
    },
    props: {
        // 传递给组件的动态数据，page的数据更新会同步至组件
        address () {
            return '北京市' + this.data.district + '区';
        },
    },
    events: {
        // 组件暴露到外部的事件
        changeCity (data) {
            this.data.district = '';
        }
    }
});

Page(blabla);

```

addTo同理。

### 关于css和wxml

没有额外的要求或者改动，目的是保持轻量和便于适配微信官方后续的迭代。唯一的措施是，组件内的bindtap等事件可以通过花括号 {{ eventName }} 来绑定内部的方法。引入widget后会保证每个组件的事件名互不冲突。
