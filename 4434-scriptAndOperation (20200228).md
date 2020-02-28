## 4434-scriptAndOperation

#### [**【平台】审计日志**](https://cil.5upm.com/story-view-4434.html)【韩兆青01】

  - 目标数据库
    
    - alinter
  - 备份操作
    
    - 备份`sys_menu`表，导出文件名为alinter.sys_menu.yyyymmdd.json
  - 变更操作
    
    - 执行如下脚本
    
    ```json
    INSERT INTO `alinter`.`sys_menu` ( `menu_id`, `menu_name`, `menu_uri`, `menu_desc`, `menu_status`, `menu_icon`, `menu_state`, `un_support_platform` ) VALUES ( 297, '审计日志', '#/trade/auditNum', '审计日志', 1, '', 'trade.auditNum', 'alipay,boccc,nttd' );
    
    INSERT INTO `alinter`.`sys_menu` ( `menu_id`, `menu_name`, `menu_uri`, `menu_desc`, `menu_status`, `menu_icon`, `menu_state`, `un_support_platform` ) VALUES ( 298, '审计日志查询', '/public/masterLog/find', '审计日志', 3, '', 'trade.auditNum', 'alipay,boccc,nttd' );
    ```
    
  - 验证方法
  
    - 执行如下脚本，查看对应的菜单是否存在，如存在则说明执行成功
    
    ```json
    select * from sys_menu where menu_id = 297;
    select * from sys_menu where menu_id = 298;
    ```
    
  - 回退操作(仅限回退时执行)
  
    ```json
    delete from sys_menu where menu_id = 297;
    delete from sys_menu where menu_id = 298;
    ```



####  [**【平台】审计日志**](https://cil.5upm.com/story-view-4434.html)【韩兆青02】

- 目标数据库

  - `alinter`

- 备份操作
  
  - 备份`sys_role_menu`表，导出文件名为alinter.sys_role_menu.yyyymmdd.json
- 变更操作
  - 执行如下脚本**（执行如下脚本需要确保01脚本执行成功）**

    ```json
    INSERT INTO sys_role_menu(role_id,menu_id)
    SELECT role_id ,"297" menu FROM `sys_role` where role_type = 1;
    
    INSERT INTO sys_role_menu(role_id,menu_id)
    SELECT role_id ,"298" menu FROM `sys_role` where role_type = 1;
    ```
  
- 验证方法

  - 执行如下脚本，查看是否有数据

    ```json
    select * from sys_role_menu where menu_id = 297;
    select * from sys_role_menu where menu_id = 298;
    ```
 - 回退操作(仅限回退时执行)
    ```json
    delete from sys_role_menu where menu_id = 297;
    delete from sys_role_menu where menu_id = 298;
    ```




