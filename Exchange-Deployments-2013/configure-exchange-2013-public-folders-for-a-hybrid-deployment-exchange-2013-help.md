﻿---
title: 'Настройка общедоступных папок Exchange 2013 для гибридного развертывания: Exchange 2013 Help'
TOCTitle: Настройка общедоступных папок Exchange 2013 для гибридного развертывания
ms:assetid: b828520f-022c-4fcb-ab68-e1c330e87c33
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Dn986544(v=EXCHG.150)
ms:contentKeyID: 65452464
ms.date: 04/30/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Настройка общедоступных папок Exchange 2013 для гибридного развертывания

 

_<strong>Применимо к:</strong>Exchange Server 2013, Exchange Server 2016_

_<strong>Последнее изменение раздела:</strong>2016-12-09_

**Сводка**. Узнайте, как предоставить пользователям Exchange Online доступ к локальным общедоступным папкам в среде Exchange 2013.

В гибридном развертывании пользователи могут подключаться к Exchange Online или работать локально, и ваши общедоступные папки могут храниться в Exchange Online или локально. Иногда подключенным пользователям может потребоваться доступ к общедоступным папкам в локальной среде Exchange Server 2013. Аналогично пользователям Exchange 2013 может потребоваться доступ к общедоступным папкам в Office 365 или Exchange Online.

> [!NOTE]
> Если у вас есть общедоступные папки Exchange 2010 или Exchange 2007, ознакомьтесь с разделом <a href="https://docs.microsoft.com/ru-ru/exchange/collaboration-exo/public-folders/set-up-legacy-hybrid-public-folders">Настройка локальных общедоступных папок прежних версий для гибридного развертывания</a>.


В этой статье описывается, как предоставить пользователям Exchange Online и Office 365 доступ к общедоступным папкам в Exchange 2013. Сведения о том, как предоставить пользователям локальной среды Exchange 2013 доступ к общедоступным папкам в Exchange Online, см. в статье [Настройка общедоступных папок Exchange Online для гибридного развертывания](configure-exchange-online-public-folders-for-a-hybrid-deployment-exchange-2013-help.md).

Для доступа к общедоступным папкам Exchange 2013 пользователь Exchange Online или Office 365 должен быть представлен объектом MailUser в локальной среде Exchange. Этот объект должен быть локальным по отношению к целевой иерархии общедоступных папок Exchange 2013. Если какие-то пользователи Office 365 еще не представлены объектами MailUser в локальной среде, прочтите статью 3106618 базы знаний Майкрософт под названием [Пользователям Exchange Online недоступны локальные общедоступные папки предыдущих версий](https://go.microsoft.com/fwlink/p/?linkid=699451), чтобы создать соответствующие локальные сущности.

## Что нужно знать перед началом работы

1.  Эти указания предполагают, что использовался мастер гибридных конфигураций для настройки и синхронизации ваших локальных сред и сред Exchange Online и что записи DNS, которые использовались для ссылок AutoDiscover большинства пользователей, относятся к локальной конечной точке. Подробнее см. в разделе [Мастер гибридной конфигурации](hybrid-configuration-wizard-exchange-2013-help.md).

2.  Эти указания предполагают, что мобильный Outlook включен и работает на локальных серверах Exchange. Сведения о включении мобильного Outlook см. в разделе [Мобильный Outlook](https://technet.microsoft.com/ru-ru/library/bb123741\(v=exchg.150\)).

3.  Настраивая сосуществование общедоступных папок для гибридного развертывания Exchange с Office 365, возможно, понадобится устранять конфликты, возникающие в процессе импорта. Не поддерживающий маршрутизацию адрес электронной почты, назначенный общедоступным папкам, поддерживающим почту, может конфликтовать с другими пользователями и группами в Office 365, а также другими атрибутами.

4.  Чтобы получать доступ к общедоступным папкам на различных площадках, пользователям необходимо обновить свои клиенты Outlook с помощью общедоступного обновления Outlook за ноябрь 2012 г. или более поздней версии.
    
    1.  Чтобы скачать обновление за ноябрь 2012 г. для Outlook 2010, см. статью с [обновлением для 32-разрядного выпуска Microsoft Outlook 2010 (KB2687623)](https://www.microsoft.com/ru-ru/download/details.aspx?id=35702).
    
    2.  Чтобы скачать обновление Outlook за ноябрь 2012 г. для Outlook 2007, см. страницу [Обновление для Microsoft Office Outlook 2007 (KB2687404)](https://www.microsoft.com/ru-ru/download/details.aspx?id=35718).

5.  Доступ из Outlook 2011 для Mac или Outlook для Mac для Office 365 к общедоступным папкам в гибридных организациях не поддерживается. Чтобы открывать такие папки, пользователи Outlook 2011 для Mac или Outlook для Mac для Office 365 должны находиться в том же расположении, что и сами папки. Кроме того, пользователи, чьи почтовые ящики развернуты в Exchange Online, не смогут получить доступ к локальным общедоступным папкам с помощью Outlook Web App.
    
    > [!NOTE]
    > Доступ из Outlook 2016 для Mac к общедоступным папкам в гибридной среде поддерживается. Если клиенты в организации используют Outlook 2016 для Mac, убедитесь, что для них установлено обновление за апрель 2016 г. В противном случае такие пользователи не смогут открывать общедоступные папки при гибридной топологии. Дополнительные сведения см. в статье <a href="https://technet.microsoft.com/ru-ru/library/mt788631(v=exchg.150)">Доступ к общедоступным папкам с помощью Outlook 2016 для Mac</a>.


## Шаг 1. Загрузка скриптов

1.  На странице [Общедоступные папки с поддержкой почты: скрипт для синхронизации каталога](https://www.microsoft.com/en-us/download/details.aspx?id=46381) скачайте следующие файлы:
    
      - `Sync-MailPublicFolders.ps1`
    
      - `SyncMailPublicFolders.strings.psd1`

2.  Сохраните файлы на локальном компьютере, с которого вы планируете запустить PowerShell. Например, в папку C:\\PFScripts.

## Этап 2. Настройте синхронизацию службы каталогов

Служба синхронизации каталогов не синхронизирует общедоступные папки, поддерживающие почту. Запустив указанный ниже сценарий, можно синхронизировать такие папки между локальными серверами и Office 365. Для общедоступных папок, поддерживающих почту, необходимо повторно создать специальные разрешения в облаке, так как в сценариях гибридного развертывания не поддерживаются гибридные разрешения. Дополнительные сведения см. в статье [Гибридное развертывание Exchange Server 2013](exchange-server-hybrid-deployments-exchange-2013-help.md).

> [!NOTE]
> Синхронизированные общедоступные папки, поддерживающие почту, отображаются как объекты почтовых контактов для потока обработки почты и не видны в Центре администрирования Exchange. См. сведения о команде Get-MailPublicFolder. Чтобы повторно создать разрешения SendAs в облаке, используйте команду Add-RecipientPermission.


1.  На сервере Exchange 2013 выполните следующую команду, чтобы синхронизировать с Office 365 общедоступные папки с поддержкой почты из локального каталога Active Directory.
    
        Sync-MailPublicFolders.ps1 -Credential (Get-Credential) -CsvSummaryFile:sync_summary.csv
    
    `Credential` — ваше имя пользователя и пароль для Office 365. `CsvSummaryFile` — путь к файлу журнала в формате CSV, в котором нужно регистрировать ошибки и операции синхронизации.

> [!NOTE]
> Перед запуском сценария рекомендуем сначала выполнить имитацию его действия в вашей среде, запустив его с параметром <code>-WhatIf</code>, как описано выше.
> Рекомендуем также запускать этот сценарий ежедневно, чтобы синхронизировать общедоступные папки, поддерживающие почту.


## Step 3. Предоставьте пользователям Exchange Online доступ к локальным общедоступным папкам Exchange 2013

Остается настроить организацию Exchange Online и разрешить доступ к общедоступным папкам Exchange 2013.

Разрешите организации Exchange Online доступ к локальным общедоступным папкам. Необходимо указать все почтовые ящики локальных общедоступных папок.

    Set-OrganizationConfig -PublicFoldersEnabled Remote -RemotePublicFolderMailboxes PFMailbox1,PFMailbox2,PFMailbox3

> [!NOTE]
> Вы сможете увидеть изменения, когда завершится синхронизация ActiveDirectory. Это может занять до 3-х часов. Если вы не хотите дожидаться повторения синхронизации каждые три часа, можно в любое время выполнить принудительную синхронизацию службы каталогов. Дополнительные сведения о действиях по принудительной синхронизации службы каталогов см. в разделе <a href="http://technet.microsoft.com/ru-ru/library/jj151771.aspx">Принудительная синхронизация служб каталогов</a> соответствующей статьи.


## Как проверить, что это работает

1.  Войдите в Outlook для пользователя, который находится в Exchange Online, и выполните следующие проверки общедоступных папок.
    
      - Просмотр иерархии.
    
      - Проверка разрешений.
    
      - Создание и удаление общедоступных папок.
    
      - Опубликуйте содержимое в общедоступной папке и удалите его.

