# MySQL 知识体系大纲 (面向入门与专业发展)

## 一、 MySQL 基础入门 (Foundation)

### 1. 数据库核心概念
*   什么是数据库 (Database)？
*   关系型数据库管理系统 (RDBMS) vs NoSQL
*   数据、表 (Table)、行 (Row)、列 (Column)、主键 (Primary Key)、外键 (Foreign Key)
*   数据库范式简介 (Normalization - 1NF, 2NF, 3NF 概念)
*   事务 (Transaction) 与 ACID 属性 (Atomicity, Consistency, Isolation, Durability) 概念

### 2. MySQL 产品认知
*   MySQL 历史与发展
*   MySQL 版本 (5.x, 8.x 等) 与主要差异
*   MySQL 发行版 (Community vs Enterprise)
*   MySQL 的优势与适用场景

### 3. 安装与环境配置
*   不同操作系统下的安装 (Windows, Linux - RPM/DEB/源码, macOS)
*   Docker 环境下的 MySQL 部署
*   基础配置 (`my.cnf` / `my.ini` 文件介绍)
*   启动、停止、重启 MySQL 服务
*   数据目录 (`datadir`) 的理解

### 4. 客户端工具使用
*   官方命令行客户端 (`mysql`)：连接、基本命令 (`use`, `show`, `describe`, `source`)
*   图形化界面 (GUI) 工具：
    *   MySQL Workbench (官方)
    *   Navicat, DBeaver, DataGrip 等第三方工具
*   了解不同工具的优缺点和适用场景

## 二、 SQL 语言精粹 (SQL Mastery)

### 1. SQL 基础与分类
*   SQL (Structured Query Language) 定义与标准
*   SQL 语言分类：
    *   DDL (Data Definition Language) - 数据定义
    *   DML (Data Manipulation Language) - 数据操作
    *   DCL (Data Control Language) - 数据控制
    *   TCL (Transaction Control Language) - 事务控制

### 2. DDL - 数据定义
*   `CREATE DATABASE`, `DROP DATABASE`, `ALTER DATABASE`
*   `CREATE TABLE` (列定义, 数据类型选择, 约束)
    *   常用数据类型：`INT`, `BIGINT`, `FLOAT`, `DOUBLE`, `DECIMAL`, `VARCHAR`, `CHAR`, `TEXT`, `DATE`, `TIME`, `DATETIME`, `TIMESTAMP`, `ENUM`, `SET`, `BOOL/BOOLEAN`, `JSON` (MySQL 5.7+), `BLOB`
    *   约束 (Constraints): `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `NOT NULL`, `DEFAULT`, `CHECK` (MySQL 8.0.16+)`, `AUTO_INCREMENT`
*   `ALTER TABLE` (添加/删除/修改列, 添加/删除约束, 重命名表)
*   `DROP TABLE`
*   `TRUNCATE TABLE` (与 `DELETE` 的区别)
*   索引 (`INDEX`)
    *   `CREATE INDEX`, `DROP INDEX`
    *   索引类型简介 (B-Tree, Hash, Full-text, Spatial) - 详见索引部分
*   视图 (`VIEW`)
    *   `CREATE VIEW`, `DROP VIEW`, `ALTER VIEW`
    *   视图的作用与限制

### 3. DML - 数据操作
*   `INSERT INTO` (单行插入, 多行插入, 插入查询结果)
*   `UPDATE` (更新单表, 更新多表 - JOIN UPDATE)
    *   `WHERE` 子句的重要性
*   `DELETE FROM` (删除单表, 删除多表 - JOIN DELETE)
    *   `WHERE` 子句的重要性
*   `SELECT` (核心查询语句)
    *   基本查询: `SELECT ... FROM ...`
    *   条件过滤: `WHERE` (比较运算符, `AND`, `OR`, `NOT`, `LIKE`, `IN`, `BETWEEN`, `IS NULL`)
    *   结果排序: `ORDER BY` (`ASC`, `DESC`)
    *   限制结果集: `LIMIT` (分页查询)
    *   聚合函数: `COUNT`, `SUM`, `AVG`, `MAX`, `MIN`
    *   分组查询: `GROUP BY`, `HAVING` (与 `WHERE` 的区别)
    *   连接查询 (`JOIN`):
        *   `INNER JOIN` (内连接)
        *   `LEFT JOIN` (左外连接)
        *   `RIGHT JOIN` (右外连接)
        *   `FULL OUTER JOIN` (MySQL 不直接支持, 可用 `LEFT JOIN UNION RIGHT JOIN` 模拟)
        *   `CROSS JOIN` (交叉连接/笛卡尔积)
        *   `NATURAL JOIN` (不推荐)
        *   `USING` 和 `ON` 子句
    *   子查询 (Subquery):
        *   标量子查询 (Scalar Subquery)
        *   列子查询 (Column Subquery)
        *   行子查询 (Row Subquery)
        *   表子查询 (Table Subquery - `IN`, `EXISTS`)
        *   关联子查询 (Correlated Subquery)
    *   集合操作: `UNION`, `UNION ALL` ( `INTERSECT`, `EXCEPT` 在 MySQL 中不常用或需模拟)
    *   常用函数：字符串函数, 数值函数, 日期时间函数, 条件函数 (`IF`, `CASE WHEN`)

### 4. DCL - 数据控制
*   用户管理:
    *   `CREATE USER`
    *   `ALTER USER` (修改密码, 主机, 锁定等)
    *   `DROP USER`
    *   `RENAME USER`
*   权限管理:
    *   `GRANT` (授予权限 - 全局/数据库/表/列/存储过程级别)
    *   `REVOKE` (撤销权限)
    *   权限类型详解 (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `ALL PRIVILEGES`, `GRANT OPTION` 等)
*   角色管理 (MySQL 8.0+):
    *   `CREATE ROLE`, `DROP ROLE`
    *   `GRANT` (权限 to 角色, 角色 to 用户)
    *   `REVOKE` (从角色撤销权限, 从用户撤销角色)
    *   `SET DEFAULT ROLE`, `SET ROLE`

### 5. TCL - 事务控制
*   `START TRANSACTION` / `BEGIN`
*   `COMMIT`
*   `ROLLBACK`
*   `SAVEPOINT`
*   `ROLLBACK TO SAVEPOINT`
*   `RELEASE SAVEPOINT`
*   `SET autocommit = 0/1`

### 6. 高级 SQL 与编程
*   窗口函数 (Window Functions - MySQL 8.0+): `OVER()`, `PARTITION BY`, `ORDER BY`, `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()` 等
*   公共表表达式 (Common Table Expressions - CTE - MySQL 8.0+): `WITH ... AS ...` (递归 CTE)
*   JSON 函数 (MySQL 5.7+): `JSON_EXTRACT`, `JSON_OBJECT`, `JSON_ARRAY`, `->`, `->>` 等
*   存储过程 (Stored Procedures): `CREATE PROCEDURE`, `CALL`, 输入/输出/输入输出参数, 流程控制语句 (`IF`, `CASE`, `LOOP`, `WHILE`, `REPEAT`), 游标 (Cursor)
*   存储函数 (Stored Functions): `CREATE FUNCTION`, 返回值
*   触发器 (Triggers): `CREATE TRIGGER` (`BEFORE`/`AFTER`, `INSERT`/`UPDATE`/`DELETE`)
*   事件调度器 (Event Scheduler): `CREATE EVENT`, `ALTER EVENT`, `DROP EVENT`

## 三、 MySQL 核心架构与原理 (Core Architecture & Principles)

### 1. MySQL 逻辑架构
*   连接层 (Connection Handling)
*   服务层 (SQL Interface, Parser, Optimizer, Caches & Buffers)
    *   查询解析 (Parser)
    *   查询优化 (Optimizer) - 查询计划生成, 成本估算
    *   查询缓存 (Query Cache - MySQL 8.0 已移除, 但理解其历史和移除原因重要)
*   存储引擎层 (Pluggable Storage Engines)
*   物理文件层 (Data files, Log files)

### 2. 存储引擎 (Storage Engines)
*   **InnoDB (核心)**:
    *   特性: ACID 兼容, 行级锁 (Row-Level Locking), MVCC (Multi-Version Concurrency Control), 外键约束, 崩溃恢复, 聚簇索引 (Clustered Index)
    *   关键组件: Buffer Pool, Change Buffer, Adaptive Hash Index, Log Buffer, Doublewrite Buffer
    *   文件结构: `.ibd` 文件 (独立表空间), `ibdataX` (共享表空间), Redo Log, Undo Log
*   MyISAM:
    *   特性: 表级锁 (Table-Level Locking), 不支持事务/外键, 读取性能高 (特定场景), 全文索引支持好 (早期)
    *   文件结构: `.frm` (表结构), `.MYD` (数据), `.MYI` (索引)
*   Memory (Heap):
    *   特性: 数据存储在内存, 速度快, 表级锁, 服务器重启数据丢失, 适用于临时表
*   其他引擎: Archive, CSV, Blackhole, Federated, NDB (MySQL Cluster) - 按需了解
*   如何选择存储引擎

### 3. 事务与并发控制
*   事务隔离级别 (Isolation Levels):
    *   Read Uncommitted (读未提交) - 脏读 (Dirty Read)
    *   Read Committed (读已提交) - 不可重复读 (Non-Repeatable Read)
    *   Repeatable Read (可重复读 - **InnoDB 默认**) - 幻读 (Phantom Read) - InnoDB 通过 MVCC 和 Next-Key Locks 解决部分幻读
    *   Serializable (串行化)
*   锁机制 (Locking):
    *   锁粒度: 表锁 (Table Locks), 页锁 (Page Locks - 较少见), 行锁 (Row Locks)
    *   锁类型: 共享锁 (Shared Lock / S Lock / 读锁), 排他锁 (Exclusive Lock / X Lock / 写锁)
    *   意向锁 (Intention Locks): IS, IX
    *   记录锁 (Record Locks), 间隙锁 (Gap Locks), 临键锁 (Next-Key Locks) - InnoDB 特有
    *   死锁 (Deadlock): 产生原因, 检测 (`SHOW ENGINE INNODB STATUS`), 避免策略
*   MVCC (Multi-Version Concurrency Control): 原理, Read View, Undo Log 的作用

### 4. 索引 (Indexing)
*   索引原理: 为什么能加速查询? (减少扫描行数)
*   索引数据结构:
    *   B+ Tree 索引 (主要): 结构, 查询过程, 适用场景 (范围查询, 排序)
    *   Hash 索引: 结构, 查询过程 (等值查询快), 限制
    *   Full-text 索引: 全文搜索
    *   Spatial (R-Tree) 索引: 地理空间数据
*   索引类型:
    *   聚簇索引 (Clustered Index - InnoDB): 定义, 特点 (一个表只有一个)
    *   非聚簇索引 / 二级索引 (Secondary Index - InnoDB/MyISAM): 定义, 回表查询
    *   唯一索引 (`UNIQUE INDEX`)
    *   主键索引 (`PRIMARY KEY` - 本质是唯一的聚簇/非聚簇索引)
    *   组合索引 / 联合索引 (Composite Index): 最左前缀原则
    *   覆盖索引 (Covering Index): 避免回表
    *   前缀索引 (Prefix Index)
    *   降序索引 (Descending Indexes - MySQL 8.0+)
    *   隐藏/不可见索引 (Invisible Indexes - MySQL 8.0+)
*   索引设计原则: 选择合适的列, 考虑查询模式, 避免过多/冗余索引, 利用覆盖索引, 注意索引列基数 (Cardinality)
*   索引维护: 碎片问题, 重建/优化索引 (`OPTIMIZE TABLE`, `ALTER TABLE ... ENGINE=INNODB`)

### 5. 日志系统 (Logging System)
*   错误日志 (Error Log): 记录启动、运行、停止过程中的严重错误和警告
*   通用查询日志 (General Query Log): 记录所有连接和语句 (性能开销大, 调试用)
*   慢查询日志 (Slow Query Log): 记录执行时间超过阈值的查询, 用于性能优化
*   二进制日志 (Binary Log / Binlog):
    *   作用: 数据复制 (Replication), 数据恢复 (Point-in-Time Recovery)
    *   格式: Statement-Based Replication (SBR), Row-Based Replication (RBR - 推荐), Mixed-Based Replication (MBR)
    *   事件类型, GTID (Global Transaction Identifiers - MySQL 5.6+)
*   中继日志 (Relay Log): 复制架构中从库使用
*   Redo Log (InnoDB): 保证事务持久性 (Write-Ahead Logging - WAL)
*   Undo Log (InnoDB): 实现 MVCC 和事务回滚
*   审计日志 (Audit Log): (通常需要插件或企业版功能)

### 6. 字符集与排序规则 (Character Sets & Collations)
*   字符集 (Character Set): 定义字符以及其编码 (`utf8`, `utf8mb4`, `latin1` 等) - **`utf8mb4` 推荐**
*   排序规则 (Collation): 定义字符比较和排序规则 (`_general_ci`, `_bin`, `_utf8mb4_unicode_ci` 等)
*   服务器、数据库、表、列级别的设置与优先级
*   乱码问题分析与解决

## 四、 MySQL 管理与运维 (Administration & Operations)

### 1. 配置管理
*   配置文件 (`my.cnf` / `my.ini`): 位置, 结构 ([mysqld], [client] 等)
*   常用参数详解:
    *   内存相关: `innodb_buffer_pool_size`, `key_buffer_size` (MyISAM), `sort_buffer_size`, `join_buffer_size`, `tmp_table_size`, `max_heap_table_size`
    *   I/O 相关: `innodb_io_capacity`, `innodb_flush_log_at_trx_commit`, `sync_binlog`
    *   连接相关: `max_connections`, `thread_cache_size`, `wait_timeout`
    *   日志相关: `log_error`, `slow_query_log`, `long_query_time`, `binlog_format`, `log_bin`
    *   InnoDB 专用参数: `innodb_file_per_table`, `innodb_log_file_size`, `innodb_flush_method`
*   动态修改参数 (`SET GLOBAL`/`SESSION`) vs 静态修改 (配置文件)

### 2. 备份与恢复 (Backup & Recovery) - **核心技能**
*   备份类型:
    *   逻辑备份 (Logical Backup): `mysqldump` (导出 SQL), `mysqlpump`
    *   物理备份 (Physical Backup): 直接复制数据文件 (冷备 vs 热备)
        *   热备工具: Percona XtraBackup (常用), MySQL Enterprise Backup (MEB)
    *   全量备份 (Full Backup), 增量备份 (Incremental Backup), 差异备份 (Differential Backup)
*   备份策略制定: 备份频率, 保留周期, RPO (Recovery Point Objective), RTO (Recovery Time Objective)
*   恢复操作:
    *   逻辑恢复: `mysql` 客户端导入 SQL 文件
    *   物理恢复: 使用 XtraBackup 或 MEB 进行恢复
    *   基于 Binlog 的时间点恢复 (Point-in-Time Recovery - PITR)
*   备份验证

### 3. 监控与告警 (Monitoring & Alerting)
*   核心监控指标:
    *   性能指标: QPS, TPS, Threads_connected, Threads_running, Innodb_buffer_pool_wait_free, Innodb_buffer_pool_read_requests, Innodb_buffer_pool_reads, Slow_queries
    *   资源指标: CPU 使用率, 内存使用率, 磁盘 I/O, 网络流量
    *   复制指标: Slave_IO_Running, Slave_SQL_Running, Seconds_Behind_Master, Relay_Log_Space
    *   InnoDB 指标: `SHOW ENGINE INNODB STATUS` 解读
*   监控工具:
    *   MySQL 内建: `SHOW STATUS`, `SHOW VARIABLES`, `performance_schema`, `sys schema` (MySQL 5.7.7+)
    *   命令行工具: `mysqladmin`, `mytop`, `innotop`
    *   开源监控系统: Prometheus + Grafana (mysqld_exporter), Zabbix, Nagios, Open-Falcon
    *   商业监控工具: MySQL Enterprise Monitor (MEM), SolarWinds DPA, Datadog 等
*   告警配置

### 4. 用户与安全管理 (User & Security Management) - **核心技能**
*   遵循最小权限原则
*   安全的用户创建与密码策略 (`validate_password` 插件)
*   精细化权限控制 (DCL 部分回顾)
*   角色应用 (MySQL 8.0+)
*   网络安全: 防火墙配置, `bind-address` 设置, 禁用 `skip-name-resolve`
*   传输加密: SSL/TLS 配置 (`REQUIRE SSL`)
*   静态数据加密 (TDE - Transparent Data Encryption) - (企业版或特定社区分支)
*   SQL 注入防护 (应用层为主, 但 DBA 需理解风险)
*   安全审计 (Audit Log)
*   定期安全检查与加固

### 5. 数据库升级与迁移
*   版本升级: 小版本升级 vs 大版本升级, 升级前准备, 升级步骤 (原地升级 vs 逻辑导出导入 vs 复制方式), 回滚计划
*   数据迁移: 逻辑迁移 (`mysqldump`), 物理迁移 (Xtrabackup), 复制方式迁移, 云服务迁移工具
*   Schema 变更:
    *   在线 DDL 工具: `pt-online-schema-change` (Percona Toolkit), `gh-ost` (GitHub)
    *   MySQL 8.0+ 在线 DDL 增强 (`ALGORITHM=INPLACE`, `LOCK=NONE`)

## 五、 MySQL 性能优化 (Performance Tuning)

### 1. 性能分析方法与工具
*   性能瓶颈定位: CPU, Memory, I/O, Network, Locks
*   `EXPLAIN` / `EXPLAIN ANALYZE` (MySQL 8.0.18+): 解读执行计划 (`type`, `key`, `rows`, `Extra` 等)
*   `SHOW PROFILE` / `SHOW PROFILES` (将被 `performance_schema` 替代)
*   `performance_schema`: 配置与使用, 关键表 (`events_statements_summary_by_digest` 等)
*   `sys schema`: 基于 `performance_schema` 的视图, 更易读
*   慢查询日志分析 (`mysqldumpslow`, `pt-query-digest`)
*   压力测试工具: `sysbench`, `mysqlslap`, JMeter

### 2. SQL 语句优化
*   避免 `SELECT *`
*   `WHERE` 子句优化: 索引列使用, 避免函数/运算, 类型匹配
*   `JOIN` 优化: 选择合适的 JOIN 类型, 驱动表选择, 索引优化
*   `ORDER BY` / `GROUP BY` 优化: 利用索引排序/分组, 避免 `filesort`
*   子查询优化: 改写为 `JOIN` 或使用 EXISTS
*   `LIMIT` 分页优化: 延迟关联或书签记录法
*   使用覆盖索引
*   批量操作 (`INSERT`, `DELETE`, `UPDATE`) 优化

### 3. 索引优化 (回顾与深化)
*   识别缺失索引, 删除冗余/未使用索引 (`pt-duplicate-key-checker`, `pt-index-usage`)
*   优化索引选择性
*   组合索引列顺序优化
*   索引维护与碎片整理

### 4. Schema 设计优化
*   选择合适的数据类型 (空间、精度)
*   范式化 (Normalization) vs 反范式化 (Denormalization) 的权衡
*   避免 NULL 值 (看场景)
*   垂直拆分 vs 水平拆分 (Partitioning / Sharding) 概念

### 5. 服务器配置优化 (回顾与深化)
*   基于硬件资源和负载调整核心参数 (Buffer Pool, 日志等)
*   操作系统层面优化 (文件句柄数, Swappiness, I/O 调度器)

### 6. 硬件与架构优化
*   CPU 选择 (高频 vs 多核)
*   内存大小与速度
*   存储选择 (SSD - NVMe > SATA SSD > HDD) 与 RAID 配置
*   网络带宽与延迟
*   读写分离架构

## 六、 高可用与可扩展性 (High Availability & Scalability)

### 1. 复制 (Replication) - **核心技能**
*   复制原理: Master (Binlog) -> Dump Thread -> Slave (I/O Thread -> Relay Log -> SQL Thread -> Data)
*   复制模式:
    *   异步复制 (Asynchronous)
    *   半同步复制 (Semi-Synchronous - MySQL 5.5+) - 增强型半同步 (MySQL 5.7+)
    *   延迟复制 (Delayed Replication)
*   复制拓扑: 一主一从, 一主多从, 主主复制 (慎用), 级联复制, 多源复制 (MySQL 5.7+)
*   Binlog 格式 (SBR, RBR, MBR) 与选择
*   GTID (Global Transaction Identifiers): 原理, 优势, 配置与管理
*   复制监控: 状态检查 (`SHOW SLAVE STATUS`), 延迟监控, 数据一致性校验 (`pt-table-checksum`)
*   复制故障处理: 错误码分析, 跳过错误 (`sql_slave_skip_counter`), 重建从库

### 2. MySQL 高可用方案
*   MHA (Master High Availability): 基于 Perl 的自动主从切换方案
*   Keepalived + VIP (Virtual IP): 虚拟 IP 漂移
*   ProxySQL / HAProxy: 结合 MHA 或其他脚本实现故障转移和读写分离
*   Orchestrator: 开源的 MySQL 复制拓扑管理和故障转移工具
*   MySQL InnoDB Cluster / Group Replication (MySQL 5.7.17+):
    *   组复制 (Group Replication): 基于 Paxos 的多主写入 (单主模式推荐), 数据强一致性
    *   MySQL Shell: 配置和管理 InnoDB Cluster
    *   MySQL Router: 应用透明的连接路由
*   MySQL NDB Cluster: Shared-Nothing 架构, 内存数据库, 高可用高吞吐 (特定场景)
*   云厂商 RDS/Cloud SQL 的高可用机制

### 3. 可扩展性方案
*   垂直扩展 (Scale Up): 提升单机硬件性能 (CPU, RAM, Disk) - 有上限
*   水平扩展 (Scale Out):
    *   读写分离 (Read/Write Splitting): 通过 Proxy 或应用层实现
    *   数据库中间件 (Proxy): ProxySQL, MyCAT, ShardingSphere 等 (实现读写分离, 分库分表)
    *   分库分表 (Sharding):
        *   垂直拆分 (按业务模块)
        *   水平拆分 (按 Range, Hash, 一致性 Hash)
        *   全局 ID 生成方案
        *   分布式事务处理 (2PC, TCC, Saga 等 - 应用层挑战)
    *   表分区 (Partitioning): MySQL 内建功能, 对应用透明, 改善管理和部分查询性能 (注意限制)

## 七、 MySQL 生态与发展 (Ecosystem & Trends)

### 1. 常用工具集
*   Percona Toolkit (`pt-*` 系列工具): 强大的运维、诊断、优化工具集
*   `sysbench`: 性能压力测试工具
*   备份工具: XtraBackup
*   高可用/管理工具: MHA, Orchestrator

### 2. MySQL 与其他技术的结合
*   缓存配合: Redis, Memcached
*   搜索引擎配合: Elasticsearch (通过 Logstash, Canal 等同步数据)
*   大数据平台集成: Hadoop, Spark (Sqoop, Flume, Canal)

### 3. MySQL on Cloud (云数据库)
*   AWS RDS for MySQL, Aurora MySQL
*   Google Cloud SQL for MySQL
*   Azure Database for MySQL
*   阿里云 RDS for MySQL, PolarDB MySQL
*   云数据库的优势 (易用性, 可靠性, 弹性) 与挑战 (成本, 厂商锁定)

### 4. MySQL 新特性与发展趋势
*   关注 MySQL 大版本更新 (如 8.x 的新特性)
*   云原生数据库发展
*   HTAP (Hybrid Transactional/Analytical Processing) 趋势
*   Serverless Database 概念

## 八、 软技能与职业素养 (Soft Skills & Professionalism)

*   问题排查与解决能力 (Troubleshooting Methodology)
*   文档阅读与编写能力
*   沟通与协作能力 (与开发, 测试, 运维团队)
*   持续学习与自我驱动
*   社区参与 (提问, 分享, 贡献)
*   责任心与细致性 (数据无小事)