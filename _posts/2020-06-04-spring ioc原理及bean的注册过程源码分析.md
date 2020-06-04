---
layout: post
title:  "spring ioc原理及bean的注册过程源码分析"
categories: spring
tags:  spring
author: DuGuYu
---


![avatar](https://github.com/liuyushihao123/image/blob/master/ioc.png?raw=true)
## 一、控制反转（Spring IoC）
概念：
在每个框架中都有一个容器的概念，所谓的容器就是将常用的服务封装起来，然后，用户只需要遵循一定的规则，就可以达到统一、灵活、安全、方便、快速的目的
具有依赖注入功能的容器，负责实例化、定位、配置应用程序中的对象及建立这些对象间的依赖     
Bean的概念：
由IoC容器管理的那些组成应用程序的对象就叫Bean  
Bean就是由Spring容器初始化、装配及管理的对象，除此之外，Bean就与应用程序中的其他对象没什么区别了     
元数据 BeanDefinition
确定如何实例化Bean、管理Bean之间的依赖关系及管理Bean，就需要配置元数据，在Spring中由BeanDefintion代表      
如何工作（以XML配置方式解释）

准备配置文件：配置文件中声明 Bean 定义也就是 Bean 配置元数据。
由 IoC 容器进行解析元数据：IoC 容器的 Bean Reader 读取并解析配置文件，根据定义生成 BeanDefintion 配置元数据对象，IoC 容器根据 BeanDefintion 进行实例化、配置及组装 Bean。
实例化 IoC 容器：由客户端实例化容器，获取需要的 Bean。
