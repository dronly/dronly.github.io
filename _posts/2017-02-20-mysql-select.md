---
layout: post
title: Mysql select 语句学习
categories: mysql
header-style: text
tags:
- Mysql
---
# Mysql select 语句学习

### 一、 初始化数据
首先，创建表咯。一共有4张表。分别是学生表，课程表，教师表，成绩表 。

- 学生表Student
4个字段，SId（学生ID）,Sname(学生姓名)，Sage(学生年龄)，Ssex(学生性别)
```
create table Student(SId varchar(10),Sname varchar(10),Sage datetime,Ssex varchar(10),PRIMARY key(SId));
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-12-20' , '男');
insert into Student values('04' , '李云' , '1990-12-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-01-01' , '女');
insert into Student values('07' , '郑竹' , '1989-01-01' , '女');
insert into Student values('09' , '张三' , '2017-12-20' , '女');
insert into Student values('10' , '李四' , '2017-12-25' , '女');
insert into Student values('11' , '李四' , '2012-06-06' , '女');
insert into Student values('12' , '赵六' , '2013-06-13' , '女');
insert into Student values('13' , '孙七' , '2014-06-01' , '女');
```

- 教师表Teacher
2个字段，TId（教师id），Tname（教师姓名）
```
create table Teacher(TId varchar(10),Tname varchar(10),PRIMARY key (Tid));
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

- 课程表
3个字段，CId(课程id)，Cname（课程名称），TId（教师id）
```
create table Course(CId varchar(10),Cname nvarchar(10),TId varchar(10),PRIMARY KEY(CId));
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

- 成绩表
4个字段，skey（由于没有不重复的字段，因此创建了主键字段），SId(学生id)，CId(课程ID)，score（学生成绩）

```
create table SC(skey int ,SId varchar(10),CId varchar(10),score decimal(18,1),PRIMARY key(skey));
insert into SC values('1','01' , '01' , 80);
insert into SC values('2','01' , '02' , 90);
insert into SC values('3','01' , '03' , 99);
insert into SC values('4','02' , '01' , 70);
insert into SC values('5','02' , '02' , 60);
insert into SC values('6','02' , '03' , 80);
insert into SC values('7','03' , '01' , 80);
insert into SC values('8','03' , '02' , 80);
insert into SC values('9','03' , '03' , 80);
insert into SC values('10','04' , '01' , 50);
insert into SC values('11','04' , '02' , 30);
insert into SC values('12','04' , '03' , 20);
insert into SC values('13','05' , '01' , 76);
insert into SC values('14','05' , '02' , 87);
insert into SC values('15','06' , '01' , 31);
insert into SC values('16','06' , '03' , 34);
insert into SC values('17','07' , '02' , 89);
insert into SC values('18','07' , '03' , 98);
```
4个表建好了，那么开工。

### 二、 语句练习
1. 求每门课程的学生人数。
```
select 
Course.Cname as "课程名称", count(SC.Sid) as "人数"
from  SC  right join Course on SC.CId = Course.Cid group by SC.Cid
```
2. 查询课程编号为 01 且课程成绩在 80 分及以上的学生的学号和姓名
```
select Student.Sid, Student.Sname 
from Student join SC on Student.Sid = SC.Sid 
where SC.CId = 01 and SC.score >= 80
```
3. 统计每门课程的学生选修人数（超过 5 人的课程才统计）
```
select Course.Cname, count(SC.Sid) 
from Course JOIN SC on  Course.Cid = SC.Cid 
group by (SC.Cid) having count(SC.Sid) > 5
```
4. 检索至少选修两门课程的学生学号
```
select Sid 
from SC group by Sid having count(Sid) > 2
```
5. 选修了全部课程的学生信息
```
select Student.* 
from Student join SC on Student.Sid = SC.Sid 
group by SC.Sid having count(SC.Sid)  = (select count(*) from Course)
```
6. 查询存在不及格的课程
```
select Course.Cname 
from Course join SC on Course.Cid  = SC.Cid 
where SC.score < 60 group by Course.Cid
Select Course.Cname from Course 
where Course.Cid in (select SC.Cid from SC where SC.score < 60)
```
7. 查询任何一门课程成绩在 70 分以上的学生姓名、课程名称和分数
```
select 
Student.Sname, Course.Cname, SC.score
from Student join SC on Student.Sid = SC.Sid
join Course on SC.Cid = Course.Cid
where SC.score > 70 
```
8. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）
```
select 
Student.Sname, Course.Cname, SC.score
from Student 
left join SC on Student.Sid = SC.Sid
left join Course on SC.Cid = Course.Cid
```
9. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数
```
select 
Student.Sname, SC.score
from Student 
join SC on Student.Sid = SC.Sid
join Course on SC.Cid = Course.Cid
where SC.score < 60 and Course.Cname = '数学'
```
10. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩
```
select 
Student.Sid, Student.Sname, avg(SC.score)
from Student 
join SC on Student.Sid = SC.Sid
join Course on SC.Cid = Course.Cid 
group by Student.Sid having avg(SC.score) > 85
```
11. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列
```
select 
 Course.Cid, Course.Cname, avg(SC.score)
from Course
join SC on SC.Cid = Course.Cid 
group by Course.Cid order by avg(SC.score) desc, Course.Cid asc
```
12. 查询各科成绩最高分、最低分和平均分
```
select 
 Course.Cid, Course.Cname, avg(SC.score) as '平均分', max(SC.score) as '最大分',  min(SC.score) as '最小分'
from Course
join SC on SC.Cid = Course.Cid 
group by Course.Cid
```
13. 查询男生、女生人数
```
Select Student.Ssex, count(Student.Ssex) '人数' from Student group by Student.Ssex
```
14. 检索" 01 "课程分数小于 60，按分数降序排列的学生信息
```
Select Student.*
from Student  
join SC on Student.Sid = SC.Sid
where SC.Cid = 01 and Sc.`score` < 60 order by Sc.`score` desc
```
15. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩
描述有歧义：
平均成绩是指课程的平均成绩还是学生的平均成绩
按学生的各科平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩
16. 查询没学过"张三"老师讲授的任一门课程的学生姓名
```
select 
Student.Sname
from 
Student
where Sid not in 
(
	select SC.Sid 
	from SC 
	join Course on SC.Cid = Course.Cid 
	join Teacher on Teacher.Tid = Course.TId
	where Teacher.Tname = '张三'
)
```
17. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
```
select Student.*, max(SC.score)
from SC
join Course on SC.Cid = Course.Cid 
join Teacher on Teacher.Tid = Course.TId
join Student on Student.Sid = SC.Sid
where Teacher.Tname = '张三'
```
18. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
```
select student.*, sc.score
from student join sc on student.sid = sc.sid
join course on sc.cid = course.cid
join teacher on teacher.tid = course.tid
where  teacher.tname = '张三' and
sc.score = (select max(sc.score) 
from teacher join course on teacher.tid = course.tid 
join sc on sc.cid = course.cid
where teacher.tname = '张三')
```
19. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩
查询所有课程成绩相同的学生的学生编号、课程编号、以及成绩
```
select a.cid, a.sid, a.score from sc as a join 
sc as b
on a.sid = b.sid
and a.cid != b.cid
and a.score = b.score
GROUP BY a. cid, b.sid; 
```
20. 查询每门功成绩最好的前两名
```
select any_value(a.sid),any_value(a.cid),any_value(a.score) from sc a left join sc as b 
on a.cid = b.cid
and a.score < b.score
group by a.cid,a.sid
having count(b.score)<2
order by a.cid
```
这题也是自己联结自己。这道题比较需要注意是，要注意思维的转化。当比你分数高的人数少于2个，就是只有一个人时，你就是第一名。同时需要注意的是，你以哪个表为主，就需要那个表作为主表，其他表去联结它。

21. 查询每门课程被选修的学生数
```
select cid, count(sc.sid) from sc group by cid
```
22. 查询出只选修两门课程的学生学号和姓名
```
select student.sname, student.sid 
from student join sc on student.sid = sc.sid
group by sc.sid having count(sc.cid) = 2
```
23. 查询同名学生名单，并统计同名人数
```
select sname, count(*) from student group by sname having count(sname) >1
```
24. 查询 1990 年出生的学生名单
```
select * from Student where year(Sage)=1990
```
25. 查询各学生的年龄.
```
select SId,Sname,TIMESTAMPDIFF(year,Sage,CURDATE()) from student
```
timestampdiff的用法。timestampdiff（参数1，参数2，参数3）。参数1 可以选择小时hour，秒second，月month，年year。参与2，比较的时间数据（小的哪一个），参数3，比较的时间数据（大的那一个）。
26. 查询本周过生日的学生
```
select * from student where 
week(curdate())=week(Sage)
```
27. 查询本月过生日的学生
```
select * from student where 
month(curdate())=month(Sage)
```
28. 查询「李」姓老师的数量
```
select count(*) from Teacher where Tname like '李%'
```
29. 查有成绩的学生信息
```
select Student.* from Student where sid in (select sid from sc)
```
30. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的成绩总和
```
select student.sid, student.sname, count(sc.cid), sum(sc.score)
from student left join sc on student.sid = sc.sid group by student.sid
```
31. 查询在 SC 表存在成绩的学生信息
```
select * from student 
where id in (select sid from sc)
```
32. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩
```
select student.sid, student.sname,  avg(sc.score)
from student left join sc on student.sid = sc.sid group by student.sid having avg(sc.score) >= 60
```
33. 查询不存在" 01 "课程但存在" 02 "课程的情况
```
select * from sc where cid = '02' and sid not in (select  sid from sc  where cid='01')
```
也是需要用到 not in 的思维。当cid=02时，并且sid 并不在cid=01是选取出来
34. 查询存在" 01 "课程但可能不存在" 02 "课程的情况
```
select  * from  sc where cid ='01'
```
可能不存在，那就是说，可能有02课程也可能没有，那只要把选则01课程的选中即可。
35. 按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺
```
select a.cid ,a.sid ,any_value(a.score),count(b.sid)+1 from sc a left join sc b 
on a.cid=b.cid 
and a.score<b.score 
GROUP BY  a.cid,a.sid
order by a.cid ,count(b.sid)+1 
```
36. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数
```
select * from student a ,
(select sid, score from sc where cid = '01') b,
(select sid, score from sc where cid = '02') c
where a.sid = b.sid and a.sid = c.sid and b.score > c.score
```
37. 查询学过「张三」老师授课的同学的信息
```
select a.* from Student a ,Teacher b ,Course c ,sc d
where a.sid=d.sid
and d.cid = c.cid 
and b.tid=c.tid
and b.Tname='张三'
```
38. 查询没有学全所有课程的同学的信息
```
select student.* 
from student left join sc
on student.sid = sc.sid group by student.sid having count(sc.cid) < (
select count(cname) from course
)
```
39. 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息
```
select student.* 
from student join sc
on student.sid = sc.sid 
where cid in (select sc.cid from sc where sc.sid = '01')
group by student.sid
```
40. 查询和" 01 "号的同学学习的课程完全相同的其他同学的信息
```
select * from Student where sid in
(select  sid from sc where sid not in 
(select  sid from sc where  cid not in 
(select cid from sc where sid ='01'))
GROUP BY sid 
having count(*)=(select count(cid) from sc where sid ='01')and sid<>'01')
```
这题是真绕。看了网上很多答案，感觉这个比较清晰，比较好理解。
首先，筛选出01同学的课程——选出有谁没有选择01同学课程的学生sid——再对上一层逻辑做否定，选择了01号同学的子集或者含有01号同学所选课程之外的含有其他课程的 同学。因此最后再加上判断条件，课程数量相等。就可以判断是和01号同学学习相同的课程。




