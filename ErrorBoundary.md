## ErrorBoudary 
> 错误边界保护（页面级别）  
> 部分 UI 的 JavaScript 错误不应该导致整个应用崩溃  

> 自 React 16 起，任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载。

#### 实现
```ts
interface IProps {
    name: string;
    errorUI?: React.ReactNode;
}

interface IState {
    hasError: boolean;
}

export class ErrorBoundary extends React.Component<IProps, IState> {
    public state = {
        hasError: false
    }

    public componentDidCatch(error: any, info: any) {
        this.setState({
            hasError: true
        })
        logAction('error_boundary', {
            name: this.props.name,
            error,
            info
        })
        logMalFunction('error_boundary ' + this.props.name + ': ' + error)
    }

    public render() {
        if (this.state.hasError) {
            const { errorUI } = this.props
            if (errorUI) {
                return errorUI
            }

            return
        }
        return this.props.children
    }
}
```

#### API
```js
<ErrorBoundary name="error-component">
    <MayBeErrorComponent />
</ErrorBoundary>
```

### 参考
https://react.docschina.org/docs/error-boundaries.html  
