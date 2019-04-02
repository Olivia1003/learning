<!-- 2019/03/07 -->

# React Hooks

>无状态组件（Function）还是有状态组件（Class）

>Hook 让我们的函数变成了一个有状态的函数。


```js
import { useState } from 'react';

function Example() {
const [count, setCount] = useState(0);

return (
    <div>
    <p>You clicked {count} times</p>
    <button onClick={() => setCount(count + 1)}>
        Click me
    </button>
    </div>
);
}
```

<br>

### 多个状态

```js
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

<br>

### 记住状态
通常来说我们在一个函数中声明的变量，当函数运行完成后，这个变量也就销毁了（这里我们先不考虑闭包等情况）。不管我们反复调用add函数多少次，结果都是1。
```js
function add(n) {
    const result = 0;
    return result + 1;
}
add(1); //1
add(1); //1
```
但是，Example函数每次执行的时候，都是拿的上一次执行完的状态值作为初始值？答案是：是react帮我们记住的。

<br>

### Effect Hooks
```js
import { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // 一定注意下这个顺序：告诉react在下次重新渲染组件之后，同时是下次调用ChatAPI.subscribeToFriendStatus之前执行cleanup
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```
这种解绑的模式跟`componentWillUnmount`不一样。`componentWillUnmount`只会在组件被销毁前执行一次而已，而`useEffect`里的函数，**每次组件渲染后都会执行一遍**，包括副作用函数返回的这个清理函数也会重新执行一遍。

<br>

### 跳过一些不必要的副作用函数
```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 只有当count的值发生变化时，才会重新执行`document.title`这一句
```

<br>

### 参考
https://segmentfault.com/a/1190000016950339
