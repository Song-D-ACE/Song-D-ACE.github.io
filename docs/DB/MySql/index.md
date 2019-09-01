#### 数据库定义

数据库 `(Database) `是按照数据结构来组织、存储和管理数据的建立在计算机存储设备上的仓库。

#### 关系型数据库

` MySQL `  ` Oracle `  ` SqlServer ` ...


#### 概念

- 数据库特点
    1. 数据以表格的形式出现
    2. 表格中每一行表示一组数据
    3. 表格中每一列表示某数据对应的字段(属性)
    4. 若干这样的行和列就组成了一张表
    5. 若干的表格组成一个库


#### 运行

- node环境必备插件 MySQL2

`````javascript
(async funcion(){
	const mysql = require('mysql2');
    const connection = await mysql.createConnection({})
})()
`````

#### 执行命令 cmd

1. 链接
    ./mysql -uroot -p 或者 .\mysql.exe
2. 查看所有数据库
    show databases
3. 选择要操作的数据库
    use <database>
4. 创建新的数据库
    create database <database>
5. 都以; /g结尾的


####语句

- 查询语句
    ORDER BY ?? ASC DESC LIMIT 每页显示的条数 OFFSET 当前的页码
    
- 查询

`````sql
`SELECT ??,??,?? FROM ??`
`````    
- 增加

`````sql
'INSERT INTO ?? (??,??) VALUES ("' + variable + '")'
`````
			
- 修改

`````sql
    `UPDATE ?? SET ?? = ? WHERE ?? = ?`
`````

- 删除

`````sql
    `DELETE FROM ?? WHERE ?? = ?`
`````

- 模糊查询

`````sql
    `SELECT * FROM ?? WHERE ?? LIKE '% ${? }%'`
`````

`````sql
    `SELECT * FROM ?? WHERE ?? NOT LIKE '% ${? }%'`
`````

- 范围查询

`````sql
    `SELECT ?? FROM ?? WHERE ?? NETWEEN (? AND ?)`
`````

##### 函数

- 聚合函数(计算从列中取得的值, 返回一个单一的值)
    COUNT   COUNT()
	//返回匹配指定条件的行数
    SELECT COUNT (column_name) FROM table_name;
				SUM
				MAX
				MIN
- 标量函数(基于输入值, 返回一个单一的值)
    UCASE
    LCASE
    NOW

- 参考
    1. 存储引擎
        - innoDB
            占用空间相对较大
            读写效率差

        - MyISAM
            速度快
            占用空间小
            处理并发不行
            不支持事物完整性
			字符集编码

				utf8mb4
					四字节
					支持更多的Unicode字符
				数据校队
					基于标准的unicode来排序和比较, 能够在各种语言之间精确排序
				_bin, _cs
					区分大小写
				_ci
					不区分大小写
    2. 数据类型
        - 数字类型
            INTEGER
            INT
                4 字节
            SMALLINT
                2 字节
            TINYINT
                1 字节
                    256个数字
            MEDIUMINT
                3 字节
            BIGINT
                8 字节
            DECIMAL
            FLOAT
            DOUBLE
        - 日期时间类型
            DATE
            DATETIME
            TIMESTAMP
            TIM
            YEAR
        - 字符串类型
            CHAR
            VARCHAR
            BINARY
            VARBINARY
            BLOB
            TEXT
            ENUM
            SET
   3. 主键
        - 加快对数据的操作
			自动增长
				auto_increment
			索引
				这是 列 的概念, 列的值进行排序的一种结构,使用索引可以快速访问表中特定的信息
				加快对表中记录的查找或者排序