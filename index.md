# pjax

## pjax是什么？

pjax，全名：pushState + ajax

是基于jQuery的一个开源插件项目，它通过ajax和pushState技术提供了极速的（无刷新ajax加载）浏览体验，并且保持了真实的地址、网页标题，浏览器的后退（前进）按钮也可以正常使用。

pjax的工作原理是通过ajax从服务器端获取HTML，在页面中用获取到的HTML替换指定容器元素中的内容。然后使用pushState技术更新浏览器地址栏中的当前地址。以下两点原因决定了pjax会有更快的浏览体验：

- 不存在页面资源（js/css）的重复加载和应
- 如果服务器端配置了pjax，它可以只渲染页面局部内容，从而避免服务器渲染完整布局的额外开销

## 项目现状

jquery-pjax的维护方向：可能会继续修复重要的bug，但其功能不会再发生变化，即不会再实现新功能，也不会再扩展现有功能

## 安装

pjax依赖于jQuery 1.8或者更高版本

### 通过npm安装

```shell script
$ npm install jquery-pjax
```

### 通过js引入

下载**jquery.pjax.js**并在页面中引用：

```html
<script src="/statics/jquery.pjax.js"></script>
```

下载地址：[github地址](https://github.com/defunkt/jquery-pjax)

## 方法

### $.fn.pjax

最简单常见的pjax使用方法如下：

```javascript
$(document).pjax('a', '#pjax-container')
```

通过这种方式可以让页面中所有的链接都实现pjax加载，并指定<strong>id为pjax-container的元素</strong>作为容器元素

如果您正在迁移已有网站，可能不希望在每个地方都使用pjax。那么您可以用<strong>data-pjax</strong>来注明这是一个pjax链接，然后使用<strong>a[data-pjax]</strong>来代替全局选择器**a**。或者，您也可以使用在<strong>&lt;div data-pjax&gt;</strong>容器中的<strong>&lt;a data-pjax href="..."&gt;</strong>链接作为选择器

```javascript
$(document).pjax('[data-pjax] a, a[data-pjax]', '#pjax-container')
```

#### 服务器端配置

理论上，您在服务器端可通过检查指定的<strong>X-PJAX</strong>HTTP头来识别pjax请求，并且只渲染指定的HTML内容，这也就意味着在浏览器端我们不用重新渲染整个页面，只替换指定容器元素（在我们的示例中是<strong>#pjax-container</strong>）中的内容即可。下面的示例是在Ruby on Rails中的实现方法：

```ruby
def index
  if request.headers['X-PJAX']
    render :layout => false
  end
end
```

如果您想了解比上述方案更为自动化的方案，请查看 [Turbolinks](https://github.com/turbolinks/turbolinks-classic)

看一下您喜欢的服务器端框架是否有对应的 [pjax插件](https://gist.github.com/4283721)

也可以查看 [Playing with PJAX](http://railscasts.com/episodes/294-playing-with-pjax)

#### 参数

**$.fn.pjax**方法概述：

```javascript
$(document).pjax(selector, [container], options)
```

1. **selector**：string类型，用于click [事件委托](http://api.jquery.com/on/) 的选择器
2. **container**：string类型，用于标识唯一pjax容器的选择器
3. **options**：object类型，包含下列选项

    pjax配置选项

    <table>
        <thead>
        <tr>
            <th>选项</th>
            <th>默认值</th>
            <th>说明</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td>timeout</td>
            <td>650</td>
            <td>ajax超时时间（毫秒），超时后强制刷新整个页面</td>
        </tr>
        <tr>
            <td>push</td>
            <td>true</td>
            <td>使用
                <a href="https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/Manipulating_the_browser_history#Adding_and_modifying_history_entries" target="_blank">pushState</a>
                在浏览器中添加历史记录
            </td>
        </tr>
        <tr>
            <td>replace</td>
            <td>false</td>
            <td>替换URL地址但不添加浏览器历史记录</td>
        </tr>
        <tr>
            <td>maxCacheLength</td>
            <td>20</td>
            <td>容器元素缓存内容的最大值（次）</td>
        </tr>
        <tr>
            <td>version</td>
            <td></td>
            <td>string或function，返回当前pjax版本</td>
        </tr>
        <tr>
            <td>scrollTo</td>
            <td>0</td>
            <td>浏览器滚动条的垂直滚动位置。设为<code>false</code>时禁止滚动</td>
        </tr>
        <tr>
            <td>type</td>
            <td>"GET"</td>
            <td>参考
                <a href="http://api.jquery.com/jQuery.ajax/" target="_blank">$.ajax</a>
            </td>
        </tr>
        <tr>
            <td>dataType</td>
            <td>"html"</td>
            <td>参考
                <a href="http://api.jquery.com/jQuery.ajax/" target="_blank">$.ajax</a>
            </td>
        </tr>
        <tr>
            <td>container</td>
            <td></td>
            <td>被替换内容元素的CSS选择器</td>
        </tr>
        <tr>
            <td>url</td>
            <td>link.href</td>
            <td>string或function，返回ajax请求响应的URL</td>
        </tr>
        <tr>
            <td>target</td>
            <td>link</td>
            <td>pjax
                <a href="/#事件">事件</a>
                中<code>relatedTarget</code>属性的最终值
            </td>
        </tr>
        <tr>
            <td>fragment</td>
            <td></td>
            <td>css选择器，提取ajax响应内容中指定的内容片段</td>
        </tr>
        </tbody>
    </table>

    您可以在全局使用 **$.pjax.defaults** 对象改变默认配置：

    ```javascript
    $.pjax.defaults.timeout = 1200
    ```

### $.pjax.click

这是一个 **$.fn.pjax** 内部使用的底层方法，通过此方法可以在pjax事件之上做更多的事情

本示例使用当前的**click**上下文来设置一个祖先元素作为容器：

```javascript
if ($.support.pjax) {
  $(document).on('click', 'a[data-pjax]', function(event) {
    var container = $(this).closest('[data-pjax-container]')
    var containerSelector = '#' + container.id
    $.pjax.click(event, {container: containerSelector})
  })
}
```

注意 通过 **$.support.pjax** 我们明确浏览器是支持pjax的，但我们没有使用 **$.fn.pjax**，所以我们应该忽略绑定此事件的处理，除非浏览器实际上要使用pjax

### $.pjax.submit

通过pjax提交表单

```javascript
$(document).on('submit', 'form[data-pjax]', function(event) {
  $.pjax.submit(event, '#pjax-container')
})
```

### $.pjax.reload

使用pjax机制发起一个当前URL的请求到服务器，并且通过响应的内容替换容器元素中的内容，同时不添加浏览器历史记录

```javascript
$.pjax.reload('#pjax-container', options)
```

### $.pjax

手动调用pjax。主要用于非click事件发起pjax请求的情况。如果可以获得click事件，请使用 **$.pjax.click(event)** 来代替

```javascript
function applyFilters() {
  var url = urlForFilters()
  $.pjax({url: url, container: '#pjax-container'})
}
```

## 事件

除了 **pjax:click** 和 **pjax:clicked**，其他所有pjax事件都是在pjax容器元素上触发的

<table>
    <tbody><tr>
        <th>事件</th>
        <th width="60">取消</th>
        <th>参数</th>
        <th>说明</th>
    </tr>
    <tr>
        <th colspan="4">pjax链接事件的生命周期</th>
    </tr>
    <tr>
        <td>pjax:click</td>
        <td>✔</td>
        <td>options</td>
        <td>链接被激活的时候触发；取消的时候阻止pjax</td>
    </tr>
    <tr>
        <td>pjax:beforeSend</td>
        <td>✔</td>
        <td>xhr, options</td>
        <td>可以设置XHR头</td>
    </tr>
    <tr>
        <td>pjax:start</td>
        <td></td>
        <td>xhr, options</td>
        <td></td>
    </tr>
    <tr>
        <td>pjax:send</td>
        <td></td>
        <td>xhr, options</td>
        <td></td>
    </tr>
    <tr>
        <td>pjax:clicked</td>
        <td></td>
        <td>options</td>
        <td>pjax通过链接点击已经开始之后触发</td>
    </tr>
    <tr>
        <td>pjax:beforeReplace</td>
        <td></td>
        <td>contents, options</td>
        <td>从服务器端加载的HTML内容完成之后，替换当前内容之前</td>
    </tr>
    <tr>
        <td>pjax:success</td>
        <td></td>
        <td>data, status, xhr, options</td>
        <td>从服务器端加载的HTML内容替换当前内容之后</td>
    </tr>
    <tr>
        <td>pjax:timeout</td>
        <td>✔</td>
        <td>xhr, options</td>
        <td>在<code>options.timeout</code>之后触发；除非被取消，否则会强制刷新页面</td>
    </tr>
    <tr>
        <td>pjax:error</td>
        <td>✔</td>
        <td>xhr, textStatus, error, options</td>
        <td>ajax请求出错；除非被取消，否则会强制刷新页面</td>
    </tr>
    <tr>
        <td>pjax:complete</td>
        <td></td>
        <td>xhr, textStatus, options</td>
        <td>无论结果如何，都在ajax响应完成后触发</td>
    </tr>
    <tr>
        <td>pjax:end</td>
        <td></td>
        <td>xhr, options</td>
        <td></td>
    </tr>
    <tr>
        <th colspan="4">浏览器前进后退事件的生命周期</th>
    </tr>
    <tr>
        <td>pjax:popstate</td>
        <td></td>
        <td></td>
        <td><code>direction</code>事件的属性: "back"/"forward"</td>
    </tr>
    <tr>
        <td>pjax:start</td>
        <td></td>
        <td>null, options</td>
        <td>内容替换之前</td>
    </tr>
    <tr>
        <td>pjax:beforeReplace</td>
        <td></td>
        <td>contents, options</td>
        <td>在用缓存中的内容替换HTML之前</td>
    </tr>
    <tr>
        <td>pjax:end</td>
        <td></td>
        <td>null, options</td>
        <td>替换内容之后</td>
    </tr>
    <tr>
        <td>pjax:callback</td>
        <td></td>
        <td>null, options</td>
        <td>页面脚本加载完成后</td>
    </tr>
</tbody></table>

如果您使用了加载指示（如loading图标或“加载中”的文字）， **pjax:send** 和 **pjax:complete**这两个事件会比较有用。它们只有在XHR请求（而不是从缓存中加载内容）时才会被触发：

```javascript
$(document).on('pjax:send', function() {
  $('#loading').show()
})
$(document).on('pjax:complete', function() {
  $('#loading').hide()
})
```

以下是禁用 **pjax:timeout** 事件的示例

```javascript
$(document).on('pjax:timeout', function(event) {
  // Prevent default timeout redirection behavior
  event.preventDefault()
})
```

## 高级配置

### 在新页面中重新初始化插件/工具

pjax的特点是它不会刷新页面即可获取并插入新内容。但是，如果其他jQuery插件（或库）为页面内容绑定了加载事件（如DOMContentLoaded），那么这些事件是无效的。 比较常用的一种做法是，在更新的页面内容范围内，重新初始化插件

```javascript
$(document).on('ready pjax:end', function(event) {
  $(event.target).initializeMyPlugin()
})
```

该方法可以让 **$.fn.initializeMyPlugin()** 在页面普通加载和pjax加载时（点击链接或浏览器前进后退按钮之后）都能被调用

### 强制重载的响应类型

默认情况下，如果pjax从服务器收到以下响应之一，则会强制重载页面：

- 页面包含<strong>&lt;html&gt;</strong>标签，没有明确指定**fragment**选择器时。 pjax就会认为服务器端没有正确配置pjax响应。如果配置了**fragment**选项，pjax将根据该选择器提取页面内容
- 空白页面。pjax就会认为服务器端无法提供正确的pjax内容
- HTTP状态码为4xx或5xx，表示某些服务器错误

### 改变浏览器URL

如果服务器端需要改变浏览器地址栏中URL（如HTTP重定向），可以通过设置**X-PJAX-URL**头来实现：

```ruby
def index
  request.headers['X-PJAX-URL'] = "http://example.com/hello"
end
```

### 重载布局

静态资源或页面发生变化时，布局可被强制进行重载

首先，用一个自定义的meta标签在页面head中初始化layout版本

```html
<meta http-equiv="x-pjax-version" content="v123">
```

然后，在服务器端设置相同的**X-PJAX-Version**头

```ruby
if request.headers['X-PJAX']
  response.headers['X-PJAX-Version'] = "v123"
end
```

部署后，版本不同时整个页面会强制重载，会重新发起请求来获取新的布局和相关资源



