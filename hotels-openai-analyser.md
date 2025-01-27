# hotels-openai-analyser

## Отчет для анализа работы OpenAi

### Unloading all forge tickets | Выгружаем все SD задачи

```py
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

```py
import tinkoff as tf
import pandas as pd

Z_ENV_MAGE_TOKEN = Z_ENV_KAZ_TOKEN
query = "group='twork_proc' `proc-block`:'IVUggmLOvaxmzJf-VIsEfc'| fields _meta.@processed_date,`proc-run-id`, message"
info = tf.sage_to_df(Z_ENV_MAGE_TOKEN, query,
    date_from='2024-10-01T00:00:01Z',
    date_to='2024-10-30T23:59:59Z',
    size = 50000,
    flatten_objects=True
    )

df = pd.DataFrame(info)

# Проверяем, есть ли данные в DataFrame
if df.empty:
    print("DataFrame пустой. Убедитесь, что запрос возвращает данные.")
else:
    df.rename(columns = {'_meta.@processed_date':'processed_date', 'proc-run-id':'run_id'}, inplace = True )
    df['Text_for_AI'] = df['message'].str.extract(r'"textForAi":"([^"]+)"')
    df['Text_AI'] = df['message'].str.extract(r'"textAi":"([^"]+)"')
    df['Fix'] = df['message'].str.extract(r'"valid":"([^"]+)"')
    df['Cause'] = df['message'].str.extract(r'"cause":"([^"]+)"')
    df['Text_Operator'] = df['message'].str.extract(r'"textOperator":"([^"]+)"')
    df['Note_Topic'] = df['message'].str.extract(r'"noteTopic":"([^"]+)"')
    tf.df_to_gp(df, 'kazarmenkova_gpt_analisis', gp_service = 'gp')

print(info)
```

```sql
drop table if exists gpt_analise_unic;
create table gpt_analise_unic as

SELECT run_id, MAX(processed_date) as processed_date
from kazarmenkova_gpt_analisis
GROUP BY run_id;

SELECT gpt_analise_unic.run_id, 
gpt_analise_unic.processed_date, 
text_for_ai, 
text_ai,
text_operator,
fix,
cause,
note_topic
from gpt_analise_unic, kazarmenkova_gpt_analisis
WHERE gpt_analise_unic.run_id = kazarmenkova_gpt_analisis.run_id 
and gpt_analise_unic.processed_date=kazarmenkova_gpt_analisis.processed_date
```

|text_ai | text_operator | fix | cause | note_topic|
|--- | --- | --- | --- | ---|
|Ранее вы   сделали бронирование отеля №400005555885 на нашем сервисе.\nОбъект размещения   просит гостей напрямую связаться с ними по номеру телефона для уточнения   информации по заселению и времени заезда.\n\nЕсли остались вопросы, вы всегда   можете обратиться к нам в чате.\nДетали бронирования:\nОтель Курорт   Джамайка | Ранее вы сделали бронирование   отеля №400005555885 на нашем сервисе.\nОбъект размещения просит гостей   напрямую связаться с ними по номеру телефона для уточнения информации по   заселению и времени заезда.\n\nЕсли остались вопросы, вы всегда можете   обратиться к нам в чате.\nДетали бронирования:\nОтель Курорт   Джамайка | Нет |  | Отель просит гостя связаться с   ним|
|Ранее вы   сделали бронирование отеля №400005555885 на нашем сервисе.\nДля подтверждения   бронирования и закрытия дат просим внести предоплату 100% по номеру   в течение 24 часов (до 11:00 утра 2 октября 2024). После   этого позвоните в отель, чтобы уточнить детали по вашему заселению. При   возникновении вопросов, обращайтесь.\n\nЕсли у вас возникнут вопросы, мы   всегда готовы вам помочь.\n\n--\nПосле внесения предоплаты, пожалуйста,   отправьте нам скриншот с подтверждением.\nДетали бронирования:\nАпартаменты   Easy Travels | Ранее вы сделали бронирование   отеля №400005555885 на нашем сервисе.\n\nПередаём вам информацию от объекта   размещения:\nДля подтверждения бронирования и закрытия дат просим внести   предоплату 100% в течение 24 часов (до 11:00 утра 2 октября   2024). \nПосле этого позвоните в отель, чтобы уточнить детали по вашему   заселению. При возникновении вопросов, обращайтесь.\n\nЕсли у вас возникнут   вопросы, мы всегда готовы вам помочь.\n\nПосле внесения предоплаты,   пожалуйста, отправьте нам скриншот с подтверждением.\n\nДетали   бронирования:\nАпартаменты Easy   Travels | Да | Текст не френдли, необходимы   небольшие правки | Сообщает о необходимости внести   депозит или предоплату|
|Ранее вы   сделали бронирование отеля №400005555885 на нашем сервисе.\nОбъект размещения   просит гостей напрямую связаться с ними по номеру телефона для уточнения   информации по заселению и времени заезда.\n\nЕсли остались вопросы, вы всегда   можете обратиться к нам в чате.\nДетали бронирования:\nКвартира на   Лихвинцева | Ранее вы сделали бронирование   отеля №400005555885 на нашем сервисе.\nОбъект размещения просит вас напрямую   связаться с ними по номеру телефона для уточнения информации по заселению и   времени заезда | Да | Текст не френдли, необходимы   небольшие правки | Отель просит гостя связаться с   ним|
