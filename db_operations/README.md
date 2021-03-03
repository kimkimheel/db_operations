## **README(中文版见后)**

This is a small tool that simplifies daily operations. Hope it can be of some help.

The main function is to query Mysql, SqlServer and Oracle. You can directly obtain the basic information of the tables in the database, such as data volume, field information, table comments, etc.

In addition, if you are performing data migration and importing data using text in hive, you often need to create a table first. This small program can help you quickly obtain table-building statement templates (intermediate tables, transaction tables).

The following are some simple examples of program applications. If you encounter problems during the application process, you are welcome to contact me to discuss and communicate with me.

#### Use example：

-  **Installation**

  ```python
  pip install db_query
  ```
---

- **Connect to the database**

  **Parameters:**

  |  parameter  | type |                         description                          |
  | :---------: | :--: | :----------------------------------------------------------: |
  |   db_type   | str  |                     the type of database                     |
  |     ip      | str  |                      the ip of database                      |
  |    port     | int  |                     the port of database                     |
  |    user     | str  |             User name to connect to the database             |
  |     key     | str  |               Password to connect to database                |
  | dborsv_name | str  | database name or a combination of service name and schema name |

  Take mysql as an example:

  ```python
  from db_query import db_query
  con =db_query('mysql','ip',3306,'username','keyword','db_name')
  
  ```

  Take oracle as an example(the dborsv_name is the combination of service name and schema name, the service name is 'ORCL', the schema name is 'TEST'):

  ```python
  con =db_query('oracle','ip',3306,'username','keyword','ORCL/TEST')
  ```

- **Check if the connection is successful**

  ```python
  con.con_ok()
  >>>Connected successfully. # (Connected successfully)
  >>> Failed to connect successfully.  # (Not connected successfully)
  
  ```

-  **Close the connection (NOTE! Close the database in time when it is used up, otherwise it may cause the database process to get stuck）**

  ```python
  con.con_close()
  >>>already closed!  # (connection has been closed)
  ```


---


- **Query all table names in the database**

  **Parameters:** None

  *return*: A list containing all table names

  ```python
  tab_list = con.ob_table_list()
  ```
  
-   **Query the number of records in the table**

  **Parameters:** 

  *tab_name*: str , The name of the table to be queried

  *return*: The amount of data in the table

  ```python
  rec_num = con.ob_recnum('tab_name')
  ```
  
-  **Query the field information of the table**

  **Parameters:** 

  *tab_name*: str , The name of the table to be queried

  *return*: A table containing field names, field types, and comment information

  ```python
  col_inf = con.ob_colinfo(‘tab_name’)
  ```

- **Query the comment information of the table**

   **Parameters:** 

    *tab_name*: str , The name of the table to be queried

    *return*: The comment information of the table

  ```python
  tab_com = con. ob_tabcom(‘tab_name’)
  ```
  
- **Query the data in the table**
**Parameters:** 

    *tab_name*: str , The name of the table to be queried

   *head_num*: int , the default value is zero. when head_num equals zero, the function will return the entire table. Otherwise, it returns the table with the specified number of rows.

   *return*: Data in dataframe format(**with corresponding column names**)

   ```python
   data = con.ob_data('tab_name',head_num=0)
   ```
---
- **Perform database-related operations**
  
    **Parameters:** 
    
    *sql_text*: str , SQL statements to be executed(such as add, delete, modify and check operations)
    
    *return*:None
    
    ```python
    con_exe(sql_text)
    ```

- **SQL statement query**

  **Parameters:** 

  *sql_text*: str , SQL statements to be executed(Operations that need to return results)

  *return*:The execution result of SQL statement
    ```python
    con_exe(sql_text)
    ```
  

---

-  **Generate hive table building statement based on field information**

  When data is migrated to hive, it is sometimes necessary to generate hive table building statements based on the query information of the original database.

  **Parameters:** 
  
     *tab_name*: str , The name of the table to be queried
  
     *table_type*: int , 0 means to build an intermediate table in hive,1 means to build a transaction table in hive
  
     *return*: SQL statement in string format
  
  **1. Table creation statement of the intermediate table**

  ```python
  sql_text = con.create_hivetab(‘tab_name’,0)
  ```
  
  Examples of intermediate table creation statement：
  
  ```sql
  CREATE TABLE IF NOT EXISTS `GRADE _M`(`st_id` INT comment '主键',
  `st_name` STRING comment '学生姓名 ',
  `telephone` STRING comment '联系方式',
  `class ` STRING comment '班级 ',
  `st_img` STRING comment '档案照片 ')ROW FORMAT DELIMITED FIELDS TERMINATED BY ","
  
  ```
  
   **2. The final table build statement**
  
  ```python
  sql_text = con.create_hivetab(‘tab_name’,1)
  ```
  
  Example of the final table creation statement：
  
  ```sql
  CREATE TABLE IF NOT EXISTS `GRADE _M`(`st_id` INT comment '主键',
  `st_name` STRING comment '学生姓名 ',
  `telephone` STRING comment '联系方式',
  `class ` STRING comment '班级 ',
  `st_img` STRING comment '档案照片 ') CLUSTERED BY (*column_name*) INTO *bucket_num* BUCKETS STORED AS ORC TBLPROPERTIES ("transactional"="true")
  --*column_name*：needs to be replaced with a field for bucketing
  --*bucket_num*：Need to be replaced with the number of buckets
  ```
  
#### Complete example
```python
con = db_query('mysql','2**.*4.3*.***',3306,'xingxing','123456','db_name')
out1 = con.ob_table_list()  # Get all the table names in the db_name library
out2 = con.ob_recnum('st_info')  # Get the amount of data in the st_info table
out3 = con.ob_colinfo('st_info')  # Get the field information of the st_info table
out4 = con.ob_tabcom('st_info')  # Get the comment of st_info table
out5 = con.create_hivetab('st_info',1)  # Get the hive table statement template of the st_info table
out6 = con.ob_data('st_info') # Get the entire data of the st_info table
con.con_close()  # Close the database connection after use
```



***


## **使用参考**

这是一个简化日常操作的小工具，希望可以帮助到大家。

主要功能是对mysql，SqlServer以及oracle进行查询，可以直接获得数据库中表的基本信息，如数据量，字段信息，表注释等。

此外，若进行数据迁移，在hive中使用文本方式导入数据，常常需要先进行建表操作，这个小程序可以帮助大家快速获得建表语句模板(中间表，事务表)。

以下是对程序应用的简单示例，在应用过程中若遇到问题，也欢迎大家联系我，一起讨论交流。

#### 使用示例：

- **安装**

  ```python
  pip install db_query
  ```

---

- **连接数据库**

  **参数:**

  |    参数     | 类型 |                描述                |
  | :---------: | :--: | :--------------------------------: |
  |   db_type   | str  |             数据库类型             |
  |     ip      | str  |               ip地址               |
  |    port     | int  |                端口                |
  |    user     | str  |               用户名               |
  |     key     | str  |                密码                |
  | dborsv_name | str  | 数据库名或者为服务名与模式名的结合 |

  以mysql为例:

  ```python
  from db_query import db_query
  con =db_query('mysql','ip',3306,'username','keyword','db_name')
  
  ```

  以oracle为例(dborsv_name参数是由服务名与模式名组合而成，其中服务名为'ORCL'，模式名为'TEST'):

  ```python
  con =db_query('oracle','ip',3306,'username','keyword','ORCL/TEST')
  ```

- **查看连接成功与否**

  ```python
  con.con_ok()
  >>>Connected successfully. # (连接成功)
  >>> Failed to connect successfully.  # (未连接成功)
  
  ```

- **关闭连接（注意，用完及时关闭数据库，否则可能造成数据库进程卡住）**

  ```python
  con.con_close()
  >>>already closed!  # (连接已关闭)
  ```


---


- **查询数据库内的所有表**

  **参数:** 无

  *return*: 数据库内所有表名的列表

  ```python
  tab_list = con.ob_table_list()
  ```

- **查询表内数据量**

  **参数:** 

  *tab_name*: 字符串 , 查询表的表名

  *return*: 查询表的记录数

  ```python
  rec_num = con.ob_recnum('tab_name')
  ```

- **查询表的字段信息**

  **参数:** 

  *tab_name*: 字符串 , 查询表的表名

  *return*: 表中所有字段的字段名，字段类型及注释信息

  ```python
  col_inf = con.ob_colinfo(‘tab_name’)
  ```

- **查询表的注释信息**

  **参数:** 

   *tab_name*: 字符串 , 查询表的表名

   *return*: 表的注释信息

  ```python
  tab_com = con. ob_tabcom(‘tab_name’)
  ```

- **查询表内数据**
  **参数:** 

   *tab_name*: 字符串 , 查询表的表名

   *head_num*:整数 , 默认值为零。当head_num为零时, 函数返回整张表的数据。否则，返回指定行数的数据。

   *返回*: Dataframe格式的数据(**带有对应的列名信息**)

   ```python
   data = con.ob_data('tab_name',head_num=0)
   ```

---

- **执行数据库相关操作**

  **参数:** 

  *sql_text*: 字符串 , 需要被执行的SQL语句(例如，增删改查等操作)

  *return*:无

  ```python
  con_exe(sql_text)
  ```

- **SQL语句查询**

  **参数:** 

  *sql_text*: 字符串 , 需要被执行的SQL语句(不需要返回结果的操作)

  *return*:SQL语句的执行结果

    ```python
    con_exe(sql_text)
    ```

---

- **根据字段信息生成hive建表语句**

  数据迁移至hive中时，有时需要根据原数据库的查询信息生成hive的建表语句。

  **参数:** 

     *tab_name*: 字符串 , 查询表的表名

     *table_type*: 整数型 , 0 意味着要在hive中建立中间表,1 意味着要在hive中建立事务表

     *return*: 字符串形式的SQL语句

  **1. 中间表的建表语句**

  ```python
  sql_text = con.create_hivetab(‘tab_name’,0)
  ```

  生成的中间表建表语句示例：

  ```sql
  CREATE TABLE IF NOT EXISTS `GRADE _M`(`st_id` INT comment '主键',
  `st_name` STRING comment '学生姓名 ',
  `telephone` STRING comment '联系方式',
  `class ` STRING comment '班级 ',
  `st_img` STRING comment '档案照片 ')ROW FORMAT DELIMITED FIELDS TERMINATED BY ","
  
  ```

   **2. 最终表的建表语句**

  ```python
  sql_text = con.create_hivetab(‘tab_name’,1)
  ```

  最终表建表语句示例：

  ```sql
  CREATE TABLE IF NOT EXISTS `GRADE _M`(`st_id` INT comment '主键',
  `st_name` STRING comment '学生姓名 ',
  `telephone` STRING comment '联系方式',
  `class ` STRING comment '班级 ',
  `st_img` STRING comment '档案照片 ') CLUSTERED BY (*column_name*) INTO *bucket_num* BUCKETS STORED AS ORC TBLPROPERTIES ("transactional"="true")
  --*column_name*：needs to be replaced with a field for bucketing
  --*bucket_num*：Need to be replaced with the number of buckets
  ```

#### 完整示例

```python
con = db_query('mysql','2**.*4.3*.***',3306,'xingxing','123456','db_name')
out1 = con.ob_table_list()  # Get all the table names in the db_name library
out2 = con.ob_recnum('st_info')  # Get the amount of data in the st_info table
out3 = con.ob_colinfo('st_info')  # Get the field information of the st_info table
out4 = con.ob_tabcom('st_info')  # Get the comment of st_info table
out5 = con.create_hivetab('st_info',1)  # Get the hive table statement template of the st_info table
out6 = con.ob_data('st_info') # Get the entire data of the st_info table
con.con_close()  # Close the database connection after use
```


