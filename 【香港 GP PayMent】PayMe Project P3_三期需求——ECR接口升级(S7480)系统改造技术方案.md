# 写在前面

本文档旨在定义EVO Cloud系统改造技术方案模板，后续技术方案的编写请复制并基于本文档进行。本文档中出现的章节不可删除，可以新增，对于不适用的章节，在相关标题下说明原因即可。



# **【香港 GP PayMent】PayMe Project P3_三期需求**

`文档版本：1.0`



## 修订记录

> 在方案评审、项目实施过程中发现问题，需及时更新技术方案
>
> 更新文档时，需同步更新本章节修订记录，提交到gitlab时请斟酌描述，确保commit log简明、准确
>
> 在项目实施过程中更新技术方案，建议不要删除文档中原有内容，而是采用删除线或代码块等形式，说明哪些是旧版本的弃用逻辑，这样在后续翻出来设计文档时，可以比较完整的体现当时的整体思路

| 版本 | 日期       | 修订内容 | 编写人 | 复核人 |
| :--- | :--------- | :------- | ------ | :----- |
| v1.0 | 2020/03/31 | 初版     | 王彬彬 |        |



## 1. 需求概述

> 需求背景、禅道链接、需求范围等
>

[**【香港 GP PayMent】PayMe Project P3_三期需求**](https://cil.5upm.com/story-view-7480.html)

背景：PayMe ECR功能主体功能已经完成，正在验收中，但是在内部验收和客户验收的过程中，对原有ECR接口增加了一些新的字段或发现原有字段联机未进行处理，因此提出该接口升级需求以满足客户的需求。

需求：联机和APP对附件接口文档中标黄部分的字段进行处理并做相应的返回。



## 2. 方案概要

#### 2.1 方案思路

> 说明本技术方案的实现思路、关键逻辑等

联机系统对应payme预下单接口，查询接口，以及退款接口，请求和应答增加需求中新增的字段。



#### 2.2 交互时序

> 用户、产品、内外部系统、数据库等交互时序图（需同步维护[时序图](https://git.cardinfolink.net/doc/evo-tech/tree/master/2.系统架构/时序图)）



#### 2.3 改造范围

* [ ] Nginx

* [ ] 域名配置

  > 若涉及到域名配置，参照[域名使用规则](./evo-cloud域名使用规则.xlsx)进行各个环境的配置

* [ ] 访问控制

* [ ] 平台前端
   - [ ] 通用平台
   - [ ] Alipay平台
   - [ ] BOCCC平台
   - [ ] NTTD平台

* [ ] 平台后台

* [x] 联机系统

* [ ] 清分系统

* [ ] 风控系统

* [ ] wapiti前端

* [ ] wapiti后台

* [ ] sparrow页面

* [ ] APP后台

* [ ] iOS APP

* [ ] Android APP

* [ ] Jenkins

* [ ] magisync

* [ ] interstellar

* [ ] sphinx

* [ ] task

涉及代码仓库参考：[跨境技术团队及系统配置管理.md](https://git.cardinfolink.net/doc/evo-tech/blob/master/6.配置管理/跨境技术团队及系统配置管理.md)

#### 2.4 外部依赖

> 接口文档、报文样例、联调系统环境、渠道资质、测试参数、资源申请、尚未明确的需求等



## 3. 数据库改造

> 新增/修改/删除表，细化到字段，关键字段重点说明（需同步维护[EVO Cloud系统数据库说明文档.md](https://git.cardinfolink.net/doc/evo-tech/tree/master/2.系统架构/数据库/EVO Cloud系统数据库说明文档.md)）

* [ ] 是否涉及数据库大表查询（若涉及，则需要额外关注查询语句，是否会导致慢查询）

* `online.trans.sp.new`
* `settle.trans.sp.new`
* `settle.transSett.sp.new`
* `config.logs.sp`
* `config.logs.sp.new`
* `config.chanMer`
* `trans.logs.chan`
* `trans.logs.store`
* `trans.trans`
* `cardpay.transLog`

* [ ] 若涉及到以上表，确认生产环境数据量是测试环境的**3倍以上**
  * [ ] 超过，则临时环境搭建模拟生产环境的数据量，进行测试 

## 4. 数据清洗

> 备份、gotest/脚本数据清洗、回退说明，**提供脚本的自测案例和证据**
>

无



## 5. 系统改造

> 各子系统作为独立小节阐述现状说明、改造思路、出发点和方案细节
>
> 如涉及接口改造需细化到字段，关键字段重点说明（需同步维护[商户接口](https://git.cardinfolink.net/doc/api-pay-mer)、[机构接口](https://git.cardinfolink.net/doc/api-pay-ins)和[alinter接口](http://www.sosoapi.com/pass/apidoc/share/forward.htm?shareKey=5adf136cd1841f2ba287dc514667b128)等接口文档）
>
> 明确代码分支名（基于master切出），格式：MMDD[-developer name]-feature/bugfix-newLogicDescription[-禅道caseNum]，如：0425-linus-feature-washDataForTransScene-123

 分支名：200401-feature-PaymeEcr-7480

#### 平台改造

- 改造涉及地址：

  - 预下单：{sid}/paut/v0
- 查询：/{sid}/inqy/v0
  - 退款：/{sid}/refd/v0

- 接口参数变更详情

  - 预下单
    - 请求参数：无变化
    - 应答参数：增加如下应答对应关系

  | ECR字段      | 联机对应字段       | 备注 |
  | ------------ | ------------------ | ---- |
  | shoppingCart | **goodsList**      |      |
  | sku          | **unifiedGoodsID** |      |
  | name         | goodsName          |      |
  | quantity     | goodsNum           |      |
  | price        | price              |      |
| currencyCode | transCurrency      |      |
  | category1    | goodsCategory      |      |
  | category2    | goodsCategory2     |      |
  | category3    | goodsCategory3     |      |

```json
// 示例如下
{
	"msgId": "f8ace570d80111e7b42e060400ef5316",
    "orderNum": "PME50202003310000003",
    "termId": "3451640",
    "respCode": "09",
    "errorDetail": "INPROCESS",
    "qrcode": "https://sacctprodmobsandbox.z7.web.core.windows.net//ee92a878-a30e-4716-ba9a-e2a1f5d81c69",
    "transAmt": "000000011280",
    "transCurrency": "HKD",
    "convTransAmt": "000000011280",
    "convTransCurrency": "HKD",
    "paymentBrand": "PME",
    "merTransTime": "20200310100405+0800",
    "sysTransTime": "20200331161306+0800",
    "chanRespCode": "PR001",
	"goodsList":"[{\"goodsId\":\"31\",\"goodsName\":\"fffff\",\"goodsNum\":\"123\",\"price\":\"1\",\"transCurrency\":\"HKD\",\"goodsCategory\":\"3223423\",\"goodsCategory2\":\"23423423\",\"goodsCategory3\":\"23423423\"}]"
}
```



- 查询接口

  - 请求参数：新增refNum

  | ECR字段 | 联机对应字段 | 备注 |
  | ------- | ------------ | ---- |
  | refNum  | refNum       |      |

  - 应答报文：无变化

- 退款接口

  - 请求参数，变更字段如下

  | ECR字段         | 联机对应字段  | 备注 |
  | --------------- | ------------- | ---- |
  | oriOrderNum     | origOrderNum  |      |
  | oriChanOrderNum | 无            |      |
  | refNum          | refNum        |      |
  | reasonCode      | reasonCode    |      |
  | reasonMessage   | reasonMessage |      |

  - 应答报文：对应参数如下

  | ECR字段       | 联机对应字段  | 备注 |
  | ------------- | ------------- | ---- |
  | refNum        | refNum        |      |
  | reasonCode    | reasonCode    |      |
  | reasonMessage | reasonMessage |      |



## 6. 影响评估（必填）

ps.针对其他节点的影响，需要特别说明，整理资料汇报给TIS，尤其是针对以下应用
* quickpay
* alinter

#### 6.1 改造关联影响

##### checklist

- [x] HKG系统
- [ ] TIS系统
- [ ] TYO系统
- [ ] 瑞穗系统
- [ ] 泰国卡系统
- [ ] 泰国码系统
- [ ] 印尼系统
- [ ] 线下系统
- [ ] 客户系统

##### 详情说明

> 对勾选项进行说明
>

修改只涉及payme相关接口，不影响tis。



#### 6.2. 系统变更影响

##### checklist

- 业务影响
  - [x] 影响交易
  - [ ] 影响平台
  - [ ] 影响清分
  - [ ] 影响APP

- 权限配置
  - [ ] 新增接口权限
  - [ ] 新增菜单权限且调整菜单层级
  - [ ] 和业务沟通为角色分配权限
  - [ ] 翻译已提供
- 应用关联性
  - [ ] 外部关联系统需投产
  - [ ] 内部系统应用投产顺序已检查
- 数据库
  - [ ] 表结构变更
  - [ ] 表索引变更
  - [ ] 清洗存量数据
  - [ ] 删除表
  - [ ] 更新配置数据
  - [ ] 新增配置数据
- Jenkins
  - [ ] server IP已copy-search检查
  - [ ] hosts相关配置检查
  - [ ] user-server匹配关系已检查
  - [ ] 首次登录已执行

##### 详情说明

> 根据需要对勾选项进行说明