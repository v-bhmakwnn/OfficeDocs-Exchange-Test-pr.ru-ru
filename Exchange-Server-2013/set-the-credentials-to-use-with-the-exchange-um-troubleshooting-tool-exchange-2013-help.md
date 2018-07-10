﻿---
title: 'Указание учетных данных для средства устранения неполадок единой системы обмена сообщениями Exchange: Exchange 2013 Help'
TOCTitle: Указание учетных данных для средства устранения неполадок единой системы обмена сообщениями Exchange
ms:assetid: 542b7718-9345-40cc-bcb2-e307e70a1fa2
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Ff630916(v=EXCHG.150)
ms:contentKeyID: 56271230
ms.date: 05/22/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# Указание учетных данных для средства устранения неполадок единой системы обмена сообщениями Exchange

 

_**Применимо к:**Exchange Server 2010 Service Pack 2 (SP2), Exchange Server 2013, Exchange Server 2016_

_**Последнее изменение раздела:**2016-12-09_

Средство устранения неполадок единой системы обмена сообщениями Microsoft Exchange 2010 — командлет командной консоли Exchange, **Test-ExchangeUMCallFlow**. С помощью этого командлета вы можете диагностировать ошибки конфигурации, связанные со сценариями ответа на вызовы, и проверки правильности работы голосовой почты при локальном и гибридном развертывании единой системы обмена сообщениями в Microsoft Exchange Server 2010 с пакетом обновления 1 (SP1) или более поздней версии. Вы также можете использовать этот командлет при развертывании Microsoft Lync Server 2010 или более поздней версии для Microsoft Office, а также при развертывании единой системы обмена сообщениями с использованием VoIP-шлюзов, IP-УАТС или пограничных контроллеров сеансов (SBC).

По умолчанию при запуске средства устранения неполадок единой системы обмена сообщениями оно использует учетные данные для входа в систему. Используемые учетные данные — это те данные, которые указаны для вызывающей стороны. Необходимо установить или указать учетные данные, которые будут использоваться при запуске средства устранения неполадок единой системы обмена сообщениями в режиме `SIPClient`. Тем не менее, не требуется устанавливать учетные данные при запуске средства устранения неполадок единой системы обмена сообщениями в режиме `Gateway`.

## Что нужно знать перед началом работы

  - Предполагаемое время выполнения: 3 минуты.

  - Для выполнения этих процедур необходимы соответствующие разрешения. Сведения о необходимых разрешениях см. в статье записи "Сервер единой системы обмена сообщениями" или "Службы единой системы обмена сообщениями" в статье [Разрешения единой системы обмена сообщениями](unified-messaging-permissions-exchange-2013-help.md).

  - Убедитесь, что ваша организация Exchange 2010 или Exchange 2013 соответствует следующим требованиям:
    
      - Создана абонентская группа единой системы обмена сообщениями. Дополнительные сведения см. в разделе [Создание абонентской группы единой системы обмена сообщениями](create-a-um-dial-plan-exchange-2013-help.md).
    
      - Создана политика почтовых ящиков единой системы обмена сообщениями. Дополнительные сведения см. в разделе [Создание политики почтовых ящиков единой системы обмена сообщениями](create-a-um-mailbox-policy-exchange-2013-help.md).
    
      - Создан шлюз IP единой системы обмена сообщениями. Дополнительные сведения см. в разделе [Создание шлюза IP единой системы обмена сообщениями](create-a-um-ip-gateway-exchange-2013-help.md).
    
      - Сервер единой системы обмена СООБЩЕНИЯМИ Exchange 2010 добавлена абонентскую группу единой системы обмена СООБЩЕНИЯМИ. Если вы используете Exchange 2013 с Lync Server, добавьте всех серверах клиентского доступа и почтовых ящиков абонентских групп SIP URI. Подробное описание процедуры в разделе [Добавление сервера единой системы обмена СООБЩЕНИЯМИ для Dial Plan,](https://go.microsoft.com/fwlink/p/?linkid=313051) или [Добавьте серверы клиентского доступа и почтовых ящиков для абонентской группы SIP URI](add-mailbox-and-client-access-servers-to-a-sip-uri-dial-plan-exchange-2013-help.md).

  - Установите средство устранения неполадок единой системы обмена сообщениями. Дополнительные сведения см. в разделе [Установка средства устранения неполадок единой системы обмена сообщениями Exchange](install-the-exchange-um-troubleshooting-tool-exchange-2013-help.md).
    
    <table>
    <thead>
    <tr class="header">
    <th><img src="images/Dd876857.important(EXCHG.150).gif" title="Важно" alt="Важно" />Важно!</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td>Если будет использоваться единой системы обмена СООБЩЕНИЯМИ средство устранения неполадок в <code>SIPClient</code> режиме, существует несколько Office Communications Server 2007 R2 или Microsoft Lync Server требования и предварительные требования. Дополнительные сведения можно <a href="https://go.microsoft.com/fwlink/p/?linkid=311961">Контрольный список: развертывание Office Communications Server 2007 R2 и единой системы обмена сообщениями Exchange 2010</a> или <a href="checklist-integrate-exchange-2013-um-with-lync-server-exchange-2013-help.md">Контрольный список: Интеграция единой системы обмена сообщениями Exchange 2013 с сервером Lync</a>.</td>
    </tr>
    </tbody>
    </table>


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


## Установка учетных данных для использования в средстве устранения неполадок единой системы обмена сообщениями

1.  в меню **Пуск** откройте **Средство устранения неполадок единой системы обмена сообщениями Microsoft Exchange 2010**.

2.  В окне **Средство устранения неполадок единой системы обмена сообщениями Microsoft Exchange 2010** в командной строке введите следующую команду и нажмите клавишу ВВОД.
    
        $cred=Get-Credential

3.  В окне **Запрос учетных данных Windows PowerShell** введите домен\\имя\_пользователя и пароль, а затем нажмите кнопку **ОК**.

4.  В окне **Средство устранения неполадок единой системы обмена сообщениями Microsoft Exchange 2010** укажите обязательные параметры командлета для проверки потока вызовов. Например:
    
        Test-ExchangeUMCallFlow -Mode SIPClient -CallingParty tonysmith@contoso.com - CalledParty jamiestark@contoso.com NextHop ocsfe.contoso.com -Credential $cred
