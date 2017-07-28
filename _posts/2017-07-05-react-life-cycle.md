---
layout: post
title: "React组件生命周期"
categories: React 
tags: React
---




# 装配

1. 实例化 `constructor()`

一般把组件的 `state` 初始化工作放在 `constructor` 里面去做

2. `componentWillMount()`

一般进行组件的启动工作，数据获取、定时器启动等

3. `render()`

4. `componentDidMount()`

动画的启动（依赖 `DOM`）

# 更新

1. `componentWillReceiveProps()`

2. `shouldComponentUpdate()`

3. `componentWillUpdate()`

4. `render()`

5. `componentDidUpdate()`

# 卸载
``
1. `componentWillUnmount()`

# 组件生命周期示意图

![](http://7xr2ek.com1.z0.glb.clouddn.com/blog/image/react-life-cycle.png)

