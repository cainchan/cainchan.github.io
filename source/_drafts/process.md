---

---


#### DDL

##### 1. 招聘流程/阶段设置表

```sql
CREATE TABLE `process` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `uid` int(10) unsigned NOT NULL DEFAULT '0',
  `admin_depart_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '行政组织id',
  `process_name` varchar(60) NOT NULL DEFAULT '' COMMENT '流程名称',
  `stage_ids` varchar(200) NOT NULL DEFAULT '' COMMENT '阶段id列表json',
  `description` varchar(500) NOT NULL DEFAULT '' COMMENT '描述',
  `status` tinyint(2) unsigned NOT NULL DEFAULT '1' COMMENT '状态 1=正常 2=禁用',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='招聘流程表';

CREATE TABLE `stage` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `uid` int(10) unsigned NOT NULL DEFAULT '0',
  `process_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '流程id',
  `stage_name` varchar(30) NOT NULL DEFAULT '' COMMENT '阶段名称',
  `action_ids` varchar(1000) NOT NULL DEFAULT '' COMMENT '操作项id列表json',
  `description` varchar(500) NOT NULL DEFAULT '' COMMENT '描述',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='阶段表';

CREATE TABLE `process_stage_rule_target` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `stage_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '阶段id',
  `action_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '操作项id',
  `status_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '操作项状态/结果id',
  `target_stage_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '目标阶段id',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='触发规则表';
```



##### 2.功能/操作项/状态/结果表
![](http://192.168.20.40:4999/server/../Public/Uploads/2020-01-08/5e15c05d9d1cc.png)

```sql
CREATE TABLE `feature` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `feature_name` varchar(50) NOT NULL DEFAULT '' COMMENT '功能名称',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='功能表';


CREATE TABLE `feature_action` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `feature_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '功能id',
  `role_type` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '操作对象 1=HR 2=协同账号 3=候选人',
  `action_name` varchar(50) NOT NULL DEFAULT '0' COMMENT '操作项名称',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='功能操作项表';


CREATE TABLE `feature_status` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `feature_id` int(10) unsigned NOT NULL DEFAULT '0',
  `role_type` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '操作对象 1=HR 2=协同账号 3=候选人',
  `status_name` varchar(50) NOT NULL DEFAULT '0' COMMENT '状态名称',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='功能状态表';



CREATE TABLE `feature_result` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `feature_id` int(10) unsigned NOT NULL DEFAULT '0',
  `role_type` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '操作对象 1=HR 2=协同账号 3=候选人',
  `result_name` varchar(50) NOT NULL DEFAULT '' COMMENT '结果名称',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='操作结果表';

CREATE TABLE `action_status_result` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `feature_action_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '操作项id',
  `feature_status_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '状态id',
  `feature_result_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '结果id',
  `is_deleted` tinyint(10) unsigned NOT NULL DEFAULT '0',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='操作项状态结果关联表';
```



##### 3.流程记录表

```sql
CREATE TABLE `ats` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `uid` int(10) unsigned NOT NULL DEFAULT '0',
  `process_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '招聘流程id',
  `stage_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '当前阶段id',
  `position_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '职位id',
  `resume_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '简历id',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='流程表';

CREATE TABLE `ats_record` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `uid` int(10) unsigned NOT NULL DEFAULT '0',
  `process_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '流程id',
  `ats_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'ats表id',
  `stage_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '阶段id',
  `position_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '职位id',
  `action_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '操作项id',
  `status_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '状态id',
  `result_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '结果id',
  `is_active` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否最新 0=否 1=是',
  `is_cancel` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否取消 0=否 1=是',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='流程记录表';

CREATE TABLE `ats_extend` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0',
  `sid` int(10) unsigned NOT NULL DEFAULT '0',
  `uid` int(10) unsigned NOT NULL DEFAULT '0',
  `process_id` int(10) unsigned NOT NULL DEFAULT '0',
  `ats_id` bigint(20) unsigned NOT NULL DEFAULT '0',
  `ats_record_id` bigint(20) unsigned NOT NULL DEFAULT '0',
  `role_type` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '操作对象 1=HR 2=协同账号 3=候选人',
  `role_id` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '操作对象id 当role_type=1||2:uid, 当role_type=3:resume_id',
  `mobile` varchar(20) NOT NULL DEFAULT '' COMMENT '手机',
  `email` varchar(50) NOT NULL DEFAULT '' COMMENT '邮箱',
  `feature_status_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '状态id',
  `feature_result_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '结果id',
  `feedback_role` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '反馈角色 1=HR 2=协同账号 4=候选人',
  `feedback_time` datetime DEFAULT NULL COMMENT '反馈时间',
  `is_cancel` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否取消 0=否 1=是',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='流程记录扩展表';
```





#### API

1. 获取流程列表
2. 获取功能列表
3. 保存流程
    - 流程名
    - 阶段名称和对应所选功能列表
4. 禁用/启用/删除流程
    - 流程id
5. 获取操作项列表
6. 获取操作项状态/结果列表
7. 获取流程详情
    - 流程id
8. 获取功能列表




#### Logic

1. 每一次操作项操作时都根据action_id获取前置条件,判断是否都满足条件?
2. 每一次操作项操作后都根据action_id和status_id获取触发条件.判断是否需要转阶段?新增流程状态记录数据.