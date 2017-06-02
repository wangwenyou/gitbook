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

**Example 3.1. **

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

```html
<html>
    <head>
    </head>
    <body>
        <div id="panel"  class="panel">
        </div>
    </body>
</html>
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

## Javascript开发规范

## 编码规范

### 4.1.1. 文件

JavaScript 程序应独立保存在后缀名为 .js 的文件中。

原则上，JavaScript 代码不应该被包含在 HTML 文件中。在 HTML 中的JavaScript代码会明显增加文件大小，而且也不能对其进行缓存和压缩。

模块的公开js，放到common/js/apps/{module}下，名称为模块名称，如common/js/apps/collaboration/collaboration\_pub.js；发布时将被合并到主js文件。

页面js放到WebContent/apps/{module}下，建立与jsp同名的js文件，如WebContent/apps/collaboration/listPending.js。

### 4.1.2. 命名

命名总的原则为：骆驼、自然语言、英文

1. 名称只能包含下面的字符：26个大小写字符 \(A .. Z， a .. z\), 数字\(0 .. 9\)，和 下划线 \(\_\)。不允许使用中文或特殊字符。首字母下划线只能用于私有方法和全局变量。

2. 变量名称必须为小写字母，请使用英文名词，如department ;。对象的属性或方法名采用小驼峰式\(lower camel-case\)，如”init”, “bindEvent”, “updatePosition”：

3. 类的命名使用骆驼命名规则，例如：Account, EventHandler

4. 常量必须在对象（类）或者枚举变量的前部声明。枚举变量的命名必须要有实际的意义，并且其成员 必须 使用骆驼命名规则或使用大写：

5. 不允许使用单词简写 。私有变量名用下划线开头。如：”\_current”, “\_defaultConfig”

6. 不能使用大写名称作为变量名。常量名全部大写，单词间用下划线分隔。如：”CSS\_BTN\_CLOSE”, “TXT\_LOADING”

7. 方法和函数名应该以动词开始如getName\(\)，返回bool类型的函数应该以is开始，例如isEnable\(\)；不必担心长度，因为后期发布时会压缩。

   除了构造方法可以使用大写，首字母必须小写。

### 4.1.3. 行长度

避免每行超过 120 个字符。当一条语句一行写不下时，请考虑折行。

### 4.1.4. 缩进

缩进的单位为四个空格，避免使用 Tab 键来缩进。

### 4.1.5. 注释

使用jsdoc语法书写注释。例如：

```js
/**
 * 取得指定key的国际化资源。
 * @param {String} key 资源的key。
 * @return {String} 国际化资源文本。
 */

CTP.
prototype
.getMessage = 
function
(key){
    ...
}

```

jsdoc常用关键字参见附录。

  


| ![](http://10.3.4.240:8080/userContent/ctp_pub/docs/resources/admonitions/tip.png "\[Tip\]") | Tip |
| :--- | :--- |
|  | 不要吝啬注释。给以后需要理解你的代码的人们（或许就是你自己）留下信息是非常有用的。注释应该和它们所注释的代码一样是书写良好且清晰明了。偶尔的小幽默就更不错了。记得要避免冗长或者情绪化。 及时地更新注释也很重要。错误的注释会让程序更加难以阅读和理解。 |

### 代码风格

遵循[C++风格](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Formatting)，参照下面的示例：

```js
/**
 * 取得指定key的国际化资源。
 * @param {String} key 资源的key。
 * @return {String} 国际化资源文本。
 */
CTP.prototype.getMessage = function( key ){
    try{
        var msg= eval( '' + key );
        if( msg && arguments.length > 1 ){
        for( var i = 0; i < arguments.length - 1; i++ ) {
                var regEx = eval('messageRegEx_' + i);
                var repMe = '' + arguments[i + 1];
                if( repMe.indexOf( '$_' ) !== -1 ){
                    repMe = repMe.replace('$_', '$$_');
                }
                msg = msg.replace( regEx, repMe );
            }
        }
        return msg;
    }
    catch(e){
    }
    return "";
}
```



