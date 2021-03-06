# Redux

数据共享框架，将一个项目的数据放到一个公共的数据存储空间，供所有组件使用，避免组件之间传值的单项数据流规则带来的不可维护性。

组件向store触发操作数据的指示(动作)，store根据这个动作(可能是获取数据，也有可能是修改数据等等)然后通过reducers对数据按照指令进行处理，将处理好的新数据告诉store，然后store将数据返回给组件。

模拟借书流程:

- 组件(`Component`):借书的人
- `Action Creators`:借书人的需求(即:需要借什么样的书)
- `Store`:图书管的管理员(负责管理各种书籍)
- `Reducers`:管理员手上的书籍记录

所以,Redux的工作流程就可以是这样,借书的人想要借书,所以他需要找到图书管理员,告诉他我需要什么样的书籍,图书管理员没有办法记住所有的书本信息,所以掏出了小本本找到借书人想要借的书,然后将这本书借给借书人.

```javascript
// 新建store文件夹，首先创建reducers.js
// state指的是整个store的数据
const defaultState = {}
// state指的是整个store的数据
export default (state = defaultState, action) => {
    return state
}
```

接着：

```react
// 新建index.js文件
import { createStore } from 'redux'
import reducer from './reducer'
// 创建一个状态，状态中包含了整个仓库的数据
const store = createStore(reducer);
export default store;
```

其中:`store`是唯一的,在`/store`文件下的`index.js`文件中,`reducer`拿到`store`中的数据进行深拷贝,对深拷贝后的数据进行处理,并返回,`store`拿到新的数据对自己本身存储的数据进行更新.

```javascript
// 在需要使用store的组件中引入store
import store from './store'

class Todolist extends Component {
    constructor() {
        super(props);
        // 获取store中的数据需要使用getState方法来获取
        this.state = store.getState();
    }
}
```

改变store中的数据：

```javascript
// 创建一个动作
const action = {
    type: 'change_input_value',
    value: e.target.value
}
// 调用store中的dispatch方法将动作告诉store
store.dispatch(action)
```

在reducers中

```javascript
export default (state = defaultState, action) => {
    if (action.type === 'change_ input_value') {
        // reducer不能直接修改state里面的数据，所以我们可以将state中的数据做一份深拷贝，修改拷贝过来的数据，然后将修改后的数据返回
        const newState = JSON.parse(JSON.stringify(state));
        newState.inputValue = action.value;
        return newState;
    }
    return state
}
```

接下来非常重要，因为这个时候store中的数据已经修改了，但是组件本身并不知道，所以我们需要在组件的`constructor`中订阅`store`的变化。

```javascript
store.subscribe(this.handleStoreChange);
handleStoreChange() {
    
    this.setState(store.getState());
}
```

> 1. store必须是唯一的
> 2. 保持状态只读
> 3. Reducer必须是纯函数，即给定输入一定会有输出,而且这个输出是固定的,而且这个函数不能有任何的副作用。

## Redux-thunk

将异步请求从组件中抽离，放到actions中。

```javascript
// index.js文件
import { createStore, applyMiddleware } from 'redux'
import reducer from 'redux-thunk'
import reducer from './reducer'

const store = createStore(
    reducer,
    // 使用这个函数调用中间件
    applyMiddleware(thunk)
);
export default store;
```

## React-Redux

+ Provider

  react-redux提供的一个组件，将要渲染的组件放入该组件，并且将store以属性的形式传入Provider组件，这样Provider中的组件就可以与store通信了。

  ```javascript
  // 在项目入口indexd.js文件中
  //...
  import { Provider } from 'react-redux'
  import store from './store'
  // 通过Provider组件跟store链接，方式就是通过属性传递，这样Provider中的子组件都能够使用store中的数据
  const App = (
  	<Provider store={store}>
      	<TodoList />
      </Provider>
  );
  ReactDOM.render(App, document.getElementById('root'))
  ```

+ connect

  ```javascript
  // ...
  import store from './store'
  import { connect } from 'react-redux'
  class TodoList extends Component {
      // ...
  }
  // mapStateToProps以及mapDisPatchToProps是store与这个组件的映射关系将store中的值映射到该组件的props属性中，
  const mapStateToProps = (state) => {
      return {
          inputValue = state.inputValue
      }
  }
  // 子组件的父组件因为通过Provider跟store进行了链接，那么子组件就可以通过connect链接store来获取store中的数据
  export default connect(mapStateToProps, null)(TodoList)
  
  ```
  
  connect第一个调用中的第一个参数return的对象是store里面的数据与该组件的对应关系。而第二个参数用来保存事件方法，组件通过这里的方法派发action，来修改store的数据。

## Redux写法优化

+ action提取

  因为action的值较多，而且是在组件的文件以及reducer中共用，为了防止由于书写导致的action不一致，可以将action的名字提取到一个js文件，然后统一暴露。

+ action的创建也不应该出现在组件中

  应该在store中新建一个文件，通过暴露函数的方式将新创建的action通过暴露的函数return出去，然后再组件中通过引入并调用这些函数来创建action。

+ 在当一个项目很大，数据量过多的时候，我们应该将reducer分布到不同的组件中去

  1. 在每个组件中新建`store`目录，然后再创建属于这个组件的reducer

  2. 在主store目录的reducer.js文件中使用combineReducers将不同组件的reducer进行整合

     ```javascript
     import { combineReducers } from 'redux';
     import headerReducer from '../common/header/store/reducer'
     
     export default combineReducers({
       header: headerReducer
     })
     
     ```

  3. 需要注意的是，这样整合之后，组件中使用的store数据会多一层，这个时候一定要在mapStateToProps中函数中按照整合后的reducer对应结果来使用store

     ```javascript
     // header组件中
     const mapStateToProps = (state) => {
       return {
         // 注意这里变成了state.header.focused，其中的header与步骤二中的header对应  
         focused: state.header.focused
       }
     ```
