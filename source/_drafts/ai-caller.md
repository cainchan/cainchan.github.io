---
title: AI寻访员技术评审
---


####  定时同步脚本
1. 每半小时读取所有进行中的任务  
	```
    select * from ai_visitor_task where status = 3;
	```
2. 循环task获取第三方外呼计划id
	```
    select campaign_id from ai_visitor_call_task where status = 1;
	```
    1. 获取外呼计划详情-调用公共服务层:robot_call_campaign_detail
    2. 根据外呼计划结果循环比对数据,对有结果但未同步的数据进行获取详情并保存数据
    3. 如果外呼计划已完成则创建新的外呼计划-调用公共服务层:robot_call_campaign_add
	```
    update ai_visitor_call_task set status=3 where id=外呼任务id
    insert into ai_visitor_call_task status=1
   ```
    4.如果没有联系方式可以创建外呼计划了就把task设置为已完成
    ```
    update ai_visitor_task set status=5 where id=任务id
    ```

#### 开始任务
1. 根据传参获取任务详情
	```
	select * from ai_visitor_task where id=task_id
	```
2. 判断任务权限,不是该账号的任务报异常退出
	```
	if (account_id  != task['account_id']){ return; }
	```
3. 检查是否有外呼任务,如果有进行中的直接退出
	```
	select * from ai_visitor_call_task where task_id=task_id
	if (status == 1){ return; }
	```
4. 如果有新建和暂停的就调用公共服务层-切换状态并更新任务状态
	```
	if (status == 0||2 ){ rpc=>robot_call_campaign_change_status }
	update ai_visitor_call_task set status=进行中
	update ai_visitor_task set status=进行中
	```
5. 如果没有外呼任务,就获取待外呼的联系方式创建外呼计划并更新ai_visitor_task状态
	```
	select * from ai_visitor_list where status=1
	创建外呼计划 rpc => robot_call_campaign_add
	开始任务 rpc => robot_call_campaign_change_status 
	update ai_visitor_call_task set status=进行中
	update ai_visitor_task set status=进行中
	```



#### 暂停任务
1. 根据传参获取任务详情
	```
	select * from ai_visitor_task where id=task_id
	```
2. 判断任务权限,不是该账号的任务报异常退出
	```
	if (account_id  != task['account_id']){ return; }
	```
3. 检查是否有外呼任务,如果没有进行中的直接报错退出
	```
	select * from ai_visitor_call_task where task_id=task_id
	if (empty){ return; }
	```
4. 如果有就调用切换任务状态接口并更新数据
	```
	开始任务 rpc => robot_call_campaign_change_status 
	update ai_visitor_call_task set status=进行中
	update ai_visitor_task set status=进行中
	```
	
#### 查看外呼结果统计
1. 根据传参获取任务已呼叫完成的联系人数据
	```
	select * from ai_visitor_list where task_id=task_id and call_result in (1,2)
	```
2. 遍历数据
	```
	if (call_result == 1){成功+1}
	if (call_result == 2){失败+1}
	if (intension_result == 1){有意向+1}
	if (call_result == 2){不考虑+1}
	if (call_result == 3){不方便+1}
	if (call_result == 4){号码错误+1}
	```

#### 猎企添加BOT权限

	```
	`is_open_bot` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否开通bot 0=>否 1=>是',
	`bot_expire_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT 'bot使用期限（过期时间）',
	`bot_task_num` int(10) NOT NULL DEFAULT '0' COMMENT 'bot任务数量'
	```