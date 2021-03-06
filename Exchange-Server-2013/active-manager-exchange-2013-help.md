﻿---
title: 'Активный диспетчер: Exchange 2013 Help'
TOCTitle: Активный диспетчер
ms:assetid: f4be27b7-1d7c-47b4-87ac-bfdfcc046f00
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Dd776123(v=EXCHG.150)
ms:contentKeyID: 50489524
ms.date: 05/22/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# Активный диспетчер

 

_**Применимо к:** Exchange Server 2013_

_**Последнее изменение раздела:** 2015-04-07_

Microsoft Exchange Server 2013 содержит компонент *Active Manager*, который управляет платформой высокой доступности, включающей группу обеспечения доступности баз данных и копии базы данных почтовых ящиков. Active Manager выполняется внутри службы репликации Microsoft (MSExchangeRepl.exe) на всех серверах почтовых ящиков. На серверах почтовых ящиков, которые не входят в DAG, существует единая роль Active Manager: *автономная служба Active Manager*. На серверах, которые являются участниками группы обеспечения доступности баз данных, существуют две роли Active Manager: *основная служба Active Manager* (PAM) и *резервная служба Active Manager* (SAM). Роль PAM — это экземпляр Active Manager в группе обеспечения доступности баз данных, принимающий решения о том, какие копии будут пассивными, а какие — активными. PAM отвечает за получение уведомлений об изменении топологии и реакцию на сбои серверов. Член группы обеспечения доступности баз данных, которому принадлежит роль PAM, всегда является текущим владельцем ресурса кворума кластера (кластерной группы по умолчанию). Если на сервере-владельце ресурса кворума кластера происходит сбой, роль PAM автоматически перемещается на оставшийся сервер, который становится новым владельцем ресурса кворума кластера. Кроме того, если сервер, на котором размещен ресурс кворума кластера, необходимо перевести в автономный режим для обслуживания или обновления, сначала следует перенести роль PAM на другой сервер группы обеспечения доступности баз данных. Роль PAM контролирует все передвижения активных назначений между копиями баз данных. (Только одна копия в любой момент времени может быть активной, и она может быть подключенной или отключенной.) Роль PAM также выполняет функции роли SAM на локальном компьютере (обнаружение сбоев локальных баз данных и банка данных).

Роль SAM предоставляет сведения о том, на каком сервере размещается активная копия базы данных почтовых ящиков, другим компонентам Exchange, использующим клиентский компонент Active Manager (например, службе клиентского доступа или транспортной службе). Роль SAM обнаруживает сбои локальных баз данных и локального банка данных. Она реагирует на сбои, запрашивая у роли PAM отработку отказа (если база данных реплицирована). Роль SAM не определяет целевой сервер отработки отказа и не обновляет состояние расположения базы данных в роли PAM. Она обращается к состоянию расположения активной копии базы данных для ответа на получаемые запросы активной копии базы данных.

> [!NOTE]  
> Exchange 2013 не является кластерным приложением. Вместо этого функции библиотеки кластеризации clusapi.dll используются для реализации функциональности кластера, групп, сети кластера (пульса), управления узлами, реестра кластера и ряда задач управления. Кроме этого, служба Active Manager хранит сведения о текущем состоянии базы данных почтовых ящиков (данные об активных и пассивных копиях, а также о подключении) в базе данных кластера (также называется реестром кластера). Хотя эти сведения хранятся непосредственно в базе данных кластера, к ним не обращаются напрямую никакие другие компоненты. 


В Exchange 2013 служба репликации Microsoft Exchange периодически отслеживает работоспособность всех подключенных баз данных. Кроме этого, она также отслеживает ошибки и сбои ввода-вывода подсистемы ESE. Если служба обнаруживает сбой, она уведомляет о нем службу Active Manager. Затем Active Manager определяет, какую копию базы данных следует подключить, и что для этого необходимо. Кроме того, она отслеживает активную копию базы данных почтовых ящиков (на основании сведений о копии базы данных, подключенной последней) и предоставляет сведения о результатах отслеживания серверу клиентского доступа, к которому подключен клиент.

## Лучший выбор копирования

При возникновении сбоя, препятствующего доступу к активной копии реплицированной базы данных почтовых ящиков, Active Manager выполняет ряд действий по восстановлению путем выбора оптимальной пассивной копии сбойной базы данных, которая будет активирована. Раньше этот процесс был известен как выбор лучшей копии (BCS) в Exchange 2010, а сейчас он называется выбором лучшей копии и сервера (BCSS) в Exchange 2013. Общий процесс выглядит следующим образом.

1.  Компонент «Управляемая доступность» или Active Manager определяет сбой или администратор инициирует переключение без цели.

2.  PAM запускает внутренний алгоритм BCSS.

3.  Выполняется процесс, называемый *попыткой копирования последних журналов* (ACLL), в ходе которого предпринимается попытка скопировать с сервера все отсутствующие файлы журналов, находившиеся в активной копии базы данных до отказа или переключения.

4.  После завершения процесса ACLL значение *AutoDatabaseMountDial* для серверов почтовых ящиков, на которых хранятся копии базы данных, сравнивается с длиной очереди копирования базы данных, которая активируется. На этом этапе выполняется одно из следующих действий:
    
      - количество отсутствующих файлов журнала равно или меньше значения *AutoDatabaseMountDial*, в таком случае выполняется шаг 5; или
    
      - количество отсутствующих файлов журнала превышает значение *AutoDatabaseMountDial*, в таком случае Active Manager попытается активировать следующую наиболее доступную копию, если такая существует.

5.  PAM создает запрос на установку в банк данных Microsoft Exchange через удаленный вызов процедур (RPC). На этом этапе выполняется одно из следующих действий:
    
      - база данных подключается и становится доступной для клиентов; или
    
      - база данных не подключается, и РАМ выполняет действия 3 и 4 для следующей оптимальной копии (если она доступна).

В Exchange 2010 при процессе BCS оценивался ряд свойств всех копий баз данных, чтобы можно было определить оптимальную для активации копию. К ним относятся:

  - Copy queue length

  - длина очереди воспроизведения;

  - состояние базы данных;

  - Состояние индекса содержимого

В Exchange 2013 Active Manager запускает все те же проверки и фазы BCS, включая кроме них использование ограничения на ухудшение состояния работоспособности. В частности, BCSS включает несколько новых проверок работоспособности, входящих во встроенные компоненты наблюдения «Управляемая доступность» в Exchange 2013. Активный диспетчер теперь выполняет четыре новых дополнительных проверки (перечисление в порядке выполнения):

1.  **Все работоспособны** — проверка сервера с копией затронутой базы данных на работоспособность всех компонентов мониторинга.

2.  **Все нормальные работоспособны** — проверка сервера с копией затронутой базы данных на работоспособность всех компонентов мониторинга с нормальным приоритетом.

3.  **Все лучше источника** — проверка сервера с копией затронутой базы данных на то, что состояние всех компонентов мониторинга лучше, чем у текущего сервера с затронутой копией.

4.  **Аналогично источнику** — проверка сервера с копией затронутой базы данных на то, что состояние всех компонентов мониторинга аналогично текущему серверу с затронутой копией.

Если BCSS будет вызвано в результате отработки отказа, активированного компонентом наблюдения (т. е. через отвечающее устройство отработки отказов), вводится в действие дополнительное обязательное ограничение, устанавливающее, что работоспособность компонента целевого сервера должна быть выше, чем у сервера, на котором произошел сбой. Например, если отказ Microsoft Outlook Web App активирует отработку отказа через отвечающее устройство отработки отказов, BCSS должны выбрать сервер, на котором размещена копия сбойной базы данных, в которой размещено работоспособное приложение OWA.

## Процесс выбора оптимальной копии

По отношению к отказам базы данных (не отказам компонентов) Active Manager в Exchange 2013 выполняет такие же проверки, какие он выполнял в Exchange 2010. Active Manager начинает процесс выбора оптимальной копии с создании списка копий базы данных, которые являются потенциальными кандидатами для активации. Все недоступные или административно заблокированные для активации копии базы данных игнорируются и не используются во время процесса выбора. Порядок расположения копий в списке зависит от значения *AutoDatabaseMountDial*.

  - Если для *AutoDatabaseMountDial* указано любое значение, кроме `Lossless`, на всех серверах, на которых размещена копия базы данных, Active Manager сортирует список результатов, используя длину очереди копирования в качестве первичного ключа. Вычисление выполняется на основании LastLogInspected (с точки зрения копии), поэтому список потенциальных копий сортируется по наибольшему значению LastLogInspected (копия с наименьшей длиной очереди копирования). Если необходимо, Active Manager сортирует список второй раз, используя значение предпочтения активации в качестве вторичного ключа, чтобы разорвать все условия связывания, при которых две или больше пассивных копии имеют одинаковую длину очереди копирования. Копия с наименьшим значением предпочтения активации имеет наибольший приоритет в списке.

  - Если для *AutoDatabaseMountDial* указано значение `Lossless` на всех серверах, на которых размещена копия базы данных, Active Manager сортирует список результатов по возрастанию, используя значение предпочтения активации в качестве первичного ключа. Кроме того, когда администратор выполняет переключение базы данных или сервера без потерь, не указывая цель, Active Manager также сортирует список результатов по возрастанию, используя значение предпочтения активации в качестве первичного ключа.

Далее Active Manager пытается найти в списке копию базы данных почтовых ящиков в состоянии Healthy, DisconnectedAndHealthy, DisconnectedAndResynchronizing или SeedingSource и оценивает потенциал активации каждой копии в списке на основе упорядоченного набора из десяти условий. Active Manager определяет, отвечает ли какой-либо кандидат для активации первому набору условий:

  - Индекс содержимого имеет состояние Healthy (исправен).

  - Длина очереди копирования файлов журнала меньше 10.

  - Длина очереди преобразования файлов журнала меньше 50.

Если ни одна копия базы данных не отвечает первому набору условий, Active Manager пытается найти копию базы данных, отвечающую второму набору условий:

  - Индекс содержимого имеет состояние Crawling (сканирование).

  - Длина очереди копирования файлов журнала меньше 10.

  - Длина очереди преобразования файлов журнала меньше 50.

Если ни одна копия базы данных не отвечает второму набору условий, Active Manager пытается найти копию базы данных, отвечающую третьему набору условий:

  - Индекс содержимого имеет состояние Healthy (исправен).

  - Длина очереди преобразования файлов журнала меньше 50.

Если ни одна копия базы данных не отвечает третьему набору условий, Active Manager пытается найти копию базы данных, отвечающую четвертому набору условий:

  - Индекс содержимого имеет состояние Crawling (сканирование).

  - Длина очереди преобразования файлов журнала меньше 50.

Если ни одна копия базы данных не отвечает четвертому набору условий, Active Manager пытается найти копию базы данных, отвечающую пятому набору условий:

  - Длина очереди преобразования файлов журнала меньше 50.

Если ни одна копия базы данных не отвечает пятому набору условий, Active Manager пытается найти копию базы данных, отвечающую шестому набору условий:

  - Индекс содержимого имеет состояние Healthy (исправен).

  - Длина очереди копирования файлов журнала меньше 10.

Если ни одна копия базы данных не отвечает шестому набору условий, Active Manager пытается найти копию базы данных, отвечающую седьмому набору условий:

  - Индекс содержимого имеет состояние Crawling (сканирование).

  - Длина очереди копирования файлов журнала меньше 10.

Если ни одна копия базы данных не отвечает седьмому набору условий, Active Manager пытается найти копию базы данных, отвечающую восьмому набору условий:

  - Индекс содержимого имеет состояние Healthy (исправен).

Если ни одна копия базы данных не отвечает восьмому набору условий, Active Manager пытается найти копию базы данных, отвечающую девятому набору условий:

  - Индекс содержимого имеет состояние Crawling (сканирование).

Если ни одна копия базы данных не отвечает девятому набору условий, Active Manager пытается активировать любую копию базы данных в состоянии Healthy, DisconnectedAndHealthy, DisconnectedAndResynchronizing или SeedingSource (десятый набор условий). Если копии, отвечающие десятому набору условий, не удается найти, автоматическая активация копии базы данных невозможна.

Как только будут найдены одна или несколько копий, отвечающих хотя бы одному набору условий, процесс ACLL копирует все файлы журналов из источника в потенциальную новую активную копию. По завершении этого процесса PAM выдает запрос на подключение, и либо база данных подключается и становится доступной для клиентов, либо база данных не подключается и РАМ ищет следующую оптимальную копию (если она доступна).

