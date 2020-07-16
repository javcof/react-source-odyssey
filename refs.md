React 提供了三种使用 ref 的方式：`string ref`, `callback ref`, `createRef`.

React 会在 `componentDidMount` 和 `componentDidUpdate` 生命周期前更新 DOM 和 refs.

## createRef
React.createRef 仅仅是创建了一个包含 current 属性的 seal 对象

```js
// an immutable object with a single mutable value
function createRef() {
  var refObject = {
    current: null
  };
  Object.seal(refObject);
  return refObject;
}
```

## commitAttachRef
React 更新 refs

```js
function commitAttachRef(finishedWork) {
  var ref = finishedWork.ref;

  if (ref !== null) {
    var instance = finishedWork.stateNode;
    var instanceToUse;

    switch (finishedWork.tag) {
      case HostComponent:
        instanceToUse = getPublicInstance(instance);
        break;

      default:
        instanceToUse = instance;
    }

    if (typeof ref === 'function') {
      // 1. callback ref
      ref(instanceToUse);
    } else {
      // 2. React.createRef
      ref.current = instanceToUse;
    }
  }
}
```