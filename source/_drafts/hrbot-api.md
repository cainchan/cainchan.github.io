#### 获取识别匹配简历

POST `/atsng/hrbot/listIntentMatchCandidate`

```json
{
    "text": "毛振东",
    "employee_ids":[101], // 已确认的面试官id
    "step_id":111, // 已确认的候选人流程id
    "character":"employee", // ⽤户确认的⻆⾊，根据上⼀轮传回的character,employee/candidate
    "name":"毛振东" // 已确认的姓名
}
```

返回

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "intent": "search",
        "text":"",
        "character":"", //确认角色 employee/candidate
        "step_id": 123,
        "employee_ids" : [1],
        "employees":[
            {
                "employee_id": 1,
                "name": "yjj",
                "email": "jianjun.yan@change.com",
                "mphone": "",
                "status": 0
            }
        ],
        "candidate":{
            "can_invite_interview":0, //0=不可安排面试 1=可安排面试
            ...
        },
        "timestamp":"2019-05-16 20:00:00",
        "total": 1,
        "list": [
            {
                "icdc_resume_id": 25065051,
                "tob_resume_id": 1532089737429606427,
                "tob_position_id": 4836,
                "deliver_source": 2,
                "step_id": 5075,
                "stage_id": 3,
                "stage_type_id": 2,
                "hr_status": 0,
                "source_id": 12997,
                "created_at": "2019-04-25 23:07:05",
                "resume_name": "毛振东",
                "stage_name": "初试",
                "position_data": {
                    "name": "研发技术员",
                    "user_id": 95012,
                    "tob_position_id": 4836
                },
                "resume_data": {
                    "name": "毛振东",
                    "photo": "",
                    "age": "",
                    "gender": "M",
                    "work_experience": 5,
                    "address": "",
                    "degree_name": "高中",
                    "expect_salary_from": 0,
                    "expect_salary_to": 0,
                    "email": "82069222@qq.com",
                    "phone": "18651825586",
                    "is_private": 0,
                    "deliver_source": "2",
                    "resumeDesKey": "",
                    "rencaiku": 0,
                    "show_name": 1,
                    "resume_id": 25065051,
                    "education": {
                        "major": "",
                        "school": "舒城中学",
                        "degree": "高中",
                        "start_stop": "2016.09 - 至今"
                    },
                    "workinfo": [
                        {
                            "company": "和骊安(中国)汽车信息系统有限公司",
                            "position": "",
                            "start_stop": "2016.04 - 2017.08"
                        },
                        {
                            "company": "南京擎天科技有限公司",
                            "position": "",
                            "start_stop": "2014.08 - 2016.04"
                        }
                    ],
                    "toh_similar": [],
                    "position": "",
                    "company": "和骊安(中国)汽车信息系统有限公司",
                    "tob_resume_id": "1532089737429606427",
                    "edit_resume": 1
                }
            }
        ]
    }
}
```

#### 解析关键词

POST `/atsng/homepage/searchRedirect`

```json
{
    keyword: "关键词"
}
```

返回

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": [
    {
      "keyword": "陈好",
      "type": "name"
    },
    {
      "keyword": "搜索测试",
      "type": "keyword"
    }
  ]
}
```



#### 搜索候选人

POST `/atsng/candidate/list`

```json
{
    "name":"姓名",
    "keyword":"关键词",
    "mobile":"18600009999", //手机号
    "email":"email@qq.com", //邮箱
    "experience":"0_17", //工作年限
    "degree":1, //学历 
    "gender":"M", //性别 M=男 F=女
    "is_archive":0, //是否已归档 0:否 1:是
    "is_blacklist":0, //是否黑名单 0:否 1:是
    "page": 1, //当前页
    "size": 10, //每页数量
    "create_time_sort":"desc", // 申请时间排序 asc=升序 desc=降序
    "update_time_sort":"desc" // 更新时间排序 asc=升序 desc=降序
}
```

返回

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "data": [
      {
        "tob_resume_id": "1561400874431692827",
        "icdc_resume_id": "25489934",
        "name": "刘敏",
        "photo": "",
        "age": "",
        "gender": "M",
        "experience": 0,
        "degree": "",
        "is_blacklist": 0,
        "resume_black_list": [],
        "subinfo": [
          {
            "candidate_id": 6670,
            "tob_position_id": 5038,
            "delivery_time": "2019.05.11",
            "update_time": "2019.05.11",
            "position_name": "高级Java工程师",
            "position_status": 0,
            "position_is_deleted": "N",
            "project_info": [],
            "delivery_source": 3,
            "is_archive": 1,
            "can_invite_interview":0, //0=不可安排面试 1=可安排面试
            "stage_info": {
              "tob_resume_id": 1561400874431692800,
              "tob_position_id": 5038,
              "stage_type_name": "待处理",
              "stage_type_key": "delivery"
            }
          }
        ]
      }
    ],
    "pagination": {
      "page": 1,
      "size": 20,
      "total": 1
    },
    "with_es": true
  }
}
```



#### 评审通过安排面试

POST `/atsng/hrbot/passReviewInviteInterview`

```json
{
    "step_id":123,//必传
    ...// 同安排面试
}
```

返回

```json
{
    "err_msg": "",
    "err_no": 0,
    "results":true
}
```

#### 获取评审信息

POST `/atsng/hrbot/getReviewData`

```json
{
    "review_record_id":1 //评审记录id
}
```

返回

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "record_id": 3248,
        "step_id": 22260,
        "stage_id": 2,
        "round_id": 2282,
        "remark": "",
        "employee_id": 694,
        "employee": {
            "employee_id": 694,
            "name": "B222",
            "email": "B222@ifchange.com",
            "free": [
                "2019-05-22 10:00:00",
                "2019-05-22 10:30:00"
            ]
        },
        "resume_data":{...},
        "position_name":"职位名称",
        "can_invite_interview":0 //0=不可安排面试 1=可安排面试         
    }
}
```



