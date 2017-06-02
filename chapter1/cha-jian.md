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





