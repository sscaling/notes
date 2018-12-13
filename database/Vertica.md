Concepts
--------

-	`Logical Schema` - Defines things found in standard SQL database; schemas, tables, views etc
-	`Physical Schema` - Vertica stores physical data in Projections
	-	`Projection` - like a SQL materialized view. "They store result sets on disk rather than compute them each time they are used in a query. Vertica automatically refreshes these result sets with updated or new data"

### Analytics functions

[LAG](https://www.vertica.com/docs/9.2.x/HTML/Content/Authoring/SQLReferenceManual/Functions/Analytic/LAGAnalytic.htm) / [LEAD](https://www.vertica.com/docs/9.2.x/HTML/Content/Authoring/SQLReferenceManual/Functions/Analytic/LEADAnalytic.htm)

**Preparation**

```
create table lead_lag(a int);
insert into lead_lag values(1);
insert into lead_lag values(2);
insert into lead_lag values(3);
insert into lead_lag values(4);
insert into lead_lag values(5);
```

**Example**

```
select
  a current,
  lag(a, 1) over (order by a) prev,
  lead(a, 1) over (order by a) next
from lead_lag;

 current | prev | next
---------+------+------
       1 |      |    2
       2 |    1 |    3
       3 |    2 |    4
       4 |    3 |    5
       5 |    4 |
(5 rows)
```
