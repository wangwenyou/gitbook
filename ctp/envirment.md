## i18n国际化 {#i18n}

CTP对国际化组件进行新的设计，所有的国际化资源文件集中存放在cfgHome/i18n下，可以按照模块划分子文件夹存放，框架会在启动时自动加载全部资源文件，并形成总体的key-&gt;value映射，这种模式下要求各模块的key命名要严格按照规范进行定义，不允许出现重复，否则将会相互覆盖。系统Locale定义的配置存在于

WEB-INF/cfgHome/base/systemProperties.xml中的locales属性：

```xml
        <!-- 系统提供的语言种类 用逗号分隔 -->
        <locales>zh_CN,en,zh_TW</locales>
```

前端的调用方式如下：

```html
<tr>
    <td>ctp:i18n函数</td>
    <td>${ctp:i18n('common.button.add.label')}</td>
</tr>
<tr>
    <td>ctp:i18n_1函数，一个参数，变量参数</td>
    <td>${ctp:i18n_1('common.charactor.limit.label',path)}</td>
</tr>
<tr>
    <td>ctp:i18n_1函数，一个参数，数值参数</td>
    <td>${ctp:i18n_1('common.charactor.limit.label',10)}</td>
</tr>
<tr>
    <td>ctp:i18n_1函数，一个参数，字符串参数</td>
    <td>${ctp:i18n_1('common.charactor.limit.label','测试')}</td>
</tr>
<tr>
    <td>ctp:i18n_2函数，两个参数</td>
    <td>${ctp:i18n_2('common.charactor.limit.label','测试',3)}</td>
</tr>
<tr>
    <td>ctp:i18n_3函数，三个参数</td>
    <td>${ctp:i18n_3('common.charactor.limit.label','测试',3,3)}</td>
</tr>
<tr>
    <td>ctp:i18n_4函数，四个参数</td>
    <td>${ctp:i18n_4('common.charactor.limit.label','测试',3,3,3)}</td>
</tr>
<tr>
    <td>ctp:i18n_5函数，五个参数</td>
    <td>${ctp:i18n_5('common.charactor.limit.label','测试',3,3,3,3)}</td>
</tr>
```

后端的调用方式如下：

```java
System.out.println(ResourceUtil.getString("common.button.add.label"));
System.out.println(ResourceUtil.getString("common.charactor.limit.label", 12));
System.out.println(ResourceUtil.getString("common.date.times", 2, 15));
System.out.println(ResourceUtil.getString("common.date.times", 2, 15, 3));
System.out.println(ResourceUtil.getString("common.date.times", 2, 15, 3, 4));
System.out.println(ResourceUtil.getString("common.date.times", 2, 15, 3, 4, 5));
```

此外，框架还提供了两种前端Javascript国际化调用支持，第一种是系统默认将把“.js”结尾的资源key生成到前端国际化资源中，通过$.i18n函数获取，同时支持动态

参数，如下例：

```js
alert($.i18n('my.resource.js'));
alert($.i18n('my.resource.js','参数1','参数2'));
```

第二种是在i18n文件夹根下配置export\_to\_js.xml文件，用于指定哪些后台国际化资源要开放给前端Javascript引用，该文件内容例子如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<export>
    <resKey>common.button.cancel.label</resKey>
    <resKey>common.button.close.label</resKey>
    <resKey>common.button.ok.label</resKey>
</export>
```



