## dva入门

### 初始化环境配置

> > .roadhogrc 

```
{
  "entry": "src/index.js",
  "env": {
    "development": {
      "extraBabelPlugins": [
        "dva-hmr",
        "transform-runtime",
        ["import",{"libraryName":"antd","style":true}]
      ]
    },
    "production": {
      "extraBabelPlugins": [
        "transform-runtime",
        ["import",{"libraryName":"antd","style":true}]
      ]
    }
  }
}
```

### 初始化dva

```
import dva from 'dva';
import './index.css';

// 1. Initialize
const app = dva();

// 2. Plugins
// app.use({});

// 3. Model
// app.model(require('./models/example'));

// 4. Router
app.router(require('./router'));

// 5. Start
app.start('#root');
```

_ const app = dva()

 这部分是用来做dva初始化的部分 先给大家看一下完整的接口
