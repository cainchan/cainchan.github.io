---
title:项目专场招聘
---

##### 获取重点招聘项目

请求参数

```json
{
    "c": "positionProject",
    "m": "rpc/listImportantProject",
    "p": {
    	"uid":81,
    	"project_status":0, //项目状态 -1全部 0进行中 2已关闭   required
    	"project_type":1, //项目类型 [0.不限 1.社招 2.校招 3.实习生招聘 4. 社会&&内部招聘 5. 内部招聘]
    	"is_important":1, // 是否重点 -1全部 0非重点 1重点
    	"size":10,
    	"page":1
    }
}
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "data": [
            {
                "project_id": 134,
                "uid": 81,
                "type": 1,
                "status": 0,
                "title": "杨雨声校招校招0117",
                "description": "",
                "is_special": 1,
                "is_important": 1,
                "banner": "http://uimg.dev.ifchange.com/3.png"
            }
        ],
        "pagination": {
            "page": 1,
            "size": 10,
            "total": 1
        }
    }
}
```



##### 获取项目详情

请求参数

```json
{
    "c": "positionProject",
    "m": "rpc/getDetailToc",
    "p": {
    	"id" : 134, // 项目id
    	"uid": 81
    }
  }
```

返回格式

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": {
        "project_id": 134,
        "uid": 81,
        "type": 1,
        "status": 0,
        "title": "杨雨声校招校招0117",
        "description": "",
        "is_special": 1,
        "is_important": 1,
        "banner_wzj": "http://uimg.dev.ifchange.com/3.png",
        "banner_pc": "http://uimg.testing2.ifchange.com/2b/company/1226/1226/project/a7467ab5b3d5d109b3a1b2f4e64d4e9a.png",
        "banner_h5": "",
        "publish_pc": 1,
        "publish_h5": 0,
        "publish_wzj": 0,
        "position_list" : [...]
    }
}
```

##### 获取组织架构

请求参数

```json
{
    "c": "account",
    "m": "listOrganizationMp",
    "p": {
        "uid" : 81 // 账号id
    }
}
```

返回格式

```json
 {
     "err_msg": "",
     "err_no": 0,
     "results": [
         {
             "uid": 81,
             "organization_id": 443,
             "organization_name": "中国移动通信集团设计院有限公司",
             "city": [
                 {
                     "id": 1,
                     "city_name": "南京",
                     "city_id": 1,
                     "province_name": "江苏",
                     "province_id": 1,
                     "organization_id": 443,
                     "created_at": "2019-03-30 14:04:46",
                     "updated_at": "2019-03-30 14:04:46",
                     "is_deleted": 0
                 },
                 {
                     "id": 2,
                     "city_name": "上海",
                     "city_id": 2,
                     "province_name": "上海",
                     "province_id": 2,
                     "organization_id": 443,
                     "created_at": "2019-03-30 14:38:56",
                     "updated_at": "2019-03-30 14:38:56",
                     "is_deleted": 0
                 }
             ]
         }
     ]
 }
```

