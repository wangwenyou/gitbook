**选人界面**

框架目前提供了简单的选人界面包装，可以通过下面的HTML定义选人组件

```html
<input type="text" id="spc1" name="spc1" class="comp"
comp="type:'selectPeople',mode:'open',value:'Department|-884316703172445_1,Member|1730833917365171641',text:'开发中心、人员2'"/>
```

通过javascript函数调用选人界面的方法如下:

```js
$.selectPeople({
 params : {
  text : '开发中心、人员1',
  value : 'Department|-884316703172445_1,Member|1730833917365171641'
 },
 callback : function(ret) {
  alert(ret.value + ":" + ret.text);
 }
});
```

支持的comp属性或$.selectPeople函数的params参数如下：

| 属性 | 必填 | 说明 | 实例 |
| :--- | :--- | :--- | :--- |
| type | 是 | 'selectPeople' | type:'selectPeople' |
| panels | 是 | 要显示的面板 | Account,Department、Team、Post、Level、Role、Outworker、RelatePeople组合，逗号分隔 |
| selectType | 是 | 可选择的类型，即模型中的前7个，逗号分隔 | Account、Department、Team、Post、Level、Role、Member,Email,Mobile |
| value | - | 值为SPD字符串 | value:'Member\|123456,Department\|23456' |
| - | - | 也可以是JS变量 | value:expr1options:{maxSize:1,showOriginal:true,showAccountShortname:true} |
| text | - | 选人显示值 | 如'开发中心、人员2' |
| mode | - | 选人弹出方式，缺省以div方式打开；mode:'open'时以模态对话框方式打开 | - |
| showMe | - | 是否显示当前登录者：true\|false | - |
| maxSize | - | 最多选择项目的个数 缺省-1，表示不限制 | - |
| minSize | - | 最少选择项目的个数 ，缺省为1 | - |
| elements | - | Element\[\] 原有数据，默认为null | - |
| showOriginalElement | - | true\|false 是否回显原有数据，默认为true | - |
| hiddenSaveAsTeam | - | true\|false 是否隐藏“另存为组”，默认为false | - |
| hiddenMultipleRadio | - | true\|false 是否隐藏“多层”按钮，默认为false | - |
| includeElements | - | Element\[\] 备选的数据范围，用于在指定范围内选人 | - |
| excludeElements | - | Element\[\] 不在备选项中显示 默认为null | - |
| isNeedCheckLevelScope | - | true\|false 是否进行职务级别范围验证 默认true | - |
| onlyLoginAccount | - | true\|false 是否只显示登录单位 默认false | - |
| showAccountShortname | - | yes\|no\|auto 是否只一直显示登录简称 默认auto | - |
| showConcurrentMember | - | true\|false 是否显示兼职人员（只外单位） true | - |
| hiddenPostOfDepartment | - | true\|false 是否隐藏部门下的岗位 默认false | - |
| hiddenRoleOfDepartment | - | true\|false 是否隐藏部门下的角色 默认false | - |
| onlyCurrentDepartment | - | true\|false 是否仅显示当前部门 默认false | - |
| showDeptPanelOfOutworker | - | true\|false 当是外部人员时，显示部门面板 默认false | - |
| unallowedSelectEmptyGroup | - | true\|false不允许选择空的组、部门, 默认false | - |
| showTeamType | - | "1,2,3" 需要显示的组类型1-个人,2-系统,3-项目, 默认null，表示所有 | - |

|  |
| :--- |


| hiddenOtherMemberOfTeam | - | true\|false 是否隐藏组下的外单人员，默认false | - |
| :--- | :--- | :--- | :--- |
| hiddenAccountIds | - | "1,2,3" 隐藏的单位，将不在单位下拉中出现 | - |
| isCanSelectGroupAccount | - | true\|false 是否可以选择集团单位，默认true | - |
| showAllOuterDepartment | - | true\|false 是否显示所有的外部部门，默认false | - |
| hiddenRootAccount | - | true\|false 是否隐藏集团单位，默认false | - |
| hiddenGroupLevel | - | true\|false 是否隐藏集团职务级别，默认false | - |
| showDepartmentsOfTree | - | "部门Id," 部门树上可以显示的部门 | - |
| showFixedRole | - | true\|false 显示固定角色，默认false | - |
| hiddenAddExternalAccount | - | true\|false 显示增加外部单位连接，默认false | - |
| showDepartmentMember4Search | - | true\|false 部门查询可用时，是否显示部门下面的成员，默认false，不显示 | - |
| isAllowContainsChildDept | - | true\|false 在部门面板选择部门时，是否允许同时选择父部门和子部门，默认为false，不允许 | - |
| isConfirmExcludeSubDepartment | - | true\|false 选择部门时，是否提示“是否包含子部门”，默认false即包含子部门 | - |
| returnValueNeedType | - | true\|false 返回值是否需要带类型，默认true，如果false则只返回Id，只用于单一选择 | &lt;input type="text" id="spc2" name="spc2" class="comp" comp="type:'selectPeople', panels:'Department',selectType:'Member',returnValueNeedType:false,maxSize:1"/&gt; |

实体类型

| 名称 | 代号 | 描述 |
| :--- | :--- | :--- |
| 单位 | Account | - |
| 部门 | Department | - |
| 岗位 | Post | - |
| 职务级别 | Level | - |
| 组 | Team | - |
| 人员 | Member | - |
| 角色 | Role | - |
| 外部人员 | Outworker | 在组织模型中不存在该类型，它其实是特殊的部门和人员 |
| 关联人员 | RelatePeople | 它其实是人员 |
| 外部单位 | ExchangeAccount | 只用于公文交换 |
| 其他 | 组织模型可自行扩展，但必须有相应的解析器 | - |

要重新刷新选人组件，重新设置value和text，可以调用：

$\("\#spc1"\).comp\({value:'newval', text:'newtext'}\);

将会取代之前comp属性中设置的value和text参数值

