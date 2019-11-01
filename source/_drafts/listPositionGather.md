先添加hosts  192.168.1.109 tob.kai.com

#### 获取职位数据集合

POST `http://tob.kai.com/atsng/rpc`

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
    "c": "position",
    "m": "rpc/listPositionGather",
    "p": {
    	"uid" : 81,
        "business_id":3
    }
  }
}
```

Response

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "work_city": [
      {
        "id": 10,
        "name": "上海"
      },
      {
        "id": 231,
        "name": "深圳市"
      },
      {
        "id": 229,
        "name": "广州市"
      }
    ],
    "company_commercial_activitie": [
      {
        "id": 19,
        "name": "集团总部"
      },
      {
        "id": 21,
        "name": "物业"
      }
    ],
    "position_category": [
      {
        "id": 23,
        "name": "56898"
      },
      {
        "id": 37,
        "name": "开发测试1"
      }
    ]
  }
}
```

