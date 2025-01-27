# hotels-notification-analyser

## Отчет для анализа тикетов

### Unloading all forge tickets | Выгружаем все SD задачи

```sql
drop table if exists sd_notification;
create table sd_notification as

SELECT srnumber, prod_v_sse.sd_done_cases_info.subject , status_now,
prod_v_sse.sd_done_cases_info.number ,
prod_v_sse.sd_done_cases_info.createdon_case_dttm ,
prod_v_sse.sd_done_cases_info.case_solution_dttm ,
nohtmlsymptoms
FROM prod_v_sse.sd_done_cases_info
JOIN prod_v_ods_sd.case ON prod_v_sse.sd_done_cases_info.number = prod_v_ods_sd.case.number
WHERE service='Обработка писем Отели Первая линия'
AND createdon_case_dt BETWEEN $d_s
AND $d_e AND prod_v_sse.sd_done_cases_info.case_solution_dttm IS NOT NULL;

SELECT * FROM sd_notification
```


| srnumber | subject | status_now | number | createdon_case_dttm | case_solution_dttm | nohtmlsymptoms |
| ---------| ------- | ---------- | ------ | --------------------| ------------------ | -------------- |
|tcrm_task_353061401 | Информация для гостя по бронированию № 0000000001 | Закрыто (выполнено) | 16271872 | 2024-06-07 02:03:13 | 2024-06-07 02:16:52 | Тестовое содержание письма|
|tcrm_task_346282779 | Передать информацию гостю по бронированию № 0000000002   dates: 25.05.2024 - 26.05.2024 | Закрыто (выполнено) | 16069295 | 2024-05-25 15:41:24 | 2024-05-25 15:45:28 | Тестовое содержание письма|
|tcrm_task_335041459 | Информация для гостя по бронированию № 0000000003 | Закрыто (выполнено) | 15747333 | 2024-05-03 23:52:09 | 2024-05-04 04:55:13 | Тестовое содержание письма|


### Counting all forge tickets per month | Считаем количество SD задач в месяц

```sql
drop table if exists sd_notification;
create table sd_notification as

SELECT srnumber, prod_v_sse.sd_done_cases_info.subject , status_now,
prod_v_sse.sd_done_cases_info.number ,
prod_v_sse.sd_done_cases_info.createdon_case_dttm ,
prod_v_sse.sd_done_cases_info.case_solution_dttm ,
nohtmlsymptoms
FROM prod_v_sse.sd_done_cases_info
JOIN prod_v_ods_sd.case ON prod_v_sse.sd_done_cases_info.number = prod_v_ods_sd.case.number
WHERE service='Обработка писем Отели Первая линия'
AND createdon_case_dt BETWEEN $d_s
AND $d_e AND prod_v_sse.sd_done_cases_info.case_solution_dttm IS NOT NULL;

SELECT * FROM sd_notification
```


|yy | mes | month_name | task | count|
|--- | --- | --- | --- | ---|
|2024 | 5 | May | not null | 9800|
|2024 | 5 | May | null | 780|
|2024 | 6 | June | null | 626|
|2024 | 6 | June | not null | 8398|
|2024 | 7 | July | null | 635|
|2024 | 7 | July | not null | 7400|


### Counting the median of the number of incoming emails | Находим медиану количества поступающих писем

```sql
drop table if exists sd_notification_mes;
create table sd_notification_mes as

SELECT EXTRACT(year from createdon_case_dttm) as yy,
EXTRACT(month from createdon_case_dttm) as mes, 
to_char(createdon_case_dttm, 'Month') AS month_name,
count(number) AS sd_amount
FROM sd_notification
GROUP BY yy,mes,month_name
ORDER BY yy,mes;

SELECT ROUND(PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY sd_amount)) as median
FROM sd_notification_mes;
```

| median |
|--- |
| 9024 |


### Forge tickets SLA: general unloading | SD SLA: Общая выгрузка

```sql
drop table if exists sd_sla_notification;
create table sd_sla_notification as

SELECT prod_v_sse.sd_done_cases_info.createdon_case_dttm 
, prod_v_sse.sd_done_cases_info.case_solution_dttm-prod_v_sse.sd_done_cases_info.createdon_case_dttm as time_work
, prod_v_sse.sd_done_cases_info.number
FROM prod_v_sse.sd_done_cases_info
JOIN prod_v_ods_sd.case ON prod_v_sse.sd_done_cases_info.number = prod_v_ods_sd.case.number
WHERE service='Обработка писем Отели Первая линия' 
AND createdon_case_dt BETWEEN $d_s 
AND $d_e AND prod_v_sse.sd_done_cases_info.case_solution_dttm IS NOT NULL;

SELECT * FROM sd_sla_notification
```

|createdon_case_dttm | time_work | number|
|--- | --- | ---|
|2024-05-11   11:31:05 | 2 days 08:53:35.954516 | 15839290|
|2024-07-07   19:09:44 | 00:48:08.722134 | 16769680|
|2024-05-16   11:34:26 | 00:17:29.411177 | 15917002|
|2024-05-15   09:44:37 | 00:47:37.366719 | 15894011|
|2024-05-28   16:47:31 | 00:42:08.423624 | 16114158|
|2024-07-25   18:13:57 | 00:20:19.506387 | 17074465|
|2024-07-02   16:50:09 | 06:10:29.872216 | 16687136|
