#### 1. 交易历史

POST `/boc/EntAcqHisTransQuery`

```json
currentPage:1
pageSize:50
accountIdList:152831546
startDate:2016/01/01
endDate:2019/08/01
incomingType:A
```

Response

```json
[
    {
        "id": "64",
        "method": "EntAcqHisTransQuery",
        "status": "01",
        "result": {
            "list": [
                {
                    "accountId": 152831546,
                    "accountNo": "435167571515",
                    "accountName": "义橙网络科技（上海）有限公司",
                    "accountType": "00",
                    "currency": "001",
                    "bankName": "中国银行上海市华山路支行",
                    "accountAlias": "",
                    "actSpecialFlag": "",
                    "custName": "义橙网络科技（上海）有限公司",
                    "functionSet": null,
                    "hisTransList": [
                        {
                            "actSpecialFlagPayer": "",
                            "actSpecialFlagPayee": "",
                            "actSpecialFlagName": "",
                            "payerIbkNum": "102290019786",
                            "payerIbkName": "中国工商银行上海市吴淞路支行",
                            "payerActNum": "1001197819200038787",
                            "payerActName": "中意人寿保险有限公司上海分公司",
                            "payeeIbkNum": "05837",
                            "payeeIbkName": "中国银行上海市华山路支行",
                            "payeeActNum": "435167571515",
                            "payeeActName": "义橙网络科技（上海）有限公司",
                            "nameIbkNum": "102290019786",
                            "nameIbkName": "",
                            "nameActNum": "",
                            "nameActName": "",
                            "voucherType": "",
                            "voucherNum": "",
                            "jurnalNo": "2017112816260534",
                            "txnDate": "2017-11-28 08:51:00",
                            "tranAmount": "6000.00",
                            "acctBalance": "248,313.30",
                            "tranType": "小额普通",
                            "tranCurr": "001",
                            "creditDebitFlag": "D",
                            "channel": "其他",
                            "reserve1": "",
                            "reserve2": "",
                            "reserve3": "",
                            "usage": "",
                            "intFromDate": "2017-11-28",
                            "exchangeRate": "1.000000",
                            "summary": "BEPS102290019786  2017112876821908",
                            "remark": "",
                            "tranPostscript": "",
                            "custBusinessId": "",
                            "transactionId": "60866658932",
                            "recordId": "060866658999992932999992932",
                            "receiptFlag": "242P70EFDF2E",
                            "oldAccountNo": "",
                            "bankRefNo": ""
                        }
                    ]
                }
            ],
            "recordNumber": 1
        },
        "error": null
    }
]
```

#### 2. 历史交易回单打印

POST /`boc/EntSingleHuiDanQuery`

```
accountId:152831546
recordId:060866658999992932999992932
```

Response

```json
{
    "RECEIPT_NO": "2017112822877579",
    "TELEG_TYPE": "beps.121.001.01-客户发起",
    "REL_PAY_NAME": "中意人寿保险有限公司上海分公司",
    "REL_PAYEE_NAME": "义橙网络科技（上海）有限公司",
    "TRAN_PS": "",
    "RECEIPT_CODE": "242RCDC0D6E6",
    "TRAN_CURE_EN": "CNY",
    "TRAN_AMOUNT_CHA": "陆仟元整",
    "TRAN_CURE_CHA": "人民币",
    "REL_PAY_BR_NAME": "中国工商银行上海市吴淞路支行",
    "SNDBKM": "中国工商银行上海市吴淞路支行",
    "TRAN_DATE": "2017年11月28日",
    "RCVBKN": "中国银行上海市华山路支行",
    "TRAN_BRCH_NO": "11029",
    "PRT_TIME": "",
    "USAGE_NAME": "",
    "DEPOSIT_ACCT_NO": "435167571515",
    "REMIT_MSG_WORD": "",
    "RECEIPT_NAME": "国内支付业务收款回单",
    "BUSI_TYPE": "A100-普通汇兑",
    "RECEIPT_MSG": "如您已通过银行网点取得相应纸质回单，请注意核对，勿重复记账！",
    "RECEIPT_MOULD": "502",
    "RCVBAK": "05837",
    "RECEIPT_JRNL_NO": "60866658-932",
    "TXNIDN": "2017112876821908",
    "CUSTOMER_NO": "390020251",
    "PRT_COUNT": "",
    "TRAN_AMOUNT": "6000.00",
    "SYS_CODE_CHA": "其他",
    "REL_PAYEE_BR_NAME": "中国银行上海市华山路支行",
    "REL_PAYEE_ACCT_NO": "435167571515",
    "DEPOSIT_ACCT_NAME": "义橙网络科技（上海）有限公司",
    "BUSINESS_NO": "",
    "FPRS_NO": "",
    "SNDBAK": "中国工商银行上海市吴淞路支行",
    "REL_PAY_ACCT_NO": "1001197819200038787"
}
```



#### 3. 交易下载列表

POST `/boc/EntAcqHisTransLastQueryList`

Response

```json
{
    "hashList": [
        {
            "hashCode": "HISXLS-20191121-20191121-03833185",
            "queryDate": "2019-11-22 22:11:15",
            "isEmptyFlag": "1"
        }
    ]
}
```



#### 4. 回单下载列表

POST `/boc/EntAcqTransHuiDanLastQueryFileName`

Response

```json
{
    "list": [
        {
            "hashCode": "RECEIPT-20191121-20191121-92625241",
            "isEmptyFlag": "1",
            "fileNameList": [
                {
                    "rptFlag": "S",
                    "fileName": "RECEIPT-20191121-20191121-926252410001.pdf"
                }
            ],
            "time": "2019/11/24"
        }
    ],
    "recordNumber": 1
}
```

#### 5. 生成交易下载

POST `/boc/EntAcqHisTrans1PlusQuery`

```json
accountIdList:152831546
startDate:2019/08/01
endDate:2019/08/01
```

Response

```json
{
    "hashCode": "HISXLS-20190901-20191001-03833279"
}
```



#### 6. 下载交易文件

POST `/boc/EntAcqHisTransQueryDownload`

 ```json
"hashCode": "HISXLS-20190901-20191001-03833279"
 ```

Response

```json
下载文件:HISXLS-20190901-20191001-03833279.xls
```



#### 7. 生成回单下载文件

POST `/boc/EntAcqTransHuiDanQuery`

```json
accountIdList:152831546
startDate:2019/08/01
endDate:2019/08/01
```

Response

```json
{
    "hashCode": "RECEIPT-20191122-20191122-92624925"
}
```



#### 8. 下载回单文件

POST `/boc/EntAcqHisTransQueryDownload`

```json
"hashCode": "RECEIPT-20191122-20191122-92624925"
```

Response

```json
下载文件:RECEIPT-20191122-20191122-92624925.pdf
```

