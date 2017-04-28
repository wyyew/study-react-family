## Redux开发者工具

1. Redux DevTools

```
import { compose, applyMiddleware } from 'redux'
...

const store = createStore(counter, compose(
  applyMiddleware(thiunk),
  window.devToolsExtension ? window.devToolsExtension() : f => f
));
```

2. React 开发者工具
