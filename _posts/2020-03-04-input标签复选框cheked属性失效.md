---
layout: post
title:  "input标签复选框失效问题"
categories: html基础
tags:  html
author: DuGuYu
---

   在<input type="checkbox" />标签元素中，只要该标签元素有属性checked，  
无论你是直接设置checked，还是checked='true'; checked='false';   
checked='checked' ，你所设置的标签元素都是被选中状态。
但是，你可以在js中配置checked的属性值为true或false，
