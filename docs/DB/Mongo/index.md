### 数据库基础

#### 数据库定义

数据库 `(Database) `是按照数据结构来组织、存储和管理数据的建立在计算机存储设备上的仓库。

- 非关系型数据库


#### 账户权限

- 创建超级管理员账户 use admin

`````sql
db.createUser({
	user: 'luffy',
	pwd: '123456',
	roles: [{role: 'dbOwner',	db: 'demo'}]
})
dbOwner
`````

- 数据库配置文件 

    启用身份验证新版本 keys and values之间要加空格, 否则解析会报错

`````
security:
　　authorization: enabled
`````

- 非关系型数据库(noSQL)也属于横向扩展的数据库 不是SQL也不仅仅是SQL

- 关系型数据库(rdbs) mySQL SQLServer orical


#### 概念

- 部署数据库服务
	1. 搭建简单的单机服务
	2. 搭建具有冗余容错功能的复制集
	3. 搭建大规模数据集群
	4. 完成集群的自动部署

- 熟悉使用MongoDB的使用
	1. 最基本的文档的读写更新删除
	2. 各种不同类型的索引的创建与使用
	3. 复杂的聚合查询
	4. 对数据集合进行分片,在不同分片间维持数据均衡
	5. 数据备份与恢复
	6. 数据迁移

- 简单运维
	1. 部署MangoDB集群
	2. 处理多种常见的故障
        - 单节点失效,如何恢复工作
		- 数据库意外被杀死如何进行数据恢复
		- 数据库发生拒绝服务时如何排查原因
		- 数据库磁盘快满时如何处理
- 数据库概念
	1. 有组织地存放数据
	2. 按照不同的需求进行查询

- 数据库分类
	1. Sql数据库: 支持Sql语言的数据库
		Orical
		Mysql
	2. NoSql: 不支持Sql语言的数据库
		- redis
		- MongoBD
			1. 没有表结构的概念
			2. 每条记录可以有完全不同的结构
			3. 业务开发方便快捷
			4. sql数据库需要事先定义表结构再使用
			5. 完全的索引支持
				1. redis的key-value 单键索引
				2. hbase的单索引,二级索引需要自己实现
				3. mongoDB支持
					1. 单建索引,多建索引
						{"x":"1","y":"2"}
					2. 数组索引
						['apple', 'lemon']
					3. 全文索引
						'im ace'
					4. 地理位置索引	2D
					    最接近接口数据库的原声数据库
				4. 方便的冗余与拓展
					1. 复制集保证数据安全
					2. 分片扩展数据规模	mongos
                        - 主
							    从
							    从
                        - 主
							    从
							    从
					不用在应用层进行分发
					统一访问入口

- 命令
	1. mongod(主角)（用于开启服务器）
		DB的运行程序
		数据库的部署
	2. mongo（连接数据库服务器）
		因为它连接mongoDB数据库与服务器的客户端,我们之后对数据库的所有操作也需要先使用mongo客户端连接到mongoDB服务器之后才能进行
	3. mongoimport
		导出
	4. mongoexport
		导出
	5. mongodump 
        导入导出二进制数据,不能被直接读取,一般用来作数据的备份,与恢复
	6. mongostore
        导入导出二进制数据,不能被直接读取,一般用来作数据的备份,与恢复
	7. mongooplog 
        用来操作日志的回放 是mongoDB复制集中用来记录操作记录的数据集合
	8. mongostat
		用来查看mongoDB服务器的各种状态 导入导出二进制数据,不能被直接读取,一般用来作数据的备份,与恢复连接数据库服务器
    9. mongodb -help
        db--path arg
    10. show dbs（查看可用数据库）
    11. 开启服务器（挂起） 路径
        mongod --dbpath D:\Program Files\MongoDB\Server\4.0\data --logpath D:\Program Files\MongoDB\Server\4.0\log\mongod.log
    12. net start mongodb
	13. net stop mongodb
	14. sc delete mongodb 卸载服务		   
	15. db
		查看当前使用的数据库
	16. show dbs
		查看数据库
	17. show collections
		查看集合
	18. use dbname
		创建或切换数据库 存在就是切换,没有就是创建
	19. show tables
		查看数据表
	20. 数据操作
		- db.user.insert({"name": "ace", "age": "20"})
			插入数据,插入时自动创建集合
		- db.dropDatabase()
			删除数据库
		- db.user.find()
			查找
		- db.user.drop()
		- db.user.updata()
			修改集合名字
		- db.user.remove({条件})
		- db.user.find().pretty() 
			格式化

- bson		
	1. 是一种类似json二进制的储存格式 有Date 和 BinData类型
	2. 基于键值对,数据之间没有耦合性非常容易水平扩展
	3. 不需要通过SQL层的解析,所以高性能
	4. 以字典形式来存储
	5. 速度快
	6. 优势
		- 可以用SQL语句方便的在多个表之间做非常复杂的数据查询
		- 使得对于安全性很高的数据访问要求得以实现

#### 安装与配置

- 存储类型 二进制 json

- mongodb安装与配置

    1. 安装mongodb
    2. 创建管理员认证

`````
db.createUser({user: "admin", pwd: "admin", roles: ["root"]})
`````

`````
db.createUser({user: "ace", pwd: "answer0932.CC", roles: [{role: 'root',db: "UbSiteInfomation"}]})
`````

- 授权认证

    db.auth('用户名','密码')

- 给使用的数据库添加用户

- 配置mongodb

    1. 找到mongo和mongod命令 （mongo.cmd）
    2. 设置环境变量 （在任何地方都可以找到这两个命令）
    3. 开启服务器（挂起服务器）
    4. 服务的操作
	    net stop mongodb
	    net start mongodb
	    sc delete mongodb
    5. 测试服务器
	    mongo
	    show dbs

#### 数据库命令

- 创建数据库 use 数据库名
- 切换数据库 use数据库名
- 显示数据库列表 show dbs
- 显示当前数据库 db
- 删除数据库 db.dropDatabase()

#### 用户操作

- 删除
`````
db.dropUser('username') 
`````
`````
db.system.users.remove({user:"haha"}) 
`````

- 只读
`````
db.createUser({user:"root",pwd:"root",roles:[{role:"readWrite",db:"admin"}]})
`````

- 增加用户权限（切换到有权限操作的数据库下）
`````
db.grantRolesToUser('username',[{role:'',db:''}])
`````

- 移除用户权限（切换到有权限操作的数据库下）
`````
db.revokeRolesFromUser('username',[{role:'',db:''}])
`````

- 修改用户密码（切换到有权限操作的数据库下）
`````
db.updateUser('ace',{pwd:'456'})
`````

- 备份
`````
mongodump -u username -p password --authenticationDatabase 'admin' -d dbname -o dbpath
`````

- 恢复
`````
mongorestore -u username -p password --authenticationDatabase 'admin' -d 数据库名称 --dir 数据库备份文件路径
`````

- 导入
	1.  d:数据库名
    2.  c:collection名
    3.  -type:导入的格式默认json
    4.  f:导入的字段名
    5.  -headerline:如果导入的格式是csv，则可以使用第一行的标题作为导入的字段
    6.  -file:要导入的文件
    7.  导入json
        `````
        mongoimport -d class1712B -c books --file ~/桌面/dump/books --type json
        `````
        导入csv
        `````
        mongoimport -d class1712B -c info --file ~/桌面/dump/books --headerline --type c
        `````
- 导出
    1.  d ：数据库名
    2.  c ：collection名
    3.  o ：输出的文件名
    4.  -type ： 输出的格式，默认为json
    5.  f ：输出的字段，如果-type为csv，则需要加上-f "字段名"， 注：windows设置字段不加引号
    6.  导出json
        `````
        mongoexport -d class1804 -c books -o 备份数据库的路径/book.json --type json
        `````
    7.  导出csv
        `````
        mongoexport -d class1804 -c books -o 备份数据库的路径/books.csv --type csv -f 'by_user,likes'
        `````

- 集合命令
	1.  创建集合 db.createCollection(集合)
	2.  显示集合列表 show collections
	3.  删除集合	db.集合名.drop()

- 数据命令(文档命令)
	1.  增加文档 
            db.user.insert({"key": "value"})
	2.  修改文档
		    db.user.updata({条件: ""}, {目标: ""})
		    db.user.updata({条件: ""}, {$set: {键名: 新值}})
	3.  删除文档
		    db.user.remove({条件: ""})
	4.  查询文档
		    db.user.find({条件})
		    db.user.find().pretty() 格式化

- 开启数据库方法
	1. 创建游标
		link = db.cursor()
	2. 关闭数据库链接
		db.close()
			释放内存空间
	3. 选择数据库
		db.select_db('数据库名')
	4. 开启事物
		db.begin()
	5. 提交事物 跟下面的 二者选一
		db.commit()
			如果没有异常就执行这个
	6. 事物回滚
		db.rollback()

- 读取数据
	1. link.execute(sql show tables, 参数)
	2. link.executemany(sql, [参数])
	3. link.fetchone() 获取一条数据
	4. link.fetchmany(n) 获取多条数据

- 导入数据 
    `````
    mongoimport --host 127.0.0.1:27017 -u admin -p 123456 -d ace -c area areas.dat  --authenticationDatabase admin
    `````
- 连接数据库
	host
		主机
	user
		用户名
	password
		密码
	database
		数据库名
	charset
		字符集编码

- 数据类型
	1. Object ID
		- 文档ID: 
			每个文档都有一个属性.为_id,保证每个文档的唯一性,可以自己去设置_id插入文档,如果没有提供,mongoDB为每个文档提供了一个独特的_id,类型为objectID objectID是一个12字节的十六进制数前4个字节为当前时间戳 接下来的3个字节为机器的ID 接下来的2个字节MongoDB服务进程的id
	2. String 字符串,最常用,必须是有效的utf-8
	3. Boolean 储存一个布尔值
        - true
		- false
	4. Integer 整数可以是32位 64位, 取决于服务器
	5. Double 存储浮点值
	6. Arrays 数组或列表,多个值存储到一个键
	7. Object 用于嵌入式的文档,即一个值为一个文档
	8. Null	存储null值
	9. Timestamp 时间戳
	10. Date 存储当前日期或时间的UNIX时间格式

- 聚合函数 （	聚合管道）
	1. $project (增加 删除 重命名 筛选指定的列)

	`````javascript
	db['表名'].aggregate([
		{
			$project: {
				val_1: 1 ,
				val2_2: 2
			}
		}
	])
	`````

	2. $match （ 条件匹配 只满足条件的文档才能进入下一阶段 ）

	`````javascript
	db['表名'].aggregate([
		{
			$project: {
				val_1: 1 ,
				val2_2: 2
			},
			$match: {
				"all_price": {$gte: 90} //大于90的商品
			}
		}
	])
	`````

	3. $limit （ 限制结果的数量 ）
	
	`````javascript
	db['表名'].aggregate([
		{
			$project: {
				val_1: 1 ,
				val2_2: 2
			},
			$match: {
				"all_price": {$gte: 90} //大于90的商品
			},
			{ 
				$limit : 5 
			}
		}
	])
	`````

	4. $skip （ 跳过文档， 数据的数量 ）

	`````javascript
    db['表名'].aggregate([
		{
			$project: {
				trade_no: 1,
				all_price: 1
			}
		}, 
		{
			$match: {
				"all_price": {
					$gte: 90
				}
			}
    	}, 
		{
			$sort: {
				"all_price": -1
			}
    	}, 
		{
      		$skip: 1
    	}
	])
	`````

	5. $sort （ 条件排序 ）

	`````javascript
	db['表名'].aggregate([
		{
			$project: {
				trade_no: 1,
				all_price: 1
			}
    	},
		{
			$match: {
				"all_price": {
					$gte: 90
				}
			}
		},
		{
			$sort: {
				"all_price": -1
			}
		}	
	])
	
	`````

	6. $group （ 条件组合结果  进行分组统计）

	`````javascript
	db['表名'].aggregate([
		{
			$group: {
				_id: "$order_id",
				total: {
					$sum: "$num"
				}
			}
		}
	])
	`````

	7. $lookup （ 用以引入其他集合的数据 ）

	`````javascript
	db['表名'].aggregate([{
		$lookup: {
			from: "order_item",
			localField: "order_id",
			foreignField: "order_id",
			as: "items"
		}
    }])
	`````

	
- 安全 （暂时未整理）
- 主从服务器（暂时未整理）
- 备份与恢复（暂时未整理）

#### 高级命令
	
- 条件查询
    1.  =   
            db.user.find({"key": "value"})

	2.	and	
            db.user.find({"key": "value"},{"key2": "value2"}) 只限于一条数据的查询, 可以查询一条数据的多个属性

- 模糊查询 db.user.find({"key": /正则表达式/})

    1.  $gt 大于
			db.user.find({"key": {$gt: "value"}})
            db.集合名.find({"age": {$gte: 20},{$lte: 50} })	并且关系

	2.	$lt小于
			db.user.find({"key": {$lt: "value"}})

	3. 	$gte大于等于
	4.	$lte小于等于
	5.	$ne不等于
	6.	区间查询
			db.user.find({"key": {$gt: "value", $lt: "value"}})

	7.	$in 在集合中
			db.user.find("key": {$in: ["value1", "value2", "value3",...]})
	8.	$nin 不在集合中
	9.	$size 数组长度 判断值的个数 用于查询数组size
			db.user.find("key必须是数组": {$size: 2})返回值有两条数据的数据

	10.	$exists 是否存在某个键名() 是否存在某个字段 找数据的差异性 对应的接口
			db.user.find("key": {$exists: true | false})

	11.	$or 或者 满足其中的一个数据就OK 或者的意思
			db.user.find("height": $or: [{条件1},{条件2}])
    12. $mod 取模
    13. db.singer.find({"键名": 值})模糊查询 必须是正则表达式

- 排序
    1. sort() 排序时1代表升序 -1代表降序
        db.user.find().sort({"键名": 1}, {"键名": -1})
    2. db.user.ensureIndex({})

- 限制输出
    1. limit() (多少条数据) 从头数拿出多少条数据
        db.user.find().limit(n)
            限制n条
    2. skip() (多少条数据)从头数跳过多少条数据
        db.user.find().skip(n).limit(n)
            跳过n条显示n条 一般用于制作分页

- 配置
	D:\Program Files\MongoDB\Server\4.0\bin>mongod.exe --logpath "D:\Program Files\MongoDB\Server\4.0\log\mongod.log" --logappend --dbpath "D:\Program Files\MongoDB\Server\4.0\data\db"  --serviceName MongoDB --install
	mongod D:\Program Files\MongoDB\Server\4.0\conf\mongod.conf --auth