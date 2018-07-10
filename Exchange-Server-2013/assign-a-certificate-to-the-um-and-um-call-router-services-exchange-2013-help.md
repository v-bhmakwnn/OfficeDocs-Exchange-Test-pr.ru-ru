﻿---
title: 'Назначение сертификата службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы: Exchange 2013 Help'
TOCTitle: Назначение сертификата службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы
ms:assetid: 8a900e5f-9779-4213-92d7-ec157b15fbc5
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Dn205140(v=EXCHG.150)
ms:contentKeyID: 54652126
ms.date: 04/30/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Назначение сертификата службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы

 

_**Применимо к:**Exchange Server 2013, Exchange Server 2016_

_**Последнее изменение раздела:**2013-04-29_

Для назначения конкретным службам Exchange самозаверяющего сертификата внутренней инфраструктуры открытых ключей (PKI) или стороннего коммерческого сертификата можно использовать Центр администрирования Exchange или командную консоль. Если для назначения сертификата службам Exchange используется командлет **New-ExchangeCertificate** с параметром *Services*, вы получите запрос на назначение сертификата службам. Если для создания сертификата используется Центр администрирования Exchange, мастер создания сертификатов Exchange не предложит назначить сертификат службам Exchange. Вам понадобится изменить свойства сертификата и назначить его посредством выбора соответствующих служб.

Различные службы имеют различные требования к сертификатам. Например, для некоторых служб в полях сертификата **Имя субъекта** или **Дополнительное имя субъекта** необходимо только имя сервера, в то время как для других служб может потребоваться полное доменное имя. Убедитесь, что имя сертификата может поддерживать требования всех служб, для которых включено использование этого сертификата.

<table>
<thead>
<tr class="header">
<th><img src="images/Dd876857.Caution(EXCHG.150).gif" title="Внимание!" alt="Внимание!" />Внимание!</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Самозаверяющие сертификаты нельзя использовать при интеграции единой системы обмена сообщениями с Microsoft Lync Server.</td>
</tr>
</tbody>
</table>


Дополнительные сведения о задачах, связанных с управлением сертификатами для единой системы обмена сообщениями, см. в разделе [Развертывание сертификатов для единой системы обмена СООБЩЕНИЯМИ процедур](deploying-certificates-for-um-procedures-exchange-2013-help.md).

## Что нужно знать перед началом работы

  - Предполагаемое время для завершения: 5 минут.

  - Для выполнения этих процедур необходимы соответствующие разрешения. Сведения о необходимых разрешениях см. в статье Пункт "Управление сертификатами" в статье [Разрешения инфраструктуры Exchange и командной консоли](exchange-and-shell-infrastructure-permissions-exchange-2013-help.md) и пункт "Служба единой системы обмена сообщениями" в статье [Разрешения единой системы обмена сообщениями](unified-messaging-permissions-exchange-2013-help.md). Также необходимо войти в систему с помощью учетной записи домена, являющейся членом локальной группы "Администраторы" данного компьютера.

  - Сочетания клавиш для процедур, описанных в этой статье, приведены в статье [Сочетания клавиш в Центре администрирования Exchange](keyboard-shortcuts-in-the-exchange-admin-center-exchange-online-protection-help.md).

<table>
<thead>
<tr class="header">
<th><img src="images/Bb124558.tip(EXCHG.150).gif" title="Совет" alt="Совет" />Совет.</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Возникли проблемы? Обратитесь за помощью к участникам форумов, посвященных Exchange. Посетите форумы по таким продуктам: <a href="https://go.microsoft.com/fwlink/p/?linkid=60612">Exchange Server</a>, <a href="https://go.microsoft.com/fwlink/p/?linkid=267542">Exchange Online</a> или <a href="https://go.microsoft.com/fwlink/p/?linkid=285351">Exchange Online Protection</a>..</td>
</tr>
</tbody>
</table>


## Что необходимо сделать?

## Назначение сертификата службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы с помощью Центра администрирования Exchange

1.  В Центре администрирования Exchange последовательно выберите пункты **Серверы** \> **Сертификаты**.

2.  В представлении списка выберите сертификат, который необходимо назначить службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы, и щелкните **Изменить**![Значок редактирования](images/Bb124582.6f53ccb2-1f13-4c02-bea0-30690e6ea71d(EXCHG.150).gif "Значок редактирования").

3.  На странице *\<Имя сертификата\>* выберите **Службы**, **Единая система обмена сообщениями** и **Маршрутизатор вызовов единой системы обмена сообщениями**.

4.  Нажмите кнопку **Сохранить**.

## Назначение сертификата службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы с помощью командной консоли

В этом примере назначается сертификат службе единой системы обмена сообщениями и службе маршрутизатора вызовов этой системы.

    Enable-ExchangeCertificate -Thumbprint 5113ae0233a72fccb75b1d0198628675333d010e -Services 'UM, UMCallRouter'
