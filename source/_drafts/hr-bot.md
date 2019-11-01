---
title:HR-BOT
---

##### 获取淘汰原因列表

GET `/napi/settings/out`

返回

```json
{
  "err_no": 0,
  "err_msg": "",
  "results": {
    "config": {
      "eliminate_notice_candidate": "1",
      "eliminate_notice_tpl": "1",
      "eliminate_reason": [
        "1",
        "2"
      ]
    },
    "requireList": [
      {
        "id": 1,
        "reason": "候选人暂不符合职位要求",
        "type": "system"
      },
      {
        "id": 2,
        "reason": "候选人看其他机会",
        "type": "system"
      },
      {
        "id": 3,
        "reason": "原公司挽留",
        "type": "system"
      }
    ]
  }
}
```



##### 淘汰

POST `/atsng/atsEvent/batchEliminate`

```json
{
    "candidate":[
        {
            tob_position_id: "3195", 
            tob_resume_id: "1469062862173593627"
        }
    ],
    "eliminate_id":1,
    "eliminate_reason":"",
    "send_email":1,
    "stage_type_id":2,
    "email_subject":"很遗憾，你不是这个职位的最佳人选-【公司名】-【职位名】",
    "email_msg":"<p>亲爱的<a href="candidate_name" class="wysiwyg-mention" data-mention="" data-value="undefined">候选人姓名</a>：</p>↵<p>感谢应聘，我们对您的背景非常认可，但综合考虑下来，我们认为您不是这个职位的最佳人选。再次感谢关注，希望下次有更合适的机会加入我们，祝未来一切顺利！</p>"
}
```



##### 批量安排面试

POST `/atsng/hrbot/batchInviteInterview`

```json
{
    "step_ids":[22009,22010]
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

##### 获取候选人可面时间列表

POST  `/atsng/hrbot/getCandidateTime`

```json
{
    "step_id":22009
}
```

返回

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": [
        "2019-01-10 08:30:00",
        "2019-01-10 09:00:00",
        "2019-01-10 09:30:00",
        "2019-01-10 10:00:00",
        "2019-01-10 10:30:00",
        "2019-01-10 11:00:00",
        "2019-01-10 11:30:00",
        "2019-01-10 12:00:00",
        "2019-01-10 12:30:00",
        "2019-01-10 13:00:00",
        "2019-01-10 13:30:00",
        "2019-01-10 14:00:00",
        "2019-01-10 14:30:00",
        "2019-01-10 15:00:00",
        "2019-01-10 15:30:00",
        "2019-01-10 16:00:00",
        "2019-01-10 16:30:00",
        "2019-01-10 17:00:00",
        "2019-01-10 17:30:00",
        "2019-01-10 18:00:00"
    ]
}
```



