# 牛客SQL刷题记录

**[SQL 1 查询入职时间最晚的员工信息 ](https://www.nowcoder.com/practice/218ae58dfdcd4af195fff264e062138f?tpId=82&tqId=29753&rp=1&ru=/ta/sql&qru=/ta/sql&difficulty=&judgeStatus=&tags=/question-ranking)**

```sql
select * 
from employees
order by hire_date DESC
limit 0, 1;
```

> limit 页数索引从0开始，0最好不要省略。

```sql
select * 
from employees
where hire_date = (
    select hire_date 
    from employees
    order by hire_date DESC
    limit 0, 1
);

select * 
from employees 
where hire_date = (select max(hire_date) from employees)
```

> 报错，Execution Error
>
> SQL_ERROR_INFO: 'Access denied; you need (at least one of) the SYSTEM_USER privilege(s) for this operation'
>
> 迷





**[SQL 10 获取所有非manager的员工emp_no](https://www.nowcoder.com/practice/32c53d06443346f4a2f2ca733c19660c?tpId=82&rp=1&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql&difficulty=&judgeStatus=&tags=&title=&sourceUrl=&gioEnter=menu)**

```sql
select e.emp_no
from employees e
left join dept_manager d
on e.emp_no = d.emp_no
where dept_no is null;


select emp_no
from employees
where emp_no not in (
    select distinct emp_no
    from dept_manager
)
```

> not in 性能太差，每次都找





**[SQL 11 获取所有员工的manger](https://www.nowcoder.com/practice/e50d92b8673a440ebdf3a517b5b37d62?tpId=82&rp=1&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql&difficulty=&judgeStatus=&tags=&title=&sourceUrl=&gioEnter=menu)**

```sql
select e.emp_no, m.emp_no manager
from dept_emp e
left join dept_manager m
on e.dept_no = m.dept_no
where e.emp_no != m.emp_no
```

> 不等于:
>
> <>, != 等价，遇到某值为空时不输出
>
> is not 遇到为null，可以用is not null





**[SQL 12 获取每个部门中当前员工薪水最高的相关信息](https://www.nowcoder.com/practice/4a052e3e1df5435880d4353eb18a91c6?tpId=82&rp=1&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql&difficulty=&judgeStatus=&tags=&title=&sourceUrl=&gioEnter=menu)**

```sql
SELECT d1.dept_no, d1.emp_no, s1.salary
FROM dept_emp as d1
INNER JOIN salaries as s1
ON d1.emp_no=s1.emp_no
AND d1.to_date='9999-01-01'
AND s1.to_date='9999-01-01'
WHERE s1.salary in (SELECT MAX(s2.salary)
FROM dept_emp as d2
INNER JOIN salaries as s2
ON d2.emp_no=s2.emp_no
AND d2.to_date='9999-01-01'
AND s2.to_date='9999-01-01'
AND d2.dept_no = d1.dept_no
)
ORDER BY d1.dept_no;
```

> 不懂







**[SQL 15 查找employees表信息](https://www.nowcoder.com/practice/a32669eb1d1740e785f105fa22741d5c?tpId=82&tqId=29767&rp=1&ru=/ta/sql&qru=/ta/sql&difficulty=&judgeStatus=&tags=/question-ranking)**

```sql
select * from employees
where emp_no & 1
and last_name <> 'Mary'
order by hire_date desc;
```

> 奇数用&
>
> 偶数用emp_no = (emp_no >> 1 << 1)
>
> 不等于用<> 更快一点





**[SQL 18 工资第二高的员工信息(不准用orderby)](https://www.nowcoder.com/practice/c1472daba75d4635b7f8540b837cc719?tpId=82&tags=&title=&difficulty=0&judgeStatus=0&rp=1&sourceUrl=)**

```sql
#构造出第二高的薪水，不具有扩展性
select e.emp_no, s.salary, e.last_name, e.first_name
from employees e
join salaries s
on e.emp_no = s.emp_no
where s.salary = (
    select max(salary)
    from salaries
    where salary < (
        select max(salary)
        from salaries
    )
)

#自连接

```

> **[自连接题解](https://blog.nowcoder.net/n/f35b41269fd84707a748724827510e23?f=comment)**



