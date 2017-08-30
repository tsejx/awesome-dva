# dva

tags: dva

---

[TOC]

## 1.dva简介

### dva 是什么

dva 是阿里前端架构师 sorrycc 带 team 研发的一套轻量级前端框架，其目的是尽量避免前端重复性劳动，简化开发流程。

实际上也确实如此，一个简单的 CRUD 模块只需要1-2个小时即可完成前端作业。

### 1.1-dva 脚手架是什么

前面说到 dva 是一个框架，那么 dva 脚手架就是这个框架的具体表现。

我们习惯于称：创建一个 dva 脚手架，实际上就是创建一个项目目录。

### 1.2-脚手架都包含什么

一个完整的 dva 脚手架应该包含以下内容：

 - 自动创建一个包含 package.json 的项目。
 - 自动创建成体系的目录结构。
 - 自动安装项目需要的基础包。
 - 集成代码检查工具 ESLint。
 - 集成模拟接口工具 Mock。
 - 集成服务启动打包工具 Roadhog。
 - 集成版本控制工具 Git。

### 1.3-工具 & 环境 & 学习资料

1. 工具： dva-cli 脚手架
2. 环境：`nodejs 6.11.1` & `npm 5.3.0`
3. 学习教程：[dva-cli搭建react项目user-dashboard实践教程][1]


## 2.dva项目结构

React项目的推荐目录结构（如果使用dva脚手架创建，则自动生成如下）

    react-demo
    |── /mock/             # 数据mock的接口文件  
    |── /src/              # 项目源码目录（我们开发的主要工作区域）
    |   |── /assets        # 静态文件
    |   |── /components/   # 项目组件（用于路由组件内引用的可复用组件）   
    |   |── /routes/       # 路由组件（页面维度） 
    |   |  |── route1.js  
    |   |  |── route2.js   # 根据router.js中的映射，在不同的url下，挂载不同的路由组件
    |   |  └── route3.js    
    |   |── /models/       # 数据模型（可以理解为store，用于存储数据与方法）  
    |   |  |── model1.js  
    |   |  |── model2.js   # 选择分离为多个model模型，是根据业务实体进行划分
    |   |  └── model3.js  
    |   |── /services/     # 数据接口（处理前台页面的ajax请求，转发到后台）   
    |   |── /utils/        # 工具函数（工具库，存储通用函数与配置参数）     
    |   |  └── request.js  # dva/fetch
    |   |── router.js      # 路由配置（定义路由与对应的路由组件）  
    |   |── index.js       # 入口文件  
    |   |── index.less     # 入口文件样式表
    |   └── index.html     # 入口文件静态页
    |── package.json       # 项目信息  
    └── proxy.config.js    # 数据mock配置

### 2.1-index.js文件介绍

* 创建dva实例的五部曲

```javascript
// 引入dva库
import dva from 'dva';
// 引入dva的路由库
import { useRouterHistory, browserHistory, hashHistory } from 'dva/router';

// 1.Initialize
// 初始化，创建dva实例
const app = dva({history: browserHistory});

// 2.Plugins
// 使用插件
app.use(Hooks)

// 3.Model
// 在index.js中注册models文件夹下的所有用到的model
// 之前提到过，redux的一大原则就是唯一数据源，即单个应用内只有一个store，状态是一个树形对象
// 功能不同的业务实体分别挂在到这一唯一store下，是二级元素
// 注册后才能将model1、model2、model3中的state与函数等属性，挂载到全局唯一的store中去
app.model(require('./models/model1'));
app.model(require('./models/model2'));
app.model(require('./models/model3'));

// 4.Router
// 注册路由文件
app.router(require('./router'));

// 5.Start
// 将dva实例绑定到DOM树中的#root元素，意为所有的react-dom都加载于root节点下
app.start('#root');
```

### 2.2-router.js文件介绍

包含应用中的路由映射，`url ==> 路由组件route`

```javascript
import React, { PropTypes } from 'react';
import { Router, Route, IndexRoute, IndexRedirect } from 'dva/router';

import App from './routes/App';
import Login from './routes/Login';

export default function({ history }) {
  return (
    <Router history={history}>
      <Route path={`/`} component={App}>
        // 添加一级子路由
        <IndexRoute component={Login} />   // 指定默认首页页面
        <Route path={`login`} component={Login} />   
        <Route path={`route1`} component={route1} />
        // 添加二级子路由 
        <Route path={`route1/subroute`} component={route1} />          
        <Route path={`route2`} component={route2} >
        <Route path={`route3`} component={route3} />
        </Route>
      </Route>
    </Router>
  );
};
```

### 2.3-dva五部曲详解

#### 2.3.1-const app = dva()

这部分是用来做dva初始化的部分，完整的接口如下

```javascript
const app = dva({
    history,
    initialState,
    onError,
    onAction,
    onStateChange,
    onReducer,
    onEffect,
    onHmr,
    extraReducers,
    extraEnhancers,
})
```

* 在这里，你可以设置全局state，全部error，还有包括router的事件，state的事件等等。
* 都可以直接统一的在这边进行设置与管理
* 还有history这个参数是从react-router中来的

常用的history有三种形式，但是你也可以使用 React Router实现自定义的history。

* browserHistory
* hashHistory
* createMemoryHistory

**相关链接**

 - [react-router][2]
 - [dva初始化][3]

#### 2.3.2-app.use

`app.use(hooks)`

配置hooks或者注册插件。(插件最终返回的是hooks)

比如注册dva-loading插件的例子：

```javascript
import createLoading from 'dva-loading'
...
app.use(createLoading(opts));
```

#### 2.3.3-app.model

这个是用来接收你发送的action的

```javascript
app.model({
  namespace: 'todo',
  state: [],
  reducer: {
    add(state,{ payload: todo }) {
      // 保存数据到 state
      return [...state,todo];
    },
  },
  effects: {
    *save({ payload: todo},{ put, call }){
      // 调用saveTodoToServer,成功后触发`add` action保存到 state
      yield call(saveTodoToServer, todo);
      yield put({type: 'add', payload: todo });
    },
  },
  subscription: {
    setup({ history, dispatch }){
      // 监听 history 变化，当进入 `/` 时触发 `load` action
      return history.listen(({ pathname}) => {
        if( pathname === '/'){
          dispatch({type: 'load'});
        }
      });
    },
  },
})
```

#### 2.3.4-app.router

在这里面，进行你所有页面的初始化路由设置

写法1:

```javascript
<Router>
  <Route path='/' component={App}>
    <Route path='about' component={About} />
    <Route path='inbox' component={Inbox}>
      <Route path='message/:id' component={Message} />
    </Route>
  </Route>
</Router>
```

写法2:
```javascript
const CourseRoute = {
  path: 'course/:courseId',
  
  getChildRoutes(location, callback){
    require.ensure([], function (require){
      callback(null, [
        require('./routes/Announcements'),
        require('./routes/Assignments'),
        require('./routes/Grades'),
      ])
    })
  },
  
  getIndexRoute(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Index'))
    })
  },
  
  getComponents(location, callback){
    require.ensure([], function(require){
      callback(null, require('./components/Course'))
    })
  }
}
```

下面这种是按需加载的，所有性能会比上面的那种，要高得多，尤其是你的页面比较重的时候。 

#### 2.3.5-app.start()
 
## 3.dva框架下的react项目的组件设计

### 3.1-抽离Model

此处的Model包含了一个业务实体的状态，以及方法。model与java的class其实很像，包含了`自有变量(state)`，以及`自有方法(effects)`，不容许外界改变自己的私有变量，但可以在其他地方通过调用`Model内部的方法(effects)`，来`修改model的变量值(在effect中调用reducer)`

抽离Model，根据设计页面需求，设计相应的Model

教程中的需求是一个用户数据的表单展示，包含了增删改查等功能

提出users模型

```javascript
// models/users.js
// version1: 从数据维度抽取，更适用于无状态的数据
// version2: 从业务状态抽取，将数据与组件的业务状态统一抽离成一个model
// 新增部分为在数据维度基础上，改为从业务状态抽取而添加的代码
export default {
  namespace: 'users',
  state: {
    list: [],
    total: null,
+   loading: false, // 控制加载状态
+   current: null, // 当前分页信息
+   currentItem: {}, // 当前操作的用户对象
+   modalVisible: false, // 弹出窗的显示状态
+   modalType: 'create', // 弹出窗的类型（添加用户，编辑用户）
  },

    // 异步操作
    effects: {
        *query(){},
        *create(){},
        *'delete'(){},   // 因为delete是关键字，特殊处理
        *update(){},
    },

    // 替换状态树
    reducers: {
+       showLoading(){}, // 控制加载状态的 reducer
+       showModel(){}, // 控制 Model 显示状态的 reducer
+       hideModel(){},
        querySuccess(){},
        createSuccess(){},
        deleteSuccess(){},
        updateSuccess(){},
    }
}
```

### 3.2-设计组件

两种组件概念：容器组件与展示组件

* 容器组件：具有监听数据行为的组件，职责是绑定相关联的 model 数据，包含子组件；传入的数据来源于model

```javascript
import React, { Component, PropTypes } from 'react';

// dva 的 connect 方法可以将组件和数据关联在一起
import { connect } from 'dva';

// 组件本身
const MyComponent = (props)=>{};

// propTypes属性，用于限制props的传入数据类型 // 检验数据类型
MyComponent.propTypes = {};

//===============================================================================================
// 写法一
// 声明模型传递函数，用于建立组件和数据的映射关系
// 实际表示 将ModelA这一个数据模型，绑定到当前的组件中，则在当前组件中，随时可以取到ModelA的最新值
// 可以绑定多个Model
function mapStateToProps({ModelA}) {
  return {ModelA};
}

// 关联 model
// 正式调用模型传递函数，完成模型绑定
export default connect(mapStateToProps)(MyComponent);

// ==============================================================================================
// 写法二
export default connect( ModelA => ModelA )(MyComponent);

```

* 展示组件：展示通过 props 传递到组件内部数据；传入的数据来源于容器组件向展示组件的props

```javascript
import React, { Component, PropTypes } from 'react';

// 组件本身
// 所需要的数据通过 Container Component 通过 props 传递下来
const MyComponent = (props)=>{}
MyComponent.propTypes = {};

// 并不会监听数据
export default MyComponent;
```

### 3.3-设置路由

```javascript
// .src/router.js
import React, { PropTypes } from 'react';
import { Router, Route } from 'dva/router';
import Users from './routes/Users';

export default function({ history }) {
  return (
    <Router history={history}>
      <Route path="/users" component={Users} />
    </Router>
  );
};
```

容器组件雏形

```javascript
// .src/routes/Users.jsx
import React, { PropTypes } from 'react';

function Users() {
  return (
    <div>User Router Component</div>
  );
}

export default Users;
```

### 3.4-启动项目

```javascript
1. npm start启动项目

2. 浏览器打开localhost:8000/#/users 查看新增路由与路由中的组件
```

### 3.5-设计容器组件

自顶向下的设计方法：先设计容器组件，再逐步细化内部的展示容器

组件的定义方式：

```javascript
// 方法一： es6 的写法，当组件设计react生命周期时，可采用这种写法
// 具有生命周期的组件，可以在接收到传入数据变化时，自定义执行方法，有自己的行为模式
// 比如在组件生成后调用xx请求(componentDidMount)、可以自己决定要不要更新渲染(shouldComponentUpdate)等
class App extends React.Component({});

// 方法二： stateless 的写法，定义无状态组件
// 无状态组件，仅仅根据传入的数据更新，修改自己的渲染内容
const App = (props) => ({});
```

容器组件

```javascript
// ./src/routes/Users.jsx
import React, { Component, PropTypes } from 'react';

// 引入展示组件 （暂时都没实现）
import UserList from '../components/Users/UserList';
import UserSearch from '../components/Users/UserSearch';
import UserModal from '../components/Users/UserModal';

// 引入css样式表
import styles from './style.less'

function Users() {

  // 向userListProps中传入静态数据
  const userSearchProps = {};
  const userListProps = {
    total: 3,
    current: 1,
    loading: false,
    dataSource: [
      {
        name: '张三',
        age: 23,
        address: '成都',
      },
      {
        name: '李四',
        age: 24,
        address: '杭州',
      },
      {
        name: '王五',
        age: 25,
        address: '上海',
      },
    ],
  };
  const userModalProps = {};

  return (
    <div className={styles.normal}>
      {/* 用户筛选搜索框 */}
      <UserSearch {...userSearchProps} />
      {/* 用户信息展示列表 */}
      <UserList {...userListProps} />
      {/* 添加用户 & 修改用户弹出的浮层 */}
      <UserModal {...userModalProps} />
    </div>
  );
}

// 很关键的对外输出export；使外部可通过import引用使用此组件
export default Users;
```

展示组件UserList

```javascript
// ./src/components/Users/UserList.jsx
import React, { Component, PropTypes } from 'react';

// 采用antd的UI组件
import { Table, message, Popconfirm } from 'antd';

// 采用 stateless 的写法
const UserList = ({
    total,
    current,
    loading,
    dataSource,
}) => {
  const columns = [{
    title: '姓名',
    dataIndex: 'name',
    key: 'name',
    render: (text) => <a href="#">{text}</a>,
  }, {
    title: '年龄',
    dataIndex: 'age',
    key: 'age',
  }, {
    title: '住址',
    dataIndex: 'address',
    key: 'address',
  }, {
    title: '操作',
    key: 'operation',
    render: (text, record) => (
      <p>
        <a onClick={()=>{}}>编辑</a>
         
        <Popconfirm title="确定要删除吗？" onConfirm={()=>{}}>
          <a>删除</a>
        </Popconfirm>
      </p>
    ),
  }];

  // 定义分页对象
  const pagination = {
    total,
    current,
    pageSize: 10,
    onChange: ()=>{},
  };


  // 此处的Table标签使用了antd组件，传入的参数格式是由antd组件库本身决定的
  // 此外还需要在index.js中引入antd  import 'antd/dist/antd.css'
  return (
    <div>
      <Table
        columns={columns}
        dataSource={dataSource}
        loading={loading}
        rowKey={record => record.id}
        pagination={pagination}
      />
    </div>
  );
}

export default UserList;
```

### 3.6-添加Reducer

在整个应用中，**只有model中的reducer函数可以直接修改自己所在model的state参数**，其余都是非法操作；并且必须使用`return {...state}`的形式进行修改

#### 3.6.1-第一步：实现实现Reducer函数

```javascript
// models/users.js
// 使用静态数据返回，把userList中的静态数据移到此处
// querySuccess这个action的作用在于，修改了model的数据
export default {
  namespace: 'users',
  state： {}，
  subscriptions: {},
  effects: {},
  reducers: {
    querySuccess(state){
        const mock = {
          total: 3,
          current: 1,
          loading: false,
          list: [
            {
              id: 1,
              name: '张三',
              age: 23,
              address: '成都',
            },
            {
              id: 2,
              name: '李四',
              age: 24,
              address: '杭州',
            },
            {
              id: 3,
              name: '王五',
              age: 25,
              address: '上海',
            },
          ]
        };
        // return 的内容是一个对象，涵盖原state中的所有属性，以实现“更新替换”的效果
        return {...state, ...mock, loading: false};
      }
  }
}
```

#### 3.6.2-第二步：关联Model中的数据源

```javascript
// routes/Users.jsx

import React, { PropTypes } from 'react';

// 最后用到了connect函数，需要在头部预先引入connect
import { connect } from 'dva';

function Users({ location, dispatch, users }) {

  const {
    loading, list, total, current,
    currentItem, modalVisible, modalType
    } = users;

  const userSearchProps={};

  // 使用传入的数据源，进行数据渲染
  const userListProps={
    dataSource: list,
    total,
    loading,
    current,
  };
  const userModalProps={};

  return (
    <div className={styles.normal}>
      {/* 用户筛选搜索框 */}
      <UserSearch {...userSearchProps} />
      {/* 用户信息展示列表 */}
      <UserList {...userListProps} />
      {/* 添加用户 & 修改用户弹出的浮层 */}
      <UserModal {...userModalProps} />
    </div>
  );
}

// 声明组件的props类型
Users.propTypes = {
  users: PropTypes.object,
};

// 指定订阅数据，并且关联到users中
function mapStateToProps({ users }) {
  return {users};
}

// 建立数据关联关系
export default connect(mapStateToProps)(Users);
```

#### 3.6.3-第三步：通过发起Action，在组件中获取Model中的数据流向

```javascript
// models/users.js
// 在组件生成后发出action，示例：
componentDidMount() {
  this.props.dispatch({
    type: 'model/action',     // type对应action的名字
  });
}

// 在本次实践中，在访问/users/路由时，就是我们获取用户数据的时机
// 因此把dispatch移至subscription中
// subcription，订阅(或是监听)一个数据源，然后根据条件dispatch对应的action
// 数据源可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等  
// 此处订阅的数据源就是路由信息，当路由为/users，则派发'querySuccess'这个effects方法
subscriptions: {
    setup({ dispatch, history }) {
      history.listen(location => {
        if (location.pathname === '/users') {
          dispatch({
            type: 'querySuccess',
            payload: {}
          });
        }
      });
    },
  },
```

#### 3.6.4-第四步： 在index.js中添加models

```javascript
// model必须在此完成注册，才能全局有效
// index.js
app.model(require('./models/users.js'));
```

### 3.7-添加Effects

* Effects的作用在于**处理异步函数**，控制数据流程。
* 因为在真实场景中，数据都来自服务器，需要在发起异步请求获得返回值后再设置数据，更新state。
* 因此我们往往在Effects中调用reducer

个人理解: 以java类做类比，effects相当于public函数，可以被外部调用，而reducers相当于private函数；当effects被调用时，间接调用到了reducer函数，修改model中的state。当然effects的核心在于异步调用，处理异步请求（如ajax请求）。

```javascript
export default {
  namespace: 'users',
  state： {}，
  subscriptions: {},
  effects: {
    // 添加effects函数
    // call与put是dva的函数
    // call调用执行一个函数
    // put则是dispatch执行一个action
    // select用于访问其他model
    *query({ payload }, { select, call, put }) {
        yield put({ type: 'showLoading' });
        const { data } = yield call(query);
        if (data) {
          yield put({
            type: 'querySuccess',
            payload: {
              list: data.data,
              total: data.page.total,
              current: data.page.current
            }
          });
        }
      },
    },
  reducers: {}
}



// 添加请求处理   包含了一个ajax请求
// models/users.js
import request from '../utils/request';
import qs from 'qs';
async function query(params) {
  return request(`/api/users?${qs.stringify(params)}`);
}
```

### 3.8-把请求处理分离到service中

用意在于分离(可复用的)ajax请求

```javascript
// services/users.js
import request from '../utils/request';
import qs from 'qs';
export async function query(params) {
  return request(`/api/users?${qs.stringify(params)}`);
}

// 在models中引用
// models/users.js
import {query} from '../services/users';
```

## 4.dva框架下的react项目的数据流通

数据的改变发生通常是通过用户交互行为或者浏览器行为（如路由跳转等）触发的，当此类行为会改变数据的时候可以通过 `dispatch` 发起一个 `action`，如果是同步行为会直接通过 `Reducers` 改变 `State` ，如果是异步行为（副作用）会先触发 `Effects` 然后流向 `Reducers` 最终改变 `State`，所以在 dva 中，数据流向非常清晰简明，并且思路基本跟开源社区保持一致（也是来自于开源社区）。

![dva中的数据流向][4]

### 4.1-Model

#### 4.1.1-State

`type State = any`

State 表示 Model 的状态数据，通常表现为一个 javascript 对象（当然它可以是任何值）；操作的时候每次都要当作不可变数据（immutable data）来对待，保证每次都是全新对象，没有引用关系，这样才能保证 State 的独立性，便于测试和追踪变化。

在 dva 中你可以通过 dva 的实例属性 `_store` 看到顶部的 state 数据，但是通常你很少会用到:

```javascript
const app = dva();
console.log(app._store); // 顶部的 state 数据
```

#### 4.1.2-Action

`type AsyncAction = any`

Action 是一个普通 javascript 对象，它是改变 State 的唯一途径。无论是从 UI 事件、网络回调，还是 WebSocket 等数据源所获得的数据，最终都会通过 dispatch 函数调用一个 action，从而改变对应的数据。action 必须带有 type 属性指明具体的行为，其它字段可以自定义，如果要发起一个 action 需要使用 dispatch 函数；需要注意的是 dispatch 是在组件 connect Models以后，通过 props 传入的。

```javascript
dispatch({
  type: 'add',
});
```

#### 4.1.3-dispatch 函数

`type dispatch = (a: Action) => Action`

dispatching function 是一个用于触发 action 的函数，action 是改变 State 的唯一途径，但是它只描述了一个行为，而 dipatch 可以看作是触发这个行为的方式，而 Reducer 则是描述如何改变数据的。

在 dva 中，connect Model 的组件通过 props 可以访问到 dispatch，可以调用 Model 中的 Reducer 或者 Effects，常见的形式如：

```javascript
dispatch({
  type: 'user/add', // 如果在 model 外调用，需要添加 namespace
  payload: {}, // 需要传递的信息
});
```

#### 4.1.4-Reducer

`type Reducer<S, A> = (state: S, action: A) => S`

Reducer（也称为 reducing function）函数接受两个参数：之前已经累积运算的结果和当前要被累积的值，返回的是一个新的累积结果。该函数把一个集合归并成一个单值。

Reducer 的概念来自于是函数式编程，很多语言中都有 reduce API。如在 javascript 中：

```javascript
[{x:1},{y:2},{z:3}].reduce(function(prev, next){
    return Object.assign(prev, next);
})
//return {x:1, y:2, z:3}
```

在 dva 中，reducers 聚合积累的结果是当前 model 的 state 对象。通过 actions 中传入的值，与当前 reducers 中的值进行运算获得新的值（也就是新的 state）。需要注意的是 Reducer 必须是纯函数，所以同样的输入必然得到同样的输出，它们不应该产生任何副作用。并且，每一次的计算都应该使用immutable data，这种特性简单理解就是每次操作都是返回一个全新的数据（独立，纯净），所以热重载和时间旅行这些功能才能够使用。

#### 4.1.5-Effect

Effect 被称为副作用，在我们的应用中，最常见的就是异步操作。它来自于函数编程的概念，之所以叫副作用是因为它使得我们的函数变得不纯，同样的输入不一定获得同样的输出。

dva 为了控制副作用的操作，底层引入了redux-sagas做异步流程控制，由于采用了generator的相关概念，所以将异步转成同步写法，从而将effects转为纯函数。至于为什么我们这么纠结于 纯函数，如果你想了解更多可以阅读Mostly adequate guide to FP，或者它的中文译本JS函数式编程指南。

#### 4.1.6-Subscription

Subscriptions 是一种从 源 获取数据的方法，它来自于 elm。

Subscription 语义是订阅，用于订阅一个数据源，然后根据条件 dispatch 需要的 action。数据源可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等。

```javascript
import key from 'keymaster';
...
app.model({
  namespace: 'count',
  subscriptions: {
    keyEvent(dispatch) {
      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) });
    },
  }
});
```

### 4.2-Router

这里的路由通常指的是前端路由，由于我们的应用现在通常是单页应用，所以需要前端代码来控制路由逻辑，通过浏览器提供的 History API 可以监听浏览器url的变化，从而控制路由相关操作。

dva 实例提供了 router 方法来控制路由，使用的是react-router。

```javascript
import { Router, Route } from 'dva/router';
app.router(({history}) =>
  <Router history={history}>
    <Route path="/" component={HomePage} />
  </Router>
);
```

### 4.3-React Components

在组件设计方法中，我们提到过 Container Components，在 dva 中我们通常将其约束为 Route Components，因为在 dva 中我们通常以页面维度来设计 Container Components。

所以在 dva 中，通常需要 connect Model的组件都是 Route Components，组织在`/routes/`目录下，而`/components/`目录下则是纯组件（Presentational Components）。

## 5.数据流通的步骤注解

1.项目入口文件：index.js
2.根据浏览器中访问的url到router.js中查找对应的Route组件，完成初始的页面渲染
3.在组件被初始渲染时，依次调用以下方法：

    当组件在客户端被实例化，第一次被创建时，以下方法依次被调用：  
    1、getDefaultProps       // 获取默认props值，对于每个组件实例来讲，这个方法只会调用一次
    2、getInitialState       // 获取初始私有state
    3、componentWillMount    // 在组件生成前执行其中的方法
    4、render                // 组件渲染
    5、componentDidMount     // 组件生成后调用其中的方法
    
    //在componentDidMount函数执行时，已经渲染出真实的DOM，可以通过this.refs.[refName]获取真实DOM


在选择性完成以上几个生命钩子后，组件已经正式渲染完成，可以与用户完成交互

4.可以为渲染出的React-DOM元素绑定交互事件，将用户的请求转为dispatch，通过dispatch对象到对应的model中寻找effects方法；

    // 以点击事件为例
    // 点击后，通过全局唯一的dispatch对象，向model层派发一个action
    onclick = () => {
      const dispatch = this.props.dispatch;    // 获取全局的派发对象 dispatch
      dispatch({
        type: 'model1/effect1',   // type： 在名为'model1'的数据模型中查找effects属性中的'effect1'函数
        payload:  { key: value }  // payload：传入当前type的参数对象
      })
    }
    
5.组件完成渲染后，组件处于存在期，当因为用户的操作或其他，导致与当前组件绑定的model.state或组件内的state发生改动时，会依次调用以下方法，对组件进行重新渲染（此轮步骤可多次循环）：

    1、componentWillReceiveProps    // 当前组件绑定的model.state或组件内的state发生改动时，调用其中方法
    2、shouldComponentUpdate     // 判断是否进行重新渲染
    3、componentWillUpdate       // 在组件接收到了新的 props 或者 state 即将进行重新渲染前，执行
    4、render                    // 重新渲染
    5、componentDidUpdate        // 组件重新渲染之后调用

6.切换url时，对应的路由组件随之更换，组件被销毁，在组件销毁前执行以下操作：

    componentWillUnmount   // 在组件销毁之前执行，比如用于解绑事件监听、计时器等


参考资料：
1. [react初次实践总结][5]
2. [dva搭建简易react项目实践总结][6]
3. [dva中文文档][7]
4. [dva英文文档][8]
5. [使用dva时需要使用到的ES6技术][9]


  [1]: https://github.com/dvajs/dva-docs/tree/master/v1/zh-cn/tutorial
  [2]: https://react-guide.github.io/react-router-cn/docs/guides/basics/Histories.html
  [3]: https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md
  [4]: https://camo.githubusercontent.com/c826ff066ed438e2689154e81ff5961ab0b9befe/68747470733a2f2f7a6f732e616c697061796f626a656374732e636f6d2f726d73706f7274616c2f505072657245414b62496f445a59722e706e67
  [5]: http://www.jianshu.com/p/3f020ec08714
  [6]: http://www.jianshu.com/p/092d107b8d72
  [7]: https://github.com/dvajs/dva/blob/master/README_zh-CN.md
  [8]: http://redux.js.org/docs/Glossary.html
  [9]: https://github.com/dvajs/dva-knowledgemap