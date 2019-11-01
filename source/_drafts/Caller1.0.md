---
title: 小e外呼
---

#### 1.DDL

```sql
CREATE DATABASE sly_caller;

CREATE TABLE `caller_setting` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `uniqid` int(10) unsigned NOT NULL COMMENT '用户id',
  `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司id',
  `talking_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '话术id',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '1=开启 2=关闭',
  `recall_num` tinyint(2) NOT NULL DEFAULT '0' COMMENT '重呼次数',
  `recall_interval` int(10) NOT NULL DEFAULT '0' COMMENT '重呼间隔 单位秒',
  `call_limit` int(10) NOT NULL DEFAULT '0' COMMENT '单日同一号码呼叫限制次数',
  `day_call_limit` int(10) NOT NULL DEFAULT '0' COMMENT '单日外呼号码任务量限制',
  `begin_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始时间',
  `end_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束时间',
  `number_city_code` varchar(20) NOT NULL DEFAULT '' COMMENT '主叫号码归属地',
  `number_city_limit` tinyint(1) NOT NULL DEFAULT '0' COMMENT '只允许当前归属地号码呼叫',
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uidx_companyid` (`company_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='外呼设置表';

CREATE TABLE `tasks` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `uniqid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户id',
  `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司id',
  `tid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '租户id',
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
  `begin_date` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始日期',
  `end_date` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束日期',
  `begin_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始时间',
  `end_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束时间',
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_status_uid` (`status`,`uniqid`)
) ENGINE=InnoDB AUTO_INCREMENT=1000010 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务表';

CREATE TABLE `task_list` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `uniqid` int(10) unsigned NOT NULL COMMENT '用户id',
  `company_id` int(10) unsigned NOT NULL COMMENT '公司id',
  `tid` int(10) unsigned NOT NULL COMMENT '租户id',
  `task_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '任务id',
  `resume_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '简历id',
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
  KEY `idx_taskid_status` (`task_id`,`status`),
  KEY `idx_resumeid_status` (`resume_id`,`status`),
  KEY `idx_companyid_status_callresult` (`company_id`,`status`,`call_result`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务号码表';
```

#### 2.API

##### 1.外呼设置

POST `/api/caller/saveSetting`

```json
{
	"talking_id":0,
    "status":1,
    "recall_num":1,
    "recall_interval":600,
    "begin_time":"09:00",
    "end_time":"18:00",
    "number_city_code":12,
    "number_city_limit":0
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": 1
}
```

##### 2.获取外呼设置

POST `/api/caller/getSetting`

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "id": 1,
        "uniqid": 1,
        "company_id": 1,
        "talking_id": 0,
        "status": 1,
        "recall_num": 1,
        "recall_interval": 600,
        "call_limit": 0,
        "day_call_limit": 0,
        "begin_time": "09:02",
        "end_time": "20:00",
        "number_city_code": "11",
        "number_city_limit": 0,
        "created_at": "2019-10-16 13:59:44",
        "updated_at": "2019-10-21 12:00:05"
    }
}
```



##### 3.获取外呼列表

POST `/api/caller/listCaller`

```json
{
	"status":1,  // 1=待呼叫 2=呼叫中 3=已呼叫
    "is_count":0, // 只计数
    "page":1,
    "size":20
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": [
        {
            "id": 1,
            "tid": 81,
            "user_id": 1,
            "task_id": 1000002,
            "resume_id": 1,
            "name": "张三",
            "phone": "13899990002",
            "status": 3,
            "is_deleted": 0,
            "call_begin_time": "2019-09-10 09:47:45",
            "call_time": 45,
            "call_result": 0,
            "intention_result": "",
            "call_voice_url": "group1/M00/49/10/wKgByV12_kCAHeGyAArQOKw-Chc279.wav",
            "created_at": "2019-09-29 20:23:20",
            "updated_at": "2019-10-09 17:06:05"
        }
    ]
}
```



##### 4.获取话术列表

POST `/api/caller/listTalking`

```json
{
	"page":1,
    "size":20
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "total": 2,
        "list": [
            {
                "id": "145",
                "name": "中小灵乙测试话术2",
                "status": 1
            },
            {
                "id": "144",
                "name": "中小灵乙测试话术",
                "status": 1
            }
        ]
    }
}
```

##### 5.获取主叫号码归属地列表

POST `/api/caller/listNumberCity`

Response

```jso
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "total": 46,
        "list": [
            {
                "code": "11",
                "name": "北京"
            },
            {
                "code": "12",
                "name": "天津"
            }
        ]
    }
}
```



##### 6.获取单个呼叫详情

POST `/api/caller/detailCaller`

```json
{
    "id":1,
    "resume_id":1734600739695820820, // id和resume_id 传一个即可
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "call_records": [
            {
                "talker": "caller",
                "talk_time": "2019-09-10 09:47:46",
                "content": "喂，您好，我是e成科技负责招聘的人事，您现在方便吗？"
            },
            {
                "talker": "called",
                "talk_time": "2019-09-10 09:47:52",
                "content": "啊方便你说吧"
            },
            {
                "talker": "caller",
                "talk_time": "2019-09-10 09:47:54",
                "content": "我们e成科技是一家做人力资源方向的互联网公司，目前有一些岗位正在招聘人才，给您来电也是想确认一下您的意向，不知道您目前会考虑新的工作机会吗？"
            },
            {
                "talker": "called",
                "talk_time": "2019-09-10 09:48:11",
                "content": "哦盖考虑的"
            },
            {
                "talker": "caller",
                "talk_time": "2019-09-10 09:48:13",
                "content": "4_7"
            },
            {
                "talker": "called",
                "talk_time": "2019-09-10 09:48:21",
                "content": "嗯是"
            },
            {
                "talker": "caller",
                "talk_time": "2019-09-10 09:48:23",
                "content": "19_4"
            }
        ],
        "id": 1,
        "tid": 81,
        "user_id": 1,
        "task_id": 1000002,
        "resume_id": 1,
        "name": "张三",
        "phone": "13899990002",
        "status": 3,
        "is_deleted": 0,
        "call_begin_time": "2019-09-10 09:47:45",
        "call_time": 45,
        "call_result": 1,
        "intention_result": "",
        "call_voice_url": "group1/M00/49/10/wKgByV12_kCAHeGyAArQOKw-Chc279.wav",
        "created_at": "2019-09-29 20:23:20",
        "updated_at": "2019-10-09 17:06:05"
    }
}
```



##### 7. 外呼音频地址

GET `/api/caller/audio/1`

Response

```json
音频内容
```

##### 8.获取统计数据

POST `/api/caller/statistics`

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "err_inner": "",
    "results": {
        "all": {
            "called": 2,
            "success": 2,
            "result_yes": 0
        },
        "today": {
            "called": 0,
            "success": 0,
            "result_yes": 0
        }
    }
}
```



#### 3.Schedule

##### 1.自动同步呼叫数据到业务端

- 将`* * * * * php artisan schedule:run `添加到Crontab,会每5分钟自动同步.

- 也可手动执行`php artisan caller:sync`联调.
