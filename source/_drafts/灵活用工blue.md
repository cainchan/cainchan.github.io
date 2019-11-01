---
title:灵活用工blue-简历数据盘点
---



####  一. tob简历服务

##### 1.数据库设计:tob_resume_blue

![DB设计](C:\Users\陈正凯\Desktop\灵活用工\tob_resume_blue.bmp)

##### 2.DDL

```sql
******************************sql*********************************
-- 企业候选人表
CREATE TABLE `company_candidate_0` (
  `id` bigint(20) unsigned NOT NULL COMMENT '企业候选人ID',
  `tid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '租户ID',
  `r_name` varchar(100) NOT NULL DEFAULT '' COMMENT '名字',
  `content` longtext CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '简历json格式',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 1.删除 0.未删除',
  `deleted_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '删除时间',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  KEY `tid` (`tid`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 ROW_FORMAT=COMPACT COMMENT='企业候选人表';

-- 企业候选人ID_CARD表
CREATE TABLE `candidate_card_0` (
  `cid` bigint(20) unsigned NOT NULL COMMENT '企业候选人ID',
  `tid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '租户ID',
  `id_card` varchar(128) NOT NULL DEFAULT '' COMMENT '身份证ID',
  `r_name` varchar(100) NOT NULL DEFAULT '' COMMENT '名字',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`cid`),
  UNIQUE KEY `idc_r_U` (`tid`, `id_card`,  `r_name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 ROW_FORMAT=COMPACT COMMENT='企业候选人ID_CARD表';

-- 企业候选人PHONE表
CREATE TABLE `candidate_phone_0` (
  `cid` bigint(20) unsigned NOT NULL COMMENT '企业候选人ID',
  `tid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '租户ID',
  `phone` varchar(128) NOT NULL DEFAULT '' COMMENT '电话号',
  `r_name` varchar(100) NOT NULL DEFAULT '' COMMENT '名字',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`cid`),
  UNIQUE KEY `phone_r_U` (`tid`, `phone`, `r_name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 ROW_FORMAT=COMPACT COMMENT='企业候选人ID_CARD表';

-- 简历入库流水
CREATE TABLE `resume_record_0` (
  `id` bigint(20) unsigned NOT NULL COMMENT '简历流水ID',
  `cid` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '企业候选人ID',
  `tid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '租户ID',
  `source` tinyint(4) NOT NULL DEFAULT '0' COMMENT '来源',
  `content` longtext CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '简历json格式',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  KEY `tid_IDX` (`tid`) USING BTREE,
  KEY `source_IDX` (`source`) USING BTREE,
  KEY `cid_cat_IDX` (`cid`, `created_at`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=COMPACT COMMENT='简历入库流水表';

CREATE TABLE `resume_original_0` (
  `original_id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增ID',
  `sub_uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '原件附属uid',
  `cid` bigint(20) unsigned NOT NULL COMMENT '企业候选人ID',
  `md5` varchar(32) NOT NULL COMMENT '文件唯一标示',
  `deliver_source` tinyint(4) NOT NULL DEFAULT '0' COMMENT '来源(1.智联 2.51job 3.猎聘 4.大街 11.拉钩 12.58同城 13.赶集 99.E成...)',
  `original_type` tinyint(1) NOT NULL DEFAULT '1' COMMENT '原件类型 1.简历原件 2.简历附件',
  `file_name` varchar(255) NOT NULL DEFAULT '' COMMENT '原件显示名称',
  `file_type` tinyint(1) NOT NULL COMMENT '文件类型 1.url 2.文件 （json）',
  `file_info` varchar(1000) NOT NULL DEFAULT '' COMMENT '原件地址 url or json',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0.否 1.是',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`original_id`),
  UNIQUE KEY `uk_resume_deliver` (`cid`,`md5`) USING BTREE COMMENT '简历，来源唯一约束'
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 ROW_FORMAT=COMPACT COMMENT='简历附件表';


CREATE TABLE `resume_tag_0` (
  `tag_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '标签id',
  `cid` bigint(20) unsigned NOT NULL COMMENT '简历ID',
  `tag_name` varchar(255) NOT NULL DEFAULT '' COMMENT '标签名称',
  `sub_uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '子用户id',
  `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否已被删除,0为未被删除,1为已被删除',
  `create_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '创建人id',
  `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '修改时间',
  `update_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '修改人id',
  `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  `tag_type` int(2) NOT NULL DEFAULT '0',
  PRIMARY KEY (`tag_id`),
  UNIQUE KEY `uk_resume_tag` (`cid`,`tag_name`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=71 DEFAULT CHARSET=utf8 ROW_FORMAT=COMPACT COMMENT='简历标签表';
```



##### 3. 接口文档

[TOB简历pool服务接口文档](<http://192.168.1.150:8090/pages/viewpage.action?pageId=48267914>)

```bash
###-----------------------环境地址----------------------------------------------------
# 开发联调地址:192.168.5.152
# 测试地址:（待定）
# 线上 :（待定）
# 将文件中 localhost 修改为对应环境

 #1.新增候选人简历  saveCandidate
 #2，更新候选人简历  updateCandidate
 #3，批量查候选人简历 selectCandidateList
 #4，根据id批量查询简历记录列表 selectCandidateRecordList
 #5，根据简历id查候选人简历历史记录 listCandidateRecordsByCid
 #6，根据tid，name，phone或者idcard 获取候选人简历id getCidByNamePhoneOrIdcard
 #7，新增/修改标签 upsetTag
 #8，批量查询标签 listTagByTobResumeIds
 #9，新增简历原件信息 saveOriginalByCid
 #10，删除候选人简历原件 deleteOriginal
 #11，更新简历原件信息 updateOriginal
 #12，通过候选人简历ID获取简历原件 listOriginalByCids


### 更新候选人简历
# tid:租户id ，rName:姓名 ，id：候选人id，
# idCard：身份证， source：来源 ，phone：电话，content：简历json
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "updateCandidate",
    "p": {
      "tid":2019,
      "rName":"星期四2",
      "id":1685937538026291203,
      "idCard": 5000,
      "source":0,
      "phone":5000,
      "content": {
        "education": {
          "378462yjj": {
            "discipline_name": "",
            "discipline_id": "",
            "end_time": "2006年06月01日",
            "degree": "99",
            "school_name": "本科",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 0,
            "start_time": "2002年09月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "378462yjj"
          },
          "07CbgjSk0": {
            "discipline_name": "历史学",
            "discipline_id": "",
            "end_time": "1986年07月01日",
            "degree": "1",
            "school_name": "吉林大学历史系",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 1,
            "start_time": "1982年07月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "07CbgjSk0"
          },
          "1421507798": {
            "discipline_name": "计算机及应用",
            "discipline_id": "",
            "end_time": "2001年07月01日",
            "degree": "1",
            "school_name": "安徽省农业机械化学院",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 2,
            "start_time": "1999年09月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "1421507798"
          },
          "2WJH0Mpkv": {
            "discipline_name": "",
            "discipline_id": "",
            "end_time": "1996年06月01日",
            "degree": "99",
            "school_name": "高中",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 3,
            "start_time": "1993年09月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "2WJH0Mpkv"
          }
        },
        "work": {
          "11j8U7vBjw": {
            "reason": "wewe",
            "industry_id": "0",
            "corporation_id": "10",
            "city": "布宜诺斯艾利斯",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "宾馆"
              ],
              "internal_id": "1061434",
              "region": [
                "上5海"
              ],
              "keyword": [
                "海5虹"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "上海海虹宾馆（三星级）",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 0,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "11j8U7vBjw",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "酒店/餐饮",
            "industry_ids": "656,518,655",
            "station_id": "0",
            "end_time": "1992年01月01日",
            "position_name": "",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "海虹宾馆总经理",
            "start_time": "1990年02月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "3a369PnjH": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "网络科技"
              ],
              "internal_id": "21458938",
              "region": [
                "武汉"
              ],
              "keyword": [
                "瑞克通"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "武汉瑞克通网络科技有限公司",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 1,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "3a369PnjH",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "计算机软件",
            "industry_ids": "414",
            "station_id": "0",
            "end_time": "2005年07月01日",
            "position_name": "酒店管理",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "兼职",
            "start_time": "2005年03月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "47G1ja7vJ": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "信息技术"
              ],
              "internal_id": "21974505",
              "region": [
                "上海"
              ],
              "keyword": [
                "昱泉"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "昱泉信息技术（上海）有限公司",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 2,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "2",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": {
              "should": [],
              "must": [
                "50:0.111781"
              ]
            },
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "工程师",
            "id": "47G1ja7vJ",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "网络游戏",
            "industry_ids": "5",
            "station_id": "0",
            "end_time": "2004年01月01日",
            "position_name": "网络工程师",
            "work_type": "",
            "title_id": "38",
            "architecture_name": "网络游戏营运部",
            "start_time": "2001年12月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "12PfNJCauY": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "饭店"
              ],
              "internal_id": "1222468",
              "region": [
                "上海"
              ],
              "keyword": [
                "国际"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "上海国际饭店（四星级）",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 3,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "12PfNJCauY",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "酒店/餐饮",
            "industry_ids": "656,525,655",
            "station_id": "0",
            "end_time": "1990年02月01日",
            "position_name": "服务员",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "总经理办公室",
            "start_time": "1987年01月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "9nqGq7a66": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "739238",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "国际",
                "软件"
              ],
              "internal_id": "739238",
              "region": [
                "北京"
              ],
              "keyword": [
                "瑞星"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "北京瑞星国际软件有限公司",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 4,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "2,2",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": {
              "should": [],
              "must": []
            },
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "9nqGq7a66",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "计算机软件",
            "industry_ids": "1,2,414,9",
            "station_id": "0",
            "end_time": "2006年09月01日",
            "position_name": "实习生",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "研发部病毒组",
            "start_time": "2006年07月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "1421507796": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "环保"
              ],
              "internal_id": "1509677",
              "region": [
                "深圳",
                "武汉"
              ],
              "keyword": [
                "群鹰"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "深圳群鹰环保公司武汉分公司",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 5,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "1421507796",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "环保",
            "industry_ids": "672,514,557",
            "station_id": "0",
            "end_time": "2007年03月01日",
            "position_name": "文案/策划",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "",
            "start_time": "2007年02月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "2XgSZAeFk": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "基地"
              ],
              "internal_id": "21706150",
              "region": [
                "上海"
              ],
              "keyword": [
                "海军东海舰队"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "海军东海舰队上海基地",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 6,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "2XgSZAeFk",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "",
            "industry_ids": "0",
            "station_id": "0",
            "end_time": "1987年01月01日",
            "position_name": "",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "正营职干部",
            "start_time": "1972年01月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "5ckljbgms": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [],
              "internal_id": "20639345",
              "region": [
                "安徽省",
                "合肥"
              ],
              "keyword": [
                "江淮晨报社"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "安徽省合肥江淮晨报社",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 7,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "5ckljbgms",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "媒体/出版/影视/文化传播",
            "industry_ids": "588,585,580,583",
            "station_id": "0",
            "end_time": "2007年09月01日",
            "position_name": "",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "编辑部",
            "start_time": "2007年06月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "7eV36r03O": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [],
              "internal_id": "1452719",
              "region": [
                "吉林省"
              ],
              "keyword": [
                "视听导报社"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "吉林省视听导报社",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 8,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "文员",
            "id": "7eV36r03O",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "媒体/出版/影视/文化传播",
            "industry_ids": "580,585,588,517,583",
            "station_id": "0",
            "end_time": "2010年03月01日",
            "position_name": "会计文员",
            "work_type": "",
            "title_id": "143",
            "architecture_name": "总编/副总编/主编",
            "start_time": "2005年06月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "6WT2MQxfq": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [],
              "internal_id": "0",
              "region": [],
              "keyword": [
                "吉��省视听导报社"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "吉��省视听导报社",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 9,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "6WT2MQxfq",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "媒体/出版/影视/文化传播",
            "industry_ids": "588,585,580,583",
            "station_id": "0",
            "end_time": "2010年03月01日",
            "position_name": "初中教师",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "总编/副总编/主编",
            "start_time": "2005年06月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "0FIctQmXv": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "酒店"
              ],
              "internal_id": "0",
              "region": [],
              "keyword": [
                "西班牙帕迪尔纳庄园"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "西班牙帕迪尔纳庄园酒店",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 10,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "工程师",
            "id": "0FIctQmXv",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "酒店/餐饮",
            "industry_ids": "656,655",
            "station_id": "0",
            "end_time": "2002年03月01日",
            "position_name": "网络工程师",
            "work_type": "",
            "title_id": "38",
            "architecture_name": "餐饮部",
            "start_time": "1999年04月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "80idUVUQs": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "科技"
              ],
              "internal_id": "21201307",
              "region": [
                "合肥"
              ],
              "keyword": [
                "万博"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "合肥万博科技职业学院",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 11,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "工程师",
            "id": "80idUVUQs",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "政府/公共事业/非盈利机构",
            "industry_ids": "528,0",
            "station_id": "0",
            "end_time": "2001年12月01日",
            "position_name": "网络工程师",
            "work_type": "",
            "title_id": "38",
            "architecture_name": "图书馆",
            "start_time": "2000年07月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "10NWotwfvW": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "会所"
              ],
              "internal_id": "0",
              "region": [],
              "keyword": [
                "凯逸高端"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "凯逸高端私人会所",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 12,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "工程师",
            "id": "10NWotwfvW",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "酒店/餐饮",
            "industry_ids": "656,655",
            "station_id": "0",
            "end_time": "2004年04月01日",
            "position_name": "高级软件工程师",
            "work_type": "",
            "title_id": "38",
            "architecture_name": "会所",
            "start_time": "2002年05月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          }
        },
        "deliver_source": 1,
        "certificate": {
          "08dQrQu6T": {
            "start_time": "",
            "updated_at": "2017-10-19 11:18:09",
            "name": "大学英语四级",
            "description": "",
            "created_at": "2017-10-19 11:18:09",
            "id": "08dQrQu6T",
            "sort_id": 0
          },
          "1srEttA8U": {
            "start_time": "",
            "updated_at": "2017-10-19 11:18:09",
            "name": "钢琴10级",
            "description": "",
            "created_at": "2017-10-19 11:18:09",
            "id": "1srEttA8U",
            "sort_id": 1
          }
        },
        "basic_industry": [],
        "project": {},
        "language": {
          "1421509588": {
            "updated_at": "2015-01-17 23:16:35",
            "level": "",
            "certificate": "",
            "name": "日语",
            "created_at": "2015-01-17 23:16:35",
            "id": "1421509588"
          },
          "1421508683": {
            "updated_at": "2015-01-17 23:16:35",
            "level": "",
            "certificate": "",
            "name": "英语",
            "created_at": "2015-01-17 23:16:35",
            "id": "1421508683"
          }
        },
        "training": {},
        "setting": {
          "salary_is_visible": "1"
        },
        "is_sort": "1",
        "updated_at": "2019-05-13 01:06:14",
        "skill": {},
        "contact": {
          "qq": "277388546",
          "wechat": "",
          "msn": "",
          "created_at": "2014-12-26 15:44:44",
          "is_deleted": "N",
          "updated_at": "2017-10-20 09:43:26",
          "phone": "13810417630",
          "name": "李延辉",
          "tel": "13437142971",
          "sina": "",
          "ten": "",
          "email": "sunpeng33309@sohu.com",
          "phone_area": "1"
        },
        "basic": {
          "reason": "",
          "practice": "",
          "internal_title_name": "",
          "discipline_id": "",
          "nation": "",
          "achievement": "",
          "expect_industry_name": "媒体/出版/影视/文化传播",
          "company_info": {
            "company_type": [],
            "internal_id": "20639345",
            "region": [
              "安徽省",
              "合肥"
            ],
            "keyword": [
              "江淮晨报社"
            ]
          },
          "reporting_to": "",
          "salary_month": "",
          "skills": "",
          "live_family": "U",
          "trade_id": "",
          "discipline_desc": "",
          "options": "",
          "is_house": "U",
          "resume_name": "",
          "text": "",
          "id": "13718976",
          "station_name": "",
          "attachment_ids": "",
          "not_expect_corporation_name": "",
          "internal_title_id": "",
          "mail_id": "",
          "station_id": "0",
          "industry_ids": "588,580",
          "birth": "",
          "focused_feelings": [],
          "locked_time": "0",
          "expect_bonus": "",
          "so_far": "N",
          "certificates": "",
          "user_id": "0",
          "current_status": 1,
          "my_project": "",
          "annual_salary_from": "",
          "card": "",
          "status": "CVC",
          "bonus_text": "",
          "phone_id": "C39109010C0F121E9D12C94D",
          "position_id": "0",
          "expect_salary_to": "15",
          "expect_position_name": "",
          "study": "",
          "gender": "M",
          "customization": "",
          "city": "",
          "user_name": "yingfei.fan",
          "created_at": "2015-01-17 23:16:35",
          "annual_salary_to": "",
          "src_no": "pjwdkmjtge4dgny",
          "overseas": "N",
          "is_fertility": "U",
          "expect_city_ids": "",
          "address_province": "",
          "updated_at": "2017-09-05 10:42:18",
          "political_status": "",
          "cv_tag": [],
          "title_name": "",
          "expect_annual_salary": "",
          "subordinates_count": "0",
          "industry_name": "酒店/餐饮",
          "address": "83",
          "is_active": "U",
          "verification_at": "0",
          "position_name": "",
          "end_time": "1992年01月01日",
          "school_name": "本科",
          "is_add_v": "",
          "photo": "",
          "is_entrance": "N",
          "is_increased": "Y",
          "interests": "",
          "is_core": "N",
          "corporation_id": "0",
          "expect_work_at": "",
          "title_category_id": "1",
          "bonus": "",
          "resume_cnt": "1",
          "is_oversea": "N",
          "has_phone": "Y",
          "expect_city_names": "",
          "responsibilities": "",
          "is_author": "O",
          "basic_salary_to": "",
          "nation_id": "0",
          "degree": "99",
          "work_type": "",
          "architecture_name": "海虹宾馆总经理",
          "start_time": "1990年02月01日",
          "is_contacted": "U",
          "focused_corporations": [],
          "management_experience": "N",
          "name": "李延辉",
          "expect_type": "",
          "corporation_type": "",
          "is_private": "0",
          "industry_id": "0",
          "resume_updated_at": "2013-05-18 00:00:00",
          "expect_salary_month": "",
          "scale": "",
          "corporation_name": "上海海虹宾馆（三星级）",
          "contact_id": "15787397",
          "corporation_desc": "",
          "speciality": "",
          "architecture_id": "0",
          "expect_annual_salary_from": "",
          "is_deleted": "Y",
          "expect_salary_from": "10.1",
          "self_remark": "",
          "school_id": "",
          "annual_salary": "",
          "is_processing": "1",
          "expect_annual_salary_to": "",
          "work_experience": 360,
          "discipline_name": "",
          "src": "1",
          "other_welfare": "",
          "title_id": "0",
          "expect_remarks": "",
          "project_info": "",
          "marital": "U",
          "is_validate": "U",
          "basic_salary_from": "",
          "a_p_b": "",
          "basic_salary": "",
          "account_province": "",
          "category": "",
          "account": ""
        }
      }
    }
  }
}

### 新增候选人简历
# tid:租户id ，rName:姓名 ，
# idCard：身份证， source：来源 ，phone：电话，content：简历json
# original附件信息
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "saveCandidate",
    "p": {
      "tid":2019,
      "rName":"星期四",
      "idCard": 5000,
      "source":0,
      "phone":5000,
      "original":[],
      "content": {
        "education": {
          "120": {
            "discipline_name": "小说文学",
            "discipline_id": "",
            "end_time": "1986年07月01日",
            "degree": "1",
            "school_name": "清华大学",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 1,
            "start_time": "1982年07月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "120"
          }, "121": {
            "discipline_name": "文学888",
            "discipline_id": "",
            "end_time": "1986年07月01日",
            "degree": "1",
            "school_name": "基金大学",
            "created_at": "2015-01-17 23:16:35",
            "sort_id": 1,
            "start_time": "1982年07月01日",
            "is_entrance": "N",
            "so_far": "N",
            "updated_at": "2015-01-17 23:16:35",
            "school_id": "",
            "discipline_desc": "",
            "id": "121"
          }
        },
        "work": {
          "562": {
            "reason": "",
            "industry_id": "0",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "宾东方馆"
              ],
              "internal_id": "1061434",
              "region": [
                "上 发海"
              ],
              "keyword": [
                "海22虹"
              ]
            },
            "scale": "",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "上海考古酒店",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 0,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "562",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "酒店/餐饮",
            "industry_ids": "656,518,655",
            "station_id": "0",
            "end_time": "1992年01月01日",
            "position_name": "",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "宾馆总经理",
            "start_time": "1990年02月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          },
          "563": {
            "reason": "fjfjdfdsjf",
            "industry_id": "10",
            "corporation_id": "0",
            "city": "",
            "achievement": "",
            "title_category_id": "1",
            "bonus": "",
            "is_oversea": "",
            "company_info": {
              "company_type": [
                "宾东方bbgf馆"
              ],
              "internal_id": "1061434",
              "region": [
                "fff发海"
              ],
              "keyword": [
                "海22虹"
              ]
            },
            "scale": "2",
            "created_at": "2015-01-17 23:16:35",
            "corporation_name": "兰州拉面馆",
            "reporting_to": "",
            "salary_month": "",
            "corporation_desc": "",
            "annual_salary_to": "",
            "sort_id": 0,
            "architecture_id": "0",
            "responsibilities": "",
            "is_deleted": "N",
            "trade_id": "",
            "updated_at": "2015-01-17 23:16:35",
            "cv_tag": [],
            "basic_salary_to": "",
            "annual_salary": "",
            "title_name": "",
            "id": "563",
            "station_name": "",
            "subordinates_count": "0",
            "industry_name": "餐饮",
            "industry_ids": "656,518,655",
            "station_id": "0",
            "end_time": "1992年01月01日",
            "position_name": "",
            "work_type": "",
            "title_id": "0",
            "architecture_name": "总经理",
            "start_time": "1990年02月01日",
            "so_far": "N",
            "basic_salary_from": "",
            "a_p_b": "",
            "management_experience": "N",
            "basic_salary": "",
            "annual_salary_from": "",
            "corporation_type": "",
            "position_id": "0"
          }
        },
        "deliver_source": 1,
        "certificate": {
          "08dQrQu6T": {
            "start_time": "",
            "updated_at": "2017-10-19 11:18:09",
            "name": "大学英语4级",
            "description": "",
            "created_at": "2017-10-19 11:18:09",
            "id": "08dQrQu6T",
            "sort_id": 0
          },
          "1srEttA8U": {
            "start_time": "",
            "updated_at": "2017-10-19 11:18:09",
            "name": "钢琴10级",
            "description": "",
            "created_at": "2017-10-19 11:18:09",
            "id": "1srEttA8U",
            "sort_id": 1
          }
        },
        "basic_industry": [],
        "project": {},
        "language": {
          "1421509588": {
            "updated_at": "2015-01-17 23:16:35",
            "level": "",
            "certificate": "",
            "name": "日语",
            "created_at": "2015-01-17 23:16:35",
            "id": "1421509588"
          },
          "1421508683": {
            "updated_at": "2015-01-17 23:16:35",
            "level": "",
            "certificate": "",
            "name": "英语",
            "created_at": "2015-01-17 23:16:35",
            "id": "1421508683"
          }
        },
        "training": {},
        "setting": {
          "salary_is_visible": "1"
        },
        "is_sort": "1",
        "updated_at": "2019-05-13 01:06:14",
        "skill": {},
        "contact": {
          "qq": "277388546",
          "wechat": "",
          "msn": "",
          "created_at": "2014-12-26 15:44:44",
          "is_deleted": "N",
          "updated_at": "2017-10-20 09:43:26",
          "phone": "13810417630",
          "name": "李延辉",
          "tel": "13437142971",
          "sina": "",
          "ten": "",
          "email": "sunpeng33309@sohu.com",
          "phone_area": "1"
        },
        "basic": {
          "reason": "",
          "practice": "",
          "internal_title_name": "",
          "discipline_id": "",
          "nation": "",
          "achievement": "",
          "expect_industry_name": "媒体/出版",
          "company_info": {
            "company_type": [],
            "internal_id": "20639345",
            "region": [
              "安徽44省",
              "合43肥"
            ],
            "keyword": [
              "江淮晨报社"
            ]
          },
          "reporting_to": "",
          "salary_month": "",
          "skills": "",
          "live_family": "U",
          "trade_id": "",
          "discipline_desc": "",
          "options": "",
          "is_house": "U",
          "resume_name": "",
          "text": "",
          "id": "13718976",
          "station_name": "",
          "attachment_ids": "",
          "not_expect_corporation_name": "",
          "internal_title_id": "",
          "mail_id": "",
          "station_id": "0",
          "industry_ids": "588,580",
          "birth": "",
          "focused_feelings": [],
          "locked_time": "0",
          "expect_bonus": "",
          "so_far": "N",
          "certificates": "",
          "user_id": "0",
          "current_status": 1,
          "my_project": "",
          "annual_salary_from": "",
          "card": "",
          "status": "CVC",
          "bonus_text": "",
          "phone_id": "C39109010C0F121E9D12C94D",
          "position_id": "0",
          "expect_salary_to": "15",
          "expect_position_name": "",
          "study": "",
          "gender": "M",
          "customization": "",
          "city": "",
          "user_name": "yingfei.fan",
          "created_at": "2015-01-17 23:16:35",
          "annual_salary_to": "",
          "src_no": "pjwdkmjtge4dgny",
          "overseas": "N",
          "is_fertility": "U",
          "expect_city_ids": "",
          "address_province": "",
          "updated_at": "2017-09-05 10:42:18",
          "political_status": "",
          "cv_tag": [],
          "title_name": "",
          "expect_annual_salary": "",
          "subordinates_count": "0",
          "industry_name": "酒店/餐饮",
          "address": "83",
          "is_active": "U",
          "verification_at": "0",
          "position_name": "",
          "end_time": "1992年01月01日",
          "school_name": "本科",
          "is_add_v": "",
          "photo": "",
          "is_entrance": "N",
          "is_increased": "Y",
          "interests": "",
          "is_core": "N",
          "corporation_id": "0",
          "expect_work_at": "",
          "title_category_id": "1",
          "bonus": "",
          "resume_cnt": "1",
          "is_oversea": "N",
          "has_phone": "Y",
          "expect_city_names": "",
          "responsibilities": "",
          "is_author": "O",
          "basic_salary_to": "",
          "nation_id": "0",
          "degree": "99",
          "work_type": "",
          "architecture_name": "海虹宾馆总经理",
          "start_time": "1990年02月01日",
          "is_contacted": "U",
          "focused_corporations": [],
          "management_experience": "N",
          "name": "李延辉",
          "expect_type": "",
          "corporation_type": "",
          "is_private": "0",
          "industry_id": "0",
          "resume_updated_at": "2013-05-18 00:00:00",
          "expect_salary_month": "",
          "scale": "",
          "corporation_name": "上海海虹宾馆（三星级）",
          "contact_id": "15787397",
          "corporation_desc": "",
          "speciality": "",
          "architecture_id": "0",
          "expect_annual_salary_from": "",
          "is_deleted": "Y",
          "expect_salary_from": "10.1",
          "self_remark": "",
          "school_id": "",
          "annual_salary": "",
          "is_processing": "1",
          "expect_annual_salary_to": "",
          "work_experience": 360,
          "discipline_name": "",
          "src": "1",
          "other_welfare": "",
          "title_id": "0",
          "expect_remarks": "",
          "project_info": "",
          "marital": "U",
          "is_validate": "U",
          "basic_salary_from": "",
          "a_p_b": "",
          "basic_salary": "",
          "account_province": "",
          "category": "",
          "account": ""
        }
      }
    }
  }
}

###批量查候选人简历
#  candidateIds：候选人简历ids

POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/selectCandidateList",
    "p": {
      "candidateIds": [1685937538026291203]
    }
  }
}


###根据id批量查询简历记录列表 candidateRecordIds
#candidateIds：候选人简历记录ids

#POST http://tob-resume-service.ifchange.com/rest/
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/selectCandidateRecordList",
    "p": {
      "candidateRecordIds": [1688157284705894403]
    }
  }
}

###根据简历id查候选人简历历史记录 candidateId


POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/listCandidateRecordsByCid",
    "p": {
      "candidateId": 1685937538026291203
    }
  }
}

###根据tid，name，phone或者idcard 获取候选人简历id
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/getCidByNamePhoneOrIdcard",
    "p": {

      "tid":2019,
      "rName":"星期四",
      "phone":"5000"

    }
  }
}

###新增/修改标签
#listTag：标签集合 tagName 标签名称 tagType 标签类型
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/upsetTag",
    "p": {
      "candidateId": 2000000010989498120,
      "updateId": 1,
      "listTag":[{"tagName":"tag11","tagType":1},{"tagName":"tag10","tagType":1},{"tagName":"tag3"},{"tagName":"tag4"},{"tagName":"tag5"}]
    }
  }
}

###批量查询标签
#candidateIds：标签id集合
POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/listTagByTobResumeIds",
    "p": {
      "candidateIds": [2000000010989498120]
    }
  }
}
###新增简历原件信息
# tid:租户id  ，candidateId：候选人简历id
# deliverSource：简历来源 fileName：文件名
# originalType：原件类型 1.简历原件 2.简历附件
# fileInfo：文件内容    subUid：原件附属uid
# fileType：文件类型 1.url 2.文件 （json）
# deliverSource：简历来源

POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/saveOriginalByCid",
    "p":{
        "original": [{
        "fileName": "1",
        "originalType": "2",
        "fileInfo": "3",
        "subUid": "4",
        "fileType": "1",
        "md5": "6"
      }],
      "candidateId": "1685937538026291203",
      "deliverSource": "1",
      "tid": 2019
    }
  }
}


###删除候选人简历原件
# candidateId：候选人简历id
#subUid：原件附属uid   originalId：简历原件ID


POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/deleteOriginal",
    "p":{
      "candidateId": "1685937538026291203",
      "subUid": "4",
      "originalId": 1
    }
  }
}

###更新简历原件信息
# candidateId：候选人简历id
# fileInfo：文件内容  subUid：原件附属uid
# originalId：简历原件ID

POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/updateOriginal",
    "p":{
      "candidateId": "1685937538026291203",
      "subUid": "4",
      "originalId": 1,
      "fileInfo":"asdghh"
    }
  }
}


###通过候选人简历ID获取简历原件
# candidateId：候选人简历id
# fileInfo：文件内容  subUid：原件附属uid
# originalId：简历原件ID

POST http://localhost:20002/
Content-Type: application/json

{
  "header": {
    "appId": "31082108212",
    "ip": "192.168.0.2"
  },
  "request": {
    "c": "/resume/candidate",
    "m": "/listOriginalByCids",
    "p":{
      "candidateIds": [1685937538026291203]
    }
  }
}

###

```



##### 4.环境

- 192.168.1.201:3306 devuser devuser


#### 二. Toc简历

##### 1.数据库设计 

- toc

  ![](C:\Users\陈正凯\Desktop\灵活用工\toc.bmp)

- toc_account

  ![toc_account](C:\Users\陈正凯\Desktop\灵活用工\toc_account.bmp)

- toc_zhaogong

![DB设计](C:\Users\陈正凯\Desktop\灵活用工\toc_zhaogong.bmp)

##### 2. DDL

```sql
******************************toc*******************************
USE `toc`;
CREATE TABLE `account` (
  `id` int(10) unsigned NOT NULL COMMENT 'ID',
  `password` char(32) NOT NULL DEFAULT '' COMMENT 'md5(md5(密码)和salt)后的值',
  `salt` char(16) NOT NULL DEFAULT '' COMMENT 'salt',
  `default_resume_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'toc简历id',
  `phone` char(11) NOT NULL DEFAULT '' COMMENT '手机',
  `email` varchar(64) NOT NULL DEFAULT '' COMMENT '邮箱',
  `unionid` char(28) NOT NULL DEFAULT '' COMMENT '微信unionid',
  `verify_phone` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '验证手机 0:未验证 1:已验证',
  `verify_email` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '验证邮箱 0:未验证 1:已验证',
  `nick_name` varchar(32) NOT NULL DEFAULT '' COMMENT '昵称',
  `photo` varchar(128) NOT NULL DEFAULT '' COMMENT '头像',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '修改时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  `last_phone` varchar(11) NOT NULL DEFAULT '' COMMENT '之前手机号',
  `last_email` varchar(64) NOT NULL DEFAULT '' COMMENT '之前邮箱',
  `source` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '来源0:网站 1:apple 2:andriod 3:微信 4:手机网站 98:万科idm 99:其他',
  `come_from` varchar(128) NOT NULL DEFAULT '' COMMENT '来自渠道',
  `flag` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '状态(2:禁止修改密码 1:帐号禁用，取值后进行位与运算)',
  PRIMARY KEY (`id`),
  KEY `account_email_index` (`email`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='账号信息表';


******************************toc_account*******************************
USE `toc_account`;
CREATE TABLE `account_wechat_openid_relation` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `wechat_appid_no` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '微信appid的编号',
  `wechat_openid` char(28) NOT NULL DEFAULT '' COMMENT 'openid',
  `user_id` int(10) unsigned DEFAULT NULL COMMENT 'toc 用户id',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '修改时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_wechat_openid_wechat_appid_no` (`wechat_openid`,`wechat_appid_no`),
  KEY `idx_user_id_wechat_appid_no_updated_at` (`user_id`,`wechat_appid_no`,`updated_at`)
) ENGINE=InnoDB AUTO_INCREMENT=30 DEFAULT CHARSET=utf8 COMMENT='微信注册关系表';

CREATE TABLE `wechat_appid_register` (
  `no` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `wechat_appid` char(18) NOT NULL DEFAULT '' COMMENT '微信公众号小程序appid',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`no`),
  UNIQUE KEY `udx_wechat_appid` (`wechat_appid`)
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8 COMMENT='微信appid注册表';

******************************toc_zhaogong*******************************
USE `toc_zhaogong`;
CREATE TABLE `blue_collar_delivery` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'toc用户id',
  `tob_custom_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '定制id',
  `channel` tinyint(3) unsigned NOT NULL DEFAULT '1' COMMENT '简历类型：1社招，2校招，3实习生 6蓝领招聘',
  `position_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'B端职位id',
  `ats_id` varchar(64) NOT NULL DEFAULT '' COMMENT 'ats流程id',
  `original_info` varchar(255) NOT NULL DEFAULT '' COMMENT '投递原始简历信息',
  `status` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '投递状态（10:投递成功、20:已查看、30:评审中、40:面试中、50:不合适、90:录用中、100:已入职、101:未入职）',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_tob_custom_id_position` (`user_id`,`tob_custom_id`,`position_id`) USING BTREE,
  KEY `idx_ats_id` (`ats_id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='投递记录表';


/*Table structure for table `custom_company` */

CREATE TABLE `custom_company` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tob用户id',
  `type` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '0web1wap',
  `domain_name` varchar(64) NOT NULL DEFAULT '' COMMENT '域名',
  `company_name` varchar(64) NOT NULL DEFAULT '' COMMENT '公司名称',
  `email` varchar(64) NOT NULL DEFAULT '' COMMENT '公司的邮箱',
  `status` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '0草稿1发布',
  `config_type` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '0ai官网，1ka项目',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_tob_user_id_type` (`tob_user_id`,`type`),
  UNIQUE KEY `udx_domain_name` (`domain_name`)
) ENGINE=InnoDB AUTO_INCREMENT=1340 DEFAULT CHARSET=utf8 COMMENT='公司信息表';

/*Table structure for table `favorite` */

CREATE TABLE `favorite` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `tob_custom_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '定制id',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户ID',
  `position_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '职位ID',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_position_id` (`user_id`,`position_id`)
) ENGINE=InnoDB AUTO_INCREMENT=854 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='收藏职位';

/*Table structure for table `group_award_config` */

CREATE TABLE `group_award_config` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `top_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '公司id',
  `money` decimal(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '每月奖励金额 单位：元',
  `duration` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '奖励时长 单位：月',
  `type` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '奖励类型 0：按天   1：按月',
  `grant_date` varchar(50) NOT NULL DEFAULT '' COMMENT '每月发放日期',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8mb4 COMMENT='成团奖励配置表';

/*Table structure for table `group_draw_record` */

CREATE TABLE `group_draw_record` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '提取人id',
  `top_id` int(11) unsigned NOT NULL DEFAULT '0',
  `money` decimal(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '金额  单位：元',
  `nickname` varchar(100) NOT NULL DEFAULT '' COMMENT '微信昵称',
  `avatar_url` varchar(100) NOT NULL DEFAULT '' COMMENT '微信头像',
  `real_name` varchar(20) NOT NULL DEFAULT '' COMMENT '真实姓名',
  `mobile_phone` char(11) NOT NULL DEFAULT '' COMMENT '手机号',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '1' COMMENT '是否已提现  0:否 1:是',
  `accounting_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '到账时间',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COMMENT='提现记录表';

/*Table structure for table `group_follow` */

CREATE TABLE `group_follow` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `group_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '成团id',
  `member_uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '参团成员id',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '参团人状态  0：投递  1：面试  2：入职 3：离职 4淘汰',
  `on_work_time` timestamp NULL DEFAULT NULL COMMENT '入职时间',
  `dimission_time` timestamp NULL DEFAULT NULL COMMENT '离职时间',
  `real_entry_status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '入职标记  奖励发放用 0：未标记 1：已标记（入职时间小于当前时间 记录此状态，发放完奖励恢复默认状态）',
  `real_entry_time` timestamp NULL DEFAULT NULL COMMENT '真实入职时间',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `idx_member_id` (`member_uid`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=utf8mb4 COMMENT='拼团成员表';

/*Table structure for table `group_found` */

CREATE TABLE `group_found` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `group_initiator_uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '拼团发起人id',
  `top_id` int(11) unsigned NOT NULL DEFAULT '0',
  `position_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '职位id',
  `is_worker` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否在职 0：否  1：是',
  `group_status` tinyint(1) unsigned NOT NULL DEFAULT '1' COMMENT '拼团状态   1：有效  0：无效',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '发起人状态  0：投递  1：面试  2：入职 3：离职 4淘汰',
  `send_times` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '奖励已发放次数  ',
  `send_status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '奖励发放状态  0：未发放 1：发放中 2：已完成发放',
  `expiration_date` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '过期时间  冗余字段',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `idx_topid_positionid` (`top_id`,`position_id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=utf8mb4 COMMENT='拼团主表';

/*Table structure for table `group_grant_record` */

CREATE TABLE `group_grant_record` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `appid` varchar(50) NOT NULL DEFAULT '' COMMENT '公众号appid',
  `group_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '成团id',
  `recipient_id` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '接收人id',
  `money` decimal(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '奖励金额 单位：元',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否发放成功  1：是 0：否',
  `grant_type` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '发放类型  0：每天  1：每月',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=169 DEFAULT CHARSET=utf8mb4 COMMENT='奖励发放记录表';

/*Table structure for table `group_payment_serial_log` */

CREATE TABLE `group_payment_serial_log` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `uid` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '申请人id',
  `openid` varchar(50) NOT NULL DEFAULT '' COMMENT '收款人openid',
  `mchid` varchar(50) NOT NULL DEFAULT '' COMMENT '商户号',
  `mch_appid` varchar(150) NOT NULL DEFAULT '' COMMENT '申请商户号的appid或商户号绑定的appid',
  `partner_trade_no` varchar(32) NOT NULL DEFAULT '' COMMENT '商户订单号',
  `payment_no` varchar(64) NOT NULL DEFAULT '' COMMENT '微信付款单号',
  `payment_money` decimal(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '付款金额',
  `payment_time` varchar(50) NOT NULL DEFAULT '' COMMENT '付款成功时间',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='提现详细流水表';

/*Table structure for table `group_user_balance` */

CREATE TABLE `group_user_balance` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `uid` int(11) unsigned NOT NULL DEFAULT '0',
  `balance` decimal(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '用户余额  单位：元',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unidx_uid` (`uid`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=72 DEFAULT CHARSET=utf8mb4 COMMENT='拼团用户余额表';

/*Table structure for table `middle_blue_collar_account` */

CREATE TABLE `middle_blue_collar_account` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `phone` char(11) DEFAULT NULL COMMENT '手机号',
  `unionid` char(28) NOT NULL DEFAULT '' COMMENT '微信微信unionid',
  `openid` char(28) NOT NULL DEFAULT '' COMMENT '维信openid',
  `ending_user_id` int(11) NOT NULL DEFAULT '0' COMMENT '蓝领用户验证之后转成正式用户的用户ID',
  `updated_at` int(10) NOT NULL DEFAULT '0',
  `created_at` int(10) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_unionid` (`unionid`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf8 COMMENT='蓝领用户未验证之前的用户信息表';

/*Table structure for table `middle_blue_collar_ai_interview_record` */

CREATE TABLE `middle_blue_collar_ai_interview_record` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增id',
  `toc_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'middle_blue_colar_account表id',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'B端topid',
  `channel` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '渠道：1社招，2校招，3实习生 ,6门店',
  `key` varchar(50) NOT NULL DEFAULT '' COMMENT '问题key',
  `question` varchar(200) NOT NULL DEFAULT '' COMMENT '题目',
  `question_type` varchar(50) NOT NULL DEFAULT '' COMMENT '题目类型',
  `answer` text NOT NULL COMMENT '答案',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_toc_user_id_tob_user_id_channel_key` (`toc_user_id`,`tob_user_id`,`channel`,`key`)
) ENGINE=InnoDB AUTO_INCREMENT=23 DEFAULT CHARSET=utf8 COMMENT='未验证蓝领用户ai面试问答记录';

/*Table structure for table `middle_blue_collar_resume_relation` */

CREATE TABLE `middle_blue_collar_resume_relation` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'mongodb_id',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '未验证蓝领用户ID',
  `tob_custom_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '定制ID',
  `channel` tinyint(3) unsigned NOT NULL DEFAULT '1' COMMENT '简历类型，1社招，2校招，3实习生',
  `icdc_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '对应人才库的简历ID',
  `complete_flag` smallint(5) unsigned NOT NULL DEFAULT '0' COMMENT '完善字段，0未完善，1标签，2技能，4培训，8证书，16语言，32项目，64工作，128教育，256期望，512基本信息，1024联系方式',
  `original_info` varchar(255) NOT NULL DEFAULT '' COMMENT '投递附件存储信息',
  `sync_icdc_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '获取icdc_id时间',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_tob_custom_id_channel` (`user_id`,`tob_custom_id`,`channel`)
) ENGINE=InnoDB AUTO_INCREMENT=1156 DEFAULT CHARSET=utf8 COMMENT='未验证蓝领简历关系表，保存简历对用户和icdc对映射关系';

/*Table structure for table `middle_blue_collar_user_extra` */

CREATE TABLE `middle_blue_collar_user_extra` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键ID',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'middle_blue_colar_account表id',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tob topid',
  `user_nature` tinyint(3) unsigned NOT NULL DEFAULT '2' COMMENT '用户属性 1 在职员工 ，2 求职者',
  `shop_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '当前用户所属店铺id',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '用户真实姓名',
  `is_authorized` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '用户是否同意授权协议0未知， 1 同意，2 不同意',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_tob_user_id` (`user_id`,`tob_user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='未验证蓝领用户的其他信息';

/*Table structure for table `resume_relation` */

CREATE TABLE `resume_relation` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'mongodb_id',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户ID',
  `tob_custom_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '定制ID',
  `channel` tinyint(3) unsigned NOT NULL DEFAULT '1' COMMENT '简历类型，1社招，2校招，3实习生',
  `icdc_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '对应人才库的简历ID',
  `complete_flag` smallint(5) unsigned NOT NULL DEFAULT '0' COMMENT '完善字段，0未完善，1标签，2技能，4培训，8证书，16语言，32项目，64工作，128教育，256期望，512基本信息，1024联系方式',
  `original_info` varchar(255) NOT NULL DEFAULT '' COMMENT '投递附件存储信息',
  `sync_icdc_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '获取icdc_id时间',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_tob_custom_id_channel` (`user_id`,`tob_custom_id`,`channel`)
) ENGINE=InnoDB AUTO_INCREMENT=1223 DEFAULT CHARSET=utf8 COMMENT='简历关系表，保存简历对用户和icdc对映射关系';

/*Table structure for table `user_ai_interview_record` */

CREATE TABLE `user_ai_interview_record` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增id',
  `toc_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'toc账号id',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'B端topid',
  `channel` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '渠道：1社招，2校招，3实习生 ,6门店',
  `key` varchar(50) NOT NULL DEFAULT '' COMMENT '问题key',
  `question` varchar(200) NOT NULL DEFAULT '' COMMENT '题目',
  `question_type` varchar(50) NOT NULL DEFAULT '' COMMENT '题目类型',
  `answer` text NOT NULL COMMENT '答案',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_toc_user_id_tob_user_id_channel_key` (`toc_user_id`,`tob_user_id`,`channel`,`key`)
) ENGINE=InnoDB AUTO_INCREMENT=31 DEFAULT CHARSET=utf8 COMMENT='用户ai面试问答记录';

/*Table structure for table `user_extra` */

CREATE TABLE `user_extra` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键ID',
  `user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户id',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tob topid',
  `user_nature` tinyint(3) unsigned NOT NULL DEFAULT '2' COMMENT '用户属性 1 在职员工 ，2 求职者',
  `shop_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '当前用户所属店铺id',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '用户真实姓名',
  `is_authorized` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '用户是否同意授权协议0未知， 1 同意，2 不同意',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_user_id_tob_user_id` (`user_id`,`tob_user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8 COMMENT='关于用户的其他信息';

/*Table structure for table `user_info` */

CREATE TABLE `user_info` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `toc_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'toc用户id',
  `tob_user_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'tob用户id',
  `nick_name` varchar(32) NOT NULL DEFAULT '' COMMENT '昵称',
  `photo` varchar(128) NOT NULL DEFAULT '' COMMENT '头像',
  `updated_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '修改时间',
  `created_at` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `udx_toc_user_id_tob_user_id` (`toc_user_id`,`tob_user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=330 DEFAULT CHARSET=utf8 COMMENT='账号信息表';

```

##### 3.MongoDB存储简历内容-toc_zhaogong

![mongo](C:\Users\陈正凯\Desktop\灵活用工\mongo.png)


完整内容举例:
```json
{
    "_id" : 3,
    "contact" : {
        "phone" : "18356078242",
        "email" : "wanggang@ifchange.com",
        "wechat" : "abc",
        "phone_area" : "1"
    },
    "basic" : {
        "name" : "王刚",
        "card_type" : "护照",
        "gender" : "M",
        "birth" : "1995-09",
        "card" : "E19769135",
        "photo" : "/toc/custom/campus_resume_photo/0/58/60393_1502078176.png"
    },
    "expect" : {},
    "education" : {
        "x5983e7a147450001" : {
            "degree" : "4",
            "start_time" : "2013-01",
            "end_time" : "2015-07",
            "school_name" : "上海交通大学",
            "department_name" : "金融系",
            "discipline_name" : "统计学",
            "score" : "平均分89",
            "so_far" : "N",
            "_id" : "x5983e7a147450001"
        }
    },
    "project" : {},
    "language" : {},
    "certificate" : {},
    "training" : {},
    "user_tag" : [],
    "setting" : {},
    "attachment" : {},
    "campus" : {
        "expected_graduation_date" : "2017-06",
        "emergency_contact_name" : "王国良",
        "emergency_contact_phone" : "18451562542",
        "english_level" : "雅思",
        "it_skill" : "统计学",
        "witness_name" : "程俊生",
        "witness_identity" : "班主任",
        "witness_phone" : "18456254563",
        "in_campus_experience" : "班长"
    },
    "user_id" : 60393,
    "updated_at" : 1511350040
}
```



##### 4. 接口文档

[Toc接口文档](<http://192.168.1.150:8090/pages/viewpage.action?pageId=85557691>)

##### 5. 环境

- mysql:192.168.1.201:3308  devuser devuser
- mongo:192.168.1.110:27017 