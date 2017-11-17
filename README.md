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

## 渲染机智

1. config， 获取对应配置，拿到对应的`elementCode`去`define`接口找`elementInfoCodes`

- `search_render_info`， 搜索字段配置
- `list_render_info`，列表字段配置
- `detail_render_info`， 详情字段配置

2. define， 获取对应定义, `element_type/define`, 拿到对应的`elementInfoCodes`去`info`接口找`controllType`

3. info， 获取对应信息, `element_type/info`, 根据`controllType`去匹配组件类型

### 合并结构数据，返回结构化数据

```js
// api/utils.js

function getParam(parConfig, eleDefine, eleInfo) {
  const parConfigData = parConfig.data.content,
    eleDefineData = eleDefine.data.content,
    eleInfoData = eleInfo.data.content,
    fields = [],
    inputFields = [];
  if (!parConfigData || !parConfigData.length) {
    return false;
  }
  parConfigData.forEach((value, index) => {
    if (!eleDefineData[value.elementCode]) {
      console.error(`error, fieldConfigCode: ${value.fieldConfigCode} can not find elementInfoCodes`);
      return false;
    }
    if (!value.extraParams) {
      console.error(`error, fieldConfigCode: ${value.fieldConfigCode} can not find extraParams`);
      return false;
    }
    try {
      JSON.parse(value.extraParams)
    } catch (error) {
      console.error(value.extraParams, 'is not json, ', 'fieldConfigCode is', value.fieldConfigCode, );
      return false;
    }
    const elementCode = value.elementCode,
      fieldConfigCode = value.fieldConfigCode,
      elementInfoCodes = eleDefineData[elementCode].elementInfoCodes,
      event = eleDefineData[elementCode].event,
      extraParams = JSON.parse(value.extraParams),
      len = elementInfoCodes.length;
    value.extraParams = [];
    for (let i = 0; i < len; i++) {
      value.extraParams[i] = {};
      if ('required' in extraParams) {
        value.required = extraParams.required;
        value.extraParams[i].required = extraParams.required;
      }
      if ('detailLinkVisiable' in extraParams) {
        value.detailLinkVisiable = extraParams.detailLinkVisiable;
      }
      if ('area' in extraParams) {
        value.area = extraParams.area;
      }
    }
    for (let i = 1; i < len + 1; i++) {
      if (!extraParams) {
        console.error(`error, extraParams in fieldConfigCode: ${fieldConfigCode} is ${extraParams}`);
        return false;
      }
      Object.keys(extraParams).forEach((key) => {
        if (key.indexOf(i) > 0) {
          const param = key.replace(i, '');
          value.extraParams[i - 1][param] = extraParams[key];
        }
      });
      if (value.extraParams[i - 1]['inputKey']) {
        const inputKey = value.extraParams[i - 1]['inputKey'],
          index = inputKey.indexOf('.'),
          len = inputKey.length;
        inputFields.push({
          key: inputKey.slice(0, index),
          name: inputKey.slice(index + 1, len),
          field: value.extraParams[i - 1]['field']
        });
      }
      Object.keys(eleInfoData[elementInfoCodes[i - 1]]).forEach((key) => {
        value.extraParams[i - 1][key] = eleInfoData[elementInfoCodes[i - 1]][key];
      });
      //              self.requestList.push(value.extraParams[i - 1]);
      const type = value.extraParams[i - 1].controlType;
      if (type === 'checkbox' || type === 'radio') {
        value.extraParams[i - 1].value = [];
      } else if (type === 'select') {
        value.extraParams[i - 1].value = '';
        value.extraParams[i - 1].text = '';
      } else {
        value.extraParams[i - 1].value = '';
      }
      // value.extraParams[i - 1].showName = value.showName;
    }
    value.event = event;
    fields.push(value);
  });
  return {
    field: fields,
    inputKey: inputFields
  };
}
```
