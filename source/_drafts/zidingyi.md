#### 职位自定义字段新增

POST `/atsng/ponsitionCustomField/add`

```json
{
    "type":1, //1文本、2单选、3多选、4时间选择
    "name":"字段名称",
    "is_must": 1, //是否必填 1 必填 2可选
    "is_display":1, //是否显示 1 显示 2不显示
    "limit":1,//最多选项数量限制
    "data":[],//json数组
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "results":{
        "id": 82,
        "field": "single3",
        "limit": 1
    }
}
```



#### 职位自定义字段修改

POST `/atsng/ponsitionCustomField/edit/{id}`

```json
{
    "name":"字段名称",
    "is_must": 1, //是否必填 1 必填 2可选
    "is_display":1, //是否显示 1 显示 2不显示
    "limit":1,//最多选项数量限制
    "data":[],//json数组
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "results":true
}
```

#### 职位自定义字段删除

POST `/atsng/ponsitionCustomField/delete/{id}`

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "results": true
}
```



#### 职位自定义字段拖拽排序保存接口

POST `/atsng/ponsitionCustomField/save`

```json
{
    "data":[] //json数组
}
```

Response

```json
{
    "err_msg": "",
    "err_no": 0,
    "results":true
}
```



#### 职位自定义字段排序信息

GET `/atsng/ponsitionCustomField/info`

Response

```json
[
  {
    "name": "职位类别",
    "filed": "category_id",
    "attribute": "1",
    "type": "2",
    "is_display": "1",
    "is_must": "2",
    "auth": "1"
  },
  {
    "name": "自定义1",
    "type": "1",
    "is_must": "1",
    "is_display": "1",
    "attribute": "2",
    "id": "74"
  },
  {
    "name": "自定义2",
    "type": "1",
    "is_must": "1",
    "is_display": "1",
    "attribute": "2",
    "id": "75"
  }
]
```



#### 职位发布-增加自定义字段数据

POST `/atsng/positionAction/save`

```json
{
    "custom_field":[], //所有自定义字段数据
    ...原有参数
}
```

Response

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "tob_position_id": 5347,
    "icdc_position_id": "6875970"
  }
}
```



#### 职位详情-增加自定义字段数据

POST `/atsng/position/detail`

```JSON
{
    "tob_position_id":5347
}
```

Response

```json
{
  "err_msg": "",
  "err_no": 0,
  "results": {
    "custom_field":[...],
    ...原职位数据
  }
}
```

