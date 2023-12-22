---
title: 利用TypeScript构建优雅的React Native项目
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

文章参考：https://juejin.im/post/5cd978ef6fb9a031fe3be6bd



模板参考：https://github.com/react-native-community/react-native-template-typescript/blob/master/template/babel.config.js

https://blog.csdn.net/dick3741/article/details/91379421


文章参考：https://juejin.im/post/5ce0135a5188251ce226416d

官方文档：https://facebook.github.io/react-native/blog/2018/05/07/using-typescript-with-react-native

官方文档：https://reactnative.cn/docs/typescript/

### 集成方案

目前RN 集成 TypeScript 有两个方案：

方案一： 借助第三方编译插件react-native-typescript-transformer

方案二： RN 0.57 版本之后将 babel 升级到了 V7， Babel 从V7开始支持 TypeScript 编译，详情介绍参考这里。

关于 babel 支持TypeScript编译有以下几个问题需要我们注意：
babel 只负责在编译期将 TS 编译为JS，并没有进行类型校验，这意味着即使我们代码类型写错了也能编译通过，没有任何提示。不过目前包括 VSCode、WebStorm等开发工具基本支持 TS 语法、类型校验，所以我们就不需要强依赖编译期类型校验。

有一些 TS 特性babel不支持：

```
 namespaces
 bracket style type-assertion
 enums
```










从 React Native 0.57 版本开始，我们终于可以直接使用 TypeScript 开发，不需要任何额外的配置。本文主要介绍如何使用 TypeScript 优雅地开发 React Native 应用。

#### 一、初始化项目

初始化项目之前，请确保已经按照 React Native 中文网 的搭建开发环境文档完成了环境搭建

```
$ react-native init MyReactNativeTS
```

注意: 入口文件 index.js 需要保留，其他文件都可以使用 .ts 或 .tsx 后缀。

#### 二、TypeScript 编译器

```
$ yarn global add typescript
$ yarn add -D typescript @types/react @types/react-native
```
#### 三、配置 tsconfig.json

```
$ tsc --init --pretty --target esnext --allowJs --checkJs --jsx react-native --allowSyntheticDefaultImports --experimentalDecorators --emitDecoratorMetadata

```
注意：注意多余的注释可能会不兼容，需要移除，详细文档可查阅 编译选项。
[https://www.tslang.cn/docs/handbook/compiler-options.html](https://www.tslang.cn/docs/handbook/compiler-options.html)


### babel.config.js 配置

```
// babel.config.js
module.exports = {
    "presets": [
        "module:metro-react-native-babel-preset",
    ],
    "plugins": [
        // 解决TS中的 module 引用问题，下面会详细说明
        ["module-resolver", {
            "root": ["./src"],
            "extensions": [".js", ".ts", ".tsx", ".ios.js", ".android.js"]
        }],
        "@babel/plugin-transform-runtime",
    ],
}

```

当然我们也可以在大于 0.57 版本中继续使用 react-native-typescript-transformer 方式支持 TS，具体的实现步骤参考这里（https://github.com/ds300/react-native-typescript-transformer#step-3-configure-the-react-native-packager）。




#### 四、EsLint 代码检测
Lint 工具用于检查代码的语法是否正确、风格是否符合要求。最新的工具 ESLint 不仅允许你自定义语法规则，还允许用户创造插件，改变默认的 JavaScript 语法，比如支持 ES6 和 JSX 的语法。

安装 ESLint Plugin

配置 ESLint Plugin（这个配置需要在哪里配置？？）

```
{
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "typescript",
      "autoFix": true
    },
    {
      "language": "typescriptreact",
      "autoFix": true
    },
    {
      "language": "vue",
      "autoFix": true
    },
    {
      "language": "html",
      "autoFix": true
    }
  ],
  "eslint.autoFixOnSave": true
}

```
- eslint.validate：ESLint 插件默认只校验 javascript 和 javascriptreact，所以需要手动开启其他语言的校验支持
- eslint.autoFixOnSave: 开启保存时自动修复错误

#### 五、项目配置
注意：配置之前请确保根目录下存在 .eslintrc.js。如果要指定忽略某些文件，可以使用 .eslintignore 文件（node_modules、bower_compnents 文件夹已经默认被忽略）

执行下面的配置脚本

```
yarn add -D eslint eslint-config-airbnb eslint-plugin-jsx-a11y eslint-plugin-import eslint-plugin-react eslint-plugin-react-native @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-import-resolver-typescript

```

- eslint: 可组装的 JavaScript 和 JSX 检查工具
- eslint-config-airbnb: 该软件包提供 Airbnb 的 .eslintrc 作为可扩展的共享配置
- eslint-plugin-jsx-a11y: 用于 JSX 元素的可访问性规则的静态 AST 检查器。
- eslint-plugin-import: 此插件旨在支持 ES2015 +（ES6 +）导入/导出语法的 linting。
- eslint-plugin-react: ESLint React 校验规则插件
- eslint-plugin-react-native: 针对 ESLint 的 React Native 特定 linting 规则
- @typescript-eslint/parser: 将 TypeScript 转换为 ESTree，使 eslint 可以识别
- @typescript-eslint/eslint-plugin: 一个包含一堆特定于 TypeScript 的 ESLint 规则的插件
- eslint-import-resolver-typescript: 给 eslint-plugin-import 添加 typescript 支持的插件

