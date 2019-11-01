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
    "m": "rpc/listCampusPositionGather",
    "p": {
    	"uid" : 81, //top帐号id
        "organization_id":155, //组织架构id
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
        "organization": [
            {
                "id": "155",
                "name": "柬埔寨公司"
            },
            {
                "id": "183",
                "name": "浙江公司"
            }
        ],
        "position_category": [
            {
                "id": 19,
                "name": "测试0710"
            },
            {
                "id": 5,
                "name": "测试类"
            }
        ]
    }
}
```

