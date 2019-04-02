
# React Naitve 组件生命周期

![life circle](/images/react-life-circle.png)

### 是否能使用setState

| 生命周期	 | 调用次数 | 能否使用 setSate() |
| ------ | ------ | ------ |
| getDefaultProps | 1(全局调用一次) | - |
| getInitialState | 1 | - |
| componentWillMount | 1 | 可以，但无意义<br>相当于constructor的this.state |
| render | >=1 | - |
| componentDidMount | 1 | 可以<br>初始render两遍 |
| componentWillReceiveProps | >=0 | 可以 |
| shouldComponentUpdate | >=0 | -（禁止，死循环） |
| componentWillUpdate | >=0 | -（禁止，死循环） |
| componentDidUpdate | >=0 | -（禁止，死循环） |
| componentWillUnmount | 1 | -（不生效而且无意义） |

<br>

### 参考
https://www.race604.com/react-native-component-lifecycle/
https://juejin.im/entry/5af0396351882567236eb022
