---
title: 人岗画像
---

#### 订单表增加字段

```sql
use hunter_transaction;
set names utf8;
ALTER TABLE `hunter_transaction`.`hunter_order`   
  ADD COLUMN `tob_new_resume_id` BIGINT(20) UNSIGNED DEFAULT 0 NOT NULL COMMENT 'tob新简历id' AFTER `tob_resume_id`;
```

#### 刷tob_new_resume_id数据脚本

根据tob接口刷tob_new_resume_id

`tmpexec flushOrders`

#### 订单内部rpc接口
1. 获取订单列表,获取订单详情增加返回`tob_new_resume_id`

#### H端画像代理

1. [代理实现逻辑](http://192.168.1.150:8090/pages/viewpage.action?pageId=56033679)

#### H端API接口

1. [获取岗位画像文本信息](http://192.168.1.150:8090/pages/viewpage.action?pageId=56033954)
2. [获取岗位画像详情](http://192.168.1.150:8090/pages/viewpage.action?pageId=56033951)

#### H端岗位画像url

`http://www.testing2.chengdan.ai/portrait/review_detail?icdc_position_id=6839275&uid=1226`
