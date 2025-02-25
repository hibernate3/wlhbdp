# JAVA BACKEND FOR BDP PLATFORM

> 开源大数据平台Java后端
> 基本上Java类型的接口都走backend-java, 譬如任务构建

1、迁移logan埋点的表进入实时数仓

```markdown

use wlhbdp_mysql;
CREATE TABLE `logan_task` (
  `id` bigint(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `platform` tinyint(11) unsigned NOT NULL COMMENT '平台1android2iOS',
  `amount` varchar(64) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '文件大小',
  `app_id` varchar(32) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT 'app标识',
  `union_id` varchar(256) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '用户标识',
  `device_id` varchar(256) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '设备标识',
  `app_version` varchar(64) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT 'app版本',
  `build_version` varchar(256) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '构建版本',
  `log_file_name` varchar(512) CHARACTER SET utf8mb4 DEFAULT '' COMMENT '日志文件所在路径',
  `log_date` bigint(20) unsigned DEFAULT NULL COMMENT '日志所属日期',
  `add_time` bigint(20) unsigned NOT NULL COMMENT '业务字段，日志上报时间',
  `status` tinyint(11) unsigned NOT NULL DEFAULT '0' COMMENT '0未分析过,1分析过',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_update_time` (`update_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='上报日志任务表';

CREATE TABLE `logan_log_detail` (
  `id` bigint(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `task_id` bigint(11) unsigned NOT NULL COMMENT '所属任务id',
  `log_type` int(11) unsigned NOT NULL COMMENT '日志类型',
  `content` text COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '原始日志',
  `log_time` bigint(20) unsigned NOT NULL COMMENT '本条日志产生的具体时间戳',
  `add_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '添加时间',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_update_time` (`update_time`),
  KEY `idx_task_id` (`task_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='日志解析后的数据详情';

CREATE TABLE `web_task` (
  `id` bigint(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
  `device_id` varchar(128) NOT NULL DEFAULT '' COMMENT '设备id',
  `web_source` varchar(128) DEFAULT NULL COMMENT '来源',
  `environment` varchar(2048) DEFAULT NULL COMMENT '客户端自定义环境信息',
  `page_num` int(11) NOT NULL COMMENT '日志页码',
  `content` mediumtext NOT NULL COMMENT '日志内容',
  `add_time` bigint(20) NOT NULL COMMENT '添加时间',
  `log_date` bigint(20) NOT NULL COMMENT '日志所属日期',
  `status` int(11) NOT NULL COMMENT '日志状态0未解析，1已解析',
  `custom_report_info` varchar(2048) DEFAULT NULL COMMENT '自定义上报信息',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `log_date_deviceid` (`log_date`,`device_id`),
  KEY `add_time_deviceid` (`add_time`,`device_id`),
  KEY `idx_update_time` (`update_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='H5上报任务表';

CREATE TABLE `web_detail` (
  `id` bigint(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
  `task_id` bigint(20) NOT NULL COMMENT '所属任务id',
  `log_type` int(11) NOT NULL COMMENT '日志类型',
  `content` mediumtext NOT NULL COMMENT '日志内容',
  `log_time` bigint(20) NOT NULL COMMENT '日志所属时间',
  `log_level` int(11) DEFAULT NULL COMMENT '日志等级',
  `add_time` bigint(20) NOT NULL COMMENT '添加时间',
  `minute_offset` int(11) NOT NULL COMMENT '距离当天0点的分钟数',
  PRIMARY KEY (`id`),
  KEY `taskid_logtype` (`task_id`,`log_type`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='H5日志详情表';

```