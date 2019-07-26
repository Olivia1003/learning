
### 数据缓存
分tab缓存数据：
1. 每次记录每个tab的reqParams，请求前与上一次这个tab的请求比较，若相同则无需请求  
2. 每次请求记录下每个tab的searchApi（Promise类型） 
3. 记录每个tab的serviceStatus，若serviceStatus='CONTINUE'，则abort上次请求（Promise.abort） 
4. 每个tab的数据分开存在store中，根据type/date分开存取  


### View缓存



```tsx
// tab每次被销毁和重建
// 每次切换，重新执行prevTab的componentWillUnmount和curTab的componentDidMount
this.state.isVisible ? <MyComponent /> : null
```

```tsx
// 初始所有tab都被创建
// 每次切换，重新执行所有tab的componentWillReceiveProps，并且每个tab重新render
<View style={!this.state.isVisible&&styles.hidden}>
    <MyComponent />
</View>
```

```tsx
// 维护lastViewMap<tabId,React.ReactNode>，用来缓存tabView视图
// 每次只有选中tab执行render，其他tabView不刷新
// 每个tabView的render延迟到第一次点击，被选中后第一次执行componentDidMount，之后被选中执行componentWillReceiveProps
// 不需要展示的tabView用 { display :'none' } 进行隐藏（未激活的tabView可能为undefined）

interface IState {
    currentTabId: number
    tabIdList: number[]
}
export default class Tab extends React.Component<IProps, IState>{
    private lastViewMap: Map<TabId, React.ReactNode> = new Map() // 用于缓存tabView视图

    constructor(props: any) {
        super(props)
    }

    private tabViewRenderMap = {
        1: this.renderTabViewA.bind(this),
        2: this.renderTabViewB.bind(this),
        3: this.renderTabViewC.bind(this),
    }

    private renderTabView = (tabId) => {
        const { currentTabId } = this.state
        if (currentTabId !== tabId) { // 不渲染当前选中的tabView时（隐藏的tabView），使用缓存中的view
            return this.lastViewMap.get(tabId)
        }
        const listView = this.tabViewRenderMap[tabId]()
        this.lastViewMap.set(tabId, listView) // 保存tabView视图
        return listView
    }

   public render() {
        const { currentTabId, tabIdList } = this.state
        const allTabView = tabIdList.map((tabId) => {
            return (
                <View key={`tab${tabId}`} style={!(currentTabId === tabId) && styles.hidden}>
                    {this.renderTabView(tabId)}
                </View>
            )
        })
        return (
            <View>
                {allTabView}
            </View>
        )
    }
}

const styles = StyleSheet.create({
    hidden: {
        display: 'none',
        opacity: 0,
        width: 0,
        height: 0
    }
})
```
