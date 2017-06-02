## 1.1. 体系图

![](http://10.3.4.240:8080/userContent/ctp_pub/docs/resources/images/MVC.jpg)

## 1.2. 开发规范

1. 明确MVC各层职责，不能错误使用。

   例如Manager不能替代Dao的职责。

   HTTPRequest、HTTPResponse和HTTPSession只能出现在Controller，不允许传递到Manager层。

2. Controller层不允许编写业务逻辑，业务逻辑由Manager层处理。

   Controller层的一个方法只能调用Manager层中的一个方法，目的是为了确保统一事务。

   错误：

   \`\`\`  
   public  
   class  
    CollaborationController {

   private  
    CollaborationManager collaborationManager;

   private  
    AttachmentManager attachementManager;

   public  
    ModelAndView newCol\(\){  
           collaborationManager.save\(...\);  
           attachementManager.save\(...\);  
       }  
   }

public  
   class  
    CollaborationManagerImpl  
   implements  
    CollaborationManager{

public  
   void  
    save\(\){  
           ...  
       }  
   }

```
   正确：
```

public  
   class  
    CollaborationController {

private  
    CollaborationManager collaborationManager;

public  
    ModelAndView newCol\(\){  
           collaborationManager.save\(...\);  
       }  
   }

public  
   class  
    CollaborationManagerImpl  
   implements  
    CollaborationManager{

private  
    AttachmentManager attachementManager;

public  
   void  
    save\(\){  
           ...  
           attachementManager.save\(...\);  
       }  
   }  
   \`\`\`

1. Controller职责不宜过多，原则上一个Controller只封装一个完整业务逻辑（CRUD）。

2. Manager层中分简单复杂（或大小）方法，之间可以互相调用，目的是完成完整的业务逻辑。\(Manager本身就一层，并非像图中的三层，图中体现的是跨模块的Manager调用。\)

3. Manager和Dao层遵循接口开发要求，例如：Manager、ManagerImpl、Dao，DaoImpl。

4. 所有的异常需要抛到Manager层，由Manager层统一Catch后包装为BusinessException抛出。

   Manager的方法不允许抛出BusinessException之外的异常。

   建议Manager的所有方法都抛出BusinessException，但不作硬性要求（初期不抛，后续版本抛出异常会影响到调用代码）；

5. Dao不能省略，必须定义。

6. Dao层支持Hibernate和JDBC两种方式。

   原则上只允许使用Hibernate模式访问数据库。调用JDBC需评审并备案。

7. 只有Dao层能够书写HQL和SQL语句，Controller和Manager层不允许出现SQL。

8. 各层之间数据传递规范，PO与对应表字段一一对应，不允许增加额外的属性，不允许有业务逻辑；

   PO需继承BasePO，BasePO中已实现toXml、toJSON，出于性能考虑可以覆盖实现。

9. C层向V层只能返回VO对象，VO对象为类Map的结构。对于简单情况可以用PO代替VO。

   命名规范：XXX模块对应的MVC、DAO对应各层的命名规范为`XXX`_\_Controller、_`XXX`_Manager、_`XXX`_Dao、_`XXX`_PO、spring-_`XXX`_-controller.xml、spring-_`XXX`_-manager.xml、_`xxx`\_.hbm.xml

10. 基于接口编码，Manager和Dao必须定义接口，一个接口一个实现类。

11. 应用不允许自己调用Log4j，相应的日志由框架统一使用AOP记录。

12. 按模块和功能划分package，原则上一个package不能超过10对类（接口+实现）。

## 1.3. 工具规范

1. 设计工具

   框图可使用Visio或直接使用Powerpoint，不做特别限制，但必须提交底稿。

   UML相关（用例、时序图、类图）使用EA。

2. 数据建模工具

   使用PowerDesigner

3. JDK

   使用JDK 1.6，编译级别设为1.6。

4. XML解析器

   只允许使用dom4j

   对于将XML解析为Bean，建议使用Digester。

5. 服务器端JSON工具

   统一使用框架提供的com.seeyon.ctp.util.json.JSONUtil。



### 编码规范

**一致性**高于一切

#### 命名

编码和命名沿用 ，保持语义一致。

| 分类 | 命名 | 备注 |
| :--- | :--- | :--- |
| Package | com.seeyon.apps.{moduleName}.api | 全部小写 |
| Interface | com.seeyon.apps.{moduleName}.api.XXXApi | 如com.seeyon.apps.news.api.NewsApi |
| Impl | com.seeyon.apps.{moduleName}.api.XXXApiImpl | 如com.seeyon.apps.news.api.NewsApiImpl，实现代码放在具体的模块工程 |
| Bean Name | xXXApi | 如newsApi |
| Spring xml File Name | spring-{moduleName}-api.xml | 如spring-news-api.xml |
| Param | com.seeyon.apps.{moduleName}.api.XXXParam | 方法参数超出3个需引入Param对象作为参数 |
| PO | com.seeyon.apps.{moduleName}.po | 统一，方便维护 |

所有的接口方法**必须抛出异常**，异常类型为BusinessException

```
void
run
(
)
throws
BusinessException
;
```

**示例：**

```
import
com.seeyon.apps.news.api.NewsApi
;
import
com.seeyon.apps.news.api.NewsParam
;
...
NewsApi
api
;
long
id
;
api
.
get
(
id
);
api
.
get
(
new
NewsParam
(
newsType
,
pageNum
,
pageSize
));
```

bean定义放在具体模块的工程，如

`spring-news-api.xml`

：

```
<
bean
name=
"newsApi"
class=
"com.seeyon.apps.news.api.NewsApiImpl"
/
>
```

##### 方法命名

命名采用"动作+属性" 的方法。并且，动作以小写字母开始，属性以大写字母开始。常用的动作有：is、get、set、create、 update、delete。 遵从业界惯例，for可缩写为4，to可缩写为2

| 分类 | 命名 | 备注 |
| :--- | :--- | :--- |
| 新建 | createXXX | 不用insert，公告发布可使用issue，协同发送使用send，但须申请并增补到术语表中 |
| 修改 | updateXXX | 不用modify、save |
| 删除 | deleteXXX | 不用remove |
| 查询-单一对象 | get | 无数据统一返回null，不允许抛出异常 |
| 查询-对象列表 | find | 不用search、query，无数据不允许返回null，返回**Collections.emptyList\(\)** |
| 查询-全部对象 | findAll | 无数据时处理同上 |
| 查询-对象计数 | getXXXCount |  |

> 新建、修改和删除不允许使用boolean或int作为返回值，原则上应该是void的

```
long
id
;
String
subject
;
NewsType
type
;
// 按Id取
News
aNews
=
api
.
get
(
id
);
// 按某一特定的规则取，如果不会产生歧义，就不必加ByName、BySubject
News
aNews
=
api
.
get
(
subject
);
// 获取指定条件的列表
List
<
News
>
newsList
=
api
.
find
(
type
);
// 获取所有
List
<
News
>
all
=
api
.
findAll
();
```

##### 日志

日志使用我们封装的CtpLogFactory，变量名使用LOG

```
import
org.apache.commons.logging.Log
;
import
com.seeyon.ctp.common.log.CtpLogFactory
;
public
class
NewsApiImpl
{
private
final
Log
LOG
=
CtpLogFactory
.
getLog
(
NewsApiImpl
.
class
);
}
```

**分页：**迁移代码继续使用Pagination，CTP的新实现继续沿用Flipinfo，不再引入新的分页对象。

**当前用户：**区分用户的所有接口实现均不允许内部获取CurrentUser，必须显性传入memberId。

##### 参数设计规范

> 1. 参数名应能从字面表明参数的作用
> 2. 参数对象不允许使用基础数据类型，必须使用其封装类作为参数类型。如参数需要传Integer类型的，不允许使用int。
> 3. 接口中写明的参数在使用时都必须有用，不允许出现其中某个参数为特定值时，可以忽略另一个参数的情况。如有特殊的需求，建议拆分接口为多个。如findBulletinTypes\(int spaceType, long accountId\)方法，在spaceType为-1时，不管accountId为任何值，都会返回所有的公告板块列表。这样的接口就会产生使用上的歧义，建议增加一个接口findAllBulletinTypes\(\)取代这种情况。
> 4. 8种基础数据类型的参数，使用封装类型
>    ```
>    // 正确
>    void
>    methodOk
>    (
>    Long
>    l
>    ,
>    Integer
>    i
>    ,
>    Boolean
>    b
>    ){
>    }
>    // 错误
>    void
>    methodBad
>    (
>    long
>    l
>    ,
>    int
>    i
>    ,
>    boolean
>    b
>    ){
>    }
>    ```

#### 模块管理

模块使用唯一的int值进行标识。

* 内部模块必须在`com.seeyon.ctp.common.constants.ApplicationCategoryEnum`枚举中定义。

  > 合并com.seeyon.ctp.common.ModuleType，统一使用com.seeyon.ctp.common.constants.ApplicationCategoryEnum

* 外部扩展应用可以不在枚举中添加。
* 0-10000为内部模块预留编号，外部扩展应用不允许使用。
* 方法定义和调用方式如下，注意，参数必须为
  **int**
  ，参数名称必须为
  **category**
  ```
  // 正确定义
  List
  <
  AppLog
  >
  getAllAppLogs
  (
  int
  category
  );
  // 错误定义
  List
  <
  AppLog
  >
  getAllAppLogs
  (
  ApplicationCategoryEnum
  appEnum
  );
  // 正确调用
  getAllAppLogs
  (
  ApplicationCategoryEnum
  .
  global
  .
  getKey
  ());
  getAllAppLogs
  (
  10021
  );
  // 错误调用
  getAllAppLogs
  (
  0
  );
  ```

目前的模块定义如下

| 标识 | 名称 | 备注 |
| :--- | :--- | :--- |
| global | 0 | 全局 |
| collaboration | 1 | 协同应用 |
| form | 2 | 表单 |
| doc | 3 | 知识管理 |
| edoc | 4 | 公文 |
| plan | 5 | 计划 |
| meeting | 6 | 会议 |
| bulletin | 7 | 公告 |
| news | 8 | 新闻 |
| bbs | 9 | 讨论 |
| inquiry | 10 | 调查 |
| calendar | 11 | 日程事件 |
| mail | 12 | 邮件 |
| organization | 13 | 组织模型 |
| project | 14 | 项目 |
| relateMember | 15 | 关联人员 |
| exchange | 16 | 交换 |
| hr | 17 | 人力资源 |
| blog | 18 | 博客 |
| edocSend | 19 | 发文 |
| edocRec | 20 | 收文 |
| edocSign | 21 | 签报 |
| exSend | 22 | 待发送公文 |
| exSign | 23 | 待签收公文 |
| edocRegister | 24 | 待登记公文 |
| communication | 25 | 在线交流 |
| office | 26 | 综合办公 |
| agent | 27 | 代理设置 |
| modifyPassword | 28 | 密码修改 |
| meetingroom | 29 | 会议室 |
| taskManage | 30 | 任务管理 |
| guestbook | 31 | 留言板 |
| info | 32 | 信息报送 |
| infoStat | 33 | 信息报送统计 |
| edocRecDistribute | 34 | 收文分发 |
| notice | 35 | 公示板 |
| attendance | 36 | 签到 |
| mobileAppMgrForHTML5 | 37 | 移动应用接入-html5应用包 |
| sapPlugin | 38 | sap插件 |
| ThirdPartyIntegration | 39 | 第三方整合 |

#### Maven

> **价值**
>
> 1. 控制内部模块间依赖。
>
> 2. 管理第三方类库，避免版本冲突。
>
> 3. 自动化构建。

引入Maven管理依赖，文件放置遵从Maven的标准： Eclipse工程文件\(.project、.classpath\)请勿提交到svn上。

##### 目录结构

```
http://10.3.4.218:6666/
   └── svn
      └── v5
         ├── ctp                                                               Ctp基础框架，包括原core和T1
         │   └── trunk
         │      ├── ctp-core
         │      │   └── pom.xml
         │      ├── ctp-common                                                 平台基础组件，原ctp_t1
         │      │   └── pom.xml
         │      └── pom.xml
         ├── ctp-organization                                                  组织模型，原ctp_t2
         │   └── trunk
         │      └── pom.xml
         ├── ctp-portal                                                        门户，原ctp_t3
         │   └── trunk
         │      └── pom.xml
         ├── ctp-workflow                                                      工作流，原ctp_t4
         │   └── trunk
         │      └── pom.xml
         ├── ctp-report                                                        报表
         │   └── trunk
         │      └── pom.xml
         ├── ctp-form                                                          表单，原ctp_t5
         │   └── trunk
         │      └── pom.xml
         ├── ctp-index                                                         全文检索引擎
         │   └── trunk
         │      └── pom.xml
         ├── apps-api                                                          解耦后的开放接口
         │   └── trunk
         │      └── pom.xml
         ├── apps-common                                                       应用基础组件
         │   └── trunk
         │      └── pom.xml
         ├── apps-collaboration                                                协同应用，原f1
         │   └── trunk
         │      └── pom.xml
         ├── apps-task                                                         任务
         │   └── trunk
         │      └── pom.xml
         ├── apps-calendar                                                     日程
         │   └── trunk
         │      └── pom.xml
         ├── apps-plan                                                         计划
         │   └── trunk
         │      └── pom.xml
         ├── apps-project                                                      项目
         │   └── trunk
         │      └── pom.xml
         ├── apps-f7
         │   └── trunk
         │      ├── apps-edoc                                                  公文
         │      │   └── pom.xml
         │      ├── apps-exchange                                              公文交换
         │      │   └── pom.xml                      
         │      └── pom.xml
         ├── apps-colcube                                                      协同立方
         │   └── trunk
         │      └── pom.xml
         ├── apps-performancereport                                            
         │   └── trunk
         │      └── pom.xml
         ├── apps-meeting                                                      会议
         │   └── trunk
         │      └── pom.xml
         ├── apps-meetingroom                                                  会议室
         │   └── trunk
         │      └── pom.xml
         ├── apps-meetingsummary 
         │   └── trunk
         │      └── pom.xml
         ├── apps-office
         │   └── trunk
         │      └── pom.xml
         ├── apps-pubresource
         │   └── trunk
         │      └── pom.xml
         ├── apps-index                                                       全文检索应用
         │   └── trunk
         │      └── pom.xml
         ├── apps-blog
         │   └── trunk
         │      └── pom.xml
         ├── apps-doc                                                         文档中心
         │   └── trunk
         │      └── pom.xml
         ├── apps-rss
         │   └── trunk
         │      └── pom.xml
         ├── apps-uc
         │   └── trunk
         │      └── pom.xml
         ├── apps-addressbook                                                 通讯录
         │   └── trunk
         │      └── pom.xml
         ├── apps-bbs                                                         BBS
         │   └── trunk
         │      └── pom.xml
         ├── apps-bulletin                                                    公告
         │   └── trunk
         │      └── pom.xml
         ├── apps-inquery                                                     调查
         │   └── trunk
         │      └── pom.xml
         ├── apps-webmail                
         │   └── trunk
         │      └── pom.xml
         └── apps-hr
            └── trunk
               └── pom.xml

```

根据发布要求，支持两种目录结构的Project：

1. 需要单独发布的模块使用自己的trunk，可单独打Tag和Branch，如apps-collaboration。

2. 多个小模块可使用同一trunk，合并版本发布，采用多模块目录结构，如上例中的ctp-core和ctp-common。

标准Project目录结构

```
apps-xxx/                                    http://10.3.4.218:6666/svn/v5/apps-xxx/trunk
   ├── src
   │   ├── main
   │   │   ├── java
   │   │   │   └── com
   │   │   │      └── seeyon
   │   │   ├── resources
   │   │   └── webapp
   │   │      ├── WEB-INF
   │   │      │   └── web.xml
   │   │      └── index.jsp
   │   └── test
   │      ├── java
   │      │   └── com
   │      │      └── seeyon
   │      └── resources
   └── pom.xml
```

分模块目录结构

```
apps-xxx/                                    http://10.3.4.218:6666/svn/v5/apps-xxx/trunk
   ├── apps-news
   │   ├── src
   │   │   ├── main
   │   │   │   ├── java
   │   │   │   │   └── com
   │   │   │   │      └── seeyon
   │   │   │   ├── resources
   │   │   │   └── webapp
   │   │   │      ├── WEB-INF
   │   │   │      │   └── web.xml
   │   │   │      └── index.jsp
   │   │   └── test
   │   │      ├── java
   │   │      │   └── com
   │   │      │      └── seeyon
   │   │      └── resources
   │   └── pom.xml
   ├── apps-bbs
   │   ├── src
   │   │   ├── main
   │   │   │   ├── java
   │   │   │   │   └── com
   │   │   │   │      └── seeyon
   │   │   │   ├── resources
   │   │   │   └── webapp
   │   │   │      ├── WEB-INF
   │   │   │      │   └── web.xml
   │   │   │      └── index.jsp
   │   │   └── test
   │   │      ├── java
   │   │      │   └── com
   │   │      │      └── seeyon
   │   │      └── resources
   │   └── pom.xml
   └── pom.xml           
```

解耦后工程代码结构如下

[![](http://open.seeyon.com:4567/tmp/c6c3f4f1ff66aa10e46010d8d3dc62973b30401e.png "Graphviz image")](http://open.seeyon.com:4567/tmp/c6c3f4f1ff66aa10e46010d8d3dc62973b30401e.png)

##### 版本管理

jar的命名规则为

```
seeyon-{分类}-{模块名称}-{主版本}.{次版本}.{增量版本}-{里程碑版本}.jar


分类取值为ctp、apps、api
模块名称如core、collaboration

如 seeyon-ctp-core-1.1.12-alpha-1.jar
```

* 除非发生重大架构变更，今后很长一段时间，我们的主版本号均使用1，如
  `seeyon-ctp-core-1.2.1`
* 增加了新特性次改变版本号。
* 进行了重大Bug修复时改变增量版本号。
* 里程碑版本如 alpha-1、beta-2
* 版本号记录在meta中，release时去除jar的版本号 示例如下

seeyon-ctp-common

seeyon-ctp-organization

seeyon-ctp-portal

seeyon-ctp-workflow

seeyon-ctp-report

seeyon-apps-collaboration

seeyon-apps-news

seeyon-apps-meeting

##### POM

| 值域 | 取值 | 备注 |
| :--- | :--- | :--- |
| Group Id | com.seeyon.{category} | category取值范围为ctp、apps、api，如com.seeyon.ctp、com.seeyon.apps、com.seeyon.api |
| Artifact Id | {category}-{moduleName} | category同上，如ctp-core、apps-collaboration、apps-news |
| Version |  |  |
| Packaging | jar |  |

##### 产品部署包版本

* ftp目录：productPack/v5/6.0/
* 文件名 : SVN版本号+产品内容

### 文档规范

> 面向开发、测试和维护人员。
>
> 详细的文档帮助开发人员快速学习，降低解释成本。
>
> 详细的文档有助于单元测试建立用例，保证代码质量。
>
> 详细的文档有助于后续的维护人员管理。

使用Markdown作为设计和开发手册文档工具。 使用javadoc编写API文档，文档可以使用Markdown语法，。

* 文档在Interface中编写，Impl不需重复。
* 需写清楚场景

```
/**
   * 
<
pre
>

   * 得到我能访问的组
   * 正常情况：
   * 1、普通用户包括（前提是这个单位下的组）:
   * 1.1 我建的私有组
   * 1.2 我是成员或关联人员系统组
   * 1.3 公开范围有我的系统组(单位、集团、项目)
   * 
<
del
>
1.4 我是部门管理员的部门系统组
<
/del
>

   *
   * 2、单位管理员：
   * 2.1 这个单位所有的单位系统组（不包括部门组）
   * 2.2 集团公开范围有这个单位的系统组
   *
   * 3、集团、审计、系统管理员管理员
   * 3.1 所有的集团系统组
   * 3.2 这个单位的系统组
   * 
<
del
>
2. 公开范围有我的系统组
<
/del
>

   *
   * 4.其它:
   * 4.1 看到集团组的前提是单位在集团树下
   * 
<
/pre
>

   *
   *
   * @param memberId 人员id
   * @param accountId 单位id，每次都返回集团组
   * @return 组实体列表
   * @throws BusinessException
   */
List
<
V3xOrgTeam
>
getTeamsByMember
(
Long
memberId
,
Long
accountId
)
throws
BusinessException
;
```

* PO的文档统一写在setter方法上，getter不用赘述。
* 善用@link

```
/**
   * 按Id取岗位。 如果id为空或-1，则返回未分配岗位（id为{@link com.seeyon..organization.domain.OrgEntity#DEFAULT_NULL_ID 空}）。
   * 
   * @param id
   *            岗位Id
   * @return 岗位实体
   * @throws BusinessException
   */
V3xOrgPost
getPostById
(
Long
id
)
throws
BusinessException
;
```

* 参数和返回值要写清楚各种取值的含义

```
/**
   * 获取岗位下的人员，支持标准岗
   * 
<
pre
>

   * /--- PostId --|-- accountId --|--------------- 返回值 ------------/
   * |   标准岗    |   null/集团ID |   全集团所有单位引用自建岗下的人员  |
   * |   标准岗    |   单位ID      |   指定单位引用自建岗下的人员        |
   * |   单位自建岗|   此参数被忽略|   指定单位引用自建岗下的人员        |
   * 
<
/pre
>

   * 
   * @param postId
   * @param accountId
   * @return
   * @throws BusinessException
   */
public
List
<
V3xOrgMember
>
getMembersByPost
(
Long
postId
,
Long
accountId
)
throws
BusinessException
;
```

* 后续追加的接口和方法要写清楚支持的版本

```
@since
5.0
Sp2
@since
5.1
Sp1
m201411
```

* 支持并强烈建议使用Markdown语法书写javadoc

      /**
         * 获取岗位下的人员，支持集团基准岗，限制单位可见范围
         * 
         * - 首先
         * - 其次
         * 
         * ```java
         *     // 示例代码
         *     OrgManager orgManager;
         *     orgManager.getMembersByPost4Access();
         * ```
         * 
         * | PostId                  | accountId     | 返回值                              |
         * |-------------------------|---------------|-------------------------------------|
         * |   标准岗                |   null/集团ID |   全集团所有单位引用自建岗下的人员  |
         * |   标准岗                |   单位ID      |   指定单位引用自建岗下的人员        |
         * |   单位自建岗            |   此参数被忽略|   指定单位引用自建岗下的人员        |
         * @param postId 岗位id
         * @param accountId 当前登录者的当前登录单位id
         * @return
         * @throws BusinessException
         */
      List
      <
      V3xOrgMember
      >
      getMembersByPost4Access
      (
      Long
      postId
      ,
      Long
      accountId
      )
      throws
      BusinessException
      ;

* 使用中文标点符号。



