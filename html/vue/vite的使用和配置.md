## 总览（摘自官网）
Vite (法语意为 "快速的"，发音 /vit/) 是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，它基于 原生 ES 模块 提供了 丰富的内建功能，如速度快到惊人的 模块热更新（HMR）。

- 一套构建指令，它使用 Rollup 打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。

Vite 意在提供开箱即用的配置，同时它的 插件 API 和 JavaScript API 带来了高度的可扩展性，并有完整的类型支持。

---
## 使用vite创建vue项目 (使用官方的方式)
- npm创建
`npm init @vitejs/app`
- 创建并使用模板
    ``` 
    # npm 6.x
    npm init @vitejs/app my-vue-app --template vue

    # npm 7+, 需要额外的双横线：
    npm init @vitejs/app my-vue-app -- --template vue

    # yarn
    yarn create @vitejs/app my-vue-app --template vue
    ```
- 其他的创建语句
`npm init vite-app kfy-pc`

---
## vite搭建vue项目的参考文章
1. [vite+vue3+element-plus项目搭建基本流程配置](https://blog.csdn.net/superKM/article/details/111328781)
2. [vue-cli3 + elementui 搭建后台管理界面(五)-递归生成侧栏路由](https://www.jianshu.com/p/b5a16d00e173)
3. [手把手教你使用npm和vite 2.0创建vue项目（非ts），配置打包环境](https://blog.csdn.net/weixin_41722909/article/details/115011765)
4. [vite项目中加载icon](https://github.com/anncwb/vite-plugin-svg-icons/blob/2e63ab8f58e152a2db91cac571bfd8d24617b2e0/README.zh_CN.md)

---

## 拓展vue-cli4 全配置   

1. [vue-cli4-config](https://github.com/staven630/vue-cli4-config)