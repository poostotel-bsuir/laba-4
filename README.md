# 🎯 Lab 4: Monitoring and maintaining of SQL Server

## 💡 Introduction
The DBMS Semester 2 curriculum involves learning how to administer, maintain and manage data warehouse infrastructure. As part of the curriculum update, it was decided to provide students with the opportunity to perform laboratory work using containerisation tools instead of virtualisation tools. This series of GitHub materials will provide a methodological guide for performing basic tasks from the mandatory labs using Docker containers with MS SQL Server on board.

**The report to the laboratory work should be prepared in Microsoft Word according to ***СТП 2024*** and attached in .pdf format to the repository files.**

> Happy Hunger Games and may the odds be ever in your favor!

## 🔗 Resources

- [СТП 2024](https://www.bsuir.by/m/12_100229_1_185586.pdf)

- [Инструкция по установке Docker Desktop и развёртыванию вашего первого контейнера с MS SQL Server](https://github.com/poostotel-bsuir/laba-1/blob/main/%D0%98%D0%BD%D1%81%D1%82%D1%80%D1%83%D0%BA%D1%86%D0%B8%D1%8F_%D0%BF%D0%BE_MS_SQL_%D0%BD%D0%B0_%D0%B1%D0%B0%D0%B7%D0%B5_Docker_Desktop.docx)

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

- [Configure Database Mail](https://learn.microsoft.com/en-us/sql/relational-databases/database-mail/configure-database-mail?view=sql-server-ver15)

- [Configure SQL Server Agent Mail to Use Database Mail](https://learn.microsoft.com/en-us/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail?view=sql-server-ver15)

- [SQL Server Index Architecture and Design Guide](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-index-design-guide?view=sql-server-ver15)

- [SQL Server Index Basics](https://www.red-gate.com/simple-talk/databases/sql-server/learn/sql-server-index-basics/)

## 📚 Objective
Familiarise yourself with Database Mail automatic mailing tools. Work with the SMTP protocol used to create operator mail. Develop sessions of automatic logging. Learn to handle interlocks that occur as a result of two transactions accessing the same data in order to change it.

## 📝 Tasks

### 📌 Task 1: Create alert
- Create alert, that should monitor deadlock occurrence. Imitate deadlock, counter of alert should be changed after deadlock.

### 📌 Task 2: Create a database mail account
- Create a database mail account, that could send notifications. Use your custom email system.

### 📌 Task 3: Repeat deadlock
- Repeat deadlock, receive email about its occurrence.

### ⚡ Extra task 1: Create an SQL Agent Job
- Create an SQL Agent Job, that will run backup of any DB on the second SQL server.
- Execute it successfully 

### ⚡ Extra task 2: Create extended events logging

### ⚡ Extra task 3: Create a Custom SQL job

### ⚡ Extra task 4: Create a scheduled Windows task
- Create a scheduled Windows (not SQL job) task, that should analyze deadlock occurrence according the documentation, and send a report, that list of deadlocks could be found in the .xel file.

### ⚡ Extra task 5: Creating daily deadlock report mailing
- Create simple job with the name like 'deadlock filtering' for each server with any Powershell script execution - even run command "get-date".
-	In the Advanced properties of Powershell execution step Set 'lock.txt' output file in the same directory as *.xel report file located. It could be any with .txt extension.
-	For each alert set additional response - "execute job" - and select job, created in the previous step.
-	Create scheduled windows job, that will run  a PS script to write a message to an email if it found a ‘lock’ file that newer than an hour. 


## 📙 Manual

### 🟡 Настройка профиля Database Mail

Откройте компонент Database Mail (управление -> Компонент Database Mail -> Настроить компонент Database Mail).

Поиск компонента Database Mail

![image](https://github.com/user-attachments/assets/c87a3760-0af1-4449-80b5-c82950a7aa97)

Откройте мастер настройки компонент, нажмите далее, выберите пункт “Установить компонент Database Mail, выполнив следующие задачи:”, нажмите далее и укажите имя профиля на латинице, например MySMTPprofile, добавьте учётную запись SMTP. В качестве учётной записи можно использовать: yandex, outlook365 и др. (gmail использовать не рекомендуется так как в январе 2025 года для аккаунтов Google Workspace была прекращена поддержка устройств, сторонних и менее защищенных приложений, которые позволяют при входе в аккаунт указывать только имя пользователя и пароль).

Созданный профиль сделать профилем по умолчанию и указать как открытый.

Мастер настройки компонента Database Mail

![image](https://github.com/user-attachments/assets/92304e09-2a2e-4a7d-8716-25d63996cc8d)

Создание учетной записи

![image](https://github.com/user-attachments/assets/5c6a82b6-4b37-47f3-8790-d5aa70251517)

Результат настройки компонента Database Mail

![image](https://github.com/user-attachments/assets/6b771231-f0cc-4e34-8207-4be2f6abcf38)

Далее необходимо проверить корректность настройки компонента путём отправки тестового письма на почту.

Отправка письма на почту

![image](https://github.com/user-attachments/assets/5163d21c-fd26-4a2c-8d3b-306fed59d383)

Если письмо на указанную почту не пришло, подробную информацию ошибки можно увидеть в журнале компонента Database Mail.

>Ошибки и возможные их решения:
- > The mail could not be sent to the recipients because of the mail server failure. – выбранный smtp-сервер не поддерживает протокол IMAP или же разрешение на использование данного протокола не было предоставлено в настройках почты
- > profile name is not valid – неверное имя профиля (создания профиля используя символы кириллицы). Необходимо удалить созданный профиль и создать новый.

### 🟡 Настройка SQL Agent

В свойствах SQL Agent включите почтовый профиль.

Включение почтового профиля

![image](https://github.com/user-attachments/assets/60450891-e362-4b79-9a3d-dbaa9ce85f75)

Так как SSMS имеет полную поддержку взаимодействия с SQL Agent через службу Windows, то при попытке изменить конфигурацию SQL Agent в рамках docker-среды, будет получена ошибка, для решения данной проблемы необходимо перейти в терминал и подключиться к текущему контейнеру и выполнить следующие команды:
```powershell
docker exec -it my_vm1 bash
```

```powershell
/opt/mssql/bin/mssql-conf list
```

> Здесь мы устанавливаем новый созданный нами ранее почтовый профиль - MySMTPprofile.

```powershell
/opt/mssql/bin/mssql-conf set sqlagent.databasemailprofile MySMTPprofile
```

Затем перезапустите контейнер:
```powershell
docker stop my_vm1
```

```powershell
docker start my_vm2
```

Далее создадим нового оператора для отправки уведомлений, например при успешном завершении задания будем высылать письмо на почту.

Создание оператора

![image](https://github.com/user-attachments/assets/f0f38d67-74f7-4bb4-a939-8f24e5d66191)

Перейдём в свойства существующего задания и добавим уведомление.

Настройка уведомления

![image](https://github.com/user-attachments/assets/b25b2e94-31c8-4f31-b348-38cf5885040f)

Теперь при успешном выполнении задания, с помощью настроенного нами ранее профиля Database Mail, будет происходит отправка письма на почту оператора NewOperator.

Можно также убедиться, что письмо было отправлено, перейдя в журнал компонента Database Mail.

Проверка письма на почте

![image](https://github.com/user-attachments/assets/58f3695f-caec-4fce-8831-8382100d6e8b)

### 🟡 Логирование deadlock

Далее необходимо включить логирование взаимоблокировок(deadlock), а также включить трассировку следующими командами:
```sql
-- Включить трассировку ошибок взаимоблокировок
USE MASTER;	
EXEC master.sys.sp_altermessage 1205, 'WITH_LOG', TRUE;
GO
EXEC master.sys.sp_altermessage 3980, 'WITH_LOG', TRUE;
GO
-- Включить расширенный отчет об ошибках, который используется 
--«Расширенными событиями»
DBCC TRACEON (1204, -1);
DBCC TRACEON (1222, -1);
```

Далее необходимо создать новый сеанс расширенных событий для мониторинга взаимоблокировок. Для этого перейдите в Управление – Расширенные события. Правой кнопкой мыши выберите “Новый мастер сеанса”. В качестве шаблона сеанса событий выберите “TSQL-Locks”, далее на вкладке выбранных событий уберите все sp_statement и sql_statement события, а также в поиске найдите событие error_reported и перенесите его в выбранные и также добавьте фильтр необходимых сообщений об ошибке

Добавление фильтрации событий

![image](https://github.com/user-attachments/assets/7cfd8175-2ec1-4b5f-8ee2-9c79b7576fce)

Также укажите способ сбора данных, указав сохранение данных в файл, имя файла на сервере оставьте по умолчанию, путь по умолчанию, по которому будет сохранён файл в рамках докер контейнера: /var/opt/mssql/log тоесть, в смонтированной папке которую мы указали в команде создания контейнера (см. лаб 1): D:\vm1\Logs.

Способ сбора данных для анализа

![image](https://github.com/user-attachments/assets/e86b8e64-602f-4633-a7f7-d69aeff04fd0)

Нажмите “Готово”. Запустите сеанс и перейдите в смонтированную папку и убедитесь, что файл журнала событий создан.

Спроецируем взаимоблокировку и убедимся, что созданный сеанс событий “DeadLockMonitor” работает корректно.

Откройте журнал событий сеанса, а также две вкладки в SSMS, в каждой из которых выполните одновременно команды:
```sql
--для первой вкладки:
USE myTest_db
BEGIN TRAN;
UPDATE DimProduct
SET DimProduct.EnglishDescription = 'Adjustable Race'
WHERE DimProduct.ProductKey = 1;
-- Подождите 5-10 секунд, чтобы вторая транзакция заблокировала другой ресурс
WAITFOR DELAY '00:00:10';
UPDATE DimAccount
SET AccountCodeAlternateKey = 8050
WHERE AccountKey = 89;
COMMIT;
--для второй вкладки:
USE myTest_db
BEGIN TRAN;
UPDATE DimAccount
SET AccountCodeAlternateKey = 8050
WHERE AccountKey = 89;
WAITFOR DELAY '00:00:10';
UPDATE DimProduct
SET DimProduct.EnglishDescription = 'Adjustable Race1'
WHERE DimProduct.ProductKey = 1;
COMMIT;
```

Ошибка взаимоблокировки

![image](https://github.com/user-attachments/assets/b85e48a8-75ad-4939-8243-493b65823e71)

Записи в журнале сеанса

![image](https://github.com/user-attachments/assets/5934fbce-f0d4-4197-902a-2296b58413e1)

Теперь создадим задачу для рассылки уведомления при возникновении взаимоблокировок.

Создадим служебную таблицу для хранения времени последней проверки:
> Замените почту и название .xel файла.
```sql
USE msdb;
GO
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'DeadlockMonitorState')
CREATE TABLE DeadlockMonitorState (
    LastCheckedTime DATETIME,
    LastOffset BIGINT
);

--Создание новой задачи	
EXEC sp_add_job 
    @job_name = 'MonitorDeadlocksAndNotify', 
    @enabled = 1;

EXEC sp_add_jobstep 
    @job_name = 'MonitorDeadlocksAndNotify', 
    @step_name = 'CheckNewDeadlocksAndSendEmail', 
    @subsystem = 'TSQL', 
    @command = '
	    SET QUOTED_IDENTIFIER OFF;
        DECLARE @LastCheckedTime DATETIME;
        DECLARE @LastOffset BIGINT;
        DECLARE @NewDeadlockCount INT;

        SELECT @LastCheckedTime = LastCheckedTime, @LastOffset = LastOffset
        FROM msdb.dbo.DeadlockMonitorState;

        SELECT @NewDeadlockCount = COUNT(*)
        FROM sys.fn_xe_file_target_read_file(''/var/opt/mssql/log/DeadLockMonitor*.xel'', NULL, NULL, NULL)
        WHERE CAST(event_data AS XML).value(''(/event/data[@name="error_number"]/value)[1]'', ''INT'') = 1205
          AND CAST(event_data AS XML).value(''(/event/@timestamp)[1]'', ''DATETIME'') > @LastCheckedTime;

        IF @NewDeadlockCount > 0
        BEGIN
            EXEC msdb.dbo.sp_send_dbmail 
                @profile_name = ''MySMTPprofile'',
                @recipients = ''укажите свою почту@yandex.ru'',
                @subject = ''New Deadlock Detected'',
                @body = ''A new deadlock (error 1205) has been detected. Check Extended Events for details.'';

            UPDATE msdb.dbo.DeadlockMonitorState
            SET LastCheckedTime = GETDATE(),
                LastOffset = (SELECT MAX(file_offset) 
                              FROM sys.fn_xe_file_target_read_file(''/var/opt/mssql/log/DeadLockMonitor*.xel'', NULL, NULL, NULL));
        END',
    @on_success_action = 1;

EXEC sp_add_jobschedule 
    @job_name = 'MonitorDeadlocksAndNotify', 
    @name = 'CheckEvery5Minutes', 
    @freq_type = 4, 
    @freq_interval = 1, 
    @active_start_time = 000000, 
    @freq_subday_type = 4, 
    @freq_subday_interval = 5;

EXEC sp_add_jobserver 
    @job_name = 'MonitorDeadlocksAndNotify', 
    @server_name = N'(local)';
```

Для проверки корректности работы задачи можно выполнить T-SQL скрипт отдельно. Далее воспроизведите взаимоблокировку снова и проверьте что письмо приходит на почту, теперь каждые 5 минут отрабатывает задача, которая проверяет возникали ли новые взаимоблокировки с момента последней проверки, если такие блокировки возникали, то на почту будет отправляться письмо.

Получение уведомления о взаимоблокировке

![image](https://github.com/user-attachments/assets/e840998b-5878-4592-bf18-a41e7b8bf424)

## 🧠 Self-Check Questionnaire

### 🔍 Question 1:
What are the key steps to configure Database Mail in SQL Server, and why is it important to avoid using Gmail for SMTP accounts?

### 🔍 Question 2:
How can Extended Events be used to track deadlocks? Explain the role of error numbers 1205 and 3980 in deadlock logging.

### 🔍 Question 3:
Describe the process of creating an alert for deadlock occurrences. What additional actions can be triggered when a deadlock is detected?

### 🔍 Question 4:
How would you set up an SQL Agent job to automate database backups on a secondary server? What permissions are required?

### 🔍 Question 5:
Provide a T-SQL example to simulate a deadlock between two transactions. What methods can be used to resolve or prevent deadlocks?
