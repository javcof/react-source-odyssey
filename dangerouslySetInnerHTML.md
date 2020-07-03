## dangerouslySetInnerHTML

`dangerouslySetInnerHTML` 是 React 提供了 innerHTML 的申明式的写法, `dangerouslySetInnerHTML` 是可能会引起 XSS 攻击的，React 在设计这个 API 的时候也是很用心的为我们标注了 `dangerouslySet*`.

在客户端渲染使用 `dangerouslySetInnerHTML` 动态注入 `<script>...</script>` 是不会执行的。服务器端渲染（SSR） 是可能产生 XSS 攻击的。

```js
var DANGEROUSLY_SET_INNER_HTML = 'dangerouslySetInnerHTML';

function setInitialDOMProperties(tag, domElement, rootContainerElement, nextProps, isCustomComponentTag) {
  for (var propKey in nextProps) {
    if (!nextProps.hasOwnProperty(propKey)) {
      continue;
    }

    var nextProp = nextProps[propKey];

    if (propKey === DANGEROUSLY_SET_INNER_HTML) {
      var nextHtml = nextProp ? nextProp['__html'] : undefined;

      if (nextHtml != null) {
        setInnerHTML(domElement, nextHtml);
      }
    }
  }
}

/**
 * Set the innerHTML property of a node
 *
 * @param {DOMElement} node
 * @param {string} html
 * @internal
 */

var setInnerHTML = createMicrosoftUnsafeLocalFunction(function (node, html) {
  if (node.namespaceURI === Namespaces.svg) {
    ...
  }

  node.innerHTML = html;
});
```
