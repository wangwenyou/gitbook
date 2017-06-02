## 2.1. 命名规范

### 2.1.1. Package命名 \[NAMING.PKG-2\]

Package命名 \[NAMING.PKG-2\]

描述：

Package名必须为小写字母或数字，且必须以字母开头；

平台产品中要求包名为com.seeyon.ctp.xxx或com.seeyon.apps.xxx

xxx为应用名如collaboration、edoc ...

示例：

```java
// CTP平台表单模块
package
 com.seeyon.ctp.form;

// 会议应用
package
 com.seeyon.apps.meeting;
```

### 2.1.2. Class命名 \[NAMING.NCL-2\]

描述：

Class名首字母必须大写，如果是某个Interface的实现，要求命名采用Interface

+Impl

示例：

```java
public
interface
 CollaborationManager{};

public
class
 CollaborationManagerImpl 
implements
 CollaborationManager{
}
```

### 2.1.3. Interface命名 \[NAMING.NITF-2\]

描述：

Interface命名首字母必须大写

示例：

```java
public interface CollaborationManager{
}
```

### 2.1.4. Exception Class命名 \[NAMING.NE-2\]

描述：

Exception class命名必须以Exception结尾

示例：

```
public
class
 NEFormatException 
extends
 Exception{
}
```

### 2.1.5. Unit Test Class命名 \[NAMING.NE-2\]

描述：

Unit test class命名必须以Test结尾

示例：

```
public
class
 FooTest 
extends
 TestCase{
}
```

### 2.1.6. Enum type命名 \[NAMING.NENUM-2\]

描述：

Enum type命名首字母必须大写

示例：

```
private
 enum Grade { 
    A, B, C, D, F 
};
```

### 2.1.7. Methods命名 \[NAMING.NMP-2\]

描述：

Method命名首字母必须小写

示例：

```
public
class
 NMFixed {

void
 method () {  
// FIXED

    }
}
```

### 2.1.8. Local variables命名 \[NAMING.NLV-2\]

描述：

Local variable命名首字母必须小写

示例：

```
private
int
 example = 
0
;
```

### 2.1.9. 常量命名 \[NAMING.USF-2\]

描述：

所有用 ”final”、”static” 修饰的变量命名要求全大写

示例：

```
public
static
final
int
 SIZE = 
10
;

private
static
final
 Logger LOGGER;
```

### 2.1.10. 类名禁止使用java内置类名 \[NAMING.DJLO-2\]

描述：

类名禁止使用java内置类名

错误示例：

```
public
class
 Integer{
}
```

### 2.1.11. 标示符禁止使用java关键字 \[NAMING.DJLO-2\]

描述：

标示符禁止使用java关键字

错误示例：

```
private
 String enum;

private
int
 assert;
```

### 2.1.12. 禁止使用sun预留包名 \[NAMING.RPKG-2\]

描述：

禁止使用sun预留包名

错误示例：

```
package
 java.rules.naming;
```

### 2.1.13. 禁止仅通过大小写区分变量 \[NAMING.UUVN-4\]

描述：

禁止仅通过大小写区分变量

错误示例：

```
int
 foo = 
9
;

int
 Foo = 
9
;
```

## 2.2. 注释规范

### 2.2.1. java文件头注释 \[FORMAT.MCH-2\]

描述：

所有java文件需要在文件头添加如下注释段：

```
/**
 * $Author$
 * $Rev$
 * $Date::                     $:
 *
 * Copyright (C) 2012 Seeyon, Inc. All rights reserved.
 *
 * This software is the proprietary information of Seeyon, Inc.
 * Use is subject to license terms.
 */
package
 xx.xx.xx;

import
 xx.xx;
```

### 2.2.2. java类注释 \[JAVADOC.PJDC-2\]

描述：

所有java类需按以下格式添加类注释：

```java
/**
 * 
<
p
>
Title: 应用模块名称
<
/p
>

 * 
<
p
>
Description: 代码描述
<
/p
>

 * 
<
p
>
Copyright: Copyright (c) 2012
<
/p
>

 * 
<
p
>
Company: seeyon.com
<
/p
>

 */
public
final
class
 MyClass
```

### 2.2.3. 方法注释 \[JAVADOC.PJDM-2\]

描述：

所有方法需按以下格式添加方法注释，继承的方法可以使用’@see’、’ @inheritDoc’ tag：

```java
/**
  * Executes a mapped SQL SELECT statement that returns data to populate
  * a number of result objects within a certain range.
  *
  * @param id The name of the statement to execute.
  * @param skip The number of results to ignore.
  * @param max The maximum number of results to return.
  * @return A List of result objects.
  * @throws java.sql.SQLException If an error occurs.
  */

  List queryForList(String id, 
int
 skip, 
int
 max) 
throws
 SQLException;
```

相关校验点：

> \[JAVADOC.DPMT-2\]                避免无用的javadoc tag
>
> \[JAVADOC.MDJT-2\]                注释内容要有意义
>
> \[JAVADOC.PARAM-2\]        每个param都要有'@param' tag
>
> \[JAVADOC.MRDC-2\]                有返回值的方法必须使用'@return' tag
>
> \[JAVADOC.THROW-2\]        有异常抛出的方法必须使用'@throws' 或 '@exception' tag
>
> \[JAVADOC.VMCR-2\]                无返回值的方法不要有'@return' tag
>
> \[JAVADOC.BT-4\]                不允许使用javadoc中未定义的'@' tag

## 2.3. 格式规范

### 2.3.1. 代码长度限制 \[FORMAT.LL-2\]

描述：

每行代码长度不能超过120个字符

### 2.3.2. 每个声明占用一个代码行 \[FORMAT.OSPL-2\]

描述：

每个声明占用一行代码

错误示例：

```
int
 i = a + b; 
return
 i;
```

正确示例：

```
int
 i = a + b;

return
 i;
```

### 2.3.3. 使用括号分隔复杂表达式 \[FORMAT.APAREN-3\]

描述：

使用括号分隔复杂表达式提高代码可读性

错误示例：

```
if
 (i 
>
= j 
&
&
 i 
>
= 
0
)
```

正确示例：

```
if
 ((i 
>
= j) 
&
&
 (i 
>
= 
0
))
```

### 2.3.4. 避免过于复杂的判断表达式

```java
if
 ( workTimeSpecials.size() 
>
0
&
&
 !workTimeSpecials.get(
0
).getIsRest().equals(
"0"
)) 
    || workTimeSpecials.size() == 
0
&
&
 !isWorkDay) {

return
;
}
```

遇到这样的表达式，请使用Extract Local Variable提取为意义明确的变量。

### 2.3.5. 代码缩进使用空格代替tab \[FORMAT.DUT-3\]

描述：

代码缩进使用空格代替tab保证代码在非windows系统环境下的可读性

### 2.3.6. 数组声明格式 \[FORMAT.IAD-3\]

描述：

数组声明\[\]放在type后面而不是变量后面

错误示例：

```
private
 String str1[], str2[], str3[];
```

正确示例：

```
private
 String [] str1, str2, str3;
```

### 2.3.7. 正确使用equals

错误示例：

```
str.equals(
"0"
);
```

正确示例：

```
"0"
.equals(str); 
// 规避str为null时的空指针异常
```

### 2.3.8. 避免不必要的小括号 \[FORMAT.UP-3\]

描述：

避免不必要的小括号

错误示例：

```
i = (i + 
2
);
```

### 2.3.9. 与文件同名的类应放在所有类声明的最前面 \[FORMAT.FCN-4\]

描述：

如果一个文件中定义了多个类，那么与文件同名的类作为主类应放在代码的最前面

### 2.3.10. 声明中修饰符的使用应遵循正确顺序 \[FORMAT.MO-4\]

描述：

声明中修饰符的使用应遵循以下顺序：

> annotations
>
> public/protected/private
>
> abstract
>
> static
>
> final
>
> transient
>
> volatile
>
> synchronized
>
> native
>
> strictfp



