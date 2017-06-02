## 基本原则

1. 兼容性

   兼容多浏览器 （IE6、7、8、9、10、Firefox、Chrome、Safari）

   但暂不考虑跨设备的兼容，只对iPad作一定的兼容性改进。

2. 一致性

   UE交互保持一致性

   UI风格保持一致性

   代码风格保持一致性

3. 易测性

   应用组件开发时必须考虑易测性，为自动化测试提供方便 。

4. 适度先进性

   谨慎引入第三方前端库，引入时需要充分考虑性能、稳定性、可维护性和技能要求。

## 页面开发规范

## 代码结构

jsp文件放到各Project的WebContent/WEB-INF/jsp/apps/{module}下。

如



**Example 3.1. **

`f1_collaboation/WebContent/WEB-INF/jsp/apps/collaboration/listPending.jsp`。

  


## 命名

camel命名风格

## 页面结构

禁止在页面中

1. 定义css样式。

2. 在style属性中直接书写样式。

3. 引入除本页面同名js之外的任何js文件。（Portal如需引入第三方js，需要评审）

4. 使用@page import引入Java类。

## HTML书写规范

1. HTML 标签必须成对使用，base br col hr img input meta link除外。

2. HTML里的注释：&lt;%-- 被注释的内容 --%&gt;。

3. 所有的标签名必须小写。

4. 所有的标签属性名必须小写。

5. 所有的标签属性值都必须用双引号包起来。

6. 书写 HTML 代码的时候代码缩进。

7. 表现与结构完全分离，代码中不涉及任何的表现元素，如style、font、bgColor、border等。

8. 尽可能减少 div 嵌套。

9. 给图片加上alt标签。

规范的书写示例如下：

```
<
html
>
<
head
>
<
/head
>
<
body
>
<
div
id
=
"panel"
class
=
"panel"
>
<
/div
>
<
/body
>
<
/html
>
```

## JSTL & EL

限制使用，只能使用循环、el、少用if。

原有的taglib逐步进行迁移

只允许使用JSTL core、JSTL fmt 和JSTL functions 的部分tag，不允许使用JSTL sql和JSTL XML。

允许使用的tag列表

1. c:out

2. c:url

3. c:forEach & c:if

4. fmt:message



