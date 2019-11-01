---
title: 小e外呼
---

#### 1.DDL

```sql
CREATE DATABASE sly_caller;
CREATE TABLE `tasks` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `tid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '租户id',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户id',
  `group_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '外呼业务id',
  `task_name` varchar(100) NOT NULL DEFAULT '' COMMENT '任务名称',
  `talking_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '话术id',
  `recall_num` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '重呼次数',
  `recall_interval` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '重呼间隔 单位秒',
  `call_limit` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单日同一号码呼叫限制次数',
  `number_city_code` varchar(20) NOT NULL DEFAULT '' COMMENT '主叫号码归属地',
  `number_city_limit` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '只允许当前归属地号码呼叫',
  `priority` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '优先级 [0,1,2],数字越大优先级越高',
  `status` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '状态 0=待添加 1=待呼叫 2=进行中 3=已暂停 4=已完成',
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_status_userid` (`status`,`user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1000009 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务表';

CREATE TABLE `task_list` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `user_id` int(10) unsigned NOT NULL COMMENT '用户id',
  `task_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '任务id',
  `resume_id` bigint(10) unsigned NOT NULL DEFAULT '0' COMMENT '简历id',
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT '候选人姓名',
  `phone` varchar(11) NOT NULL DEFAULT '' COMMENT '候选人号码',
  `status` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '状态 1=待呼叫 2=呼叫中 3=已呼叫 4=已过期',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除',
  `call_begin_time` timestamp NOT NULL COMMENT '呼叫时间',
  `call_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '通话时长',
  `call_result` tinyint(2) NOT NULL DEFAULT '0' COMMENT '呼叫结果',
  `intention_result` varchar(100) NOT NULL DEFAULT '' COMMENT '意向结果',
  `call_voice_url` varchar(100) NOT NULL DEFAULT '' COMMENT '音频地址',
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_taskid_phone` (`task_id`,`phone`),
  KEY `idx_taskid_status_isdeleted` (`task_id`,`status`,`is_deleted`),
  KEY `idx_resumeid` (`resume_id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务号码表';
```

#### 2.API

##### 1.添加外呼任务

POST `/api/caller/addTask`

```json
{
    "task_name": "任务名称",
    "talking_id": 1, //话术id
    "list": [
        {
        	"resume_id" : 1,
            "name": "张三",
            "phone": "13899990000"
        },
        {
        	"resume_id" : 2,
            "name": "李四",
            "phone": "13899990001"
        }
    ],
    "priority": 0 //优先级
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": 1000002 //taskId
}
```



##### 2.获取外呼任务列表

POST `/api/caller/listTask`

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": [
        {
            "id": 1000000,
            "tid": 1,
            "user_id": 1,
            "group_id": 1,
            "task_name": "任务名称",
            "talking_id": 1,
            "recall_num": 1,
            "recall_interval": 600,
            "call_limit": 0,
            "number_city_code": "shanghai",
            "number_city_limit": 0,
            "priority": 0,
            "status": 1,
            "created_at": "2019-09-29 19:54:36",
            "updated_at": "2019-09-29 19:54:36"
        }
    ]
}
```

##### 3.获取外呼任务信息

POST `/api/caller/getTaskDetail`

```json
{
    "task_id": 1000000
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "id": 1000002,
        "tid": 1,
        "user_id": 1,
        "group_id": 1,
        "task_name": "任务名称",
        "talking_id": 1,
        "recall_num": 1,
        "recall_interval": 600,
        "call_limit": 0,
        "number_city_code": "shanghai",
        "number_city_limit": 0,
        "priority": 0,
        "status": 0,
        "created_at": "2019-09-29 20:23:20",
        "updated_at": "2019-09-29 20:23:20",
        "list": [
            {
                "id": 1,
                "user_id": 1,
                "task_id": 1000002,
                "resume_id": 1,
                "name": "张三",
                "phone": "13899990000",
                "status": 1,
                "is_deleted": 0,
                "call_begin_time": "0000-00-00 00:00:00",
                "call_time": 0,
                "call_result": 0,
                "call_voice_url": "",
                "created_at": "2019-09-29 20:23:20",
                "updated_at": "2019-09-29 20:23:20"
            },
            {
                "id": 2,
                "user_id": 1,
                "task_id": 1000002,
                "resume_id": 2,
                "name": "李四",
                "phone": "13899990001",
                "status": 1,
                "is_deleted": 0,
                "call_begin_time": "0000-00-00 00:00:00",
                "call_time": 0,
                "call_result": 0,
                "call_voice_url": "",
                "created_at": "2019-09-29 20:23:20",
                "updated_at": "2019-09-29 20:23:20"
            }
        ]
    }
}
```

##### 4.获取单个呼叫详情

POST `/api/caller/getCallerDetail`

```json
{
    "task_id":1000001,
    "phone":13899990001
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results":[]
}
```



#### 3.Schedule

##### 1.自动同步呼叫数据到业务端

- 将`* * * * * php artisan schedule:run `添加到Crontab,会每5分钟自动同步.

- 也可手动执行`php artisan caller:sync`联调.
