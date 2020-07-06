## defaultProps

`defaultProps` 是在 `createElement` 的时候处理的，如果 props 没有传递该属性，则会在 defaultProps 中去读取.
`key` 和 `ref` 虽然也是通过 props 传递给组件的，却被特殊处理了，作为独立的参数传递给 ReactElement 方法.

```js

var RESERVED_PROPS = {
  key: true,
  ref: true,
};

function createElement(type, config, children) {
  var propName;

  var props = {};
  var key = null;
  var ref = null;

  if (config != null) {
    // 过滤 key 和 ref 属性，其他的属性会赋值给 props 属性
    for (propName in config) {
      if (hasOwnProperty.call(config, propName) && !RESERVED_PROPS.hasOwnProperty(propName)) {
        props[propName] = config[propName];
      }
    }
  }

  // Resolve default props
  if (type && type.defaultProps) {
    var defaultProps = type.defaultProps;

    for (propName in defaultProps) {
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }

  return ReactElement(type, key, ref, self, source, ReactCurrentOwner.current, props);
}
```