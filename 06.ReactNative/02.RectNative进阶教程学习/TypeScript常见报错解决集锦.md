---
title: ReactNative开发环境搭建
date: 2014-07-20 00:00:00
updated: 2022-01-05 00:00:00
tags: [RectNative,跨平台开发,前端开发]
type: [RectNative,跨平台开发,前端开发]
comments: ReactNative开发
description: ReactNative开发
keywords: ReactNative开发
top_img:
mathjax:
katex:
aside:
aplayer:
highlight_shrink:



---

[TOC]

# 概述

**TS1192: Module '"/Volumes/repos/tc-web-ts/node_modules/@types/react/index"' has no default export.**

解决方法：
把"allowSyntheticDefaultImports": true添加到tsconfig.json






**TS7016: Could not find a declaration file for module '../styledComponents/loginAndRegister'. '/Users/michealye/Desktop/myProduct/shebao520-mobile/src/components/styledComponents/loginAndRegister.jsx' implicitly has an 'any' type.**


解决方法：没有指定类型 Edit your tsconfig.json "noImplicitAny": false
