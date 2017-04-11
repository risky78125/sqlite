#SQL练习题

1. 查询"01"课程比"02"课程成绩高的学生的信息及课程分数
    
    1. 查询同时存在"01"课程和"02"课程的情况
        
        ```
        select A.*, B.C_SCORE[1课程分数], C.C_SCORE[2课程分数] from T_STUDENT AS A, T_SCORE AS B, T_SCORE AS C
        where A.C_STUDENT_NUM = B.C_STUDENT_NUM 
        and A.C_STUDENT_NUM = C.C_STUDENT_NUM 
        AND B.C_COURSE_NUM = 1 
        AND C.C_COURSE_NUM = 2 
        AND B.C_SCORE > C.C_SCORE;
        ```
        
    2. 查询同时存在"01"课程和"02"课程的情况和存在"01"课程但可能不存在"02"课程的情况

        ```
        select A.* , B.C_SCORE [课程"01"的分数],C.C_SCORE [课程"02"的分数] from T_STUDENT AS A 
        left join T_SCORE AS B on A.C_STUDENT_NUM = B.C_STUDENT_NUM and B.C_COURSE_NUM = 1
        left join T_SCORE AS C on A.C_STUDENT_NUM = C.C_STUDENT_NUM and C.C_COURSE_NUM = 2
        where B.C_SCORE > IFNULL(C.C_SCORE,0);
        ```

2. 查询"01"课程比"02"课程成绩低的学生的信息及课程分数

    1. 查询同时存在"01"课程和"02"课程的情况
    2. 查询同时存在"01"课程和"02"课程的情况和不存在"01"课程但存在"02"课程的情况

3. 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

    ```
    select A.C_STUDENT_NUM , A.C_NAME , avg(B.C_SCORE) avg_score
    from T_STUDENT AS A , T_SCORE AS B
    where A.C_STUDENT_NUM = B.C_STUDENT_NUM
    group by A.C_STUDENT_NUM , A.C_NAME
    having avg(B.C_SCORE) >= 60 
    order by A.C_STUDENT_NUM;
    ```

4. 查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

    1. 查询在sc表存在成绩的学生信息的SQL语句。
    2. 查询在sc表中不存在成绩的学生信息的SQL语句。

5. 查询所有同学的学生编号. 学生姓名. 选课总数. 所有课程的总成绩
    1. 查询所有有成绩的SQL。

        ```
        SELECT A.C_STUDENT_NUM, A.C_NAME, COUNT(B.C_STUDENT_NUM), SUM(B.C_SCORE)
        FROM T_STUDENT AS A, T_SCORE AS B
        WHERE A.C_STUDENT_NUM = B.C_STUDENT_NUM
        GROUP BY A.C_STUDENT_NUM, A.C_NAME;
        ```
    
    2. 查询所有(包括有成绩和无成绩)的SQL。

        ```
        SELECT A.C_STUDENT_NUM, A.C_NAME, COUNT(B.C_STUDENT_NUM), SUM(B.C_SCORE)
        FROM T_STUDENT AS A 
        LEFT JOIN T_SCORE AS B 
        ON A.C_STUDENT_NUM = B.C_STUDENT_NUM
        GROUP BY A.C_STUDENT_NUM, A.C_NAME;
        ```
6. 查询"李"姓老师的数量 

    ```
    select count(C_TEACHER_NAME) from T_TEACHER where C_TEACHER_NAME like '李%';
    ```
    
7. 查询学过"张三"老师授课的同学的信息

    ```
    select T_STUDENT.*
    from T_STUDENT, T_COURSE, T_TEACHER, T_SCORE
    where T_TEACHER.C_TEACHER_NUM = T_COURSE.C_TEACHER_NUM
    and T_COURSE.C_COURSE_NUM = T_SCORE.C_COURSE_NUM
    and T_SCORE.C_STUDENT_NUM = T_STUDENT.C_STUDENT_NUM
    and T_TEACHER.C_TEACHER_NAME = '张三';
    ```
8. 查询没学过"张三"老师授课的同学的信息 

    ```
    SELECT T_STUDENT.* FROM T_STUDENT
    WHERE T_STUDENT.C_STUDENT_NUM NOT IN (
    select T_STUDENT.C_STUDENT_NUM
    from T_STUDENT, T_COURSE, T_TEACHER, T_SCORE
    where T_TEACHER.C_TEACHER_NUM = T_COURSE.C_TEACHER_NUM
    and T_COURSE.C_COURSE_NUM = T_SCORE.C_COURSE_NUM
    and T_SCORE.C_STUDENT_NUM = T_STUDENT.C_STUDENT_NUM
    and T_TEACHER.C_TEACHER_NAME = '张三'
    );
    ```
9. 查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

    ```
    SELECT T_STUDENT.* FROM T_STUDENT, T_SCORE AS A, T_SCORE AS B
    WHERE T_STUDENT.C_STUDENT_NUM = A.C_STUDENT_NUM
    AND T_STUDENT.C_STUDENT_NUM = B.C_STUDENT_NUM
    AND A.C_COURSE_NUM = 1
    AND B.C_COURSE_NUM = 2;
    ```
10. 查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

    ```
    SELECT T_STUDENT.* 
    FROM T_STUDENT, T_SCORE
    WHERE T_STUDENT.C_STUDENT_NUM = T_SCORE.C_STUDENT_NUM
    AND T_SCORE.C_COURSE_NUM = 1
    AND T_STUDENT.C_STUDENT_NUM NOT IN (
    SELECT T_STUDENT.C_STUDENT_NUM 
    FROM T_STUDENT, T_SCORE
    WHERE T_STUDENT.C_STUDENT_NUM = T_SCORE.C_STUDENT_NUM
    AND T_SCORE.C_COURSE_NUM = 2
    );
    ```
11. 查询没有学全所有课程的同学的信息 

    ```
    SELECT T_STUDENT.*
    FROM T_STUDENT, T_SCORE
    WHERE T_STUDENT.C_STUDENT_NUM = T_SCORE.C_STUDENT_NUM
    GROUP BY C_NAME
    HAVING COUNT(C_NAME) < (
    SELECT COUNT(T_COURSE.C_COURSE_NUM) FROM T_COURSE)
    ORDER BY T_STUDENT.C_STUDENT_NUM;
    ```
12. 查询至少有一门课与学号为"01"的同学所学相同的同学的信息 
13. 查询和"01"号的同学学习的课程完全相同的其他同学的信息 
14. 查询没学过"张三"老师讲授的任一门课程的学生姓名 
15. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 
16. 检索"01"课程分数小于60，按分数降序排列的学生信息

    ```
    SELECT T_STUDENT.*, T_SCORE.C_SCORE
    FROM T_STUDENT, T_SCORE
    WHERE T_STUDENT.C_STUDENT_NUM = T_SCORE.C_STUDENT_NUM
    AND T_SCORE.C_COURSE_NUM = 1
    AND T_SCORE.C_SCORE < 60
    ORDER BY T_SCORE.C_SCORE DESC;
    ```
17. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

    ```
    SELECT A.C_STUDENT_NUM, A.C_NAME, B.C_SCORE[语文], C.C_SCORE[数学], D.C_SCORE[英语], AVG(E.C_SCORE) avg_score
    FROM T_STUDENT AS A
    LEFT JOIN T_SCORE AS B 
    ON A.C_STUDENT_NUM = B.C_STUDENT_NUM
    AND B.C_COURSE_NUM = 2
    LEFT JOIN T_SCORE AS C
    ON A.C_STUDENT_NUM = C.C_STUDENT_NUM
    AND C.C_COURSE_NUM = 1
    LEFT JOIN T_SCORE AS D
    ON A.C_STUDENT_NUM = D.C_STUDENT_NUM
    AND D.C_COURSE_NUM = 3
    LEFT JOIN T_SCORE AS E
    ON A.C_STUDENT_NUM = E.C_STUDENT_NUM
    GROUP BY E.C_STUDENT_NUM
    ORDER BY avg_score DESC;
    ```

    ```
    select A.C_STUDENT_NUM 学生编号 , A.C_NAME 学生姓名 ,
        max(case C.C_COURSE_NAME when '语文' then B.C_SCORE else null end) [语文],
        max(case C.C_COURSE_NAME when '数学' then B.C_SCORE else null end) [数学],
        max(case C.C_COURSE_NAME when '英语' then B.C_SCORE else null end) [英语],
        avg(B.C_SCORE) 平均分
    from T_STUDENT AS A 
    left join T_SCORE AS B on A.C_STUDENT_NUM = B.C_STUDENT_NUM
    left join T_COURSE AS C on B.C_COURSE_NUM = C.C_COURSE_NUM
    group by A.C_STUDENT_NUM , A.C_NAME
    order by 平均分 desc;
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

