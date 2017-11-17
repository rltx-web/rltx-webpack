## 项目依赖库
[vue](https://github.com/vuejs/vue)
[es6](es6.ruanyifeng.com)
[babel](https://github.com/babel/babel)
[sass](https://github.com/webpack-contrib/sass-loader)
[eslint](https://github.com/eslint/eslint)
[axios](https://github.com/mzabriskie/axios)
[webpack](https://github.com/webpack/webpack)
[express](https://github.com/expressjs/express)
[element](https://github.com/ElemeFE/element)

## 项目安装

```
npm install
```

## 指令

我们可以在`package.json`里面查找对应的命令

```
"scripts": {
  "dev": "node --max_old_space_size=2048 build/dev-item-server.js",
  "start": "node --max_old_space_size=2048 build/dev-server.js",
  "build": "node --max_old_space_size=2048 build/build.js",
}
```

### 页面和指令配置

我们可以在`config/pathConfig.js`里面查找对应的命令

### 如何运行指令

```
npm run dev item // 运行开发环境
npm run build dev // 生成文件
```

## 项目开发

### 目录解释

```
+ build // webpack配置
+ config // 一些config配置
+ dist // 输出目录
+ src // 开发代码目录
 - components // 组件文件夹
+ api // api controllers
```

### 开发流程

eg，我们需要建立一个`truck/add.html`的页面，然后入口js文件是`truck/add.js`，接下来我们进行配置

1. 配置

```js
// config/pathConfig.js
const configList = {
};
//在configList中添加如下配置
const configList = {
  truck: ['role/add'],
};
```
2. 在template文件夹中新增truck/add.html文件
3. 在src文件夹中新增truck/add.vue, truck/add.js文件

接下来我们运行 `npm run dev item truck`启动项目

## 渲染机制

1. config， 获取对应配置，拿到对应的`elementCode`去`define`接口找`elementInfoCodes`

- `search_render_info`， 搜索字段配置
- `list_render_info`，列表字段配置
- `detail_render_info`， 详情字段配置

2. define， 获取对应定义, `element_type/define`, 拿到对应的`elementInfoCodes`去`info`接口找`controllType`

3. info， 获取对应信息, `element_type/info`, 根据`controllType`去匹配组件类型

### 合并数据，返回结构化数据

```js
// 详见api/utils.js getParam()
```

## webpack配置

### 接口地址配置

```js
// build/webpack.item.prod.conf.js
// gatewayUrl，接口地址
// filePath，输出目录
let gatewayUrl = '';
if (process.argv[2] === 'dev') {
  filePath = '';
}

```
