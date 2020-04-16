```mysql
# 创建数据库
CREATE DATABASE mooc;
-- 使用表
USE mooc;
-- 用户名
CREATE TABLE IF NOT EXISTS user(
   id INT,
   username VARCHAR(20),
   password CHAR(32),
   email VARCHAR(50),
   age TINYINT,
   card CHAR(8),
   tel CHAR(11),
   salary FLOAT(9,2),
   married TINYINT,
   sex ENUM('男','女','保密'),
   addr VARCHAR(100)
)ENGINE=INNODB CHARSET='UTF8';


-- 测试整型
 CREATE TABLE test_int(
    a tinyint,
    b smallint,
    c mediumint,
    d int,
    e bigint
 );
 
 -- 插入数据
  INSERT test_int(a,b) VALUES(127,3);
  
 -- 添加超过范围的数据时，会产生截断的效果
  INSERT test_int(a,b) VALUES(129,-300);
  select * from test_int;
+------+------+------+------+------+
| a    | b    | c    | d    | e    |
+------+------+------+------+------+
|  127 |    3 | NULL | NULL | NULL |
|  126 | -300 | NULL | NULL | NULL |
|  127 | -300 | NULL | NULL | NULL |
+------+------+------+------+------+
3 rows in set (0.00 sec)
 -- 测试无符号(出现负号，就会以0来标记)
  CREATE TABLE test_int1(
    a tinyint ,
    b tinyint unsigned
 );
 INSERT test_int1(a,b) VALUES(-127,-3);
  select * from test_int1;
+------+------+
| a    | b    |
+------+------+
| -127 |    0 |
+------+------+
1 row in set (0.00 sec)

-- 测试零填充
CREATE TABLE test_int2(
    a tinyint zerofill,
    b smallint zerofill,
    c mediumint zerofill,
    d int zerofill,
    e bigint zerofill
 );
 INSERT test_int2(a,b) VALUES(2,3);
 desc test_int2;
+-------+--------------------------------+------+-----+---------+-------+
| Field | Type                           | Null | Key | Default | Extra |
+-------+--------------------------------+------+-----+---------+-------+
| a     | tinyint(3) unsigned zerofill   | YES  |     | NULL    |       |
| b     | smallint(5) unsigned zerofill  | YES  |     | NULL    |       |
| c     | mediumint(8) unsigned zerofill | YES  |     | NULL    |       |
| d     | int(10) unsigned zerofill      | YES  |     | NULL    |       |
| e     | bigint(20) unsigned zerofill   | YES  |     | NULL    |       |
+-------+--------------------------------+------+-----+---------+-------+
5 rows in set (0.00 sec)

mysql> INSERT test_int2(a,b) VALUES(2,3);
Query OK, 1 row affected (0.00 sec)

mysql> select * from test_int2;
+------+-------+------+------+------+
| a    | b     | c    | d    | e    |
+------+-------+------+------+------+
|  002 | 00003 | NULL | NULL | NULL |
+------+-------+------+------+------+
1 row in set (0.00 sec)

-- 测试char和varchar
CREATE TABLE test_str(
    a char(5),
    b varchar(5)
);
INSERT test_str(a,b) VALUES('','');
INSERT test_str(a,b) VALUES('A','A');
INSERT test_str(a,b) VALUES('ad','dd');
INSERT test_str(a,b) VALUES('asd','sss');
INSERT test_str(a,b) VALUES('ssss','ddas');
INSERT test_str(a,b) VALUES('sssss','ddase');
INSERT test_str(a,b) VALUES('ssssss','ddased');#截取前面的，因为超过了
#在进行Select查询时，CHAR类型会自动将123后面的空格去掉
#https://class.imooc.com/course/qadetail/181041(解释)
INSERT test_str(a,b) VALUES(' 123 ',' 123 ');

-- text不能有默认值，
 CREATE TABLE test_str1(
    content TEXT DEFAULT 'SSSSS'#会出现报错，检索的时候不区分大小写。
);

-- 测试ENUM
CREATE TABLE test_enum(
    sex ENUM('男','女','保密')
);
INSERT test_enum(sex) VALUES('男')                                     
INSERT test_enum(sex) VALUES('男1')#报错
INSERT test_enum(sex) VALUES(1)#男 这个和数组不一样，数组从0开始，这个从1开始
INSERT test_enum(sex) VALUES(NULL);#可以插入空值

-- 测试SET
CREATE TABLE test_set(
   a SET('A','B','C','D','E','F')
);
INSERT test_set(a) VALUES('a');
INSERT test_set(a) VALUES('a,b');
INSERT test_set(a) VALUES('a,b,c');
INSERT test_set(a) VALUES('z');#不显示，因为不在集合里面SET('A','B','C','D','E','F')
INSERT test_set(a) VALUES(1);#显示A

```

```mysql
-- HH:MM:SS [D HH:MM:SS] D表示天数0-34
CREATE TABLE test_time(
    a TIME
);
INSERT test_time(a) VALUES('12:33:44');
INSERT test_time(a) VALUES('2 12:33:44');#60:33:44 
INSERT test_time(a) VALUES('12:22');# 12:22:00
INSERT test_time(a) VALUES('22');#| 00:00:22
INSERT test_time(a) VALUES('2 22');#| 70:00:00
-- HHMMSS
INSERT test_time(a) VALUES('123344');# 12:33:44 
INSERT test_time(a) VALUES('0');# 00:00:00 
INSERT test_time(a) VALUES(0);#  00:00:00 
INSERT test_time(a) VALUES('787878');#超出范围会报错
-- 查询现在的时间
INSERT test_time(a) VALUES(NOW());
INSERT test_time(a) VALUES(CURRENT_TIME);

-- YYYY-MM-DD YYYYMMDD
CREATE TABLE test_day(
    a DATE
);
INSERT test_day(a) VALUES('1996-12-22');
INSERT test_day(a) VALUES('1996*12*22');#任意字符也可以识别
-- 70-99 转换成1970-1999 00-69转换成2000-2069
INSERT test_day(a) VALUES('96-12-22');
INSERT test_day(a) VALUES(NOW());
INSERT test_day(a) VALUES(CURRENT_DATE);

-- 测试DATETIME
CREATE TABLE test_daytime(a DATETIME
);

INSERT test_daytime(a) VALUES('96-12-22 12:23:23');
INSERT test_daytime(a) VALUES('961222122323');
INSERT test_daytime(a) VALUES(NOW());

-- 测试TIMESTAMP
CREATE TABLE test_timestamp(
    a TIMESTAMP
);
INSERT test_timestamp(a) VALUES('96-12-22 12:23:23');
-- 插入null，插入空，插入CURRENT_TIMESTAMP 都是显示现在的时间
-- https://class.imooc.com/course/qadetail/160218
 INSERT test_timestamp(a) VALUES(CURRENT_TIMESTAMP);
 INSERT test_timestamp(a) VALUES();
 INSERT test_timestamp(a) VALUES(NULL);

-- 测试YEAR
CREATE TABLE test_year(
    a YEAR
);
INSERT test_year(a) VALUES(1901);
-- 70-99 转换成1970-1999 00-69转换成2000-2069
-- 0 插入的结果是0000
-- ‘0’ 插入的结果是2000
```

```mysql
-- 测试主键
CREATE TABLE test_primarykey(
   id INT UNSIGNED PRIMARY KEY,
   username VARCHAR(20)
);

INSERT test_primarykey(id,username) VALUES(1,"EDDDIE");
INSERT test_primarykey(id,username) VALUES("SANDY");#报错，因为主键不能为空
INSERT test_primarykey(id,username) VALUES(1,"SANDY");#报错，主键不可以重复

-- 其他两种定义主键的办法
CREATE TABLE test_primarykey1(
   id INT UNSIGNED KEY,
   username VARCHAR(20)
);

CREATE TABLE test_primarykey2(
   id INT UNSIGNED,
   username VARCHAR(20),
   PRIMARY KEY(id)
);

-- 复合主键
-- 这个会报错，因为不能有两个主键
CREATE TABLE test_primarykey3(
   id INT UNSIGNED PRIMARY KEY,
   courseId VARCHAR(20) PRIMARY KEY,
   username VARCHAR(20),
   email VARCHAR(20)
);

CREATE TABLE test_primarykey3(
   id INT UNSIGNED ,
   courseId VARCHAR(20),
   username VARCHAR(20),
   email VARCHAR(20),
   PRIMARY KEY(id,courseId)
);
-- 这两组不算一样，复合主键要复合的两个键都是一样才成立
INSERT test_primarykey3(id,courseId,username,email) 
VALUES(1,"a","EDDDIE","hhh");
INSERT test_primarykey3(id,courseId,username,email) 
VALUES(1,"b","EDDDIE","hhh");
INSERT test_primarykey3(id,courseId,username,email) 
VALUES(1,"b","EDDDIE","hhh");##这个就不行了，因为这个复合主键和上面一样

-- 测试自动增长
-- 默认情况下，从1开始增加
-- 自动编号的字段必须定义为主键
CREATE TABLE test_auto_increment(
      id INT KEY AUTO_INCREMENT,
      username VARCHAR(20)
);
INSERT test_auto_increment(username) VALUES('A');
INSERT test_auto_increment(username) VALUES('B');
INSERT test_auto_increment(username) VALUES('C');
# show create tabele test_auto_increment 的结果里有 AUTO_INCREMENT为15
INSERT test_auto_increment(id,username) VALUES(14,'D');


-- 测试非空
CREATE TABLE test_not_null(
   a varchar(20),
   b varchar(20) NOT NULL
);

INSERT test_not_null(a,b) VALUES('','');
INSERT test_not_null(a,b) VALUES(NULL,'abc');
#下面这两个都会报错，因为b要不能为空
INSERT test_not_null(a,b) VALUES(NULL,NULL);
INSERT test_not_null(a,b) VALUES(NULL,);

-- 测试默认值
CREATE TABLE test_default(
    id INT KEY AUTO_INCREMENT,
    username VARCHAR(20) NOT NULL,
    age TINYINT UNSIGNED DEFAULT 18,
    email VARCHAR(50) NOT NULL DEFAULT '825563102@qq.com'
);
INSERT test_default(username) VALUES('zhang');
INSERT test_default(id,username,age,email) VALUES(6,'hu',19,'7474@qq.com');
INSERT test_default(id,username,age,email) VALUES(7,'xia',NULL,NULL);#报错
INSERT test_default(id,username,age,email) VALUES(7,'xia',NULL,'74@qq.com');
INSERT test_default(id,username,age,email) VALUES(10,'xia',NULL,DEFAULT);

CREATE TABLE test_default1(
 id INT KEY AUTO_INCREMENT,
 sex ENUM('a','c','b') NOT NULL DEFAULT 'a'
);
INSERT test_default1(sex) VALUES(DEFAULT);
INSERT test_default1(id) VALUES(2);#结果也是a，因为没有指定sex

-- 测试unique
CREATE TABLE test_unique(
   id INT UNSIGNED AUTO_INCREMENT KEY,
   username VARCHAR(20) NOT NULL UNIQUE KEY,
   email VARCHAR(50) UNIQUE,
   card CHAR(18) UNIQUE
);

INSERT test_unique(username,email,card) VALUES('zhang','825563102@qq.com','ss');
#报错，因为username唯一
INSERT test_unique(username,email,card) VALUES('zhang','8225563102@qq.com','s2s');
# NULL的时候，unique key不会检测
INSERT test_unique(username,email,card) VALUES('zh',NULL,NULL);

--  创建一个完整的表
CREATE TABLE IF NOT EXISTS imooc_user(
   id INT UNSIGNED AUTO_INCREMENT KEY COMMENT 'id',
   username VARCHAR(20) NOT NULL UNIQUE COMMENT '名字',
   password VARCHAR(20) NOT NULL COMMENT '密码',
   email VARCHAR(50) NOT NULL UNIQUE COMMENT '邮箱',
   age TINYINT NOT NULL DEFAULT 18 COMMENT '年龄',
   sex ENUM('男','女','保密') NOT NULL DEFAULT '保密' COMMENT '性别',
   tel VARCHAR(30) NOT NULL UNIQUE COMMENT '电话',
   addr VARCHAR(50) NOT NULL DEFAULT '北京' COMMENT '地址',
   card VARCHAR(50) NOT NULL UNIQUE COMMENT '身份证',
   married TINYINT NOT NULL DEFAULT 0 COMMENT '结婚状况',
   salary FLOAT(8,2) NOT NULL DEFAULT 0 COMMENT '工资'
)ENGINE=INNODB DEFAULT CHARSET='UTF8';
```