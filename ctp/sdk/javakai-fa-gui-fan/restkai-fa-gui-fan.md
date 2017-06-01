# 本规范试用范围

**所有6.1新增的REST接口**\(已提供给客户的除外\)，请各位开发经理自行清理并按标准规范修正代码。

老的返回值方法会统一安排修改方法名，详情各开发经理会通知大家。

# 更新记录

2017-3-20 合并新版REST规范初稿

2017-3-23 启用正式规范

# 什么是RESTful

**REST**-- Representational State Transfer：表现层状态转移。通俗来讲就是：资源在网络中以某种表现形式进行状态转移，RESTful即这种设计的风格

用一句话概述即：**URL定位资源，用HTTP动词描述操作**

参考：[https://www.zhihu.com/question/28557115](https://www.zhihu.com/question/28557115)

# REST开发规范

## 原则

* 所有的REST接口均基于JAX-RS规范进行开发，
  应用代码里禁止出现对jersey的调用
* REST支持GET（获取）、POST（新建）、PUT（修改）和DELETE（删除）
* 为简化调用降低学习门槛，我们只使用GET和POST
* 已确定/发布的接口禁止修改
  **请求方法/URL/参数名/返回值结构/内容**
  ，禁止删除
  **接口/参数/返回值中的属性**

> 如有上述变更，请新启用一个接口
>
> 允许新增参数，但该参数不能为必填，如必填需提供缺省值兼容旧的调用

## 协议约定

* 根据CRUD来设计接口，R\(Retrieve\)使用GET，CUD\(create,update,delete\)使用POST
* 使用GET方法时，
  URL里不能包含动词
* 使用POST方法时，使用create/add表示新增，update表示修改，remove表示删除
* 仅需要传递id的CUD操作，使用body传递id
* 正常情况下所有R\(获取数据\)操作都使用GET方法
  ，查询内容参数使用QueryParams（仅允许在查询请求参数非常多的情况下，如参数数量大于5个，才可以使用post）
* 缺省接受JSON格式的参数，输出JSON。
  ```
  @Consumes
  (
  "application/json"
  )
  @Produces
  (
  "application/json"
  )
  ```
* 使用Jackson进行JSON处理，缺省将Date输出为长整型值。 参数option.n\_a\_s=1时将数值输出为字符串\(Number as String\)，以避免在Javascript中解析长整型的精度问题。
* 对象和方法命名语义需与内部保持一致，不允许自行创造。比如不要把Member叫做Person。

## 代码规范

* **代码使用com.seeyon.ctp.rest.resources包名**
  ，放置在各自模块的工程下自行管理。例如
  `com.seeyon.ctp.rest.resources.MemberResource`
* 所有资源均需继承BaseResource
* 资源必须以名词命名，并且同时提供单数和复数两种形式的资源，路径首字母小写

| 资源名称 | 路径 | ClassName | 备注 |
| :--- | :--- | :--- | :--- |
| Member | member | MemberResource | 单个人员 |
| Members | members | MembersResource | 人员列表 |
| Affair | affair | AffairResource | 单条事项 |
| Affairs | affairs | AffairsResource | 事项列表 |

* 方法名称不允许改变，不允许重载

* 使用POST进行remove操作，不需要传递userId，会取currentUser作为参数

* 文档必须完备，所有必填内容必须注明到javadoc中

* 对外接口需要增加标注@RestInterfaceAnnotation

## URL命名

根据RESTful的定义，我们知道，REST定义的URL描述的是资源

虽然我们没有使用PUT、DELETE，但是URL即资源的描述这个属性不会改变

需要特别注意：URL并不是动作的描述，因尽量避免使用增删改以外的动词。

* URL传递unicode字符一定要encode

* URL建议按照对象/功能/参数命名，例

```
  news/like
  news/replay/remove/{id} 或 news/replay/{id}/remove
  news/{id}/replays
```

* get方法不需要再在URL使用get开始，如一个bbs模块的方法
  _getConent_
  ，应命名为
  _bbs/content/{id}_
* @Path注解中的值，不能以/开始
* URL遵从Java代码规范，
  不允许使用缩写或拼音
  ，规避以下JavaScript关键字

```
delete、in、enum、let、function、typeof、debugger、console、prototype
```

### 例

**正确**

```
 GET tasks
 GET bbs/{id}/replys
POST affair/remove
   BODY affairId:{id}
POST task/update     特殊：为兼容老代码允许使用task/update/{id}
   BODY taskId:{id},title:{title},...
```

**错误**

```
 GET plan/getPlans         应为 GET plans
POST meeting/getOrderDate  应为 GET meeting/orderDate/{roomId}
   BODY roomId:{id}
```

### 缩写

**禁止使用缩写**

#### 例

**错误**

```
POST uc/modifypwd
```

**正确**

```
POST uc/password
```

### 多单词

**在一个功能中，原则上一段URL只有一个单词，如一段URL里需要多个单词，需使用驼峰命名**

#### 例

**错误**

```
POST coll/transRepalValid
POST coll/transStepBackValid
```

**建议**

```
POST collaboration/valid/repeal    repeal是一种资源，名词
POST collaboration/valid/rollback  rollback是一种资源，名词
```

**如需使用不合规范的命名，请提前与平台部门沟通**

## 接口文档规范

### 原则

ApiDoc格式因其工具不稳定，废弃

1. 使用标准格式的JAVADOC
2. 必须包含接口说明，参数说明，返回值说明。格式必须易读，请编写时注意调整注释的格式
3. JAVADOC应如实反映接口的功能。如有必要，可以在接口说明中增加场景
4. JAVADOC中，参数需要列明其名称、是否必填、允许值范围以及必要的说明，如参数间有关联，需要备注
5. 如参数为枚举/对象，必须将其使用的属性、值完全说明
6. 返回值应注明返回数据中的数据结构以及要有必要的参数说明
7. 应使用@since标明接口的启用版本
8. 应使用@date标明接口编写的时间

### 示例

```
/**
 * 保存秀吧信息
 * URL show/showbar
 * @since 6.0sp1
 * @date 2016-12-01
 * @param params 秀吧的参数
 * 
<
pre
>

 *    类型    名称            必填    备注
 *    Long    showbarId        Y    主题Id
 *    Long    coverPicture        N    封面Id
 *    String    showbarName        Y    主题名称
 *    String    summary            N    主题简介
 *    String    address            Y    主题发布地址
 *    String    startDate        Y    主题开始时间
 *    String    endDate            Y    主题结束时间
 *    String    showbarAuthScope    Y    授权范围类型
 *             All                全部
 *            All_extend_externalStaff    全部（外部人员除外）
 *            Part                部分授权
 *            All_group            全集团
 *    String    showbarAuth        Y    授权范围字符串
 * 
<
/pre
>

 * @return 返回对象com.seeyon.apps.show.po.ShowbarInfo
 * 
<
pre
>

 *     成功 {success:true,data:showBarData}
 *                 showBarData    来自于对象com.seeyon.apps.show.po.ShowbarInfo
 *                         {
 *                         "accountId"    所属单位ID,
 *                         "commentNum"    评论总数,
 *                         "coverPicture"    封面图片ID(对应show_imgae表),
 *                         "createFrom"    创建自：PC、M1,
 *                         "createTime"    创建时间,
 *                         "createUserId"    秀吧创建人ID,
 *                         "extraMap"    额外信息,
 *                         "id"    秀吧id,
 *                         "imgNum"    照片总数,
 *                         "likeNum"    点赞次数,
 *                         "new"    是否为新建,
 *                         "orderNum"    序号,
 *                         "settopTime"    置顶时间（未置顶则为空）,
 *                         "showbarName"    秀吧名称,
 *                         "status"    状态标识：0删除，1正常，2系统预制秀,
 *                         "viewNum"    浏览次数
 *                        }
 *    失败 {success:false,msg:errorMessage}
 *
<
/pre
>

 * @throws BusinessException    出错信息
 */
@POST
@Path
(
"showbar"
)
@Consumes
({
MediaType
.
APPLICATION_XML
,
MediaType
.
APPLICATION_JSON
})
public
Response
saveShowbarInfo
(
Map
<
String
,
Object
>
params
)
throws
BusinessException
```

## Response定义

**平台统一定义格式，各应用只需要传入数据及消息信息**

### 返回值格式

**成功的请求**

```
{
  code: 0,
  data: 返回数据,
  message: 'messges if exists'
}
```

返回的数据格式需满足实体和列表的规范

**失败的请求**

```
{
  code: 1,
  message: 'error messges'
}
```

### 特别注意

**code定义**

返回值的code 0为正常，非0为异常 目前定义的错误码：

| 编码 | 内容 |
| :--- | :--- |
| 0 | 正常 |
| 1 | 错误（未定义错误类型，可以作为常规错误） |
| 2-999 | 自定义错误扩展码 |
| 1XXX | 网络错误 |
| 2XXX | 认证错误 |
| 3XXX | 资源错误 |
| 其他 | 其他扩展的公用错误 |

**message定义**

如果是要提示给最终用户的message，必须进行国际化

**成功的请求**

大家只需要关心data内的内容，如果有需要传递的消息，请设置msg参数（非必填，如传空，平台将不返回该字段）

**失败的请求**

message必须填写,且内容恰当

### HTTP HEAD

#### HTTP STATUS CODE

异常情况需设置HTTP CODE（平台统一处理）

### 返回值

必须return Response，而不是具体的POJO对象；不允许自己进行JSON的toString，直接调用**ok**或者**error**方法由框架进行JSON转换。

```
// 错误
public
V3xOrgMember
getMemberByLoginName
(
@QueryParam
(
"loginName"
)
String
loginName
)
throws
Exception
{
return
getOrgManager
().
getMemberByLoginName
(
decode
(
loginName
));
}
// 正确
public
Response
getMemberByLoginName
(
@QueryParam
(
"loginName"
)
String
loginName
)
throws
Exception
{
return
ok
(
getOrgManager
().
getMemberByLoginName
(
decode
(
loginName
)));
}
```

### 建议格式

**正常返回值**

```
{
  code: 0,
  data: {
    "listSent": 64,
    "listPending": 13,
    "listDone": 9
  }
}
```

```
{
  code : 0,
  data: [
    {
      "name": "协同",
      "value": "collaboration"
    },
    {
      "name": "审批",
      "value": "approve"
    },
    {
      "name": "知会",
      "value": "inform"
    },
    {
      "name": "新闻审批",
      "value": "newsaudit"
    },
    {
      "name": "公告审批",
      "value": "bulletionaudit"
    },
    {
      "name": "阅读",
      "value": "read"
    },
    {
      "name": "意见必填",
      "value": "意见必填"
    }
  ]
}
```

**错误返回值**

```
{
  code: 1,
  message:"Some error occurred!"
}
```

### 异常

* 异常只允许抛出BusinessException，不允许对异常进行try catch后自行处理，框架会对异常统一处理返回

### 实体

* 与Java API保持一致，Java返回的是什么数据类型就返回什么
* 不存在，返回null，不允许返回""、{}或抛异常

### 列表

* 如返回值为空\(请求正确的情况\)，返回\[\]，不允许返回""、{}或抛异常
* 如请求参数错误，抛异常

## Spring bean引用

因为所有的REST实现都不受Spring管理，所以对于Spring bean不能采取依赖注入方式，请按照下面的方式进行处理

```
public
class
MemberResource
extends
BaseResource
{
private
OrgManager
orgManager
;
public
OrgManager
getOrgManager
()
{
if
(
orgManager
==
null
)
{
orgManager
=
(
OrgManager
)
AppContext
.
getBean
(
"orgManager"
);
}
return
orgManager
;
}
@GET
@Path
(
"{id}"
)
@Produces
(
MediaType
.
APPLICATION_JSON
)
@RestInterfaceAnnotation
public
Response
get
(
@PathParam
(
"id"
)
long
id
)
throws
Exception
{
return
ok
(
getOrgManager
().
getEntityById
(
getActualClass
(),
id
));
}
}
```

## JSON输出过滤和扩展

### JSON过滤

不输出实体的某些属性，比如

```
{
"orgAccountId"
:
-
7580270040522800906
,
"id"
:
-
4133790465478605204
,
"name"
:
"自动1"
,
"code"
:
"5"
,
"createTime"
:
1429064089000
,
"updateTime"
:
1429064089000
,
"sortId"
:
0
,
"isDeleted"
:
false
,
"enabled"
:
true
,
"status"
:
1
,
"description"
:
""
,
"orgLevelId"
:
8562916345585944089
,
"orgPostId"
:
-
2379980414295520995
,
"orgDepartmentId"
:
-
5380398112991065519
,
"password"
:
"123456"
,
......
}
```

不希望输出V3xOrgMember的password属性。

* 侵入式修改，在需要过滤的域的get方法上加@JsonIgnore注解。
  ```
  public
  class
  V3xOrgMember
  @JsonIgnore
  public
  String
  getPassword
  ()
  {
  }
  }
  ```
* 非侵入式修改

  ```
  // 定义一个空的类，增加注解列出要过滤的class
  @JsonIgnoreProperties
  (
  value
  =
  {
  "v3xOrgPrincipal"
  ,
  "password"
  })
  public
  class
  MemberWriteFilter
  {
  }
  // 初始化时调用注册Mixin
  com
  .
  seeyon
  .
  ctp
  .
  rest
  .
  util
  .
  MapperFactory
  .
  getInstance
  ().
  addMixInAnnotations
  (
  V3xOrgMember
  .
  class
  ,
  MemberWriteFilter
  .
  class
  );
  ```

  所有的V3xOrgMember转为JSON时将不输出过滤的属性列表

### JSON扩展

需要在输出的Bean基础上增加属性时使用，比如输出V3xOrgMember时输出所在单位的名称orgAccountName

```
com
.
seeyon
.
ctp
.
rest
.
util
.
MapperFactory
.
getInstance
().
register
(
V3xOrgMember
.
class
,
new
BeanSerializerFactory
.
Builder
()
{
public
Map
addFields
(
Object
bean
)
{
Map
<
String
,
String
>
data
=
new
HashMap
<
String
,
String
>
();
V3xOrgMember
member
=
(
V3xOrgMember
)
bean
;
long
orgAccountId
=
member
.
getOrgAccountId
();
data
.
put
(
"orgAccountName"
,
orgManager
.
getAccountById
(
orgAccountId
).
getName
());
data
.
put
(
"orgDepartmentName"
,
balabala
);
data
.
put
(
"orgPostName"
,
balabala
);
return
data
;
}
});
```

## 实现

REST实现必须调用Api，不允许调用模块的Manager

## 分页

统一使用pageSize和pageNo两个QueryParam控制分页

```
// CTP获取FlipInfo对象
getFlipInfo
();
// V3X的迁移代码在方法实现首行进行设置
setPagination
();
```

## 国际化

通过http header的Accept-Language控制国际化使用的语言

```
  Accept-Language:en_US
```

REST Client中提供方法设置语言

```
client.setLocale
(
Locale.SIMPLIFIED_CHINESE
);
```

JSSDK中缺省以浏览器的语言进行国际化，取不到浏览器语言时使用zh\_CN 如果需要强制指定，可以在最后一个参数中使用AcceptLanguage指定语言。

```
$s
.
Token
.
getToken
(
'rest'
,
'123456'
,
''
,{
'AcceptLanguage'
:
'zh_CN'
})
```

## 当前用户

禁止通过前端传递member的id，比如取某某用户的待办。必须同时支持以下两种方式：

1.从Header和QueryParam中取ticket，通过ticket获取member的Id。

2.如果ticket为空，在Resource层取CurrentUser（禁止在Manager、Api、Dao层取）。 ​  
如果取不到CurrentUser，而且也没有传递ticket则抛出异常。

已经支持token绑定用户，绑定用户后任何地方的代码获取当前用户就不会是null了 有两种绑定方式 1、获取Token时加?loginName=s1参数

```
POST http://127.0.0.1/seeyon/rest/token/?loginName=s1 HTTP/1.1
Accept: application/json
Host: 127.0.0.1
Content-Type: application/json
Content-Length: 39

{"userName":"rest","password":"123456"}
```

2、获取Token后单独调用 PUT /rest/token {"token":"xxx", "loginName":"xxx"} 进行绑定

## 内容协商

对于实体返回，建议同时支持JSON和XML，在Resource的Class或方法加入如下声明

```
@GET
@Path
(
"{userName}/{password}"
)
@Produces
({
MediaType
.
APPLICATION_JSON
,
MediaType
.
APPLICATION_XML
})
public
Response
getToken
(
@PathParam
(
"userName"
)
String
userName
,
@PathParam
(
"password"
)
String
password
,
@QueryParam
(
"loginName"
)
String
loginName
,
@QueryParam
(
"userAgentFrom"
)
String
userAgentFrom
)
throws
Exception
{
return
ok
(
_getToken
(
userName
,
password
,
loginName
,
userAgentFrom
));
}
```

ok方法会根据请求header的accept自动返回json或xml。

如果需要返回html和纯文本，可以新建一个方法，使用相同的Path

```
@GET
@Path
(
"{userName}/{password}"
)
@Produces
(
MediaType
.
TEXT_PLAIN
)
public
Response
getTokenString
(
@PathParam
(
"userName"
)
String
userName
,
@PathParam
(
"password"
)
String
password
,
@QueryParam
(
"loginName"
)
String
loginName
,
@QueryParam
(
"userAgentFrom"
)
String
userAgentFrom
)
throws
Exception
{
UserToken
token
=
_getToken
(
userName
,
password
,
loginName
,
userAgentFrom
);
return
ok
(
token
.
getId
());
}
```

例如

请求

```
GET http://127.0.0.1/seeyon/rest/token/rest/123456 HTTP/1.1
Accept: application/json
Host: 127.0.0.1
```

返回

```
{
"bindingUser"
:
null
,
"id"
:
"a5ad648c-0a40-49b0-bedd-9fd7025313b5"
}
```

请求

```
GET http://127.0.0.1/seeyon/rest/token/rest/123456 HTTP/1.1
Accept: application/xml
Host: 127.0.0.1
```

返回

```
<
UserToken
>
<
bindingUser/
>
<
id
>
55b69a17-b2be-4dfa-80ac-c3a211ec652d
<
/id
>
<
/UserToken
>
```

请求

```
GET http://127.0.0.1/seeyon/rest/token/rest/123456 HTTP/1.1
Accept: text/plain
Host: 127.0.0.1
```

返回

```
d72640bd-48b0-46cd-87a1-ca9a449da185
```

参考 Parse的REST文档[https://www.parse.com/docs/rest/guide](https://www.parse.com/docs/rest/guide)Bmob的JS-SDK文档[http://docs.bmob.cn/jssdk/developdoc/index.html?menukey=develop\_doc&key=develop\_jssdk](http://docs.bmob.cn/jssdk/developdoc/index.html?menukey=develop_doc&key=develop_jssdk)

# Import &Export Pattern

## 导入

使用什么样的数据规范

* CSV：导入大量数据优先建议使用CSV，容易和第三方系统对接。 首行必须为列名称

```
FirstName,LastName,Email,Name,IsMaasUser,Upn
John,Smith,john.smith@hpe.com,John Smith,false,john.smith@hpe.com
John,Doe,john.doe@hpe.com,John Doe,true,john.doe@hpe.com
```

* JSON：适用于数据来源于对方开发人员硬编码组织。

```
[
{
"name"
:
"Jane"
,
"address"
:
"London"
,
"email"
:
"janoe@gmail.com"
,
"age"
:
42
},
{
"name"
:
"Joe"
,
"address"
:
"New york"
,
"email"
:
"joe@gmail.com"
,
"age"
:
28
},
]
```

* XML：如果是要对接EDI或者早期SOAP遗留系统，可以支持XML格式。
* 部分特定场景，可能需要支持类似VCARD或XLS之类的特殊格式，具体问题具体分析，但必须是可解析的结构化或半结构化数据，如果你不能确定，请第一时间联系我们。

### CSV导入

使用POST MULTIPART\_FORM\_DATA方式提交

客户端请求：

```
POST http://127.0.0.1/seeyon/data/import HTTP/1.1
Content-Type: multipart/form-data; boundary=---------------------------7d91b51290a82

-----------------------------7d91b51290a82
Content-Disposition: form-data; name="data"; filename="C:\tmp\data.csv"
Content-Type: application/octet-stream
�������,����
-----------------------------7d91b51290a82--
```

服务器实现：

```
@POST
@Path("import")
@Consumes(MediaType.MULTIPART_FORM_DATA)
public Response importCsv(
    @FormDataParam("data") InputStream uploadedInputStream,
    @FormDataParam("data") FormDataContentDisposition fileDetail) {
       String csv = IOUtility.toString(uploadedInputStream);
    // 解析，处理 ...
    return ok(output);
}
```

#### JSON 导入

客户端请求：

```
POST http://127.0.0.1/seeyon/rest/member HTTP/1.1
User-Agent: application/json
Accept: application/json
token: 9e4352a8-a8e4-484c-97c4-eb119b248463
Host: 127.0.0.1

[
    {"name": "Jane", "address": "London", "email": "janoe@gmail.com", "age": 42},
    {"name": "Joe", "address": "New york", "email": "joe@gmail.com", "age": 28},
]
```

服务器实现：

```
@POST
@Path("import")
@Consumes({ MediaType.APPLICATION_JSON })
public Response importCsv(List data) {
    // 解析，处理 ...
    return ok(output);
}
```



