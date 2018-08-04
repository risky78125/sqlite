#  MySQL练习

## 数据初始化

```
drop table if exists t_score;
drop table if exists t_courses;
drop table if exists t_students;
drop table if exists t_teachers;

CREATE TABLE IF NOT EXISTS `t_students` (
  `stu_id`     INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT
  COMMENT '学生id',
  `stu_name`   VARCHAR(255) NOT NULL
  COMMENT '学生姓名',
  `birthday`   DATE COMMENT '学生出生日期',
  `stu_gender` VARCHAR(10) COMMENT '学生性别'
)
  COMMENT '学生表';

CREATE TABLE IF NOT EXISTS `t_courses` (
  `course_id`   INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT
  COMMENT '课程id',
  `course_name` VARCHAR(255) NOT NULL
  COMMENT '课程名称',
  `teacher_id`  INT UNSIGNED COMMENT '教师id'
)
  COMMENT '课程表';

CREATE TABLE IF NOT EXISTS `t_teachers` (
  `teacher_id`   INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT
  COMMENT '教师id',
  `teacher_name` VARCHAR(255) NOT NULL
  COMMENT '教师姓名'
)
  COMMENT '教师表';

CREATE TABLE IF NOT EXISTS `t_score` (
  `score_id`  INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT
  COMMENT '分数id',
  `stu_id`    INT UNSIGNED NOT NULL
  COMMENT '学生id',
  `course_id` INT UNSIGNED NOT NULL
  COMMENT '课程id',
  `score`     INT                               DEFAULT 0
  COMMENT '分数'
)
  COMMENT '分数表';

ALTER TABLE `t_score`
  ADD CONSTRAINT `fk_t_score_t_students_stu_id_fk`
FOREIGN KEY (`stu_id`)
REFERENCES `t_students` (`stu_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE;

ALTER TABLE `t_score`
  ADD CONSTRAINT `t_score_t_courses_course_id_fk`
FOREIGN KEY (`course_id`)
REFERENCES `t_courses` (`course_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE;

ALTER TABLE `t_courses`
  ADD CONSTRAINT `t_courses_t_teachers_teacher_id_fk`
FOREIGN KEY (`teacher_id`)
REFERENCES `t_teachers` (`teacher_id`)
  ON DELETE NO ACTION
  ON UPDATE CASCADE;

insert into t_students (stu_name, birthday, stu_gender) values
  ('赵雷', '1990-01-01', '男'),
  ('钱电', '1990-12-21', '男'),
  ('孙风', '1990-05-20', '男'),
  ('李云', '1990-08-06', '男'),
  ('周梅', '1991-12-01', '女'),
  ('吴兰', '1992-03-01', '女'),
  ('郑竹', '1989-07-01', '女'),
  ('王菊', '1990-01-20', '女');

insert into t_teachers (teacher_name) values
  ('孙悟空'),
  ('贝吉塔'),
  ('特兰克斯');

insert into t_courses (course_name, teacher_id) values
  ('语文', 2),
  ('数学', 1),
  ('英语', 3);

insert into t_score (stu_id, course_id, score) values
  (1, 1, 80),
  (1, 2, 90),
  (1, 3, 99),
  (2, 1, 70),
  (2, 2, 60),
  (2, 3, 80),
  (3, 1, 80),
  (3, 2, 80),
  (3, 3, 80),
  (4, 1, 50),
  (4, 2, 30),
  (4, 3, 20),
  (5, 1, 76),
  (5, 2, 87),
  (6, 1, 31),
  (6, 3, 34),
  (7, 2, 89),
  (7, 3, 98);
	
```

## 练习题

1. 查询"01"课程比"02"课程成绩高的学生的信息及课程分数

    ```sql
    select stu.stu_id, stu.stu_name, stu.birthday, stu.stu_gender,
        ifnull(sc1.score, 0) as '1课程分数', ifnull(sc2.score, 0) as '2课程分数'
    from t_students stu
    left join t_score sc1 on stu.stu_id = sc1.stu_id and sc1.course_id = 1
    left join t_score sc2 on stu.stu_id = sc2.stu_id and sc2.course_id = 2
    where ifnull(sc1.score, 0) > ifnull(sc2.score, 0);
    ```
    
2. 查询"01"课程比"02"课程成绩低的学生的信息及课程分数

    ```sql
    select stu.stu_id, stu.stu_name, stu.birthday, stu.stu_gender,
        ifnull(sc1.score, 0) as '1课程分数', ifnull(sc2.score, 0) as '2课程分数'
    from t_students stu
    left join t_score sc1 on stu.stu_id = sc1.stu_id and sc1.course_id = 1
    left join t_score sc2 on stu.stu_id = sc2.stu_id and sc2.course_id = 2
    where ifnull(sc1.score, 0) < ifnull(sc2.score, 0);
    ```

3. 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

    ```sql
    select stu.stu_id, stu.stu_name, round(avg(sc.score), 2) as avg_score
    from t_students stu
    left join t_score sc on stu.stu_id = sc.stu_id
    group by stu.stu_id, stu.stu_name
    having avg_score >= 60;
    ```

4. 查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

    ```sql
    select stu.stu_id, stu.stu_name, round(ifnull(avg(sc.score), 0), 2) as avg_score
    from t_students stu
    left join t_score sc on stu.stu_id = sc.stu_id
    group by stu.stu_id, stu.stu_name
    having avg_score < 60;
    ```

5. 查询所有同学的学生编号. 学生姓名. 选课总数. 所有课程的总成绩

    ```sql
    select stu.stu_id, stu.stu_name, 
        count(sc.course_id) as 'qty_course', -- 选课数量
        ifnull(sum(sc.score), 0) as 'sum_score' -- 总分数
    from t_students stu
    left join t_score sc on stu.stu_id = sc.stu_id
    group by stu.stu_id, stu.stu_name;
    ```

6. 查询"李"姓老师的数量 

    ```sql
    select count(0) from t_teachers
    where teacher_name like '李%';
    ```
    
7. 查询学过"张三"老师授课的同学的信息

    ```sql
    select stu.* from t_students stu
    where stu.stu_id in (
        select sc.stu_id from t_score sc
        left join t_courses cou on sc.course_id = cou.course_id
        left join t_teachers te on cou.teacher_id = te.teacher_id
        where te.teacher_name = '贝吉塔'
    );
    ```

8. 查询没学过"张三"老师授课的同学的信息 

    ```sql
    select stu.* from t_students stu
    where stu.stu_id not in (
        select sc.stu_id from t_score sc
        left join t_courses cou on sc.course_id = cou.course_id
        left join t_teachers te on cou.teacher_id = te.teacher_id
        where te.teacher_name = '贝吉塔'
    );
    ```

9. 查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

    ```sql
    select stu_id, stu_name, birthday, stu_gender
    from t_students
    where stu_id in (
        select stu_id
        from t_score
        where course_id = 1)
    and stu_id in (
        select stu_id
        from t_score
        where course_id = 2);
    ```

10. 查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

    ```sql
    select stu_id, stu_name, birthday, stu_gender
    from t_students
    where stu_id in (
        select stu_id
        from t_score
        where course_id = 1)
    and stu_id not in (
        select stu_id
        from t_score
        where course_id = 2);
    ```

11. 查询没有学全所有课程的同学的信息 

    ```sql
    select stu_id, stu_name, birthday, stu_gender
    from t_students
    where stu_id in (
      select stu_id
      from t_score
      group by stu_id
      having count(course_id) < (
        select count(0)
        from t_courses
      )
    );
    ```

12. 查询至少有一门课与学号为"01"的同学所学相同的同学的信息 
13. 查询和"01"号的同学学习的课程完全相同的其他同学的信息 
14. 查询没学过"张三"老师讲授的任一门课程的学生姓名 
15. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 
16. 检索"01"课程分数小于60，按分数降序排列的学生信息

17. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

    ```sql
    select stu.stu_id, stu.stu_name,
      ifnull(sc1.score, 0) as yw_score, -- 语文分数
      ifnull(sc2.score, 0) as sx_score, -- 数学分数
      ifnull(sc3.score, 0) as yy_score, -- 英语分数
      round(ifnull(avg_t.avg_score, 0), 2) as avg_score
    from t_students stu
    left join t_score sc1 on stu.stu_id = sc1.stu_id and sc1.course_id = 1 -- 语文成绩表
    left join t_score sc2 on stu.stu_id = sc2.stu_id and sc2.course_id = 2 -- 数学成绩表
    left join t_score sc3 on stu.stu_id = sc3.stu_id and sc3.course_id = 3 -- 英语成绩表
    left join ( -- 子查询, 临时表
        select stu_id, avg(score) as avg_score -- 平均分
        from t_score
        group by stu_id
        order by avg_score
        ) avg_t on stu.stu_id = avg_t.stu_id
    order by avg_t.avg_score desc;
    ```

18. 查询各科成绩最高分. 最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90
19. 按各科成绩进行排序，并显示排名

20. 查询学生的总成绩并进行排名
    1. 查询学生的总成绩
    2. 查询学生的总成绩并进行排名，sql 2000用子查询完成，分总分重复时保留名次空缺和不保留名次空缺两种。
    3. 查询学生的总成绩并进行排名，sql 2005用rank,DENSE_RANK完成，分总分重复时保留名次空缺和不保留名次空缺两种。
21. 查询不同老师所教不同课程平均分从高到低显示 
22. 查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

23. 统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比 
    1. 统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]
    2. 统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[<60]及所占百分比 
24. 查询学生平均成绩及其名次 
    1. 查询学生的平均成绩并进行排名，sql 2000用子查询完成，分平均成绩重复时保留名次空缺和不保留名次空缺两种。
    2. 查询学生的平均成绩并进行排名，sql 2005用rank,DENSE_RANK完成，分平均成绩重复时保留名次空缺和不保留名次空缺两种。
25. 查询各科成绩前三名的记录
    1. 分数重复时保留名次空缺
    2. 分数重复时不保留名次空缺，合并名次

26. 查询每门课程被选修的学生数 
27. 查询出只有两门课程的全部学生的学号和姓名 
28. 查询男生. 女生人数 
29. 查询名字中含有"风"字的学生信息
30. 查询同名同性学生名单，并统计同名人数 
31. 查询1990年出生的学生名单 
32. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列 
33. 查询平均成绩大于等于85的所有学生的学号. 姓名和平均成绩 
34. 查询课程名称为"数学"，且分数低于60的学生姓名和分数 
35. 查询所有学生的课程及分数情况； 
36. 查询任何一门课程成绩在70分以上的姓名. 课程名称和分数； 
37. 查询不及格的课程
38. 查询课程编号为01且课程成绩在80分以上的学生的学号和姓名； 
39. 求每门课程的学生人数 
40. 查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩
    1. 当最高分只有一个时
    2. 当最高分出现多个时
41. 查询不同课程成绩相同的学生的学生编号. 课程编号. 学生成绩 
42. 查询每门功成绩最好的前两名 
43. 统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列  
44. 检索至少选修两门课程的学生学号 
45. 查询选修了全部课程的学生信息 
46. 查询各学生的年龄
    1. 只按照年份来算
    2. 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一
47. 查询本周过生日的学生
48. 查询下周过生日的学生
49. 查询本月过生日的学生
50. 查询下月过生日的学生



