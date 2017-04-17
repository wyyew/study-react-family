## Redux开发者工具

1. Redux DevTools

```
const store = createStore(counter, componse(
  applyMiddleware(thiunk),
  window.devToolsExtension ? window.devToolsExtension() : f => f
));
```

2. React 开发者工具
