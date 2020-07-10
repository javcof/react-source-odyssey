React Elements 是我们构建应用的基础，我们通过定义的 Component（类组件和函数组件） 返回具体的 React Elements，React Element 是 immutable 的。

`createElement` 由 JSX 编译过来的, 真正创建 `React Element` 的是 `ReactElement` 工厂方法。

`createElement` 会做一些前置处理，从 `config` 中提取 `key` 和 `ref` 这种特殊属性，另外会合并 `defaultProps` 定义的属性

```js
var REACT_ELEMENT_TYPE = Symbol.for('react.element');

// Verifies the object is a ReactElement.
function isValidElement(object) {
  return typeof object === 'object' && object !== null && object.$$typeof === REACT_ELEMENT_TYPE;
}

export function createElement(type, config, children) {
  ...
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props,
  );
}

// 创建 React Elements 的工厂方法
const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    // 通过检查 $$typeof 的值，来判断是否是 React Element
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    type: type,
    key: key,
    ref: ref,
    props: props,

    // Record the component responsible for creating this element.
    _owner: owner,
  };

  // immutable 处理
  if (Object.freeze) {
    Object.freeze(element.props);
    Object.freeze(element);
  }

  return element
}
```