## 项目依赖库
[vue](https://github.com/vuejs/vue)，[es6](https://github.com/babel/babel)，[eslint](https://github.com/eslint/eslint)，[axios](https://github.com/mzabriskie/axios)，[webpack](https://github.com/webpack/webpack)，[express](https://github.com/expressjs/express)，[jszip](https://github.com/Stuk/jszip)
## 项目安装

```
npm install
```

## 指令

我们可以在`package.json`里面查找对应的命令

```
"scripts": {
    "dev": "node build/dev-server.js",
    "start": "node build/dev-server.js",
    "build": "node build/build.js",
    "unit": "cross-env BABEL_ENV=test karma start test/unit/karma.conf.js --single-run",
    "e2e": "node test/e2e/runner.js",
    "test": "npm run unit && npm run e2e",
    "lint": "eslint --ext .js,.vue src test/unit/specs test/e2e/specs"
  }
```

### 如何运行指令

```
npm run dev // 运行开发环境
npm run build // 生成文件
```

### 项目启动

```
npm run dev 551 // 运行开发环境，获得的是orgId为551的组织的数据
```

## 项目开发

### 目录解释

```
+ build // webpack配置
+ config // 一些config配置
+ dist // 输出目录
+ src // 开发代码目录
+ test // 测试目录
+ zip //zip代码
```

### 开发流程

eg，我们需要建立一个`app.html`的页面，然后入口js文件是`main.js`，接下来我们进行配置

```js
// build/webpack.base.conf.js
// 在entry添加对应的配置

entry: {
    app: './src/main.js'
}

// build/webpack.dev.conf.js
// 在plugins添加对应的配置
plugins: [
    new HtmlWebpackPlugin({
        filename: 'app.html',
        template: 'app.html',
        chunks: ['app'],
        inject: true
    })
]

// build/webpack.prod.conf.js
// 在plugins添加对应的配置
plugins: [
    new HtmlWebpackPlugin({
      filename: process.env.NODE_ENV === 'testing'
        ? 'app.html'
        : config.build.app,
      template: 'app.html',
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeAttributeQuotes: true
      },
      chunksSortMode: 'dependency'
    })
]

// config/index
// 在build添加对应的配置
module.exports = {
  build: {
    env: require('./prod.env'),
    app: path.resolve(__dirname, '../dist/app.html')
  }
}
```
然后我们就可以运行
```
npm run dev orgId
```
来启动项目了。


## jsZip

我们这里在`build done`之后会调用方法来进行文件压缩，我们在`zip/zipConfig.js`进行配置
```js
module.exports = {
  outPath:'./zipFile/', // 输出目录
  config: [
    {
      name : 'out', //压缩之后的压缩包名称
      file : [
        {
          fileName: 'index.html', // 压缩包内的文件名称
          filePath: './dist/index.html' // 文件路径
        },
        {
          fileName: 'app.js', // 压缩包内的文件名称
          filePath: './dist/static/js/app.js' // 文件路径
        },
        {
          fileName: 'vendor.js', // 压缩包内的文件名称
          filePath: './dist/static/js/vendor.js' // 文件路径
        },
        {
          fileName: 'manifest.js', // 压缩包内的文件名称
          filePath: './dist/static/js/manifest.js' // 文件路径
        },{
          fileName: 'app.css', // 压缩包内的文件名称
          filePath: './dist/static/css/app.css' // 文件路径
        }
      ],
    }
  ]
};
```
