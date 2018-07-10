﻿---
title: 'Настройка количества неудачных попыток входа в систему перед блокировкой пользователя голосовой почты: Exchange 2013 Help'
TOCTitle: Настройка количества неудачных попыток входа в систему перед блокировкой пользователя голосовой почты
ms:assetid: 855e1980-2868-4983-b097-0b5f63f202b8
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Bb123544(v=EXCHG.150)
ms:contentKeyID: 50556453
ms.date: 04/30/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Настройка количества неудачных попыток входа в систему перед блокировкой пользователя голосовой почты

 

_**Применимо к:**Exchange Online, Exchange Server 2013, Exchange Server 2016_

_**Последнее изменение раздела:**2013-02-22_

Можно настроить допустимое количество неудачных попыток входа в систему, при превышении которого доступ к почтовым ящикам пользователей голосового доступа Outlook блокируется. Допустимое количество неудачных попыток входа в систему до блокировки почтового ящика настраивается в политике почтовых ящиков единой системы обмена сообщениями и применяется для всех пользователей с включенной поддержкой этой системы, связанных с данной политикой. По умолчанию установлено значение 15.

Чтобы повысить безопасность, следует уменьшить максимальное число неудачных попыток. Однако следует помнить, что уменьшение этого значения до значения, значительно меньшего, чем установлено по умолчанию, может привести к ненужным блокировкам пользователей. Единая система обмена сообщениями создает события предупреждения, которые можно просматривать с помощью средства просмотра событий, если пользователь с включенной поддержкой этой системы не прошел проверку подлинности ПИН-кода или ему не удалось успешно войти в систему. Этот параметр должен быть больше количества неудачных попыток входа в систему до сброса ПИН-кода.

Сведения о дополнительных задачах, связанных с обеспечением безопасности при помощи ПИН-кода голосового доступа к Outlook, см. в разделе [Процедуры безопасности ПИН-кода](pin-security-procedures-exchange-2013-help.md).

## Что нужно знать перед началом работы

  - Предполагаемое время для завершения: менее 1 минуты.

  - Для выполнения этих процедур необходимы соответствующие разрешения. Сведения о необходимых разрешениях см. в статье Запись «Политики почтовых ящиков единой системы обмена сообщениями» в разделе [Разрешения единой системы обмена сообщениями](unified-messaging-permissions-exchange-2013-help.md).

  - Перед выполнением этих процедур убедитесь, что абонентская группа единой системы обмена сообщениями создана. Дополнительные сведения см. в разделе [Создание абонентской группы единой системы обмена сообщениями](create-a-um-dial-plan-exchange-2013-help.md).

  - Перед выполнением этих процедур убедитесь, что политика почтовых ящиков единой системы обмена сообщениями создана. Дополнительные сведения см. в разделе [Создание политики почтовых ящиков единой системы обмена сообщениями](create-a-um-mailbox-policy-exchange-2013-help.md).

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

## Использование Центра администрирования Exchange для настройки количества неудачных попыток входа в систему до того, как пользователь голосовой почты будет заблокирован

1.  В Центре администрирования Exchange последовательно выберите пункты **Единая система обмена сообщениями** \> **Абонентские группы единой системы обмена сообщениями**.

2.  Выберите из списка абонентскую группу единой системы обмена сообщениями, которую необходимо изменить, и нажмите кнопку **Правка**![Значок редактирования](images/Bb124582.6f53ccb2-1f13-4c02-bea0-30690e6ea71d(EXCHG.150).gif "Значок редактирования").

3.  На странице **Абонентская группа единой системы обмена сообщениями** в разделе **Политики почтовых ящиков единой системы обмена сообщениями** выберите изменяемую политику и нажмите кнопку **Правка**![Значок редактирования](images/Bb124582.6f53ccb2-1f13-4c02-bea0-30690e6ea71d(EXCHG.150).gif "Значок редактирования").

4.  Щелкните **Политики ПИН-кодов** и рядом с полем **Количество неудачных попыток входа в систему перед блокировкой** введите значение между 1 и 999.

5.  Нажмите кнопку **Сохранить**.

## Использование командной консоли для настройки количества неудачных попыток входа в систему до того, как пользователь голосовой почты будет заблокирован

В этом примере для максимального количества неудачных попыток входа в систему устанавливается значение 10 для пользователей с включенной поддержкой единой системы обмена сообщениями, связанных с политикой почтовых ящиков системы с именем `MyUMMailboxPolicy`.

    Set-UMMailboxPolicy -Identity MyUMMailboxPolicy -MaxLogonAttempts 10

В этом примере устанавливаются следующие значения. Количество неудачных попыток входа перед сбросом ПИН-кода пользователя — 3, максимальное количество попыток входа — 5 и минимальная длина ПИН-кода — 9 для пользователей с включенной поддержкой единой системы обмена сообщениями, связанных с политикой почтовых ящиков системы с именем `MyUMMailboxPolicy`.

    Set-UMMailboxPolicy -Identity MyUMMailboxPolicy -LogonFailuresBeforePINReset 3
    -MaxLogonAttempts 5 -MinPINLength 9
