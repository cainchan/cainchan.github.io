#### 申请测试环境部署

- web服务器:10.9.10.65, 10.9.10.66

- mysql服务器:10.9.10.6   (新起一个Mysql实例,要求监听binlog)

- 测试域名: testing2.laowu.ifchange.com

- 环境要求:  (Laravel5.5)>=PHP7.1, Nginx, Memcache

##### 仓库地址

- 后端业务项目地址:`git@gitlab.ifchange.com:toh/service/sly.git`  分支:`20191115`

- 后端框架项目地址:`git@gitlab.ifchange.com:toh/service/laravel-system.git`    分支:`publish`

- 前端业务项目地址: `git@gitlab.ifchange.com:toh/client/sly-web.git`    分支:`20191115`

- 小程序业务项目地址: `git@gitlab.ifchange.com:toh/client/sly-wechat.git `    分支:`20191115`

##### Nginx配置:

1. Nginx配置
    ```nginx
    server {
            listen       9090;
            server_name  testing2.laowu.ifchange.com;
            charset utf-8;
            root    /opt/wwwroot/toh/client/sly-web/dist;
    	
    	access_log  /opt/log/laowu.log main;
            error_log   /opt/log/laowu_err.log;	
            index  index.html index.htm index.php;
    
    	location / {
           	try_files $uri $uri/ /index.html?$query_string;
      	}
    	location /api {
    		try_files $uri $uri/ /index.php?$query_string;
            }
    
    	location ~ \.php$ {
    		root /opt/wwwroot/toh/service/sly/public;
    		fastcgi_pass            laowu ;
    		fastcgi_index           index.php;
    		fastcgi_split_path_info     ^(.+\.php)(.*)$;
    		fastcgi_param       PATH_INFO                $fastcgi_path_info;
    		fastcgi_param       PATH_TRANSLATED        $DOCUMENT_ROOT$fastcgi_path_info;
    		fastcgi_param       SCRIPT_FILENAME  $DOCUMENT_ROOT/$fastcgi_script_name;
    		include             fastcgi_params;
    	}
    }
    ```

2. 定时脚本
    1.自动同步呼叫数据到业务端

    - 将`* * * * * php artisan schedule:run `添加到Crontab

3. DDL

    ```sql
    DROP DATABASE sly;
    DROP DATABASE sly_caller;
    DROP DATABASE sly_talent;
    
    /*
    SQLyog Trial
    MySQL - 5.7.11-log : Database - sly
    *********************************************************************
    */
    
    /*!40101 SET NAMES utf8 */;
    
    /*!40101 SET SQL_MODE=''*/;
    
    /*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
    /*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
    /*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
    CREATE DATABASE /*!32312 IF NOT EXISTS*/`sly` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
    
    USE `sly`;
    
    /*Table structure for table `account` */
    
    CREATE TABLE `account` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `parent_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '父ID',
      `uid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户ID',
      `uniqid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '当前绑定公司的唯一ID',
      `role_type` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '角色类型 0:总经理 1:项目经理 2:驻场主管 3:招聘专员',
      `mobile` varchar(32) NOT NULL DEFAULT '' COMMENT '手机号(登录账号)',
      `password` varchar(64) NOT NULL DEFAULT '' COMMENT '密码',
      `name` varchar(32) NOT NULL DEFAULT '' COMMENT '姓名',
      `email` varchar(128) NOT NULL DEFAULT '' COMMENT '邮箱',
      `job_status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '在职状态 0:离职 1:在职',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_mobile` (`mobile`),
      UNIQUE KEY `uniq_uid` (`uid`),
      UNIQUE KEY `uniq_uniqid` (`uniqid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `account_company` */
    
    CREATE TABLE `account_company` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `account_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '账号ID',
      `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司ID',
      `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0:否 1:是',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_account_id_company_id` (`account_id`,`company_id`),
      KEY `idx_account_id_is_deleted` (`account_id`,`is_deleted`),
      KEY `idx_company_id_is_deleted` (`company_id`,`is_deleted`)
    ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `company` */
    
    CREATE TABLE `company` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `tid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '租户ID',
      `name` varchar(128) NOT NULL DEFAULT '' COMMENT '公司名称',
      `short_name` varchar(128) NOT NULL DEFAULT '' COMMENT '公司简称',
      `credit_code` varchar(32) NOT NULL DEFAULT '' COMMENT '社会信用代码',
      `logo` varchar(128) NOT NULL DEFAULT '' COMMENT '公司Logo',
      `qrcode` varchar(128) NOT NULL DEFAULT '' COMMENT '公众号二维码',
      `license` varchar(128) NOT NULL DEFAULT '' COMMENT '营业执照',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_credit_code` (`credit_code`),
      UNIQUE KEY `uniq_tid` (`tid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `company_service` */
    
    CREATE TABLE `company_service` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司ID',
      `service_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '服务ID',
      `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0:否 1:是',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_company_id_service_id` (`company_id`,`service_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `customer` */
    
    CREATE TABLE `customer` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '用户id',
      `company_id` int(11) NOT NULL DEFAULT '0' COMMENT '公司id',
      `name` varchar(100) NOT NULL DEFAULT '' COMMENT '客户全称',
      `short_name` varchar(50) NOT NULL DEFAULT '' COMMENT '客户简称',
      `address` varchar(50) NOT NULL DEFAULT '' COMMENT '客户地址',
      `recruit_notice` varchar(255) NOT NULL DEFAULT '' COMMENT '招聘须知',
      `entry_process` varchar(255) NOT NULL DEFAULT '' COMMENT '入职流程',
      `level_process` varchar(255) NOT NULL DEFAULT '' COMMENT '离职流程',
      `other` varchar(255) NOT NULL DEFAULT '' COMMENT '其他信息',
      `status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '状态:0 合作中, 1 合同终止',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=20 DEFAULT CHARSET=utf8mb4 COMMENT='甲方信息表';
    
    /*Table structure for table `customer_aggrement` */
    
    CREATE TABLE `customer_aggrement` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `customer_id` int(11) NOT NULL COMMENT '客户id',
      `first_party_name` varchar(50) NOT NULL DEFAULT '' COMMENT '甲方名称',
      `sccond_party_name` varchar(50) NOT NULL DEFAULT '' COMMENT '乙方名称',
      `start_date` int(11) DEFAULT '0' COMMENT '合同开始时间',
      `end_date` int(11) NOT NULL DEFAULT '0' COMMENT '合同结束时间',
      `pay_date` int(11) NOT NULL DEFAULT '0' COMMENT '付费日期',
      `charge_standard` varchar(255) NOT NULL DEFAULT '' COMMENT '收费标准',
      `other` varchar(255) NOT NULL DEFAULT '' COMMENT '其他信息',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`),
      UNIQUE KEY `uidx_customerid` (`customer_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8mb4 COMMENT='客户合同信息';
    
    /*Table structure for table `customer_attachment_file` */
    
    CREATE TABLE `customer_attachment_file` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `customer_id` int(11) NOT NULL DEFAULT '0' COMMENT '客户id',
      `filename` varchar(50) NOT NULL DEFAULT '' COMMENT '文件名',
      `md5` varchar(40) NOT NULL DEFAULT '' COMMENT '文件内容hash',
      `dfs_group_name` varchar(20) NOT NULL DEFAULT '',
      `dfs_file_name` varchar(100) NOT NULL DEFAULT '',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8mb4 COMMENT='合同附件';
    
    /*Table structure for table `customer_bd` */
    
    CREATE TABLE `customer_bd` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `customer_id` int(11) NOT NULL DEFAULT '0' COMMENT '甲方客户id',
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '添加者id',
      `bd_account_id` int(11) NOT NULL DEFAULT '0' COMMENT 'BD账号id',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='乙方BD人员';
    
    /*Table structure for table `customer_contact` */
    
    CREATE TABLE `customer_contact` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `customer_id` int(11) NOT NULL DEFAULT '0' COMMENT '客户id',
      `name` varchar(50) NOT NULL DEFAULT '',
      `mobile` varchar(15) NOT NULL DEFAULT '',
      `email` varchar(30) NOT NULL DEFAULT '',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8mb4 COMMENT='甲方联系人';
    
    /*Table structure for table `customer_relation_account` */
    
    CREATE TABLE `customer_relation_account` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `customer_id` int(11) NOT NULL DEFAULT '0' COMMENT '甲方客户id',
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '添加者id',
      `relation_uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '相关账号id',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=20 DEFAULT CHARSET=utf8mb4 COMMENT='乙方相关人员';
    
    /*Table structure for table `external_account` */
    
    CREATE TABLE `external_account` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID(外部渠道账号ID)',
      `mobile` varchar(32) NOT NULL DEFAULT '' COMMENT '手机号(登录账号)',
      `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '状态 0:禁用 1:启用',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_mobile` (`mobile`)
    ) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `external_channel` */
    
    CREATE TABLE `external_channel` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `name` varchar(64) NOT NULL DEFAULT '' COMMENT '外部渠道名称',
      `uniqid` int(10) NOT NULL DEFAULT '0' COMMENT '账号与公司绑定的唯一ID',
      `company_id` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `cooperation_state` tinyint(1) NOT NULL DEFAULT '0' COMMENT '合作状态 0:未合作 1:合作中 2:合作终止',
      `remark` varchar(512) NOT NULL DEFAULT '' COMMENT '备注',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_name_company_id` (`name`,`company_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `external_channel_account` */
    
    CREATE TABLE `external_channel_account` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `channel_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '渠道ID',
      `account_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '外部账号ID',
      `company_id` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `name` varchar(16) NOT NULL DEFAULT '' COMMENT '姓名',
      `email` varchar(128) NOT NULL DEFAULT '' COMMENT '邮箱',
      `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0:否 1:是',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uniq_company_id_account_id` (`company_id`,`account_id`),
      KEY `idx_channel_id_company_id` (`channel_id`,`company_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `file_records` */
    
    CREATE TABLE `file_records` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `name` varchar(20) NOT NULL DEFAULT '20' COMMENT '姓名',
      `card` varchar(20) NOT NULL DEFAULT '' COMMENT '身份证号',
      `phone` varchar(20) NOT NULL DEFAULT '' COMMENT '手机号码',
      `gender` tinyint(1) NOT NULL DEFAULT '0' COMMENT '性别 0未知 1男 2女',
      `birth` varchar(60) NOT NULL DEFAULT '' COMMENT '出生年月',
      `address` varchar(60) NOT NULL DEFAULT '' COMMENT '居住地',
      `native_place` varchar(60) NOT NULL DEFAULT '' COMMENT '籍贯',
      `expect_city_names` varchar(60) NOT NULL DEFAULT '' COMMENT '期望地点',
      `degree` tinyint(1) NOT NULL DEFAULT '1' COMMENT '学历',
      `position_name` varchar(100) NOT NULL DEFAULT '' COMMENT '职位',
      `corporation_name` varchar(100) NOT NULL DEFAULT '' COMMENT '公司',
      `expect_position_name` varchar(100) NOT NULL DEFAULT '' COMMENT '期望职位',
      `reason` varchar(100) NOT NULL DEFAULT '' COMMENT '失败原因',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '账号ID',
      `o_id` int(10) NOT NULL DEFAULT '0' COMMENT '组织ID',
      `resume_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '简历ID',
      `file_id` int(10) NOT NULL DEFAULT '0' COMMENT '上传的文件ID',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_file` (`file_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='excel导入失败表';
    
    /*Table structure for table `jobs` */
    
    CREATE TABLE `jobs` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
      `queue` varchar(255) NOT NULL,
      `payload` longtext NOT NULL,
      `attempts` tinyint(3) unsigned NOT NULL,
      `reserved_at` int(10) unsigned DEFAULT NULL,
      `available_at` int(10) unsigned NOT NULL,
      `created_at` int(10) unsigned NOT NULL,
      PRIMARY KEY (`id`),
      KEY `jobs_queue_index` (`queue`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
    
    /*Table structure for table `migrations` */
    
    CREATE TABLE `migrations` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `migration` varchar(255) NOT NULL,
      `batch` int(11) NOT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4;
    
    /*Table structure for table `positions` */
    
    CREATE TABLE `positions` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '自增ID',
      `name` varchar(255) NOT NULL DEFAULT '' COMMENT '职位名称',
      `parent_id` int(10) NOT NULL DEFAULT '0' COMMENT '父职位ID',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0、否 1、是',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8 COMMENT='职位配置表';
    
    /*Table structure for table `project` */
    
    CREATE TABLE `project` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '自增ID',
      `name` varchar(255) NOT NULL DEFAULT '' COMMENT '项目名称',
      `expired` int(10) NOT NULL DEFAULT '0' COMMENT '项目周期 0：长期， 非0：时间戳',
      `address` varchar(1000) NOT NULL DEFAULT '' COMMENT '工作地址',
      `customer_id` int(10) NOT NULL DEFAULT '0' COMMENT '甲方客户ID',
      `status` tinyint(4) NOT NULL DEFAULT '1' COMMENT '项目状态 1、正常 2、暂停 3、完成 4、关闭',
      `other_info` varchar(1000) NOT NULL DEFAULT '' COMMENT '其他信息',
      `company_id` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `uniqid` int(10) NOT NULL DEFAULT '0' COMMENT '创建人',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 COMMENT='项目表';
    
    /*Table structure for table `project_allot` */
    
    CREATE TABLE `project_allot` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '自增ID',
      `project_id` int(10) NOT NULL DEFAULT '0' COMMENT '项目ID',
      `uniqid` int(10) NOT NULL COMMENT '内部渠道ID',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0、否',
      `channel` tinyint(1) NOT NULL DEFAULT '0' COMMENT '渠道 1、内部 2、外部',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 COMMENT='项目归属';
    
    /*Table structure for table `project_position` */
    
    CREATE TABLE `project_position` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '自增ID',
      `position_id` int(10) NOT NULL DEFAULT '0' COMMENT '岗位ID',
      `department` varchar(255) NOT NULL DEFAULT '' COMMENT '部门',
      `number` int(10) NOT NULL DEFAULT '0' COMMENT '招聘人数 0：不限 非0：具体人数',
      `salary` varchar(300) NOT NULL DEFAULT '' COMMENT '薪资',
      `benifits` varchar(300) NOT NULL DEFAULT '' COMMENT '福利待遇',
      `work_time` varchar(300) NOT NULL DEFAULT '' COMMENT '工作时间',
      `skill_requirement` varchar(300) NOT NULL DEFAULT '' COMMENT '技能要求',
      `degree` int(10) NOT NULL DEFAULT '0' COMMENT '学历要求',
      `age_range` varchar(255) NOT NULL DEFAULT '-' COMMENT '年龄要求',
      `project_id` int(11) NOT NULL DEFAULT '0' COMMENT '项目ID',
      `status` tinyint(4) NOT NULL DEFAULT '1' COMMENT '状态 1、进行中 2、完成',
      `uniqid` int(10) NOT NULL DEFAULT '0' COMMENT '创建人',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `created_at` datetime NOT NULL COMMENT '创建时间',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8 COMMENT='项目岗位表';
    
    /*Table structure for table `remarks` */
    
    CREATE TABLE `remarks` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `content` varchar(255) NOT NULL DEFAULT '' COMMENT '备注内容',
      `resume_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '简历ID',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `o_id` int(10) NOT NULL DEFAULT '0' COMMENT '组织ID',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `edit_id` int(10) NOT NULL DEFAULT '0' COMMENT '更新这ID',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否1是',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_resumeId` (`resume_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历备注表';
    
    /*Table structure for table `resume_operates` */
    
    CREATE TABLE `resume_operates` (
      `id` int(10) NOT NULL AUTO_INCREMENT,
      `resume_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '简历ID',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '操作者',
      `type` tinyint(1) NOT NULL DEFAULT '1' COMMENT '类型',
      `content` text NOT NULL COMMENT '内容',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_resumeId` (`resume_id`,`updated_at`)
    ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='简历操作记录';
    
    /*Table structure for table `resume_remarks` */
    
    CREATE TABLE `resume_remarks` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `resume_id` int(20) NOT NULL DEFAULT '0' COMMENT '简历ID',
      `remark` varchar(500) NOT NULL DEFAULT '' COMMENT '备注',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_resumeId_updated_at` (`resume_id`,`updated_at`)
    ) ENGINE=InnoDB AUTO_INCREMENT=26 DEFAULT CHARSET=utf8mb4 COMMENT='简历备注表';
    
    /*Table structure for table `service` */
    
    CREATE TABLE `service` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `name` varchar(32) NOT NULL DEFAULT '' COMMENT '服务名称',
      `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除 0:否 1:是',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`)
    ) ENGINE=MyISAM AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
    
    /*Table structure for table `tags` */
    
    CREATE TABLE `tags` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `name` varchar(100) NOT NULL DEFAULT '' COMMENT '标签名称',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否1是',
      `cnt` int(10) NOT NULL DEFAULT '0' COMMENT '使用次数',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `idx_tid_name` (`tid`,`name`)
    ) ENGINE=InnoDB AUTO_INCREMENT=30 DEFAULT CHARSET=utf8mb4 COMMENT='公司标签表';
    
    /*Table structure for table `talent_recommend` */
    
    CREATE TABLE `talent_recommend` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '关联推荐人账号唯一ID',
      `channel_type` tinyint(4) NOT NULL DEFAULT '0' COMMENT '渠道来源 1=>外部渠道 2=>内部渠道',
      `company_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联公司ID',
      `project_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联项目ID',
      `project_position_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联项目岗位ID',
      `resume_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '关联简历ID',
      `name` varchar(20) NOT NULL DEFAULT '' COMMENT '姓名',
      `gender` tinyint(4) NOT NULL DEFAULT '0' COMMENT '性别 0=>未知 1=>男 2=>女',
      `age` tinyint(4) unsigned NOT NULL DEFAULT '0' COMMENT '年龄',
      `card` varchar(20) NOT NULL DEFAULT '' COMMENT '身份证号',
      `phone` varchar(20) NOT NULL DEFAULT '' COMMENT '手机号码',
      `status` tinyint(4) NOT NULL DEFAULT '1' COMMENT '推荐状态 1=>推荐中',
      `insure_status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '保险状态 0=>未知 1=>有保险 2=>无保险',
      `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=>否 1=>是',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_resume_id` (`resume_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COMMENT='人才推荐表';
    
    /*Table structure for table `talent_recommend_comment` */
    
    CREATE TABLE `talent_recommend_comment` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '关联账号唯一ID',
      `company_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联公司ID',
      `talent_recommend_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联人才推荐表ID',
      `type` tinyint(4) NOT NULL DEFAULT '1' COMMENT '备注类型 1=>人才备注 2=>状态变更',
      `status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '备注时状态 1=>推荐中',
      `comment` varchar(255) NOT NULL DEFAULT '' COMMENT '备注信息',
      `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=>否 1=>是',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `idx_talent_recommend_id` (`talent_recommend_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=21 DEFAULT CHARSET=utf8mb4 COMMENT='人才推荐备注表';
    
    /*Table structure for table `talent_recommend_entry` */
    
    CREATE TABLE `talent_recommend_entry` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `uniqid` int(11) NOT NULL DEFAULT '0' COMMENT '关联操作人账号唯一ID',
      `company_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联公司ID',
      `talent_recommend_id` int(11) NOT NULL DEFAULT '0' COMMENT '关联人才推荐表ID',
      `card` varchar(20) NOT NULL DEFAULT '' COMMENT '身份证号',
      `open_bank` varchar(255) NOT NULL DEFAULT '' COMMENT '开户银行',
      `bank_number` varchar(25) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` int(11) NOT NULL DEFAULT '0' COMMENT '入职时间',
      `project_position_id` int(11) NOT NULL DEFAULT '0' COMMENT '入职岗位 关联项目岗位ID',
      `department` varchar(255) NOT NULL DEFAULT '' COMMENT '入职部门',
      `salary_type` tinyint(4) NOT NULL DEFAULT '1' COMMENT '薪资类型 1=>时薪 2=>日薪 3=>月薪',
      `salary` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '薪资金额',
      `subsidy` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '补贴金额',
      `stay` varchar(255) NOT NULL DEFAULT '' COMMENT '住宿信息',
      `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=>否 1=>是',
      `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
      `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `unique_talent_recommend_id` (`talent_recommend_id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COMMENT='人才推荐入职信息表';
    
    /*Table structure for table `talent_table_titles` */
    
    CREATE TABLE `talent_table_titles` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '账号ID',
      `type` tinyint(1) NOT NULL DEFAULT '1' COMMENT '类型：1人才公海 2潜在员工 3派遣员工',
      `content` varchar(2000) COLLATE utf8mb4_german2_ci NOT NULL DEFAULT '' COMMENT '表头项',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `idx_userId_type` (`user_id`,`type`)
    ) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_german2_ci COMMENT='人才库表头设置表';
    
    /*Table structure for table `upload_files` */
    
    CREATE TABLE `upload_files` (
      `id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'ID',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '公司ID',
      `user_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '唯一账号ID uniq_id',
      `md5` varchar(64) NOT NULL DEFAULT '' COMMENT '文件md5',
      `file_path` varchar(255) NOT NULL DEFAULT '' COMMENT '存储文件路径',
      `group_name` varchar(255) NOT NULL DEFAULT '' COMMENT '存储文件组',
      `status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '状态：0已上传 1解析中 2已入库',
      `name` varchar(500) NOT NULL DEFAULT '' COMMENT '文件名',
      `upload_res` varchar(400) NOT NULL DEFAULT '' COMMENT '上传结果',
      `fail_data` mediumblob NOT NULL COMMENT '失败数据,如果dfs存储异常',
      `channel_id` int(10) NOT NULL DEFAULT '0' COMMENT '渠道ID',
      `source` tinyint(4) NOT NULL DEFAULT '0' COMMENT '来源渠道 0无 1外部 2内部',
      `position_id` int(10) NOT NULL DEFAULT '0' COMMENT '项目下职位ID',
      `talent_type` tinyint(1) NOT NULL DEFAULT '1' COMMENT '1公海 2潜在员工 3派遣',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `idx_md5_companyId` (`md5`,`tid`),
      KEY `idx_userId` (`user_id`,`updated_at`)
    ) ENGINE=InnoDB AUTO_INCREMENT=17 DEFAULT CHARSET=utf8mb4 COMMENT='上传简历excel表';
    
    /*Table structure for table `wechat_app_bind` */
    
    CREATE TABLE `wechat_app_bind` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `unionid` varchar(128) NOT NULL DEFAULT '' COMMENT '微信UnionID',
      `openid` varchar(64) NOT NULL DEFAULT '' COMMENT '微信openid',
      `mobile` varchar(32) NOT NULL DEFAULT '' COMMENT '绑定的账号',
      `type` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '账号类型 0:内部渠道账号 1:外部渠道账号',
      `created_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '添加时间',
      `updated_at` datetime NOT NULL DEFAULT '0001-01-01 00:00:00' COMMENT '更新时间',
      PRIMARY KEY (`id`),
      KEY `uniq_openid_mobile_type` (`openid`,`mobile`,`type`),
      KEY `idx_unionid` (`unionid`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    
    /*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
    /*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
    /*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
    
    
    
    
    
    
    /*
    SQLyog Trial
    MySQL - 5.7.11-log : Database - sly_caller
    *********************************************************************
    */
    
    /*!40101 SET NAMES utf8 */;
    
    /*!40101 SET SQL_MODE=''*/;
    
    /*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
    /*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
    /*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
    CREATE DATABASE /*!32312 IF NOT EXISTS*/`sly_caller` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
    
    USE `sly_caller`;
    
    /*Table structure for table `caller_setting` */
    
    CREATE TABLE `caller_setting` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `uniqid` int(10) unsigned NOT NULL COMMENT '用户id',
      `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司id',
      `talking_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '话术id',
      `status` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '1=开启 2=关闭',
      `recall_num` tinyint(2) NOT NULL DEFAULT '0' COMMENT '重呼次数',
      `recall_interval` int(10) NOT NULL DEFAULT '0' COMMENT '重呼间隔 单位秒',
      `call_limit` int(10) NOT NULL DEFAULT '0' COMMENT '单日同一号码呼叫限制次数',
      `day_call_limit` int(10) NOT NULL DEFAULT '0' COMMENT '单日外呼号码任务量限制',
      `begin_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始时间',
      `end_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束时间',
      `number_city_code` varchar(20) NOT NULL DEFAULT '' COMMENT '主叫号码归属地',
      `number_city_limit` tinyint(1) NOT NULL DEFAULT '0' COMMENT '只允许当前归属地号码呼叫',
      `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NULL DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `uidx_companyid` (`company_id`),
      KEY `idx_updated` (`updated_at`)
    ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='外呼设置表';
    
    /*Table structure for table `task_list` */
    
    CREATE TABLE `task_list` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `uniqid` int(10) unsigned NOT NULL COMMENT '用户id',
      `company_id` int(10) unsigned NOT NULL COMMENT '公司id',
      `tid` int(10) unsigned NOT NULL COMMENT '租户id',
      `task_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '任务id',
      `resume_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '简历id',
      `name` varchar(20) NOT NULL DEFAULT '' COMMENT '候选人姓名',
      `phone` varchar(11) NOT NULL DEFAULT '' COMMENT '候选人号码',
      `status` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '状态 1=待呼叫 2=呼叫中 3=已呼叫 4=已过期',
      `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '是否删除',
      `call_begin_time` timestamp NOT NULL COMMENT '呼叫时间',
      `call_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '通话时长',
      `call_result` tinyint(2) NOT NULL DEFAULT '0' COMMENT '呼叫结果 1=成功 2=拒接 3=空号 4=关机 5=占线 6=停机 7=超时未接 10=失败',
      `intention_result` varchar(100) NOT NULL DEFAULT '' COMMENT '意向结果',
      `call_voice_url` varchar(100) NOT NULL DEFAULT '' COMMENT '音频地址',
      `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NULL DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `idx_taskid_phone` (`task_id`,`phone`),
      KEY `idx_taskid_status` (`task_id`,`status`),
      KEY `idx_resumeid_status` (`resume_id`,`status`),
      KEY `idx_companyid_status_callresult` (`company_id`,`status`,`call_result`)
    ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务号码表';
    
    /*Table structure for table `tasks` */
    
    CREATE TABLE `tasks` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `uniqid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '用户id',
      `company_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '公司id',
      `tid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '租户id',
      `group_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '外呼业务id',
      `task_name` varchar(100) NOT NULL DEFAULT '' COMMENT '任务名称',
      `talking_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '话术id',
      `recall_num` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '重呼次数',
      `recall_interval` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '重呼间隔 单位秒',
      `call_limit` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单日同一号码呼叫限制次数',
      `number_city_code` varchar(20) NOT NULL DEFAULT '' COMMENT '主叫号码归属地',
      `number_city_limit` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '只允许当前归属地号码呼叫',
      `priority` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '优先级 [0,1,2],数字越大优先级越高',
      `status` tinyint(2) unsigned NOT NULL DEFAULT '0' COMMENT '状态 0=待添加 1=待呼叫 2=进行中 3=已暂停 4=已完成',
      `begin_date` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始日期',
      `end_date` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束日期',
      `begin_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务开始时间',
      `end_time` varchar(10) NOT NULL DEFAULT '' COMMENT '任务结束时间',
      `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
      `updated_at` timestamp NULL DEFAULT NULL,
      PRIMARY KEY (`id`),
      KEY `idx_status_uid` (`status`,`uniqid`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1000010 DEFAULT CHARSET=utf8mb4 COMMENT='外呼任务表';
    
    /*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
    /*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
    /*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
    
    
    
    
    /*
    SQLyog Trial
    MySQL - 5.7.11-log : Database - sly_talent
    *********************************************************************
    */
    
    /*!40101 SET NAMES utf8 */;
    
    /*!40101 SET SQL_MODE=''*/;
    
    /*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
    /*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
    /*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
    CREATE DATABASE /*!32312 IF NOT EXISTS*/`sly_talent` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
    
    USE `sly_talent`;
    
    /*Table structure for table `resumes_0` */
    
    CREATE TABLE `resumes_0` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_1` */
    
    CREATE TABLE `resumes_1` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_2` */
    
    CREATE TABLE `resumes_2` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_3` */
    
    CREATE TABLE `resumes_3` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_4` */
    
    CREATE TABLE `resumes_4` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_5` */
    
    CREATE TABLE `resumes_5` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_6` */
    
    CREATE TABLE `resumes_6` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*Table structure for table `resumes_7` */
    
    CREATE TABLE `resumes_7` (
      `id` bigint(20) NOT NULL COMMENT '简历ID',
      `has_insurance` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否买保险 0否1是',
      `salary` varchar(100) NOT NULL DEFAULT '' COMMENT '薪资组成',
      `is_leave` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否离职 0否 1是',
      `user_id` int(10) NOT NULL DEFAULT '0' COMMENT '人才归属 uniqId',
      `last_intention` varchar(100) NOT NULL DEFAULT '' COMMENT '最近外呼意向',
      `call_during` int(10) NOT NULL DEFAULT '0' COMMENT '通话时长 单位秒',
      `tid` int(10) NOT NULL DEFAULT '0' COMMENT '租户ID',
      `last_call_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最近外呼时间',
      `call_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼状态',
      `is_deleted` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否删除 0否 1是',
      `is_validate` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否有效0未知 1有效 2无效',
      `add_id` int(10) NOT NULL DEFAULT '0' COMMENT '创建者ID',
      `bank` varchar(50) NOT NULL DEFAULT '' COMMENT '开户银行',
      `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
      `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '更新时间',
      `bank_no` varchar(40) NOT NULL DEFAULT '' COMMENT '银行卡号',
      `entry_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '入职时间',
      `leave_time` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '离职时间',
      `is_latent` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否潜在',
      `is_wait_follow` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否待跟进 1是0否',
      `call_cnt` tinyint(1) NOT NULL DEFAULT '0' COMMENT '外呼次数',
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='简历宽表';
    
    /*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
    /*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
    /*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
    
    
    ```


