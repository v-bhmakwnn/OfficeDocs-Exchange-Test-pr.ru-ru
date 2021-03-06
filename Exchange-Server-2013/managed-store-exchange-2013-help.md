﻿---
title: 'Управляемое хранилище: Exchange 2013 Help'
TOCTitle: Управляемое хранилище
ms:assetid: efdaf80b-335c-491c-8eb5-1fafd297e8a2
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Dn792020(v=EXCHG.150)
ms:contentKeyID: 62607045
ms.date: 04/30/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Управляемое хранилище

 

_**Применимо к:** Exchange Server 2013_

_**Последнее изменение раздела:** 2014-07-14_

Все предыдущие версии Exchange Server, от Exchange Server 4.0 до Exchange Server 2010, поддерживали запуск единого экземпляра процесса хранилища данных (Store.exe) на роли сервера почтовых ящиков. В этом едином хранилище размещены все базы данных на сервере: активные, пассивные, изолированные и базы данных восстановления. В предыдущих архитектурах Exchange существует незначительная изоляция между различными базами данных, размещенными на сервере почтовых ящиков. Проблемы с единой базой данных почтовых ящиков могут негативно сказаться на всех остальных базах данных, а аварийное завершение в результате повреждения почтового ящика может повлиять на работу служб для всех пользователей, базы данных которых размещены на таком сервере.

Еще одна трудность при использовании единого экземпляра хранилища в предыдущих версиях Exchange состоит в том, что расширенный обработчик хранилищ (ESE) хорошо масштабируется для 8–12 процессорных ядер, а кроме этого взаимодействие между процессорами и проблемы синхронизации кэша могут привести к отрицательному масштабированию. В условиях использования современных серверов с системами с 16 и большим числом ядер это усложняет администрирование, ведь придется управлять сходством 8–12 ядер для ESE, а остальные ядра использовать для процессов, не связанных с хранилищем (например, для помощников, платформ поиска, управляемой доступности и т. п.). Кроме того, предыдущая архитектура ограничивала увеличение масштаба для процессов хранилища.

За многие годы процесс Store.exe был значительно усовершенствован, как и Exchange Server, но в конечном итоге его масштабируемость как одного процесса ограничена и представляет единственную точку отказа. Из-за этих ограничений в Exchange 2013 процесс Store.exe заменен на управляемое хранилище.

## Управляемое хранилище

Управляемое хранилище — это имя процессов банка данных (или хранилища) в Exchange Server 2013. Управляемое хранилище использует модель процессов-контроллеров и рабочих процессов, которая обеспечивает изоляцию процессов хранилища и более быструю отработку отказа базы данных. Оно также включает новый механизм кэширования статических баз данных, который заменяет алгоритм динамической буферизации в предыдущих версиях Exchange Server. В модели нескольких процессов, используемой в управляемом хранилище, для каждой подключенной базы данных есть один процесс-контроллер службы хранилища (в этом случае Microsoft.Exchange.Store.Service.exe, он же — MSExchangeIS) и один рабочий процесс (в этом случае Microsoft.Exchange.Store.Worker.exe). После подключения базы данных создается экземпляр нового рабочего процесса, который обслуживает только эту базу данных. После отключения базы данных ее рабочий процесс завершается.

Например, если на сервере подключено 40 баз данных, для управляемого хранилища будет выполняться 41 процесс: по одному для каждой базы данных и один для процесса-контроллера службы хранилища.

Процесс-контроллер службы хранилища — очень тонкий и надежный. Но в случае его прекращения или завершения прекращаются все его рабочие процессы (они обнаружат, что процесс-контроллер службы исчез и выйдут). Процесс-контроллер хранилища наблюдает за работоспособностью всех рабочих процессов на сервере. Принудительное или неожиданное завершение процесса Microsoft.Exchange.Store.Service.exe приводит к немедленной отработке отказа всех копий активных баз данных. Управляемое хранилище также тесно интегрировано со службой репликации Microsoft Exchange (MSExchangeRepl.exe) и компонентом Active Manager. Процесс-контроллер, рабочие процессы и служба репликации вместе работают над обеспечением большей доступности и надежности.

  - Процесс службы репликации Microsoft Exchange (MSExchangeRepl.exe):
    
      - отвечает за выдачу хранилищу операций по подключению и отключению;
    
      - инициирует действие по восстановлению хранилища или базы данных после сбоя, о котором сообщило хранилище, расширенный обработчик хранилищ (ESE) и ответчики управляемой доступности;
    
      - обнаруживает неожиданные сбои баз данных;
    
      - предоставляет интерфейс администрирования задач управления.

  - Процесс службы хранилища или контроллер (Microsoft.Exchange.Store.Service.exe):
    
      - управляет жизненным циклом каждого рабочего процесса на основании операций по подключению и отключению, полученных из службы репликации;
    
      - обрабатывает входящие запросы из диспетчер служб Windows;
    
      - записывает в журнал элементы со сбоями в случае обнаружения проблем с рабочими процессами (например, завершение или неожиданный выход);
    
      - завершает рабочие процессы хранилища в ответ на отработку отказа.

  - Рабочий процесс хранилища (Microsoft.Exchange.Store.Worker.exe):
    
      - отвечает за выполнение операций RPC для почтовых ящиков в базе данных;
    
      - экземпляр конечной точки RPC в рабочем процессе — это GUID базы данных;
    
      - предоставляет кэш базы данных для базы данных.

## Алгоритм кэширования статических баз данных

Из Exchange 2013 исключен алгоритм кэширования баз данных (или динамическое распределение буферизации), введенный в Exchange Server 5.5 и используемый банком данных в Exchange 2000 Server, Exchange Server 2003, Exchange Server 2007 и Exchange Server 2010. Exchange 2013 использует очень простой алгоритм для определения кэша базы данных. Управляемое хранилище больше не перераспределяет кэш между базами данных в случае возникновения отказа. Это значительно упрощает управление внутренним кэшем. Вместо этого для кэша каждой базы данных (например, для каждого рабочего процесса хранилища) выделяется память на основе количества копий локальных баз данных и значения параметра *MaximumActiveDatabases*, если он настроен. Если значение параметра *MaximumActiveDatabases* превышает количество текущих копий баз данных, кэш рассчитывается исходя из количества копий баз данных.

Статический алгоритм, используемый в Exchange 2013, выделяет память для кэша ESE каждого рабочего процесса хранилища исходя из физической оперативной памяти. Иными словами, это *максимальный целевой кэш* базы данных. На кэш ESE выделено 25 % всей памяти сервера. Иными словами, это *целевой размер кэша сервера*.

> [!NOTE]  
> Целевой размер кэша сервера, как и объем памяти, выделенный в хранилище для кэша ESE, можно переопределить с помощью атрибута <em>msExchESEParamCacheSizeMax</em> объекта <em>InformationStore</em> в Active Directory (заданное значение — это количество страниц по 32 КБ, которые выделяются для всех процессов в хранилище).


Для активных и пассивных копий выделяется статический объем этого кэша. Для рабочих процессов хранилища максимальный целевой кэш выделяется только при обслуживании копии активной базы данных. Для копий пассивных баз данных выделяется 20 % максимального целевого кэша. Остальную часть резервирует хранилище и выделяет ее для рабочих процессов при переходе базы данных из пассивного в активное состояние.

Максимальный целевой кэш рассчитывается только во время запуска хранилища. Потому в случае добавления или удаления баз данных или их копий необходимо перезапустить службу контроллера хранилища (MSExchangeIS) для соответствующей регулировки кэша. Если не перезапустить службу, целевой размер кэша новых баз данных будет меньше, чем у баз данных, созданных до запуска службы. В этом случае сумма целевых размеров кэшей баз данных скорее всего будет превышать целевой размер кэша сервера, пока не будет перезапущена служба MSExchangeIS.

## Примеры расчетов кэша базы данных

Ниже приведены расчеты кэша базы данных на основе объема памяти сервера почтовых ящиков и конфигурации базы данных.

**Пример 1**

В этом примере объем памяти сервера почтовых ящиков — 48 ГБ. На нем размещены две активных и две пассивных базы данных. Кроме того, параметр *MaximumActiveDatabases* не настроен. В этой конфигурации количество кэша базы данных составляет 3 ГБ для каждого рабочего процесса копии активной базы данных и 0,6 ГБ — для каждого рабочего процесса копии пассивной базы данных. Эти значения получены следующим образом.

Чтобы узнать целевой размер кэша сервера, умножьте объем памяти на 25 %:

48 ГБ X 25 % = 12 ГБ

Чтобы определить максимальный целевой кэш базы данных, разделите целевой размер кэша сервера на общее количество активных и пассивных баз данных:

12 ГБ/4 базы данных = 3 ГБ

Чтобы определить объем памяти, используемый копиями пассивных баз данных, умножьте максимальный целевой кэш базы данных на 20 %:

3 ГБ X 20 % = 0,6 ГБ

Из 12 ГБ памяти, выделенных на целевой размер кэша сервера, 7,2 ГБ будут использовать рабочие процессы базы данных, а 4,8 ГБ хранилище данных зарезервирует для двух копий пассивных баз данных на тот случай, если они станут активными. В таком случае они будут использовать максимальный целевой кэш в размере 3 ГБ.

**Пример 2**

В этом примере объем памяти сервера почтовых ящиков — 48 ГБ. На нем размещены две активных и две пассивных базы данных. Для параметра *MaximumActiveDatabases* задано значение 2. В этой конфигурации количество кэша базы данных составляет 5 ГБ для каждого рабочего процесса копии активной базы данных и 0,2 ГБ — для каждого рабочего процесса копии пассивной базы данных. Эти значения получены следующим образом.

Чтобы узнать целевой размер кэша сервера, умножьте объем памяти на 25 %:

48 ГБ X 25 % = 12 ГБ

Чтобы определить максимальный целевой кэш базы данных, разделите целевой размер кэша сервера на сумму общего количества активных баз данных и общего количества пассивных баз данных, умноженного на 20 %:

12 ГБ/(2А + (2П X 20 %)) = 5 ГБ

Чтобы определить объем памяти, используемый копиями пассивных баз данных, умножьте максимальный целевой кэш базы данных на 20 %:

5 ГБ X 20 % = 1 ГБ

Из 12 ГБ памяти, выделенных на целевой размер кэша сервера, рабочие процессы базы данных будут использовать 12 ГБ, а хранилище данных не будет резервировать память для двух копий пассивных баз данных, так как в этой конфигурации они не могут стать активными (ведь для параметра *MaximumActiveDatabases* задано значение 2, а на сервере уже есть 2 копии активных баз данных).

