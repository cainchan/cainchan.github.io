---
title: toh-worker-api
---

#### 1.查询账号是否存在
```php
// 接收参数:
['w' =>'toh_account',
'c' => 'account/Logic_account',
'm' => 'ex_email',
'p' => ['email' => 'email@qq.com',
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => ['exist' => 1,
		'account_id' => 10000001,
		'email' => 'email@qq.com',
		'company_name' => '公司名称',
		],
];
// exist = 1 代表存在
// exist = 0 代表不存在
```


#### 2.tob开通账号
```php
// 接收参数:
['w' =>'toh_account',
'c' => 'account/Logic_account',
'm' => 'reg',
'p' => ['email' => 'email@qq.com',
	'password' => '123123',
	'company_name' => '公司名称',
    'company_license' => '社会信用代码',
	],
];

// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => ['account_id' => 10000001,
		'email' => 'email@qq.com',
		'company_name' => '公司名称',
	]
];
```
#### 3.tob推送职位
```php
// 接收参数:
['w' =>'toh_position',
'c' => 'position/Logic_position',
'm' => 'saveTobPosition',
'p' => ['uid' => 81,
        'parent_id' => 12,
		'toh_account_ids' => [10000001,10000002],
		'position_ids' => [123321,345664],
		'company_name' => '上海逸橙work',
		'company_id' => '1',
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => ['msg' => 'push successed',
    'error_account' => [222,333],
	]
];
```
#### 4.tob关闭职位
```php
// 接收参数:
['w' =>'toh_position',
'c' => 'position/Logic_position',
'm' => 'closeTobPosition',
'p' => [
    'toh_account_ids' => [10000001,10000002],
    'position_ids' => [123321,345664],
    ],
],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => ['msg' => 'close successed',
    'error_account' => [222,333],
	]
];
```
#### 5.tob推送面试进度
```php
// 接收参数:
['w' =>'toh_position',
'c' => 'position/Logic_position',
'm' => 'saveTobProgress',
'p' => ['uid' => 81,
		'account_id' => '10000001',
		'position_id' => 123456,
		'resume_id' => '1111',
		'progress' => '面试',
		'status' => '1',
		'evaluate' => '非常满意',
		'finished' => '0', // 是否是最后进度
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => ['id' => 123,
		'msg' => 'push successed',
	]
];
```

#### 根据email账号开通公司交易功能
```php
// 接收参数:
['w' =>'toh_account',
'c' => 'account/Logic_account',
'm' => 'open_transaction',
'p' => ['email' => 'email@qq.com',
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => true
];
```
#### 获取已开通交易公司列表
```php
// 接收参数:
['w' =>'toh_account',
'c' => 'account/Logic_account',
'm' => 'get_transaction_companys',
'p' => ['company_name' => '上海xx公司',//可选参数
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => [
    {
        'id' => 20000001,
        'company_name' => '上海xx公司',
        'open_transaction' => 1
    }
]
];
```

#### 获取已开通交易账号列表
```php
// 接收参数:
['w' =>'toh_account',
'c' => 'account/Logic_account',
'm' => 'get_transaction_accounts',
'p' => ['email' => '1@qq.com',//可选参数
          'page' => 1,//可选参数
          'pagesize' => 20,//可选参数
	],
];
// 返回参数:
{
    "err_no": 0,
    "err_msg":'',
    "results":{
        'total':1,
        'accounts' :[{
            "toh_account_id":10000023,
            "toh_company_id":2000001,
            "toh_company_name":'某某猎头公司',
            "toh_account_parent_id":0,
            "email":"1@qq.com",
            "contact":'联系人',
            "mobile":'13888888888',
            "worked":'3-5',
            "adept_industry":'互联网',
            "adept_function":"开发工程师",
            "introduce":"个人介绍...",
            "served_company":"腾讯,阿里",
            "toh_account_img":"/group0/xxx/fhjgjdfhjgkfd435734535.png"
        }
    ]
}
}
```

#### 更新订单PA账号id
```php
// 接收参数:
['w' =>'toh_order',
'c' => 'order/Logic_order',
'm' => 'update_order_pa',
'p' => ['order_id' => '2017102410000001',
         'pa_account_id' => 123,
	],
];
// 返回参数:
['err_no' => 0,
'err_msg' => '',
'results' => true
];
```