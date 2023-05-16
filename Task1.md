## Задача 1
Задание:
Для каждого дня, представленного в таблицах user_actions и courier_actions, рассчитайте следующие показатели:

Число новых пользователей.
Число новых курьеров.
Общее число пользователей на текущий день.
Общее число курьеров на текущий день.
Колонки с показателями назовите соответственно new_users, new_couriers, total_users, total_couriers. Колонку с датами назовите date. Проследите за тем, чтобы показатели были выражены целыми числами. Результат должен быть отсортирован по возрастанию даты.

Поля в результирующей таблице: date, new_users, new_couriers, total_users, total_couriers

```sql
with new_users_table as (SELECT time,
                                count(distinct user_id) as new_users
                         FROM   (SELECT user_id,
                                        min(time)::date as time
                                 FROM   user_actions
                                 GROUP BY user_id
                                 ORDER BY user_id) min_user_time
                         GROUP BY time), new_couriers_table as (SELECT time,
                                              count(distinct courier_id) as new_couriers
                                       FROM   (SELECT courier_id,
                                                      min(time)::date as time
                                               FROM   courier_actions
                                               GROUP BY courier_id
                                               ORDER BY courier_id) min_courier_time
                                       GROUP BY time)
SELECT new_users_table.time as date,
       new_users,
       new_couriers,
       (sum(new_users) OVER(ORDER BY new_users_table.time))::integer as total_users,
       (sum(new_couriers) OVER(ORDER BY new_users_table.time))::integer as total_couriers
FROM   new_users_table join new_couriers_table using(time)
```