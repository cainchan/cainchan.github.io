#### 获取候选人列表

```json
{
  "header": {
	"uid":81, //账号uid
	"top_id":81, //账号top_id
	"uname":"zyh",
	"version":1,
	"signid":2132,
	"provider":"cv42b",
	"ip":123123,
	"auth":"eh",
	"app_id":19,
	"appid":19
  },
  "request": {
    "c": "hrbot",
    "m": "rpc/listCandidate",
    "p": {
    	"uid" : 81,  //账号uid
    	"type": 1  // 0=全部 1=待安排面试 2=已安排面试
    }
  }
}
```

返回参数

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "total": 22,
        "list": [
            {
                "resume_id": 14680,
                "tob_resume_id": 1225069582608179220,
                "tob_position_id": 4072,
                "deliver_source": 100,
                "step_id": 2601,
                "stage_id": 46,
                "stage_type_id": 2,
                "hr_status": 0,
                "source_id": 55020,
                "resume_name": "张毅"
            },
            {
                "resume_id": 4080704,
                "tob_resume_id": 1284602923116339220,
                "tob_position_id": 3921,
                "deliver_source": 99,
                "step_id": 2871,
                "stage_id": 3,
                "stage_type_id": 2,
                "hr_status": 0,
                "source_id": 55289,
                "resume_name": "陈伟"
            }
        ]
    }
}
```

#### 获取面试官列表

```json
{
  "header": {
	"uid":81,
	"top_id":81,
	"uname":"zyh",
	"version":1,
	"signid":2132,
	"provider":"cv42b",
	"ip":123123,
	"auth":"eh",
	"app_id":19,
	"appid":19
  },
  "request": {
    "c": "hrbot",
    "m": "rpc/listEmployeeByUid",
    "p": {
    	"uid" : 81
    }
  }
}
```

返回

```json
 {
        "err_msg": "",
        "err_no": 0,
        "results": {
            "total": 640,
            "list": [
                {
                    "employee_id": 1,
                    "name": "yjj",
                    "email": "jianjun.yan@change.com",
                    "mphone": "",
                    "status": 0
                }
            ]
        }
 }
```

