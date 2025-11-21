# notification-automation-process
## Ведение процесса автоматизации нотификаций отелей

Отели могут нотифицировать клиентов по конкретным бронированиям. 

**Проблематика:** наличие "легких" нотификаций, которые нужно просто перефразировать для клиента и отправить. Ресурсы сотрудников тратятся на "механическую" работу.
Есть цель автоматизировать такие информирования.

### Так выглядит процесс AS IS:

![image](https://github.com/user-attachments/assets/3f66aa86-7496-4f1b-9596-802123a01477)

**Стороны взаимодействия:**

**Отель** – объект размещения (состоящий из номеров или апартаменты)

**Партнер** – участник взаимодействия между нами и отелем (участвует не везде)

**Поддержка** – наша сторона , которая занимается передачей информации клиенту (когда информация поступила нам в Outlook). Поддержка делиться на 2 типа: Бэк-офис и Первая линия. 

**Бэк-офис** – поддержка, которая занимается решением сложных претензионных обращений. На данную группу направляются информирования, которые были направлены в следствии работ по разрешению клиентских обращений.

**Первая линия** – сотрудники первичной поддержки. Отвечают за "легкие" вопросы клиентов, под которые уже готов скрипт действий. На данную группу направляются первичные информирования от отеля: правила заезда, необходимость внесения депозита, контактная информация и т.д.

Отель направляет нам уведомление для клиента через партнера или напрямую. Письмо обрабатывается тикетной системой "Forge" для распределения письма на группу "Первой линии" или "Бэк-офиса". 
Распределение осуществляется на основании темы письма.

### В рамках исследования предстоит:

1. Изучить ценность и выгоду данной задачи;

2. Найти инструмент, который бы преобразовал информацию от отеля для клиента;

3. Подготоить документацию с описанием работы;

### Исследование:

Для исследования мной был написан [отчет](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/hotels-notification-analyser.md) на SQL

<img src="https://github.com/user-attachments/assets/b949c11c-c462-43f9-a55e-705722e3b158" width="495" height="195">


<img src="https://github.com/user-attachments/assets/2eb61083-4c1b-41bf-b57b-5781ec4b84aa" width="200" height="195">



### Предлагаемый процесс:
Так как направление развивается и к нам подключается все больше и больше отелей, то количество информирований будет расти. В зависимости от сезонности их будет сложнее контролировать. Предлагается перевести этот процесс на автоматическую отправку, заменив ручную проверку и подготовку писем на Open AI. 

### Процесс TO BE:
1. Отель направляет нам на почтовый ящик письмо (поступает в Outlook)
2. Этот ящик привязан к тикет системе Forge. При поступлении письма создается SD , которое дублирует письмо
3. Далее письмо распределяется по сценарию по очередям, так как отель нам может прислать не только уведомление для клиента (ниже представлен сценарий Forge, который я создала сама):
![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/Hotel%20notification%20Forge%20escenario.png)
4. Далее, если тикет попал в очередь для нотификаций клиента, в системе CRM системе создавался task-запрос с текстом полученного письма, который обрабатывался crm ботом по созданной мной процедуре:
![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/C%D1%85%D0%B5%D0%BC%D0%B0%20%D0%B2%D0%B7%D0%B0%D0%B8%D0%BC%D0%BE%D0%B4%D0%B5%D0%B9%D1%81%D1%82%D0%B2%D0%B8%D1%8F%20TCRM%20%D0%B8%20Forge.png)
5. Принцип работы процедуры следующий: 
   - Процедура читает описание созданного таска
   - Проверяет наличие персональных данных
   - В случае наличия персональных данных , маскируем их с помощью адаптированного метода. Маскированные данные процедура запоминает.
   - После маскироваки персональных данных, текст письма подается на Open AI, в которую уже подан ПРОМТ о необходимости подготовки письма для клиента (убрать лишние слова, перевразировать сложные фразы и так далее). Также в ПРОМТ подана необходимость определения темы письма для дальнейшего сбора статистики с целью оптимизаций.
   - После обработки текста Open AI возвращаем в письмо персональные данные
   - Отправляем письмо клиенту вчат банка/ электронную почту
![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/Open%20AI%20road.png)

### Отчётность и анализ
1. Проанализированы доступные инструменты для выгрузки данных: внутренние ноутбуки, Helicopter и Sage.
2. Создана выгрузка данных через Sage для снятия ограничений по содержимому текста.
3. Изучена библиотека TinkoffPy для написания Python-кода и работы с данными из Sage.
4. Написан итоговый отчёт по ключевым метрикам, важным для планирования процесса.
5. Собраны и структурированы результаты тестирования.

### 📊 Результаты
1. За первую половину теста обработано ~7000 кейсов, затем объём снизился до ~4000 (сезонный фактор, отпуска).
2. В среднем за месяц — 10 700 кейсов.

### 🛡️ Качество обработки
1. Общая стабильность работы — 97,3%.
2. 80% коммуникаций, содержащих критичные недочёты, были корректно обработаны.

### Сэкономленная стоимость 
В среднем 3 000 000 RUB  в год

---

## Managing the Hotel Notification Automation Process

Hotels may send notifications to customers regarding specific bookings.

**Problem:** There is a large volume of “simple” notifications that only need to be rephrased and forwarded to the customer. Employee resources are spent on repetitive, mechanical work.  
The goal is to automate this type of communication.

### The AS IS process looks like this:

![image](https://github.com/user-attachments/assets/3f66aa86-7496-4f1b-9596-802123a01477)

**Interaction Parties:**

**Hotel** – the accommodation provider (rooms or apartments).

**Partner** – an intermediary between us and the hotel (involved not in all cases).

**Support Team** – our internal team that relays information to the customer (when the hotel’s message arrives in Outlook). The Support Team is split into two groups: Back Office and Front Line.

**Back Office** – the team that handles complex, complaint-related cases. They receive notifications that appear as part of resolving customer issues.

**Front Line** – primary support agents. They handle “light” requests from customers with predefined scripts. This group receives initial hotel notifications such as check-in rules, deposit requirements, contact details, etc.

The hotel sends a notification for the customer either through a partner or directly. The email is processed by the “Forge” ticketing system, which assigns it to the Front Line or the Back Office.  
Distribution is based on the email subject.

---

### Scope of the research:

1. Analyze the value and benefits of this automation task.
2. Identify a tool capable of transforming hotel messages into customer-friendly communication.
3. Prepare documentation describing the workflow.

---

### Research:

For this project, I prepared an [SQL report](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/hotels-notification-analyser.md).

<img src="https://github.com/user-attachments/assets/b949c11c-c462-43f9-a55e-705722e3b158" width="495" height="195">

<img src="https://github.com/user-attachments/assets/2eb61083-4c1b-41bf-b57b-5781ec4b84aa" width="200" height="195">

---

### Proposed process:

As this direction continues to grow, and more hotels connect to our platform, the volume of notifications will keep increasing. Depending on seasonality, controlling these volumes becomes more difficult.  
It is proposed to transfer this process to automated sending, replacing manual review and email preparation with OpenAI.

---

### TO BE Process:

1. The hotel sends an email to our mailbox (arrives in Outlook).
2. This mailbox is linked to the Forge ticketing system. When an email arrives, Forge creates an SD ticket linked to the message.
3. The email is then routed according to a scenario, because the hotel may send not only customer notifications (below is the Forge scenario I designed):

![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/Hotel%20notification%20Forge%20escenario.png)

4. If the ticket is routed to the customer-notification queue, the CRM system creates a task containing the email text, which is then processed by the CRM bot using a procedure I developed:

![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/C%D1%85%D0%B5%D0%BC%D0%B0%20%D0%B2%D0%B7%D0%B0%D0%B8%D0%BC%D0%BE%D0%B4%D0%B5%D0%B9%D1%81%D1%82%D0%B2%D0%B8%D1%8F%20TCRM%20%D0%B8%20Forge.png)

5. The procedure works as follows:
   - It reads the description of the newly created task.
   - Checks for personal data.
   - If personal data is found, the system masks it using an adapted method. The masked data is stored temporarily.
   - After masking, the email text is submitted to OpenAI with a predefined prompt requesting customer-friendly rephrasing, removal of unnecessary wording, adjustment of complex phrasing, etc.  
     The prompt also instructs OpenAI to identify the topic of the email for statistical and process optimization.
   - After processing, personal data is restored in the output text.
   - The final message is sent to the customer via bank chat or email.

![image](https://github.com/KazzarmenkovaSofia/notification-automation-process/blob/main/Open%20AI%20road.png)

---

### Reporting & Analysis

1. Analyzed available tools for data extraction: internal notebooks, Helicopter, Sage.
2. Created a data export in Sage to remove limitations on text content.
3. Studied the TinkoffPy library to write Python code and work with data from Sage.
4. Prepared a final report with key metrics important for process planning.
5. Collected and structured testing results.

---

### 📊 Results

1. ~7,000 cases processed in the first half of the test, later decreasing to ~4,000 (seasonal factor: vacations).
2. Monthly average — 10,700 cases.

---

### 🛡️ Processing Quality

1. Overall system stability — **97.3%**.
2. **80%** of communications containing critical errors were successfully corrected.

---

### 💰 Cost Savings

Annual cost savings: **3,000,000 RUB**

