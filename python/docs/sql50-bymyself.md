
## 练习数据

数据表  
--1.学生表  
--s_id 学生编号,s\_name 学生姓名,s\_birth 出生年月,s_\_sex 学生性别

--2.课程表  
-- c_id --课程编号,c\_name 课程名称,t_\_id\_ 教师编号

--3.教师表  
 -- t_id 教师编号,t_\_name 教师姓名

--4.成绩表  
 --s_id 学生编号,c\_id 课程编号,s_\_\_score 分数

创建测试数据

学生表 Student

```sql
CREATE TABLE `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);
-- 插入数据 
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
```

科目表 Course

```sql
CREATE TABLE `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);

insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

教师表 Teacher

```sql
CREATE TABLE `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);

insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

成绩表 SC

```sql
CREATE TABLE `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);

insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```



## 练习题目



-- 1.查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号

```sql
SELECT * FROM    Student st
JOIN Score B ON st.s_id = B.s_id
JOIN Score C ON st.s_id = C.s_id
WHERE B.c_id='01' AND C.c_id='02' AND B.s_score > C.s_score
```

-- 方法2

```sql
SELECT * FROM Student st
LEFT JOIN (SELECT * FROM Score WHERE c_id = '01') B ON st.s_id = B.s_id 
LEFT JOIN (SELECT * FROM Score WHERE c_id = '02') C ON st.s_id = C.s_id 
WHERE B.s_score > C.S_score
```

-- 方法3

```sql
SELECT * FROM Student
WHERE s_id IN (
SELECT A.s_id FROM (SELECT * FROM SCORE WHERE c_id = '01') A 
JOIN (SELECT * FROM SCORE WHERE c_id = '02') B ON A.s_id=B.s_id
WHERE A.s_score>=B.s_score
)
```

-- 2、查询平均成绩大于60分的学生的学号和平均成绩

```sql
SELECT s_id,AVG(s_score) FROM Score
GROUP BY s_id
HAVING AVG(s_score)>=60
```

```sql
SELECT * FROM Student
WHERE s_id NOT IN
(SELECT s_id FROM Score
GROUP BY s_id
HAVING AVG(s_score)>=60
)
```

-- 3、查询所有学生的学号、姓名、选课数、总成绩

```sql
SELECT So.s_id,Student.s_name,COUNT(So.c_id)AS 科目,SUM(So.s_score)AS 总分 FROM Score So
LEFT JOIN Student ON So.s_id=Student.s_id
GROUP BY So.s_id
```

-- 4、查询姓“猴”的老师的个数

```sql
SELECT COUNT(*) FROM Teacher
WHERE t_name LIKE "张%"
```

-- 5、查询没学过“张三”老师课的学生的学号、姓名

```sql
SELECT * FROM Student
WHERE s_id NOT IN ( 
SELECT s_id FROM Score WHERE c_id IN 
(SELECT c.c_id FROM Course c JOIN Teacher t ON c.t_id= t.t_id 
WHERE t_name = "张三")
)
```

-- 6、查询学过“张三”老师所教的所有课的同学的学号、姓名

```sql
SELECT * FROM Student
WHERE s_id IN ( 
SELECT s_id FROM Score WHERE c_id IN 
(SELECT c.c_id FROM Course c JOIN Teacher t ON c.t_id= t.t_id 
WHERE t_name = "张三")
)
```

-- 7、查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号、姓名

```sql
SELECT * FROM Student
WHERE s_id IN (SELECT a.s_id FROM 
(SELECT * FROM Score WHERE c_id = '01' ) a 
INNER JOIN (SELECT * FROM Score WHERE c_id = '02' ) b 
ON a.s_id = b.s_id)
```

-- 8、查询课程编号为“02”的总成绩

```sql
SELECT SUM(s_score) FROM Score
WHERE c_id='02'
```

-- 9、查询所有课程成绩小于60分的学生的学号、姓名

```sql
SELECT * FROM Student
WHERE s_id NOT IN (
SELECT s_id FROM Score where s_score>=60)
```

-- 10.查询没有学全所有课的学生的学号、姓名

```sql
SELECT s_id FROM Score
GROUP BY s_id
HAVING COUNT(c_id) != (SELECT COUNT(*) FROM Course)
```

-- 11、查询至少有一门课与学号为“01”的学生所学课程相同的学生的学号和姓名

```sql
SELECT * FROM Student
WHERE s_id IN (SELECT DISTINCT(s_id) FROM Score
WHERE c_id IN (SELECT c_id FROM Score  WHERE s_id ='01') AND s_id !='01')
```

-- 12.查询和“01”号同学所学课程完全相同的其他同学的学号

```sql
SELECT s_id FROM Score
GROUP BY s_id
HAVING COUNT(c_id)  = (SELECT COUNT(c_id) FROM Score WHERE s_id = '01')
```

-- 13、查询没学过"张三"老师讲授的任一门课程的学生姓名 和47题一样

```sql
SELECT * FROM STudent
WHERE s_id NOT IN (
SELECT s_id FROM Score WHERE c_id IN (
SELECT C.c_id FROM Course C INNER JOIN Teacher T ON T.t_id = C.t_id WHERE T.t_name  ='张三'
))
```

-- 15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

-- 方法1

```sql
SELECT s_id FROM
(SELECT * FROM Score WHERE s_score<60) AS A
GROUP BY s_id
```

-- 方法2

```sql
SELECT s_id FROM Score
WHERE s_score <60
GROUP BY s_id
HAVING COUNT(c_id)>1
```

-- 16、检索"01"课程分数小于60，按分数降序排列的学生信息

-- 方法1

```sql
select student.*
from Student,Score
where Score.c_id ='01'
and   Score.s_score<60
and   Student.s_id=Score.s_id
ORDER BY Score.s_score DESC
```

-- 方法2

```sql
SELECT * FROM Student
WHERE s_id IN (SELECT s_id FROM Score
WHERE c_id ='01' AND s_score<60
ORDER BY s_score DESC)
```

-- 17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
SELECT s_id,max(case c_id when '01' then s_score else 0 END) AS c_id_01,
max(case c_id when '02' then s_score else 0 END) AS c_id_02,
max(case c_id when '03' then s_score else 0 END) AS c_id_03,
AVG(s_score)
FROM Score
GROUP BY s_id
ORDER BY AVG(s_score) DESC
```

-- 18.查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率，及格为&gt;=60，中等为：70-80，优良为：80-90，优秀为：&gt;=90 \(超级重点\)

```sql
SELECT s.c_id,c.c_name,MAX(s_score),MIN(s_score),AVG(s_score),
AVG(CASE WHEN s_score<60 THEN    1    ELSE    0 END ) AS '不及格率',
AVG(CASE WHEN s_score>=60 THEN    1    ELSE    0 END ) AS '及格率',
AVG(CASE WHEN s_score>70 AND s_score<=80 THEN    1    ELSE    0 END ) AS '中等率',
AVG(CASE WHEN s_score>80 AND s_score<=90 THEN    1    ELSE    0 END ) AS '优良率',
AVG(CASE WHEN s_score>90 THEN    1    ELSE    0 END ) AS '优秀率'
FROM Score S
LEFT JOIN Course c ON S.c_id=c.c_id
GROUP BY s.c_id
```

-- 19、按各科成绩进行排序，并显示排名\(

```sql
SELECT s.*,(@i:=@i+1) AS 'rank'
FROM Score s,(SELECT @i:=0) a
ORDER BY s_score DESC
```

-- 20、查询学生的总成绩并进行排名

```sql
SELECT s.s_id,SUM(s.s_score),(@i:=@i+1) AS 'rank'
FROM Score s,(SELECT @i:=0) a
GROUP BY s.s_id
```

-- 21 、查询不同老师所教不同课程平均分从高到低显示

```sql
select s.c_id,c.c_name,t.t_name,AVG(s.s_score) FROM Score s
JOIN Course c ON s.c_id = c.c_id
JOIN Teacher t ON c.t_id=t.t_id
GROUP BY s.c_id
ORDER BY AVG(s.s_score) DESC
```

-- 22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩 - 方法1

```sql
(SELECT c_id,s_id FROM Score WHERE c_id = '01' ORDER BY s_score DESC LIMIT 1,2)
UNION
(SELECT c_id,s_id FROM Score WHERE c_id = '02' ORDER BY s_score DESC LIMIT 1,2)
UNION
(SELECT c_id,s_id FROM Score WHERE c_id = '03' ORDER BY s_score DESC LIMIT 1,2)
```

-- 23、使用分段\[100-85\],\[85-70\],\[70-60\],\[&lt;60\]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称

```sql
SELECT c_id,
SUM(case when s_score>85 AND s_score<=100 then 1 else 0 end) AS '[100-85]',
AVG(case when s_score>85 AND s_score<=100 then 1 else 0 end) AS '[100-85]率',
SUM(case when s_score>70 AND s_score<=85 then 1 else 0 end) AS '[85-70]',
AVG(case when s_score>70 AND s_score<=85 then 1 else 0 end) AS '[85-70]率',
SUM(case when s_score>=60 AND s_score<=70 then 1 else 0 end) AS '[70-60]',
AVG(case when s_score>=60 AND s_score<=70 then 1 else 0 end) AS '[70-60]率',
SUM(case when s_score<60 then 1 else 0 end) AS '[>60]',
AVG(case when s_score<60 then 1 else 0 end) AS '[>60]率'
FROM Score
GROUP BY c_id
```

-- 24、查询学生平均成绩及其名次

```sql
SELECT a.*,(@i:=@i+1) AS 'rank' FROM
(
SELECT st.s_id,AVG(s.s_score)
FROM Score s,Student st
WHERE s.s_id= st.s_id
GROUP BY s.s_id
ORDER BY AVG(s.s_score) DESC
) a, (SELECT @i:=0) b
```

-- 25、查询各科成绩前三名的记录（不考虑成绩并列情况）-方法1

```sql
(SELECT c_id,s_score FROM Score WHERE c_id='01' ORDER BY s_score DESC LIMIT 3)
UNION ALL
(SELECT c_id,s_score FROM Score WHERE c_id='02' ORDER BY s_score DESC LIMIT 3)
UNION ALL
(SELECT c_id,s_score FROM Score WHERE c_id='03' ORDER BY s_score DESC LIMIT 3)
```

-- 25、查询各科成绩前三名的记录（不考虑成绩并列情况）-方法2

```sql
SELECT *
FROM Score
WHERE (c_id = '01' AND s_score >= (SELECT s_score FROM Score WHERE c_id='01' ORDER BY s_score DESC LIMIT 2,1))
OR (c_id = '02' AND s_score >= (SELECT s_score FROM Score WHERE c_id='02' ORDER BY s_score DESC LIMIT 2,1))
OR (c_id = '03' AND s_score >= (SELECT s_score FROM Score WHERE c_id='03' ORDER BY s_score DESC LIMIT 2,1))
ORDER BY c_id,s_score DESC
```

-- 26、查询每门课程被选修的学生数

```sql
SELECT c_id,COUNT(s_score) FROM Score
GROUP BY c_id
```

-- 27、 查询出只有两门课程的全部学生的学号和姓名

```sql
SELECT s.s_id,COUNT(s.c_id),t.s_name FROM Score s,Student t
WHERE s.s_id=t.s_id
GROUP BY s.s_id
HAVING COUNT(s.c_id) =2
```

-- 28、查询男生、女生人数

```sql
SELECT s_sex,COUNT(s_id)
FROM Student
GROUP BY s_sex
```

-- 29 查询名字中含有"风"字的学生信息

```sql
SELECT *
FROM Student
WHERE s_name LIKE "%风%"
```

-- 31、查询1990年出生的学生名单

```sql
SELECT *
FROM Student
WHERE s_birth LIKE "1990%"
```

```sql
SELECT *
FROM Student
WHERE YEAR(s_birth) =1990
```

-- 32、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

```sql
SELECT s.s_id,t.s_name,AVG(s.s_score)
FROM Student t,Score s
WHERE s.s_id=t.s_id
GROUP BY s.s_id
HAVING AVG(s.s_score)>85
```

-- 33、查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列

```sql
SELECT c_id,AVG(s_score)
FROM Score
GROUP BY c_id
ORDER BY AVG(s_score),c_id DESC
```

-- 34、查询课程名称为"数学"，且分数低于60的学生姓名和分数

```sql
SELECT t.s_name,s.s_score
FROM Score s
JOIN Student t ON t.s_id =s.s_id
JOIN Course c ON c.c_id = s.c_id
WHERE c.c_name='数学' AND s.s_score<60
```

-- 35、查询所有学生的课程及分数情况

```sql
SELECT s_id,
MAX(case  when c_id = '01' then s_score else 0 end) AS "语文",
MAX(case  when c_id = '02' then s_score else 0 end) AS "数学",
MAX(case  when c_id = '03' then s_score else 0 end) AS "英语"
FROM Score
GROUP BY s_id
```

-- 36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数

```sql
SELECT t.s_name,c.c_name,s.s_score
FROM Score s
JOIN Course c ON c.c_id=s.c_id
join Student t on t.s_id= s.s_id
WHERE s.s_score>70
```

-- 37、查询不及格的课程并按课程号从大到小排列

```sql
SELECT s.s_id,c.c_name,t.s_name,s.s_score
FROM Score s
JOIN Student t ON t.s_id = s.s_id
JOIN Course c ON c.c_id = s.c_id
WHERE s.s_score<60
ORDER BY s.c_id DESC
```

-- 38、查询课程编号为03且课程成绩在80分以上的学生的学号和姓名

```sql
SELECT s.s_id,t.s_name,s.s_score
FROM Score s
JOIN Student t ON t.s_id = s.s_id
WHERE s.s_score>80 AND s.c_id='03'
```

-- 39、求每门课程的学生人数

```sql
SELECT c_id,COUNT(s_score)
FROM Score
GROUP BY c_id
```

-- 40、查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩

```sql
SELECT s.s_id,st.s_name,s.s_score
FROM Score s
JOIN Course c ON c.c_id = s.c_id
JOIN Teacher t ON t.t_id = c.t_id
JOIN Student st ON st.s_id = s.s_id
WHERE t_name='张三'
ORDER BY s_score DESC
LIMIT 1
```

-- 41.查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

```sql
SELECT *
FROM Score
WHERE s_id = (
SELECT s_id FROM Score a
GROUP BY s_id
HAVING  SUM(s_score)/COUNT(s_score) = ALL(SELECT s_score FROM Score b WHERE b.s_id = a.s_id))
```

-- 43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```sql
SELECT c_id,COUNT(s_id)
FROM Score
GROUP BY c_id
HAVING COUNT(s_id)>5
```

-- 44、检索至少选修两门课程的学生学号

```sql
SELECT s_id
FROM Score
GROUP BY s_id
HAVING COUNT(c_id)>=2
```

-- 45、 查询选修了全部课程的学生信息

```sql
SELECT s_id ,COUNT(c_id)
FROM Score
GROUP BY s_id
HAVING COUNT(c_id) = (SELECT COUNT(*) FROM Course)
```

-- 46、查询各学生的年龄

-- 年

```sql
SELECT s_id,TIMESTAMPDIFF(YEAR,s_birth,'2019-05-19')
FROM Student
```

-- 月

```sql
SELECT s_id,TIMESTAMPDIFF(MONTH,s_birth,'2019-05-19')/12
FROM Student
```

-- 47、查询没学过“张三”老师讲授的任一门课程的学生姓名

```sql
SELECT s_id,s_name
FROM Student
WHERE s_id NOT IN (
SELECT s_id FROM Score WHERE c_id= (
SELECT c_id FROM Course WHERE t_id = (
SELECT t_id FROM Teacher WHERE t_name='张三'
)))
```

-- 48、查询两门以上不及格课程的同学的学号及其平均成绩

```sql
SELECT s_id,AVG(s_score)
FROM Score
WHERE s_id IN (SELECT s_id
FROM Score
WHERE s_score<60
GROUP BY s_id
HAVING COUNT(c_id)>=2)
GROUP BY s_id
```

-- 49、查询本月过生日的学生

```sql
SELECT s_id,MONTH(s_birth)
FROM Student
WHERE MONTH(s_birth) = MONTH(now())
```



