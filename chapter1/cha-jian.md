## 插件化

框架插件机制提供了全面的插件扩展开发支持，包括插件启动、插件自定义配置文件、i18n、spring和hibernate PO加载等

### 插件规范

1. 插件相关的配置文件存放于cfgHome/plugin目录中，文件夹名称需与插件id保持一致。其中包括插件基础配置、插件自定义配置文件、插件i18n国际化配置、

   spring配置文件

2. 插件代码包必须在“com.seeyon.apps.插件id”，比如com.seeyon.apps.samples

3. 插件的PO对象和配置文件必须在“com.seeyon.apps.插件id.po”下，可以带子包用于配置自定义HQL的hbm文件

### 插件基础配置

插件基础配置文件位于“cfgHome/plugin/插件id”目录下，文件名必须为pluginCfg.xml，例子如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<plugin>
    <id>samples</id>                                   
    <name>例子模块</name>                               
    <category>10000</category>
</plugin>
```

| [![1](images/callouts/1.png)](#framework_plugin1_1)id | 插件id， |
| --- | --- |
| [![2](images/callouts/2.png)](#framework_plugin1_2)name | 插件名称 |
| [![3](images/callouts/3.png)](#framework_plugin1_3)category | 插件类别，产品插件分类从10000至19999，客开插件从20000开始 |

### 插件启动初始化

系统启动时可以进行插件的启动和系统停止时的销毁操作，需继承自com.seeyon.ctp.common.AbstractSystemInitializer，并在spring中注册即可，例子代码如下

```
import com.seeyon.ctp.common.AbstractSystemInitializer;

public class SamplesInitializer extends AbstractSystemInitializer {
    public void destroy() {
        System.out.println("销毁Samples模块");
    }

    public void initialize() {
        System.out.println("初始化Samples模块");
    }
}
```

Spring注册例子如下（插件相关spring配置文件命名规则为spring-插件id-plugin.xml）：

```
<bean name="samples_systemInitializer" class="com.seeyon.apps.samples.SamplesInitializer">
  <property name="sortOrder">
    <value>7</value>
  </property>
</bean>
<bean name="samples_systemInitializer2" class="com.seeyon.apps.samples.SamplesInitializer2">
  <property name="sortOrder">
    <value>2</value>
  </property>
</bean>
```

其中sortOrder为插件初始化顺序从小到大执行，如果没有配置该属性，则优先执行，在没有配置的多个初始化类之间顺序不定

### 插件自定义配置文件

插件自定义配置文件位于“cfgHome/plugin/插件id”目录下，文件名必须为pluginProperties.xml，例子如下：

```
<?xml version="1.0" encoding="utf-8"?>
<ctpConfig>
    <samples>
        <test name="test配置">插件配置测试</test>
    </samples>
</ctpConfig>
```

该例中可通过AppContext.getSystemProperty\("samples.test.name"\)取得“test配置”，AppContext.getSystemProperty\("samples.test"\)取得“插件配置测试”

注意：属性key必须以插件id开始



### 插件国际化配置文件

插件国际化配置文件位于“cfgHome/plugin/插件id/i18n”目录下，按照JDK国际化文件的配置规则命名，可采用多组文件。使用方式请参考i18n国际化组件部分说明。

注意：属性key必须以插件id开始

默认插件国际化资源配置只能后台使用（ResourceUtil接口，国际化页面function - ctp:i18n），前端javascript无法使用，如果要开放给前端，可以在插件的i18n

文件夹根下创建名为“export\_to\_js.xml”导出为javascript的$.i18n函数可使用的国际化资源，具体请参考国际化一节



### 插件spring配置 {#spring}

插件spring配置文件位于“cfgHome/plugin/插件id/spring”目录下，可有多个spring配置文件，按照框架规范命名包括：

1. spring-插件id-controller.xml controller配置，url需要形如“/插件id/xxx.do”

2. spring-插件id-manager.xml manager配置，bean id需要形如“插件id\_mgr\_xxx”

3. spring-插件id-dao.xml dao配置，bean id需要形如“插件id\_dao\_xxx”

### 插件hibernatePO加载 {#hibernatepo}

插件的PO对象和配置文件必须在“com.seeyon.apps.插件id.po”下，可以带子包用于配置自定义HQL的hbm文件

