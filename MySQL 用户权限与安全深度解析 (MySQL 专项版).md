好的，您指出的非常对！非常抱歉，我之前的示例确实大量使用了 PostgreSQL 的语法，忽略了您对 MySQL 的特定需求。这对于希望学习和应用在 MySQL 环境下的您来说，确实不够实用。

我现在将以 **MySQL** 为核心，重新梳理和提供代码示例，并重点解释 MySQL 在用户权限和安全方面的具体实现、语法和最佳实践。我们会覆盖从用户创建、权限授予、角色管理（在支持的版本中）到安全配置的各个方面。

------

### **MySQL 用户权限与安全深度解析 (MySQL 专项版)**

#### **一、 核心安全原则 (同样适用于 MySQL)**

- **认证 (Authentication)**: 确认身份。
- **授权 (Authorization)**: 分配权限。
- **审计 (Auditing)**: 记录行为。
- **最小权限原则**: 核心指导思想。

#### **二、 MySQL 用户管理**

1. **创建用户 (`CREATE USER`)**

   - MySQL 用户总是包含 **用户名** 和 **来源主机** (`'username'@'hostname'`)。主机部分可以使用 `%` 作为通配符，表示任意主机（有安全风险），`localhost` 表示仅限本机连接。
   - 身份认证方式：
     - **密码认证 (常用)**: 使用 `IDENTIFIED BY` 指定密码。MySQL 8.0+ 默认使用 `caching_sha2_password` 插件，旧版本可能是 `mysql_native_password`。
     - **其他认证插件**: 如 `auth_socket` (Unix Socket 连接)、LDAP 插件等。

   SQL

   ```sql
   -- 创建一个只能从本地连接的用户 'app_user'
   CREATE USER 'app_user'@'localhost' IDENTIFIED BY '一个健壮的密码!App1';
   
   -- 创建一个可以从指定 IP (192.168.1.100) 连接的用户 'remote_user'
   CREATE USER 'remote_user'@'192.168.1.100' IDENTIFIED BY '另一个健壮密码!Rmt2';
   
   -- 创建一个可以从任意主机连接的用户 'any_host_user' (谨慎使用!)
   CREATE USER 'any_host_user'@'%' IDENTIFIED BY '非常危险的密码!Any3';
   
   -- MySQL 8.0+: 创建用户并指定使用旧密码插件 (兼容旧客户端)
   CREATE USER 'legacy_user'@'localhost' IDENTIFIED WITH mysql_native_password BY '兼容模式密码!Leg4';
   ```

2. **修改用户 (`ALTER USER`)**

   - 修改密码、密码过期策略、账户锁定、资源限制等。

   SQL

   ```sql
   -- 修改用户密码
   ALTER USER 'app_user'@'localhost' IDENTIFIED BY '新的更健壮的密码!NewP@ss5';
   
   -- 设置用户密码永不过期 (MySQL 5.7+)
   ALTER USER 'app_user'@'localhost' PASSWORD EXPIRE NEVER;
   
   -- 设置用户密码下次登录时必须修改 (MySQL 5.7+)
   ALTER USER 'app_user'@'localhost' PASSWORD EXPIRE;
   
   -- 设置密码按全局策略过期 (默认行为)
   ALTER USER 'app_user'@'localhost' PASSWORD EXPIRE DEFAULT;
   
   -- 设置密码有效期为 90 天 (MySQL 5.7+)
   ALTER USER 'app_user'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
   
   -- 锁定账户 (MySQL 5.7+)
   ALTER USER 'remote_user'@'192.168.1.100' ACCOUNT LOCK;
   
   -- 解锁账户 (MySQL 5.7+)
   ALTER USER 'remote_user'@'192.168.1.100' ACCOUNT UNLOCK;
   
   -- 修改用户资源限制 (示例)
   ALTER USER 'app_user'@'localhost' WITH
       MAX_QUERIES_PER_HOUR 1000       -- 每小时最大查询数
       MAX_UPDATES_PER_HOUR 500        -- 每小时最大更新数
       MAX_CONNECTIONS_PER_HOUR 100    -- 每小时最大连接数
       MAX_USER_CONNECTIONS 10;        -- 最大并发连接数
   ```

3. **删除用户 (`DROP USER`)**

   SQL

   ```sql
   DROP USER 'legacy_user'@'localhost';
   DROP USER 'any_host_user'@'%';
   ```

4. **重命名用户 (`RENAME USER`)**

   SQL

   ```sql
   RENAME USER 'app_user'@'localhost' TO 'webapp_user'@'localhost';
   ```

#### **三、 MySQL 权限管理 (`GRANT` / `REVOKE`)**

1. **权限级别**:

   - **全局权限 (Global Privileges)**: 应用于服务器上所有数据库。使用 `ON *.*` 授予。如 `PROCESS`, `RELOAD`, `REPLICATION SLAVE`, `CREATE USER`, `SUPER` (高风险)。
   - **数据库权限 (Database Privileges)**: 应用于指定数据库的所有对象。使用 `ON database_name.*` 授予。如 `CREATE`, `DROP`, `ALTER`, `INDEX`, `EVENT`, `TRIGGER`，以及在该数据库上的 `SELECT`, `INSERT`, `UPDATE`, `DELETE` 等。
   - **表权限 (Table Privileges)**: 应用于指定数据库中的特定表。使用 `ON database_name.table_name` 授予。主要是 `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `REFERENCES`, `ALTER`, `INDEX`, `CREATE VIEW`, `SHOW VIEW`, `TRIGGER`。
   - **列权限 (Column Privileges)**: 应用于表的特定列。主要用于 `SELECT`, `INSERT`, `UPDATE`。
   - **存储过程/函数权限 (Routine Privileges)**: `EXECUTE` (执行), `ALTER ROUTINE` (修改)。
   - **代理用户权限 (Proxy User Privilege)**: `PROXY`，允许一个用户模拟另一个用户。

2. **授予权限 (`GRANT`)**

   SQL

   ```sql
   -- 授予用户 'webapp_user'@'localhost' 对 'my_app_db' 数据库的所有权限 (常用但不推荐给普通应用)
   -- GRANT ALL PRIVILEGES ON my_app_db.* TO 'webapp_user'@'localhost';
   
   -- 推荐：授予应用用户对其数据库的基本 DML 和 SELECT 权限
   GRANT SELECT, INSERT, UPDATE, DELETE ON my_app_db.* TO 'webapp_user'@'localhost';
   
   -- 授予只读用户查询 'reporting_db' 中所有表的权限
   CREATE USER 'readonly_user'@'%' IDENTIFIED BY 'ReadOnlyP@ss!RO1';
   GRANT SELECT ON reporting_db.* TO 'readonly_user'@'%';
   
   -- 授予用户对特定表 'products' 的查询和更新权限
   GRANT SELECT, UPDATE ON my_app_db.products TO 'webapp_user'@'localhost';
   
   -- 授予用户对 'employees' 表的 'salary' 列的更新权限 (列级权限)
   GRANT UPDATE (salary) ON company_db.employees TO 'hr_admin'@'localhost';
   
   -- 授予用户执行特定存储过程的权限
   GRANT EXECUTE ON PROCEDURE my_app_db.calculate_bonus TO 'manager_user'@'localhost';
   
   -- 授予用户创建用户和角色的全局权限 (非常危险，通常仅限管理员)
   -- GRANT CREATE USER, ROLE_ADMIN ON *.* TO 'admin_user'@'localhost';
   
   -- 允许用户将自己拥有的权限授予他人 (谨慎使用!)
   GRANT SELECT ON reporting_db.* TO 'report_manager'@'%' WITH GRANT OPTION;
   ```

   - **`USAGE` 权限**: 比较特殊，`GRANT USAGE ON *.* TO ...` 意味着授予用户连接服务器的权限，但**没有任何其他权限**。通常在创建用户后隐式拥有，或用于创建无权限的账户。

3. **撤销权限 (`REVOKE`)**

   SQL

   ```
   -- 撤销 'webapp_user'@'localhost' 对 'my_app_db' 的 DELETE 权限
   REVOKE DELETE ON my_app_db.* FROM 'webapp_user'@'localhost';
   
   -- 撤销 'readonly_user'@'%' 对 'reporting_db' 的所有权限
   -- 注意：撤销 ALL PRIVILEGES 不会移除 USAGE 权限 (连接权限)
   REVOKE ALL PRIVILEGES ON reporting_db.* FROM 'readonly_user'@'%';
   
   -- 彻底撤销所有全局权限 (包括 GRANT OPTION)
   REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'admin_user'@'localhost';
   
   -- 撤销列级权限
   REVOKE UPDATE (salary) ON company_db.employees FROM 'hr_admin'@'localhost';
   ```

4. **查看权限 (`SHOW GRANTS`)**

   SQL

   ```
   -- 查看指定用户的权限
   SHOW GRANTS FOR 'webapp_user'@'localhost';
   
   -- 查看当前用户的权限
   SHOW GRANTS;
   -- 或者
   SHOW GRANTS FOR CURRENT_USER();
   ```

#### **四、 MySQL 角色管理 (MySQL 5.7 / 8.0+)**

- MySQL 在较新版本中引入了角色，用法与其他数据库类似，但需要注意**角色激活**。

1. **创建角色 (`CREATE ROLE`)**

   SQL

   ```
   CREATE ROLE 'app_read', 'app_write';
   ```

2. **给角色授权 (`GRANT ... TO ROLE`)**

   SQL

   ```
   GRANT SELECT ON my_app_db.* TO 'app_read';
   GRANT INSERT, UPDATE, DELETE ON my_app_db.* TO 'app_write';
   
   -- 角色也可以包含其他角色 (角色层级)
   CREATE ROLE 'app_developer';
   GRANT 'app_read', 'app_write' TO 'app_developer';
   ```

3. **将角色授予用户 (`GRANT ROLE ... TO USER`)**

   SQL

   ```
   GRANT 'app_developer' TO 'webapp_user'@'localhost';
   GRANT 'app_read' TO 'readonly_user'@'%';
   ```

4. **激活角色**

   - 用户登录后，默认情况下可能不会自动激活所有被授予的角色。

   - 设置默认角色

     : 用户登录时自动激活的角色。

     SQL

     ```
     -- 设置用户登录时自动激活 app_developer 角色
     SET DEFAULT ROLE 'app_developer' TO 'webapp_user'@'localhost';
     
     -- 设置用户登录时自动激活所有被授予的角色
     SET DEFAULT ROLE ALL TO 'webapp_user'@'localhost';
     
     -- 取消默认角色
     SET DEFAULT ROLE NONE TO 'webapp_user'@'localhost';
     ```

   - 手动激活角色

     : 在当前会话中临时激活角色。

     SQL

     ```
     -- 激活当前会话的 app_read 角色
     SET ROLE 'app_read';
     
     -- 激活当前会话的所有角色
     SET ROLE ALL;
     
     -- 激活特定角色组合
     SET ROLE 'app_read', 'app_write';
     
     -- 取消当前会话所有已激活的角色
     SET ROLE NONE;
     ```

   - 检查当前会话激活的角色: `SELECT CURRENT_ROLE();`

5. **撤销角色 (`REVOKE ROLE ... FROM`)**

   SQL

   ```
   REVOKE 'app_write' FROM 'app_developer'; -- 从角色撤销角色
   REVOKE 'app_developer' FROM 'webapp_user'@'localhost'; -- 从用户撤销角色
   ```

6. **删除角色 (`DROP ROLE`)**

   SQL

   ```
   DROP ROLE 'app_read', 'app_write', 'app_developer';
   ```

#### **五、 MySQL 安全审计**

- **MySQL Community Edition**:

  - 通用查询日志 (General Query Log)

    : 记录所有连接和执行的语句。对性能影响大，日志量巨大，不适合生产环境长期开启用于安全审计。

    SQL

    ```
    -- 启用通用日志 (会话级或全局级)
    SET GLOBAL general_log = 'ON';
    SET GLOBAL log_output = 'TABLE'; -- 或 'FILE'
    -- 查看日志 (如果输出到表)
    -- SELECT * FROM mysql.general_log;
    ```

  - **慢查询日志 (Slow Query Log)**: 主要用于性能分析，也可记录未使用索引的查询。

  - **二进制日志 (Binary Log)**: 主要用于复制和数据恢复，记录数据更改事件 (INSERT, UPDATE, DELETE)。可以通过 `mysqlbinlog` 工具查看，但非专门审计格式。

  - **使用触发器**: 自定义审计逻辑，但复杂且影响性能。

- **MySQL Enterprise Edition**:

  - **MySQL Enterprise Audit**: 提供强大的、基于策略的审计功能，可细粒度配置审计事件、过滤用户、控制日志格式（XML, JSON）和输出（文件、Syslog）。这是 MySQL 环境下最推荐的专业审计方案。配置通常在 `my.cnf`/`my.ini` 中完成。

- **其他方案**:

  - **Percona Audit Log Plugin / MariaDB Audit Plugin**: 流行的开源审计插件，提供类似 Enterprise Audit 的功能。

#### **六、 MySQL 其他安全机制与最佳实践**

1. **密码策略**:

   - `VALIDATE PASSWORD` 组件 (MySQL 5.6+)

     : 强制密码复杂度、长度、过期策略。

     SQL

     ```
     -- 安装 (如果未安装)
     INSTALL COMPONENT "file://component_validate_password";
     -- 设置策略级别 (0-LOW, 1-MEDIUM, 2-STRONG)
     SET GLOBAL validate_password.policy = 'MEDIUM';
     -- 查看和调整具体策略参数
     SHOW GLOBAL VARIABLES LIKE 'validate_password%';
     ```

   - 结合 `ALTER USER ... PASSWORD EXPIRE` 使用。

2. **网络安全**:

   - **防火墙**: 限制对 MySQL 端口 (默认 3306) 的访问。

   - SSL/TLS 加密连接

     :

     - 服务器端配置 (`my.cnf`/`my.ini`)

       :

       Ini, TOML

       ```
       [mysqld]
       ssl_ca = /path/to/ca.pem
       ssl_cert = /path/to/server-cert.pem
       ssl_key = /path/to/server-key.pem
       # require_secure_transport = ON  # MySQL 8.0+: 强制所有连接使用SSL/TLS
       ```

     - **客户端连接**: 使用 `--ssl-mode=REQUIRED/VERIFY_CA/VERIFY_IDENTITY` 等参数。

     - 用户级别强制 SSL

       :

       SQL

       ```
       -- 要求特定用户必须使用 SSL 连接
       ALTER USER 'secure_user'@'%' REQUIRE SSL;
       -- 要求使用 X509 证书认证
       -- ALTER USER 'cert_user'@'%' REQUIRE X509;
       ```

3. **SQL 注入防护**: **必须** 在应用程序层面使用参数化查询（Prepared Statements）或 ORM 提供的安全机制。这是防止 SQL 注入的最有效方法，与数据库类型无关。

4. **权限最小化与定期审查**: 严格遵循，定期使用 `SHOW GRANTS` 检查并回收不必要的权限。

5. **数据加密 (TDE)**: MySQL Enterprise Edition 或 Percona Server 等提供表空间级 TDE 功能。

6. **禁用或保护 `FILE` 权限**: 该权限允许用户读写服务器文件系统上的文件，风险极高，非特殊情况绝不能授予普通用户。

7. **安全配置**:

   - **`secure_file_priv`**: 限制 `LOAD DATA INFILE`, `SELECT ... INTO OUTFILE` 的目录。建议设置为特定安全目录或为空（禁止）。
   - **避免 `skip-grant-tables`**: 仅用于紧急密码恢复，用完后立即恢复并重启。
   - **及时更新补丁**。

8. **备份与恢复**: 定期进行物理备份（如 Percona XtraBackup）或逻辑备份 (`mysqldump`)，并测试恢复流程。

------

这次的讲解完全围绕 MySQL 展开，提供了更具体、更准确的语法示例和配置说明。希望能更好地满足您学习 MySQL 数据库权限与安全的需求。MySQL 的安全体系，特别是社区版和企业版在审计等高级功能上的差异需要注意。如果在实践中遇到具体问题，欢迎继续提问！