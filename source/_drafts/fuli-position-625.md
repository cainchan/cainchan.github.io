##### 职位列表

POST `/atsng/position/list`

Response

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "data": [
      {
        "name": "产品经理",
        "architecture_name": "",
        "city_ids": {
          "10": "上海"
        },
        "status": 0,
        "created_at": "2019-06-18 17:54:54",
        "updated_at": "2019-06-21 16:40:17",
        "refreshed_at": "0000-00-00 00:00:00",
        "user_id": 94887,
        "icdc_position_id": 6876173,
        "salary_begin": 1000,
        "salary_end": 500000,
        "experience_begin": 0,
        "experience_end": 0,
        "degree_id": 1,
        "approve_status": -1,
        "corporation_name": "富力集团",
        "tob_position_id": 6,
        "recruit_type": 1,
        "is_secret": 0,
        "is_urgent": 0,
        "is_hot": 1,
        "number": 0,
        "organization": "39",
        "is_headhunter": 0,
        "is_interpolate": 0,
        "is_bot": 0,
        "is_bot_auto": 0,
        "bot_setting": null,
        "address": "复兴广场b座305",
        "headhunter_account_list": [],
        "requirement": "职位要求2222222222222",
        "description": "工作内容111111111111",
        "is_headhunter_trade": 0,
        "category_id": 0,
        "custom_data": "",
        "campus_process_id": 0,
        "is_auto_circulation": 0,
        "auto_circulation_interview": {
          "2_3": {
            "stage_id": 3,
            "stage_type_id": 2,
            "is_auto": 0,
            "reviewers": []
          },
          "2_4": {
            "stage_id": 4,
            "stage_type_id": 2,
            "is_auto": 0,
            "reviewers": []
          },
          "2_658": {
            "stage_id": 658,
            "stage_type_id": 2,
            "is_auto": 0,
            "reviewers": []
          }
        },
        "auto_circulation_review": {
          "1_663": {
            "stage_id": 663,
            "stage_type_id": 1,
            "is_auto": 0,
            "reviewers": []
          }
        },
        "category_name": "",
        "share_user_ids": [],
        "is_edited": 1,
        "share_type": 0,
        "organization_name": "富力集团",
        "diqu_name": "",
        "quyu_name": "",
        "mokuai_name": "",
        "interview_second": 2,
        "interview_first": 1,
        "resume_count": 6,
        "user_name": "我",
        "user_email": "rfhr@rfchina.com",
        "interpolate_info": {
          "interpolate_type": 1,
          "interpolate_integral": "",
          "interpolate_gift": ""
        },
        "city_name": "上海",
        "share_user_info": [],
        "headhunter_account_info": [],
        "recommend_count": 0,
        "yueta_count": 0,
        "campus_ats_count_info": [],
        "is_show_bi": 1,
        "can_refresh": 1,
        "is_in_archives_bot_task": 0,
        "is_archives_bot_task_expire": 1,
        "position_filter": {
          "id": 6,
          "tob_position_id": 6,
          "age_min": 0,
          "age_max": 0,
          "sex": "U",
          "degree": 0,
          "working_year_min": 0,
          "working_year_max": 0,
          "is_marry": "",
          "created_at": "2019-06-18 17:54:54",
          "updated_at": "2019-06-18 17:54:54",
          "is_985_211": 0,
          "is_enabled": 0
        }
      }
    ],
    "account_limit": 1,
    "pagination": {
      "page": 1,
      "size": 20,
      "total": 1
    }
  }
}
```

##### 导出职位列表数据

GET `/atsng/position/exportPositionList`

```json
参数同职位列表
```

Response

```json
文件下载
```



