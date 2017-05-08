## 1.1. 体系图

![](http://10.3.4.240:8080/userContent/ctp_pub/docs/resources/images/MVC.jpg)

## 1.2. 开发规范

1. 明确MVC各层职责，不能错误使用。

   例如Manager不能替代Dao的职责。

   HTTPRequest、HTTPResponse和HTTPSession只能出现在Controller，不允许传递到Manager层。

2. Controller层不允许编写业务逻辑，业务逻辑由Manager层处理。

   Controller层的一个方法只能调用Manager层中的一个方法，目的是为了确保统一事务。

   错误：

   ```
   public
   class
    CollaborationController {
    
   private
    CollaborationManager collaborationManager;
    
   private
    AttachmentManager attachementManager;
    
   public
    ModelAndView newCol(){
           collaborationManager.save(...);
           attachementManager.save(...);
       }
   }


   public
   class
    CollaborationManagerImpl 
   implements
    CollaborationManager{
    
   public
   void
    save(){
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
    ModelAndView newCol(){
           collaborationManager.save(...);
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
    save(){
           ...
           attachementManager.save(...);
       }
   }
   ```

3. Controller职责不宜过多，原则上一个Controller只封装一个完整业务逻辑（CRUD）。

4. Manager层中分简单复杂（或大小）方法，之间可以互相调用，目的是完成完整的业务逻辑。\(Manager本身就一层，并非像图中的三层，图中体现的是跨模块的Manager调用。\)

5. Manager和Dao层遵循接口开发要求，例如：Manager、ManagerImpl、Dao，DaoImpl。

6. 所有的异常需要抛到Manager层，由Manager层统一Catch后包装为BusinessException抛出。

   Manager的方法不允许抛出BusinessException之外的异常。

   建议Manager的所有方法都抛出BusinessException，但不作硬性要求（初期不抛，后续版本抛出异常会影响到调用代码）；

7. Dao不能省略，必须定义。

8. Dao层支持Hibernate和JDBC两种方式。

   原则上只允许使用Hibernate模式访问数据库。调用JDBC需评审并备案。

9. 只有Dao层能够书写HQL和SQL语句，Controller和Manager层不允许出现SQL。

10. 各层之间数据传递规范，PO与对应表字段一一对应，不允许增加额外的属性，不允许有业务逻辑；

    PO需继承BasePO，BasePO中已实现toXml、toJSON，出于性能考虑可以覆盖实现。

11. C层向V层只能返回VO对象，VO对象为类Map的结构。对于简单情况可以用PO代替VO。

    命名规范：XXX模块对应的MVC、DAO对应各层的命名规范为_`XXX`_Controller、_`XXX`_Manager、_`XXX`_Dao、_`XXX`_PO、spring-_`XXX`_-controller.xml、spring-_`XXX`_-manager.xml、_`xxx`_.hbm.xml

12. 基于接口编码，Manager和Dao必须定义接口，一个接口一个实现类。

13. 应用不允许自己调用Log4j，相应的日志由框架统一使用AOP记录。

14. 按模块和功能划分package，原则上一个package不能超过10对类（接口+实现）。

## 1.3. 工具规范

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



