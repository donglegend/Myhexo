---
title: 关于应用中数据存储Store
date: 2020-04-15 17:24:43
categories: javascript
tags: store
---
如何更好地管理应用Store
<!-- more -->


## Stores(存储)
Store的主要职责是将 逻辑 和 状态从组件中移至一个独立的，可测试的单元，这个单元在javascript前端和后端中都可以使用

## 用户界面状态的store
至少两个 store 可以让绝大多数应用从中受益。 一个用于 UI 状态，一个或多个用于领域状态。 分离这两个 store 的优点是可以重用和测试领域状态，并且可以很好地在其他应用中重用它。 然而，UI 状态 store 对于你的应用来说通常非常特别。 但通常也很简单。 这个 store 通常没有太多的逻辑，但会存储大量的松散耦合的 UI 相关的信息。 这是理想状况下的，因为大多数应用在开发过程中会经常性地改变 UI 状态。

通常可以在 UI stores 中找到的:

- Session 信息
- 应用已经加载了的相关信息
- 不会存储到后端的信息
- 全局性影响 UI 的信息
    1. 窗口尺寸
    2. 可访问性信息
    3. 当前语言
    4. 当前活动主题
- 用户界面状态瞬时影响多个、毫不相关的组件:
    1. 当前选择
    2. 工具栏可见性, 等等
    3. 向导的状态
    4. 全局叠加的状态

这些信息开始作为某个特定组件的内部状态会是不错的选择(例如工具栏的可见性)。 但过了一段时间，你发现应用中的其他地方也需要这些信息。 您只需将状态移动到 UI 状态 store，而不是在组件树中向上推动状态，就像在普通的 React 应用中所做的那样。

对于同构应用程序，你可能还希望使用正常默认值提供这个 store 的存根实现，以便所有组件按预期呈现。 可以通过在应用中传递属性到组件树中或使用 mobx-react 包中的 Provider 和 inject 来分发 UI 状态 store。

store 示例 (使用 ES6 语法):
```js
import { observable, computed, asStructure } from 'mobx'
import jquery from 'jquery'

export class UiState {
    @observable language = 'en_US'
    @observable pendingRequestCount = 0

    // .struct 确保不会通知观察者，除非尺寸对象以深度相等的方式改变
    @observable.struct windowDimensions = {
        width: jquery(window).width(),
        height: jquery(window).height()
    }

    constructor() {
        jquery.resize(() => {
            this.windowDimensions = getWindowDimensions()
        })
    }

    @computed get appIsInSync() {
        return this.pendingRequestCount === 0
    }
}
```

## 领域store
你的应用应该包含一个或多个领域 store。 这些 store 存储你的应用所关心的数据。 待办事项、用户、书、电影、订单、凡是你能说出的。 你的应用很有可能至少有一个领域 store。

单个领域 store 应该负责应用中的单个概念。 然而，单个概念可以采取多个子类型的形式，并且它通常是(循环)树结构。 举例来说，一个领域 store 负责产品，一个负责订单。 根据经验来说，如果两个概念之间的关系的本质是包含的，则它们通常应在同一个 store 中。 所以说一个 store 只是管理 领域对象。

Store 的职责:
- 实例化领域对象， 确保领域对象知道它们所属的 store。
- 确保每个领域对象只有一个实例。 同一个用户、订单或者待办事项不应该在内存中存储两次。 这样，可以安全地使用引用，并确保正在查看的实例是最新的，而无需解析引用。 当调试时这十分快速、简单、方便。
- 提供后端集成，当需要时存储数据。
- 如果从后端接收到更新，则更新现有实例。
- 为你的应用提供一个独立、通用、可测试的组件。
- 要确保 store 是可测试的并且可以在服务端运行，你可能需要将实际的 websocket/http 请求移到单独的对象，以便你可以通过通信层抽象。
- Store 应该只有一个实例。

## 领域对象
每个领域对象应使用自己的类(或构造函数)来表示。 建议以非规范化形式存储数据。 不必把客户端应用的状态看做数据库的一种。真实引用、循环数据结构和实例方法都是 JavaScript 中非常强大的概念。允许领域对象直接引用来自其他 store 的领域对象。记住: 我们想保持我们的操作和视图尽可能简单，并且需要管理引用和自己做垃圾回收可能是一种倒退。 不同于其他 Flux 系统架构，使用 MobX 不需要对数据进行标准化，而且这使得构建应用本质上复杂的部分变得更简单: 你的业务规则、操作和用户界面。 

领域对象可以将其所有逻辑委托给它们所属的 store，如果这更符合你的应用的话。 可以将领域对象表示成普通对象，但类比普通对象有一些重要的优势:
- 它们可以有方法。 这使得领域概念更容易独立使用，并减少应用所需的上下文感知的数量。 只是传递对象。 你不需要传递 store，或者必须弄清楚哪些操作可以在对象上应用，如果它们只是作为实例方法可用
- 对于属性和方法的可见性，它们提供了细粒度的控制。
- 使用构造函数创建的对象可以自由地混合 observable 属性和函数，以及非 observable 属性和方法。
- 它们易于识别，并且可以进行严格的类型检查。

## 领域store示例
```js
import {observable, autorun} from 'mobx'
import uuid from 'node-uuid'

export class TodoStore {
    authorStore;
    transportLayer;
    @observable todos = [];
    @observable isLoading = true;

    constructor(transportLayer, authorStore) {
        this.authorStore = authorStore; // 可以为我们提供 author 的 store
        this.transportLayer = transportLayer; // 可以为我们发起服务端请求的东西
        this.transportLayer.onReceiveTodoUpdate(updatedTodo => {
            this.updateTodoFromServer(updatedTodo));
        })
        this.loadTodos();
    }

    /**
     * 从服务端拉取所有的 todo
     */
    loadTodos() {
        this.isLoading = true;
        this.transportLayer.fetchTodos().then(fetchedTodos => {
            fetchedTodos.forEach(json => this.updateTodoFromServer(json));
            this.isLoading = false;
        });
    }

    /**
     * 使用服务器中的信息更新 todo。保证一个 todo 只存在一次。
     * 可能构造一个新的 todo，更新现有的 todo,
     * 或删除 todo，如果它已经在服务器上被删除的话。
     */
    updateTodoFromServer(json) {
        var todo = this.todos.find(todo => todo.id === json.id);
        if (!todo) {
            todo = new Todo(this, json.id);
            this.todos.push(todo);
        }
        if (json.isDeleted) {
            this.removeTodo(todo);
        } else {
            todo.updateFromJson(json);
        }
    }

    /**
     * 在客户端和服务端都创建一个新的 todo
     */
    createTodo() {
        var todo = new Todo(this);
        this.todos.push(todo);
        return todo;
    }

    /**
     * 如果一个 todo 被删除了，将其从客户端内存中清理掉
     */
    removeTodo(todo) {
        this.todos.splice(this.todos.indexOf(todo), 1);
        todo.dispose();
    }
}

export class Todo {
    /**
     * todo 的唯一 id, 不可改变。
     */
    id = null;

    @observable completed = false;
    @observable task = "";

    /**
     * 引用一个 author 对象(来自 authorStore)
     */
    @observable author = null;

    store = null;

    /**
     * 指示此对象的更改是否应提交到服务器
     */
    autoSave = true;

    /**
     * 为自动存储此 Todo 的副作用提供的清理方法
     * 参见 @dispose.
     */
    saveHandler = null;

    @computed get asJson() {
        return {
            id: this.id,
            completed: this.completed,
            task: this.task,
            authorId: this.author ? this.author.id : null
        };
    }

    constructor(store, id=uuid.v4()) {
        this.store = store;
        this.id = id;

        this.saveHandler = reaction(
            // 观察在 JSON 中使用了的任何东西:
            () => this.asJson,
            // 如果 autoSave 为 true, 把 json 发送到服务端
            (json) => {
                if (this.autoSave) {
                    this.store.transportLayer.saveTodo(json);
                }
            }
        );
    }

    /**
     * 在客户端和服务端中删除此 todo
     */
    delete() {
        this.store.transportLayer.deleteTodo(this.id);
        this.store.removeTodo(this);
    }

    /**
     * 使用服务端信息更新此 todo
     */
    updateFromJson(json) {
        // 请确保我们的更改不会发送回服务器
        this.autoSave = false;
        this.completed = json.completed;
        this.task = json.task;
        this.author = this.store.authorStore.resolveAuthor(json.authorId);
        this.autoSave = true;
    }

    dispose() {
        // 清理观察者
        this.saveHandler();
    }
}
```

## 组合多个stores

一个经常被问到的问题就是，如何不使用单例来组合多个 stores 。它们之间如何通信呢？

一种高效的模式是创建一个 RootStore 来实例化所有 stores ，并共享引用。这种模式的优势是:
1. 设置简单
2. 很好的支持强类型
3. 使得复杂的单元测试变得简单，因为你只需要实例化一个根 store

示例：
```js
class RootStore {
  constructor() {
    this.userStore = new UserStore(this)
    this.todoStore = new TodoStore(this)
  }
}

class UserStore {
  constructor(rootStore) {
    this.rootStore = rootStore
  }

  getTodos(user) {
    // 通过根 store 来访问 todoStore
    return this.rootStore.todoStore.todos.filter(todo => todo.author === user)
  }
}

class TodoStore {
  @observable todos = []

  constructor(rootStore) {
    this.rootStore = rootStore
  }
}

当使用 React 时，这个根 store 通常会通过使用 <Provider rootStore={new RootStore()}><App /></Provider> 来插入到组件树之中。

```

[原文链接](https://cn.mobx.js.org/best/store.html)