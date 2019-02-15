# 1. 自关联查询
## 1.1. 数据库中的自关联查询
现有表结构如下：
```sql
CREATE TABLE [dbo].[t_news](
	[id] [int] IDENTITY(1,1) NOT NULL,
	[newsname] [varchar](50) NOT NULL,
	[pid] [int] NOT NULL
) ON [PRIMARY]

```
| id | newsname       | pid |
|----|----------------|-----|
| 1  | 娱乐新闻       | 0   |
| 2  | 体育新闻       | 0   |
| 3  | NBA            | 2   |
| 4  | CBA            | 2   |
| 5  | 湖人           | 3   |
| 6  | 火箭           | 3   |
| 7  | 北京篮球俱乐部 | 4   |
| 8  | 上海篮球俱乐部 | 4   |
| 9  | 港台新闻       | 1   |
| 10 | 内地新闻       | 1   |
| 12 | 港台花边       | 9   |
| 13 | 内地明星家务事 | 10  |

id:新闻栏目id,pid：新闻栏目 父栏目的id
根据该表结构简历java实体类如下：
```java
public class NewsLabel {
    private Integer id;
    private String newsname;
    private Set<NewsLabel> children;
    //省略getter,setter,toString方法
}
```
**需求：根据新闻栏目的id查询其及其所有子孙栏目**
在sqlServer中可以使用公用表表达式(CTE)来实现。
公用表表达式特点：公用表表达式 (CTE) 具有一个重要的优点，那就是能够引用其自身，从而创建递归 CTE。递归 CTE 是一个重复执行初始 CTE 以返回数据子集直到获取完整结果集的公用表表达式。
递归CTE的格式如下：
```sql
WITH cte_name ( column_name [,...n] )

AS

(

CTE_query_definition –- Anchor member is defined.

UNION ALL

CTE_query_definition –- Recursive member is defined referencing cte_name.

)

-- Statement using the CTE

SELECT *

FROM cte_name
```
根据上述需求，查询语句如下：
```sql
-- 1、2、3处的column要一致
use news;
with cte_new(id,newsname,pid) --1
as 
(
select id,newsname,pid --2
from t_news where id=2
union all --union all 不能少
 select n.id,n.newsname,n.pid --3
 from cte_new
 inner join t_news n on n.pid=DET_NEW.id
 )
 select * from cte_new
 ```
 **此处发现的不足：学完mybatis后，需要练习50条经典SQL，要能手敲存储过程、游标、索引、自定义函数**
 ## 1.2 mybatis实现递归查询
 需求：根据pid查询，其所有子孙栏目
 mapper.xml
 ```xml
     <select id="selectChildrenByPid" resultMap="newsLabelMapper" resultType="NewsLabel">
        <!--查询A-->
        select id,newsname,pid from t_news
        where pid=#{pid}
    </select>

    <resultMap id="newsLabelMapper" type="NewsLabel">
        <id property="id" column="id"/>
        <result column="newsname" property="newsname"/>
        <collection property="children"  ofType="NewsLabel"
                    select="selectChildrenByPid"
                    column="id"/>
    </resultMap>
```
```sequence
selectChildrenByPid->newsLabelMapper:执行第一次查询,得到pid=m的栏目的id(记为id1)
newsLabelMapper->selectChildrenByPid:执行第二次查询，并得到id2(在第二次查询中pid=id1)
selectChildrenByPid-->newsLabelMapper:执行第N次查询，得到idN
newsLabelMapper-->selectChildrenByPid:执行第N+1次查询，pid=idN，查询执行完毕，封装结果
```
```log
00:00:35.303 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ==>  Preparing: select id,newsname,pid from t_news where pid=? 
00:00:35.393 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ==> Parameters: 1(Integer)
00:00:35.436 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <==    Columns: id, newsname, pid
00:00:35.446 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <==        Row: 9, 港台新闻, 1
00:00:35.448 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ====>  Preparing: select id,newsname,pid from t_news where pid=? 
00:00:35.448 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ====> Parameters: 9(Integer)
00:00:35.449 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <====    Columns: id, newsname, pid
00:00:35.449 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <====        Row: 12, 港台花边, 9
00:00:35.449 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ======>  Preparing: select id,newsname,pid from t_news where pid=? 
00:00:35.449 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ======> Parameters: 12(Integer)
00:00:35.450 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - <======      Total: 0
00:00:35.459 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - <====      Total: 1
00:00:35.460 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <==        Row: 10, 内地新闻, 1
00:00:35.460 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ====>  Preparing: select id,newsname,pid from t_news where pid=? 
00:00:35.460 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ====> Parameters: 10(Integer)
00:00:35.462 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <====    Columns: id, newsname, pid
00:00:35.462 [main] TRACE dao.INewsLabelDao.selectChildrenByPid - <====        Row: 13, 内地明星家务事, 10
00:00:35.463 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ======>  Preparing: select id,newsname,pid from t_news where pid=? 
00:00:35.463 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - ======> Parameters: 13(Integer)
00:00:35.463 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - <======      Total: 0
00:00:35.464 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - <====      Total: 1
00:00:35.464 [main] DEBUG dao.INewsLabelDao.selectChildrenByPid - <==      Total: 2
NewsLabel{id=9, newsname='港台新闻', children=[NewsLabel{id=12, newsname='港台花边', children=[]}]}
NewsLabel{id=10, newsname='内地新闻', children=[NewsLabel{id=13, newsname='内地明星家务事', children=[]}]}
```