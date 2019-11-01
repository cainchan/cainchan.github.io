---
title : 面试官后台-设置面试时间
---

##### 简历评审-设置面试时间[免登录]

POST `/partner-api/review/setEmployeeFree`

```json
{
    "token":"b824rNH6ujEiX1cldt9adt",
    "free_times": ["2019-04-11 21:00:00","2019-04-11 21:30:00"]
}
```

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": true
}
```



##### 简历评审-获取已选面试时间[免登录]

GET `/partner-api/review/getEmployeeFree?token=b824rNH6ujEiX1cldt9adt`

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": [
        "2019-04-11 21:00:00",
        "2019-04-11 21:30:00"
    ]
}
```

##### 简历评审-获取已有面试时间[免登录]

GET `/partner-api/review/getEmployeeInterviewTime?token=b824rNH6ujEiX1cldt9adt`

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": [
        {
            "round_id": 5926,
            "started_time": 1555050000
        }
    ]
}
```



##### 约面-设置面试时间[免登录]

POST `/partner-api/itm/setEmployeeFree`

```json
{
    "token":"b824rNH6ujEiX1cldt9adt",
    "free_times": ["2019-04-11 21:00:00","2019-04-11 21:30:00"]
}
```

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": true
}
```



##### 约面-获取已选面试时间

GET `/partner-api/itm/getEmployeeFree?token=b824rNH6ujEiX1cldt9adt`

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": [
        "2019-04-11 21:00:00",
        "2019-04-11 21:30:00"
    ]
}
```



##### 约面-获取已有面试时间[免登录]

GET `/partner-api/itm/getEmployeeInterviewTime?token=b824rNH6ujEiX1cldt9adt`

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": [
        {
            "round_id": 5926,
            "started_time": 1555050000
        }
    ]
}
```



##### 获取约面记录列表

POST `/partner-api/itm/listItm`

```json
{
    "page":1,
    "size":15
}
```

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": {
        "pagination": {
            "page": 1,
            "size": 15,
            "total": 1
        },
        "items": [
            {
                "hr": {
                    "uid": 81,
                    "email": "cage1618@qq.com",
                    "name": "李先生"
                },
                "record": {
                    "record_id": 1,
                    "position_name": "zheli yeshi ceshi",
                    "launch_time": "2019-04-12 15:14",
                    "stage_name": "初试",
                    "token": "",
                    "tob_resume_id": "1393473798429184020",
                    "tob_position_id": 5220,
                    "status": 1,
                    "feedback_time": "",
                    "is_post": 0
                },
                "resume": [...]
            }
        ]
    }
}
```



##### 约面-设置面试时间

POST `/partner-api/itm/itmFeedBack`

```json
{
    "free_times": ["2019-04-11 21:00:00","2019-04-11 21:30:00"]
}
```

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": true
}
```



##### 首页获取统计数据

POST `/partner-api/homePage/countTodo`

返回

```json
{
    "err_no": 0,
    "err_msg": "",
    "results": {
        "reviewNeedFeedbackCount": 6, // 待评审
        "interviewNeedFeedbackCount": 1, // 待面试评价
        "hiringNeedFeedbackCount": 5, // 待录用审批
        "todayInterviewCount": 0, // 今日面试
        "readyInterviewCount": 0, // 待面试
        "itmNeedFeedbackCount": 0 // 待反馈面试时间
    }
}
```



