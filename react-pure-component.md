### 摘要

- PureComponent 原型上会有 isPureReactComponent = true 属性
- PureComponent 会进行对 props, state 进行 **sCU** 优化 (浅比较)

### 定义

```js

/**
 * Base class helpers for the updating state of a component.
 */
function Component(props, context, updater) {
  this.props = props;
  ...
}

Component.prototype.isReactComponent = {};

// this.setState(...)
Component.prototype.setState = function(partialState, callback) {
  ...
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
}

// this.forceUpdate(...)
Component.prototype.forceUpdate = function(callback) {
  ...
}

/**
 * Convenience component with default shallow equality check for sCU.
 */
function PureComponent(props, context, updater) {
  this.props = props;
  ...
}

var pureComponentPrototype = PureComponent.prototype;
...

// PureComponent 原型会存在 Component 原型的属性
_assign(pureComponentPrototype, Component.prototype);

// PureComponent 原型上会有 isPureReactComponent 属性
pureComponentPrototype.isPureReactComponent = true;


```

### 检查是否需要更新

```js
function checkShouldComponentUpdate(workInProgress, ctor, oldProps, newProps, oldState, newState, nextContext) {
  var instance = workInProgress.stateNode;

  // 如果实例定义 shouldComponentUpdate 生命周期方法,则调用 shouldComponentUpdate
  if (typeof instance.shouldComponentUpdate === 'function') {
    ...
    var shouldUpdate = instance.shouldComponentUpdate(newProps, newState, nextContext);

    {
      // 只会检查 shouldComponentUpdate 有无返回值, 并不会检查返回值的类型?
      if (shouldUpdate === undefined) {
        // error
      }
    }

    return shouldUpdate;
  }

  // 如果原型上有 isPureReactComponent 属性, 则进行 ShallowEqual 比较 props, state
  if (ctor.prototype && ctor.prototype.isPureReactComponent) {
    return !shallowEqual(oldProps, newProps) || !shallowEqual(oldState, newState);
  }

  // 实例没有定义 shouldComponentUpdate 方法, 实例的原型上也没有 isPureReactComponent 属性, 则默认返回 true
  return true;
}
```

### 浅比较

```js
/**
 * Performs equality by iterating through keys on an object and returning false
 * when any key has values which are not strictly equal between the arguments.
 * Returns true when the values of all keys are strictly equal.
 */

function shallowEqual(objA, objB) {
  if (objectIs(objA, objB)) {
    return true;
  }

  if (typeof objA !== 'object' || objA === null || typeof objB !== 'object' || objB === null) {
    return false;
  }

  var keysA = Object.keys(objA);
  var keysB = Object.keys(objB);

  if (keysA.length !== keysB.length) {
    return false;
  } // Test for A's keys different from B.


  for (var i = 0; i < keysA.length; i++) {
    if (!hasOwnProperty$2.call(objB, keysA[i]) || !objectIs(objA[keysA[i]], objB[keysA[i]])) {
      return false;
    }
  }

  return true;
}
```