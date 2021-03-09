## **README**

This is a small tool for daily operations of PostgreSQL, mainly relying on psycopg2 module.

The main function is to operate PostgreSQL, which can directly obtain the data of tables in the database, execute SQL statements and insert data.

The following are some simple examples of program applications. If you encounter problems during the application process, you are welcome to contact me to discuss and communicate with me:happy:.

[TOC]

#### Use example：

##### - **Installation**

  ```python
  pip install db_operations
  ```

---

##### - **Connect to the database**

  **Parameters:**

|  parameter  | type |                         description                          |
| :---------: | :--: | :----------------------------------------------------------: |
| db_base | str  | database name |
|  user   | str  |   User name to connect to the database   |
|   key   | str  |    Password to connect to database    |
|   ip    | str  |   the ip of database   |
|  port   | int  |    the port of database    |

  examples:

  ```python
  from db_operations import con_pgsql
  con =con_pgsql('dbname','user','keyword','ip',3306)
  ```

##### - **Close the connection** 
(NOTE! Close the database in time when it is used up, otherwise it may cause the database process to get stuck）

  ```python
  con.con_close()
  >>>already closed!  # (connection has been closed)
  ```

---

##### - **Query the data in the table**
  **Parameters:** 

    *tab_name*: str , The name of the table to be queried

   *head_num*: int , the default value is zero. when head_num equals zero, the function will return the entire table. Otherwise, it returns the table with the specified number of rows.

   *return*: Data in dataframe format(**with corresponding column names**)

   ```python
   data = con.ob_data('tab_name',head_num=0)
   ```

---

##### - **Perform database-related operations**

  **Parameters:** 

  *sql_text*: str , SQL statements to be executed(such as add, delete, modify and check operations)

  *return*:None

  ```python
  con.con_exe(sql_text)
  ```

##### - **SQL statement query**

  **Parameters:** 

  *sql_text*: str , SQL statements to be executed(Operations that need to return results)

  *return*:The execution result of SQL statement

    ```python
   result = con.con_res(sql_text)
    ```

---

##### - **Insert data into table**

  Sometimes you need to store the data you need into the database and insert it into the corresponding table.

  **Parameters:** 

     *df*: dataframe,data in dataframe format(Note that the column name needs to be consistent with the database)
    
     *tab_name*: str , The name of the table to be queried

  ```python
dd = pd.DataFrame([{24, 'nancy'}])
  dd.rename(columns={0: 'id', 1: 'name'}, inplace=True)
con.insert_values(dd,'test_a') # test_a表的列名是id，name
  ```


#### Complete example

```python
con = con_pgsql("dbname","user", "key","ip",14**3) # create the connection
data = con.ob_data('test_a') # Get the data of the test_a table
con.con_exe(sql_text) # Execute SQL statements (update and other commands that do not need to be returned)
result = con.con_res(sql_text) # Execute the SQL statement that needs to return the result
dd = pd.DataFrame([{24, 'nancy'}])
dd.rename(columns={0: 'id', 1: 'name'}, inplace=True)
con.insert_values(dd,'test_a') # insert data
con.closecone() # Close the connection
```

***

## **使用参考**

这是一个日常操作PostgreSQL的小工具，主要依托于psycopg2模块。

主要功能是对PostgreSQL进行操作，可以直接获得数据库中表的数据，执行SQL语句，插入数据。

以下是对程序应用的简单示例，在应用过程中若遇到问题，也欢迎大家联系我，一起讨论交流:happy:。

#### 使用示例：

##### - **安装**

  ```python
  pip install db_operations
  ```

---

##### - **连接数据库**

  **参数:**

|  参数   | 类型 |    描述    |
| :-----: | :--: | :--------: |
| db_base | str  | 数据库类型 |
|  user   | str  |   用户名   |
|   key   | str  |    密码    |
|   ip    | str  |   ip地址   |
|  port   | int  |    端口    |


  示例：

  ```python
  from db_operations import con_pgsql
  con =con_pgsql('dbname','user','keyword','ip',3306)
  
  ```

##### - **关闭连接**

（注意，用完及时关闭数据库，否则可能造成数据库进程卡住）

  ```python
  con.con_close()
  >>>already closed!  # (连接已关闭)
  ```


---


##### - **查询表内数据**
  **参数:** 

   *tab_name*: 字符串 , 查询表的表名

   *head_num*:整数 , 默认值为零。当head_num为零时, 函数返回整张表的数据。否则，返回指定行数的数据。

   *返回*: Dataframe格式的数据(**带有对应的列名信息**)

   ```python
   data = con.ob_data('tab_name',head_num=0)
   ```

---

##### - **执行数据库相关操作**

  **参数:** 

  *sql_text*: 字符串 , 需要被执行的SQL语句(例如，增删改查等操作)

  *return*:无

  ```python
  con.con_exe(sql_text)
  ```

##### - **SQL语句查询**

  **参数:** 

  *sql_text*: 字符串 , 需要被执行的SQL语句(不需要返回结果的操作)

  *return*:SQL语句的执行结果

    ```python
    result = con.con_res(sql_text)
    ```

---

##### - **向表内插入数据**

  有时需要将所需数据存入数据库内，插入对应的表中。

  **参数:** 

     *df*：dataframe格式(注意，dataframe数据的列名需要与数据库内表的列名一直)
    
     *tab_name*: 字符串 , 查询表的表名

  ```python
dd = pd.DataFrame([{24, 'nancy'}])
  dd.rename(columns={0: 'id', 1: 'name'}, inplace=True)
con.insert_values(dd,'test_a') # test_a的列名是id，name
  ```


#### 完整示例

```python
con = con_pgsql("dbname","user", "key","ip",14**3) # 创建连接
data = con.ob_data('test_a') # 获取表内数据
con.con_exe(sql_text) # 执行SQL语句(update等不需要返回的命令)
result = con.con_res(sql_text) #执行需要返回结果的SQL语句
dd = pd.DataFrame([{24, 'nancy'}])
dd.rename(columns={0: 'id', 1: 'name'}, inplace=True)
con.insert_values(dd,'test_a') # 插入数据
con.closecone() # 关闭连接
```

