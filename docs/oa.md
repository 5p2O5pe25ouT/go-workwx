# OA数据接口

## Models

### `OAApplyEvent` 提交审批申请

Name|JSON|Type|Doc
:---|:---|:---|:--
`CreatorUserID`|`creator_userid`|`string`| 申请人userid，此审批申请将以此员工身份提交，申请人需在应用可见范围内
`TemplateID`|`template_id`|`string`| 模板id。可在“获取审批申请详情”、“审批状态变化回调通知”中获得，也可在审批模板的模板编辑页面链接中获得。暂不支持通过接口提交[打卡补卡][调班]模板审批单。
`UseTemplateApprover`|`use_template_approver`|`uint8`| 审批人模式：0-通过接口指定审批人、抄送人（此时approver、notifyer等参数可用）; 1-使用此模板在管理后台设置的审批流程，支持条件审批。默认为0
`Approver`|`approver`|`[]OAApprover`| 审批流程信息，用于指定审批申请的审批流程，支持单人审批、多人会签、多人或签，可能有多个审批节点，仅use_template_approver为0时生效。
`Notifier`|`notifyer`|`[]string`| 抄送人节点userid列表，仅use_template_approver为0时生效。
`NotifyType`|`notify_type`|`*uint8`| 抄送方式：1-提单时抄送（默认值）； 2-单据通过后抄送；3-提单和单据通过后抄送。仅use_template_approver为0时生效。
`ApplyData`|`apply_data`|`OAContents`| 审批申请数据，可定义审批申请中各个控件的值，其中必填项必须有值，选填项可为空，数据结构同“获取审批申请详情”接口返回值中同名参数“apply_data”
`SummaryList`|`summary_list`|`[]OASummaryList`| 摘要信息，用于显示在审批通知卡片、审批列表的摘要信息，最多3行

### `OAApprover` 审批流程信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Attr`|`attr`|`uint8`| 节点审批方式：1-或签；2-会签，仅在节点为多人审批时有效
`UserID`|`userid`|`[]string`| 审批节点审批人userid列表，若为多人会签、多人或签，需填写每个人的userid

### `OAContent` 审批申请详情，由多个表单控件及其内容组成，其中包含需要对控件赋值的信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Control`|`control`|`OAControl`| 控件类型：Text-文本；Textarea-多行文本；Number-数字；Money-金额；Date-日期/日期+时间；Selector-单选/多选；；Contact-成员/部门；Tips-说明文字；File-附件；Table-明细；
`ID`|`id`|`string`| 控件id：控件的唯一id，可通过“获取审批模板详情”接口获取
`Title`|`title`|`[]OAText`| 控件名称 ，若配置了多语言则会包含中英文的控件名称
`Value`|`value`|`OAContentValue`| 控件值 ，需在此为申请人在各个控件中填写内容不同控件有不同的赋值参数，具体说明详见附录。模板配置的控件属性为必填时，对应value值需要有值。
`Hidden`|`hidden`|`uint8`| 控件隐藏标识，为1表示控件被隐藏

### `OAContents` 审批申请详情，由多个表单控件及其内容组成，其中包含需要对控件赋值的信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Contents`|`contents`|`[]OAContent`| 审批申请详情，由多个表单控件及其内容组成，其中包含需要对控件赋值的信息

### `OAText` 通用文本信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Text`|`text`|`string`| 文字
`Lang`|`lang`|`string`| 语言

### `OASummaryList` 摘要行信息，用于定义某一行摘要显示的内容

Name|JSON|Type|Doc
:---|:---|:---|:--
`SummaryInfo`|`summary_info`|`[]OAText`| 摘要行信息，用于定义某一行摘要显示的内容

### `OAContentValue` 控件值 ，需在此为申请人在各个控件中填写内容不同控件有不同的赋值参数，具体说明详见附录。模板配置的控件属性为必填时，对应value值需要有值。

Name|JSON|Type|Doc
:---|:---|:---|:--
`Text`|`text`|`string`| 文本/多行文本控件（control参数为Text或Textarea）
`Number`|`new_number`|`string`| 数字控件（control参数为Number）
`Money`|`new_money`|`string`| 金额控件（control参数为Money）
`Date`|`date`|`OAContentDate`| 日期/日期+时间控件（control参数为Date）
`Selector`|`selector`|`OAContentSelector`| 单选/多选控件（control参数为Selector）
`Members`|`members`|`[]OAContentMember`| 成员控件（control参数为Contact，且value参数为members）
`Departments`|`departments`|`[]OAContentDepartment`| 部门控件（control参数为Contact，且value参数为departments）
`Files`|`files`|`[]OAContentFile`| 附件控件（control参数为File，且value参数为files）
`Table`|`children`|`[]OAContentTableList`| 明细控件（control参数为Table）
`Vacation`|`vacation`|`OAContentVacation`| 假勤组件-请假组件（control参数为Vacation）
`Location`|`location`|`OAContentLocation`| 位置控件（control参数为Location，且value参数为location）
`RelatedApproval`|`related_approval`|`[]OAContentRelatedApproval`| 关联审批单控件（control参数为RelatedApproval，且value参数为related_approval）
`Formula`|`formula`|`OAContentFormula`| 公式控件（control参数为Formula，且value参数为formula）
`DateRange`|`date_range`|`OAContentDateRange`| 时长组件（control参数为DateRange，且value参数为date_range）

### `OAContentDate` 日期/日期+时间内容

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 时间展示类型：day-日期；hour-日期+时间 ，和对应模板控件属性一致
`Timestamp`|`s_timestamp`|`string`| 时间戳-字符串类型，在此填写日期/日期+时间控件的选择值，以此为准

### `OAContentSelector` 类型标志，单选/多选控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 选择方式：single-单选；multi-多选
`Options`|`options`|`[]OAContentSelectorOption`| 多选选项，多选属性的选择控件允许输入多个

### `OAContentSelectorOption` 多选选项，多选属性的选择控件允许输入多个

Name|JSON|Type|Doc
:---|:---|:---|:--
`Key`|`key`|`string`| 选项key，可通过“获取审批模板详情”接口获得

### `OAContentMember` 所选成员内容，即申请人在此控件选择的成员，多选模式下可以有多个

Name|JSON|Type|Doc
:---|:---|:---|:--
`UserID`|`userid`|`string`| 所选成员的userid
`Name`|`name`|`string`| 成员名

### `OAContentDepartment` 所选部门内容，即申请人在此控件选择的部门，多选模式下可能有多个

Name|JSON|Type|Doc
:---|:---|:---|:--
`OpenAPIID`|`openapi_id`|`string`| 所选部门id
`Name`|`name`|`string`| 所选部门名

### `OAContentFile` 附件

Name|JSON|Type|Doc
:---|:---|:---|:--
`FileID`|`file_id`|`string`| 文件id，该id为临时素材上传接口返回的的media_id，注：提单后将作为单据内容转换为长期文件存储；目前一个审批申请单，全局仅支持上传6个附件，否则将失败。

### `OAContentTableList` 子明细列表，在此填写子明细的所有子控件的值，子控件的数据结构同一般控件

Name|JSON|Type|Doc
:---|:---|:---|:--
`List`|`list`|`[]OAContent`| 子明细列表，在此填写子明细的所有子控件的值，子控件的数据结构同一般控件

### `OAContentVacation` 请假内容，即申请人在此组件内选择的请假信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Selector`|`selector`|`OAContentSelector`| 请假类型，所选选项与假期管理关联，为假期管理中的假期类型
`Attendance`|`attendance`|`OAContentVacationAttendance`| 假勤组件

### `OAContentVacationAttendance` 假勤组件

Name|JSON|Type|Doc
:---|:---|:---|:--
`DateRange`|`date_range`|`OAContentVacationAttendanceDateRange`| 假勤组件时间选择范围
`Type`|`type`|`uint8`| 假勤组件类型：1-请假；3-出差；4-外出；5-加班

### `OAContentVacationAttendanceDateRange` 假勤组件时间选择范围

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 时间展示类型：day-日期；hour-日期+时间
``|``|`OAContentDateRange`| 时长范围

### `OAContentLocation` 位置控件

Name|JSON|Type|Doc
:---|:---|:---|:--
`Latitude`|`latitude`|`string`| 纬度，精确到6位小数
`Longitude`|`longitude`|`string`| 经度，精确到6位小数
`Title`|`title`|`string`| 地点标题
`Address`|`address`|`string`| 地点详情地址
`Time`|`time`|`int`| 选择地点的时间

### `OAContentRelatedApproval` 关联审批单控件

Name|JSON|Type|Doc
:---|:---|:---|:--
`SpNo`|`sp_no`|`string`| 关联审批单的审批单号

### `OAContentFormula` 公式控件

Name|JSON|Type|Doc
:---|:---|:---|:--
`Value`|`value`|`string`| 公式的值，提交表单时无需填写，后台自动计算

### `OAContentDateRange` 时长组件

Name|JSON|Type|Doc
:---|:---|:---|:--
`NewBegin`|`new_begin`|`int`| 开始时间，unix时间戳
`NewEnd`|`new_end`|`int`| 结束时间，unix时间戳
`NewDuration`|`new_duration`|`int`| 时长范围，单位秒


### `OATemplateDetail` 审批模板详情

Name|JSON|Type|Doc
:---|:---|:---|:--
`TemplateNames`|`template_names`|`[]OAText`| 模板名称，若配置了多语言则会包含中英文的模板名称，默认为zh_CN中文
`TemplateContent`|`template_content`|`OATemplateControls`| 模板控件信息
`Vacation`|`vacation_list`|`OATemplateControlConfigVacation`| Vacation控件（假勤控件）

### `OATemplateControls` 模板控件数组。模板详情由多个不同类型的控件组成，控件类型详细说明见附录。

Name|JSON|Type|Doc
:---|:---|:---|:--
`Controls`|`controls`|`[]OATemplateControl`| 模板名称，若配置了多语言则会包含中英文的模板名称，默认为zh_CN中文

### `OATemplateControl` 模板控件信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Property`|`property`|`OATemplateControlProperty`| 模板控件属性，包含了模板内控件的各种属性信息
`Config`|`config`|`OATemplateControlConfig`| 模板控件配置，包含了部分控件类型的附加类型、属性，详见附录说明。目前有配置信息的控件类型有：Date-日期/日期+时间；Selector-单选/多选；Contact-成员/部门；Table-明细；Attendance-假勤组件（请假、外出、出差、加班）

### `OATemplateControlProperty` 模板控件属性

Name|JSON|Type|Doc
:---|:---|:---|:--
`Control`|`control`|`OAControl`| 模板控件属性，包含了模板内控件的各种属性信息
`ID`|`id`|`string`| 模板控件配置，包含了部分控件类型的附加类型、属性，详见附录说明。目前有配置信息的控件类型有：Date-日期/日期+时间；Selector-单选/多选；Contact-成员/部门；Table-明细；Attendance-假勤组件（请假、外出、出差、加班）
`Title`|`title`|`[]OAText`| 模板控件配置，包含了部分控件类型的附加类型、属性，详见附录说明。目前有配置信息的控件类型有：Date-日期/日期+时间；Selector-单选/多选；Contact-成员/部门；Table-明细；Attendance-假勤组件（请假、外出、出差、加班）
`Placeholder`|`placeholder`|`[]OAText`| 模板控件配置，包含了部分控件类型的附加类型、属性，详见附录说明。目前有配置信息的控件类型有：Date-日期/日期+时间；Selector-单选/多选；Contact-成员/部门；Table-明细；Attendance-假勤组件（请假、外出、出差、加班）
`Require`|`require`|`uint8`| 是否必填：1-必填；0-非必填
`UnPrint`|`un_print`|`uint8`| 是否参与打印：1-不参与打印；0-参与打印

### `OATemplateControlConfig` 模板控件配置

Name|JSON|Type|Doc
:---|:---|:---|:--
`Date`|`date`|`OATemplateControlConfigDate`| Date控件（日期/日期+时间控件）
`Selector`|`selector`|`OATemplateControlConfigSelector`| Selector控件（单选/多选控件）
`Contact`|`contact`|`OATemplateControlConfigContact`| Contact控件（成员/部门控件）
`Table`|`table`|`OATemplateControlConfigTable`| Table（明细控件）
`Attendance`|`attendance`|`OATemplateControlConfigAttendance`| Attendance控件（假勤控件）

### `OATemplateControlConfigDate` 类型标志，日期/日期+时间控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 时间展示类型：day-日期；hour-日期+时间

### `OATemplateControlConfigSelector` 类型标志，单选/多选控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 选择类型：single-单选；multi-多选
`Options`|`options`|`[]OATemplateControlConfigSelectorOption`| 选项，包含单选/多选控件中的所有选项，可能有多个

### `OATemplateControlConfigSelectorOption` 选项，包含单选/多选控件中的所有选项，可能有多个

Name|JSON|Type|Doc
:---|:---|:---|:--
`Key`|`key`|`string`| 选项key，选项的唯一id，可用于发起审批申请，为单选/多选控件赋值
`Value`|`value`|`[]OAText`| 选项值，若配置了多语言则会包含中英文的选项值，默认为zh_CN中文

### `OATemplateControlConfigContact` 类型标志，单选/多选控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 选择类型：single-单选；multi-多选
`Mode`|`mode`|`string`| 选择对象：user-成员；department-部门

### `OATemplateControlConfigTable` 类型标志，明细控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Children`|`children`|`[]OATemplateControl`| 明细内的子控件，内部结构同controls

### `OATemplateControlConfigAttendance` 类型标志，假勤控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`DateRange`|`date_range`|`OATemplateControlConfigAttendanceDateRange`| 假期控件属性
`Type`|`type`|`uint8`| 假勤控件类型：1-请假，3-出差，4-外出，5-加班

### `OATemplateControlConfigAttendanceDateRange` 假期控件属性

Name|JSON|Type|Doc
:---|:---|:---|:--
`Type`|`type`|`string`| 时间刻度：hour-精确到分钟, halfday—上午/下午

### `OATemplateControlConfigVacation` 类型标志，假勤控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`Item`|`item`|`[]OATemplateControlConfigVacationItem`| 单个假期类型属性

### `OATemplateControlConfigVacationItem` 类型标志，假勤控件的config中会包含此参数

Name|JSON|Type|Doc
:---|:---|:---|:--
`ID`|`id`|`int`| 假期类型标识id
`Name`|`name`|`[]OAText`| 假期类型名称，默认zh_CN中文名称

```go
// OAControl 控件类型
type OAControl string

// OAControlText 文本
const OAControlText OAControl = "Text"

// OAControlTextarea 多行文本
const OAControlTextarea OAControl = "Textarea"

// OAControlNumber 数字
const OAControlNumber OAControl = "Number"

// OAControlMoney 金额
const OAControlMoney OAControl = "Money"

// OAControlDate 日期/日期+时间控件
const OAControlDate OAControl = "Date"

// OAControlSelector 单选/多选控件
const OAControlSelector OAControl = "Selector"

// OAControlContact 成员/部门控件
const OAControlContact OAControl = "Contact"

// OAControlTips 说明文字控件
const OAControlTips OAControl = "Tips"

// OAControlFile 附件控件
const OAControlFile OAControl = "File"

// OAControlTable 明细控件
const OAControlTable OAControl = "Table"

// OAControlLocation 位置控件
const OAControlLocation OAControl = "Location"

// OAControlRelatedApproval 关联审批单控件
const OAControlRelatedApproval OAControl = "RelatedApproval"

// OAControlFormula 公式控件
const OAControlFormula OAControl = "Formula"

// OAControlDateRange 时长控件
const OAControlDateRange OAControl = "DateRange"

// OAControlVacation 假勤组件-请假组件
const OAControlVacation OAControl = "Vacation"

// OAControlAttendance 假勤组件-出差/外出/加班组件
const OAControlAttendance OAControl = "Attendance"
```

### `OAApprovalDetail` 审批申请详情

Name|JSON|Type|Doc
:---|:---|:---|:--
`SpNo`|`sp_no`|`string`| 审批编号
`SpName`|`sp_name`|`string`| 审批申请类型名称（审批模板名称）
`SpStatus`|`sp_status`|`uint8`| 申请单状态：1-审批中；2-已通过；3-已驳回；4-已撤销；6-通过后撤销；7-已删除；10-已支付
`TemplateID`|`template_id`|`string`| 审批模板id。可在“获取审批申请详情”、“审批状态变化回调通知”中获得，也可在审批模板的模板编辑页面链接中获得。
`ApplyTime`|`apply_time`|`int`| 审批申请提交时间,Unix时间戳
`Applicant`|`applyer`|`OAApprovalDetailApplicant`| 申请人信息
`SpRecord`|`sp_record`|`[]OAApprovalDetailSpRecord`| 审批流程信息，可能有多个审批节点。
`Notifier`|`notifyer`|`[]OAApprovalDetailNotifier`| 抄送信息，可能有多个抄送节点
`ApplyData`|`apply_data`|`OAContents`| 审批申请数据
`Comments`|`comments`|`[]OAApprovalDetailComment`| 审批申请备注信息，可能有多个备注节点

### `OAApprovalDetailApplicant` 审批申请详情申请人信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`UserID`|`userid`|`string`| 申请人userid
`PartyID`|`partyid`|`string`| 申请人所在部门id

### `OAApprovalDetailSpRecord` 审批流程信息，可能有多个审批节点。

Name|JSON|Type|Doc
:---|:---|:---|:--
`SpStatus`|`sp_status`|`uint8`| 审批节点状态：1-审批中；2-已同意；3-已驳回；4-已转审
`ApproverAttr`|`approverattr`|`uint8`| 节点审批方式：1-或签；2-会签
`Details`|`details`|`[]OAApprovalDetailSpRecordDetail`| 审批节点详情,一个审批节点有多个审批人

### `OAApprovalDetailSpRecordDetail` 审批节点详情,一个审批节点有多个审批人

Name|JSON|Type|Doc
:---|:---|:---|:--
`Approver`|`approver`|`OAApprovalDetailSpRecordDetailApprover`| 分支审批人
`Speech`|`speech`|`string`| 审批意见
`SpStatus`|`sp_status`|`uint8`| 分支审批人审批状态：1-审批中；2-已同意；3-已驳回；4-已转审
`SpTime`|`sptime`|`int`| 节点分支审批人审批操作时间戳，0表示未操作
`MediaID`|`media_id`|`[]string`| 节点分支审批人审批意见附件，media_id具体使用请参考：文档-获取临时素材

### `OAApprovalDetailSpRecordDetailApprover` 分支审批人

Name|JSON|Type|Doc
:---|:---|:---|:--
`UserID`|`userid`|`string`| 分支审批人userid

### `OAApprovalDetailNotifier` 抄送信息，可能有多个抄送节点

Name|JSON|Type|Doc
:---|:---|:---|:--
`UserID`|`userid`|`string`| 节点抄送人userid

### `OAApprovalDetailComment` 审批申请备注信息，可能有多个备注节点

Name|JSON|Type|Doc
:---|:---|:---|:--
`CommentUserInfo`|`commentUserInfo`|`OAApprovalDetailCommentUserInfo`| 备注人信息
`CommentTime`|`commenttime`|`int`| 备注提交时间戳，Unix时间戳
`CommentTontent`|`commentcontent`|`string`| 备注文本内容
`CommentID`|`commentid`|`string`| 备注id
`MediaID`|`media_id`|`[]string`| 备注附件id，可能有多个，media_id具体使用请参考：文档-获取临时素材

### `OAApprovalDetailCommentUserInfo` 备注人信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`UserID`|`userid`|`string`| 备注人userid

### `OAApprovalInfoFilter` 备注人信息

Name|JSON|Type|Doc
:---|:---|:---|:--
`Key`|`key`|`OAApprovalInfoFilterKey`| 筛选类型，包括：template_id - 模板类型/模板id；creator - 申请人；department - 审批单提单者所在部门；sp_status - 审批状态。注意:仅“部门”支持同时配置多个筛选条件。不同类型的筛选条件之间为“与”的关系，同类型筛选条件之间为“或”的关系
`Value`|`value`|`string`| 筛选值，对应为：template_id - 模板id；creator - 申请人userid；department - 所在部门id；sp_status - 审批单状态（1-审批中；2-已通过；3-已驳回；4-已撤销；6-通过后撤销；7-已删除；10-已支付）

```go
// OAApprovalInfoFilterKey 拉取审批筛选类型
type OAApprovalInfoFilterKey string

// OAApprovalInfoFilterKeyTemplateID 模板类型
const OAApprovalInfoFilterKeyTemplateID  OAApprovalInfoFilterKey = "template_id"

// OAApprovalInfoFilterKeyCreator 申请人
const OAApprovalInfoFilterKeyCreator   OAApprovalInfoFilterKey = "creator"

// OAApprovalInfoFilterKeyDepartment 审批单提单者所在部门
const OAApprovalInfoFilterKeyDepartment   OAApprovalInfoFilterKey = "department"

// OAApprovalInfoFilterKeySpStatus 审批状态
const OAApprovalInfoFilterKeySpStatus  OAApprovalInfoFilterKey = "sp_status"
```
