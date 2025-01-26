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

