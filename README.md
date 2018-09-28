mysql  limit用法：获取数据库中部分数据

  语法很简单：
     1：select * from t limit ?,?;
     第一个参数?为从哪行开始，第二个?为取几行；当第一个参数为0时可以省略，
     select * from t limit ?;
     即取前?行数据，类似sqlserver中的top语法。
     需要注意的是以下语法已不能使用（以前可以获取？后的数据）：
     select * from t limit ?,-1;

    百万级数据分页查询：
      本文测试数据为300W。表结构为s1（id,name,gender,email）id为主键自增
      先上例子
        select * from s1 limit 100,10;可以看到耗时很短0.00，
      再来一个
        select * from s1 limit 1000000,10;耗时1s
        select * from s1 limit 2000000,10;耗时1.77s!
      可以看出随着偏移量的增大，耗时增加，几秒的响应速度对系统来说应该是灾难了，绝对不能接受！第二个参数的增大也会增加耗时
      但limit一般用于分页，每页的size不会太大，所以不考虑这种情况。
      那么怎么来减少耗时呢？下面就来说一下几种方案：
      1、设置查找范围（id要为索引列）
         select * from s1 where id > 2000000 limit 10;0.00s 效果还是满显著的
         这样写更加适用实际开发：
         select * from s1 where id >(page*size) limit size;
         若id为连续的可用between...and:
         SELECT * FROM s1 WHERE id BETWEEN 2000000 AND 2000010;0.00s so quickly
