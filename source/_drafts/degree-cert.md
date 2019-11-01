---
title: ATS学历证明
tags:
  - ats
---

##### 获取邮件模板类型列表

GET `/atsng/mailTpl/listMailTplGroups`

返回格式:

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": [
        {
            "label": "发起评审",
            "key": "review"
        },
        {
            "label": "邀请面试",
            "key": "interview"
        },
        {
            "label": "取消面试",
            "key": "cancel_interview"
        },
        {
            "label": "录用审批",
            "key": "hiring"
        },
        {
            "label": "淘汰",
            "key": "offer_refuse"
        },
        {
            "label": "背景调查",
            "key": "background_check"
        },
        {
            "label": "薪资证明",
            "key": "incoming_cert"
        },
        {
            "label": "学历证明",
            "key": "degree_cert"
        },
        {
            "label": "发送Offer",
            "key": "offer_send"
        },
        {
            "label": "通知候选人",
            "key": "archives"
        }
    ]
}
```

##### 获取发起学历证明邮件模板

POST `/atsng/mailTpl/listTpl`

请求参数

```json
{
    "tpl_group":"degree_cert",
	"page":1
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "pagination": {
            "page": "1",
            "size": 20,
            "total": 1
        },
        "data": [
            {
                "mail_tpl_id": 59,
                "created_uid": 0,
                "title": "学历证明-系统模板",
                "desc": "发起学历证明时，候选人收到的通知邮件样式",
                "tpl_group": "degree_cert",
                "created_at": "2019-03-26 20:10:58",
                "updated_at": "2019-03-26 20:10:58",
                "attachment_ids": "0",
                "attachment": [],
                "created_uname": "系统",
                "tpl_group_name": "学历证明",
                "is_default": 1,
                "is_share": false,
                "can_edit": 0
            }
        ],
        "titles": {
            "degree_cert": [
                "学历证明-系统模板"
            ]
        }
    }
}
```

##### 获取搜索参数

GET `/atsng/v2/process/editSearchParams?stage_type_id=3`

返回请求

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "offer": {
            "label": "offer",
            "searchParams": [
                {
                    "label": "offer状态",
                    "value": "offerStatus",
                    "using": 1,
                    "is_default": 1,
                    "must_required": 1
                },
                {
                    "label": "背景调查状态",
                    "value": "bcStatus",
                    "using": 1,
                    "is_default": 1
                },
                {
                    "label": "薪酬证明状态",
                    "value": "incomeStatus",
                    "using": 1,
                    "is_default": 1
                },
                {
                    "label": "学历证明状态",
                    "value": "degreeStatus",
                    "using": 0,
                    "is_default": 1
                },
                {
                    "label": "录用审批",
                    "value": "hiringStatus",
                    "using": 1,
                    "is_default": 1
                }
            ]
        }
    }
}
```

##### 保存搜索参数

POST `/atsng/v2/process/saveSearchParams`

请求参数

```json
{
    "stage_type_id":3,
	"search_keys":["reviewStatus", "interviewTimeMatchStatus", "estimateType", "offerStatus", "bcStatus", "hiringStatus","incomeStatus","degreeStatus","entryTime","entryStatus"]
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "processId_0_stage_0_stageTypeId_3": [
            "reviewStatus",
            "interviewTimeMatchStatus",
            "estimateType",
            "offerStatus",
            "bcStatus",
            "hiringStatus",
            "incomeStatus",
            "degreeStatus",
            "entryTime",
            "entryStatus"
        ]
    }
}
```

##### 获取阶段用户已设定字段

GET `/atsng/v2/process/listUserColumns?process_id=0&stage_type_id=3`

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": [
        "incomeCertificate",
        "degreeCertificate",
        "interviewTime",
        "interviewStatus",
        "candidateFeedback",
        "mailTrace",
        "interviewTimeMatch",
        "offerStatus"
    ]
}
```

##### 获取用户编辑设定字段

GET `/atsng/v2/process/editUserColumns?stage_type_id=3&process_id=0`

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "offer": {
            "label": "offer",
            "columns": [
                {
                    "label": "offer状态",
                    "value": "offerStatus",
                    "using": 1,
                    "is_default": 1
                },
                {
                    "label": "背景调查",
                    "value": "backgroundCheck",
                    "using": 0,
                    "is_default": 1
                },
                {
                    "label": "薪酬证明",
                    "value": "incomeCertificate",
                    "using": 1,
                    "is_default": 1
                },
                {
                    "label": "学历证明",
                    "value": "degreeCertificate",
                    "using": 1,
                    "is_default": 1
                },
                {
                    "label": "录用审批",
                    "value": "offerApprove",
                    "using": 0,
                    "is_default": 1
                }
            ]
        }
    }
}
```

##### 保存用户设定字段

POST `/atsng/v2/process/saveUserColumns`

请求参数

```json
{
	"stage_type_id":3,
	"process_id":0,
	"columns":["incomeCertificate", "degreeCertificate", "interviewTime", "interviewStatus", "candidateFeedback", "mailTrace","interviewTimeMatch","offerStatus"]
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "processId_0_stage_0_stageTypeId_3": [
            "incomeCertificate",
            "degreeCertificate",
            "interviewTime",
            "interviewStatus",
            "candidateFeedback",
            "mailTrace",
            "interviewTimeMatch",
            "offerStatus"
        ]
    }
}
```

##### 职位简历数据列表

POST `/atsng/v2/process/list`

请求参数

```json
{
    "degreeStatus":0, //0=全部 1=待反馈 2=已反馈 3=通过 4=待定 5=不通过
	"stage_type_id":3,
	"process_id":0,
	"list_type":1,
	"sort":0,
	"size":20,
	"page":1
}
```

返回格式

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "pagination": {
      "page": 1,
      "size": 20,
      "total": 137
    },
    "with_es": false,
    "queryLog": {...},
    "esLog": null,
    "dataList": [...]
  }
}
```

##### 获取流程事件信息

GET `/atsng/atsConfig/eventList`

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "135": {
            "event_id": 135,
            "event_type": "degree_certificate",
            "event_name": "学历证明",
            "company_id": 0,
            "status": 1,
            "event_id_relation": 0,
            "method": "/atsng/atsDegreeCertificate/saveCertificate",
            "event_desc": "学历证明",
            "operate_type": 1,
            "sort_rate": 89,
            "auth": 1
        }
    }
}
```



##### 发起学历证明

POST `/atsng/atsIncomeCertificate/saveCertificate`

请求参数

```json
{
	"email_content": "...",
	"email_subject": "学历证明-上海逸橙信息科技有限公司abc-保安队长sy",
	"position_name": "保安队长sy",
	"resume_email": "zhengkai.chen@ifchange.com",
	"resume_phone":18799990000,
	"resume_name": "宋海洋",
	"select_template": "学历证明-系统模板",
	"tob_position_id": 3285,
	"tob_resume_id": "1511732484135936027",
    "sms_to_candidate": 0, // 0=不发短信 1=发短信
    "sms_to_candidate_content":"短信内容"
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": "bk258ezRGWTnf1d6sfjr6l"
}
```

##### 免登录上传图片
POST `/atsng/atsMain/uploadWithToken`

请求参数

```json
{
	"name": "cfe10cd6c9303989fab098745222f1dc.jpg",
	"token": "bd3aubIlxwESI1d6s8mie2",
	"file": (binary)
}
```

返回格式

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "name": "t01f6af2d343502f1e0.jpg",
    "ext": "jpg",
    "dfs_file_name": "M00/0A/DA/CgkKAlycjJGAcT0cAACfSsv6yPs786.jpg",
    "dfs_group_name": "group1",
    "size": 40778
  }
}

```

##### 提交学历证明

POST `/atsng/atsDegreeCertificate/submitResult`

请求参数

```json
{
  "token": "bk258ezRGWTnf1d6sfjr6l",
  "attachments": [
    {
      "dfs_file_name": "M00/0A/D8/CgkKAlyZvXGAKlG1AACfSsv6yPs084.jpg",
      "dfs_group_name": "group1",
      "ext": "jpg",
      "id": "o_1d6s8tc1p51t1uej14k61n9b1vqa8",
      "name": "t01f6af2d343502f1e0.jpg",
      "size": 40778
    }
  ]
}
```

返回格式

```json
{
	"err_msg": "",
    "err_no": 0,
    "results": null
}
```

##### 获取学历反馈状态

POST `/atsng/atsDegreeCertificate/getStatusByToken`

请求参数

```json
{
	"token":"bk258ezRGWTnf1d6sfjr6l"
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "status": 2 //证明资料状态[1待反馈 2已反馈]
    }
}
```

##### HR评审反馈

POST `/atsng/atsDegreeCertificate/updateReviewStatus`

请求参数

```json
{
	"certificate_id":3,
	"review_status":1 //审批状态[1通过 2待定 3不通过]
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": true
}
```

##### 获取学历证明详情

POST `/atsng/atsDegreeCertificate/getCertificateById`

请求参数

```json
{
	"certificate_id":3
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "certificate_id": 3,
        "uid": 81,
        "top_id": 81,
        "parent_id": 81,
        "resume_id": 0,
        "tob_resume_id": 1225069313917132820,
        "tob_position_id": 5297,
        "resume_name":"候选人",
        "attachments": [
            {
                "dfs_file_name": "M00/0A/D8/CgkKAlyZvXGAKlG1AACfSsv6yPs084.jpg",
                "dfs_group_name": "group1",
                "ext": "jpg",
                "id": "o_1d6s8tc1p51t1uej14k61n9b1vqa8",
                "name": "t01f6af2d343502f1e0.jpg",
                "size": 40778
            }
        ],
        "status": 2,
        "review_status": 1,
        "created_at": "2019-03-26 15:49:40",
        "updated_at": "2019-03-26 16:15:31",
        "step_id": 3313,
        "stage_id": 8
    }
}
```

##### 下载学历证明

GET `/webank/download?dfs_group_name=xxx&dfs_file_name=xxx&name=xxx`

