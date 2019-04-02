## 使用Component还是PureComponent
当`props`或者`state`改变时，`PureComponent`将对`props`和`state`进行浅比较。

```js
if (this._compositeType === CompositeTypes.PureClass) {
  shouldUpdate = !shallowEqual(prevProps, nextProps)
  || !shallowEqual(inst.state, nextStat e);
}
return shouldUpdate;
```

`PureComponent`提供了一个具有浅比较的`shouldComponentUpdate`方法，`shadowEqual`只会浅检查组件的`props`和`state`，仅做一层比较，嵌套对象和数组是不会被比较的。

### 参考
https://www.jianshu.com/p/d782aa30ca18
