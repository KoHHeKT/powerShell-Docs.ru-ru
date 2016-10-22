---
title: "Запуск 32-разрядной версии Windows PowerShell"
ms.date: 2016-05-11
keywords: "powershell,командлет"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 12b31890-2609-4a76-8c24-0ebe78084f50
translationtype: Human Translation
ms.sourcegitcommit: fa0a541965b4121b8e8ac677b3c38e6f316b4553
ms.openlocfilehash: 4e6a0e7f8935b905d1cb19576541d1b532fcba21

---

# Запуск 32-разрядной версии Windows PowerShell
При установке Windows PowerShell на 64-разрядном компьютере в дополнение к 64-разрядной версии устанавливается **Windows PowerShell (x86)** — 32-разрядная версия Windows PowerShell. При открытии Windows PowerShell по умолчанию запускается 64-разрядная версия.

Однако в некоторых случаях нужно запустить **Windows PowerShell (x86)**, например при использовании модуля, которому требуется 32-разрядная версия, или при удаленном подключении к 32-разрядному компьютеру.

Для запуска 32-разрядной версии Windows PowerShell воспользуйтесь любой из следующих процедур.

#### Windows Server® 2012 R2

-   На экране **Пуск** щелкните **Windows PowerShell (x86)**. Щелкните плитку **Windows PowerShell x86**.

-   Выберите пункт **Windows PowerShell (x86)** в меню **Сервис** **диспетчера сервера**.

-   На рабочем столе переместите курсор в правый верхний угол, щелкните элемент **Поиск**, введите **PowerShell x86** и выберите **Windows PowerShell (x86)**.

-   В командной строке введите следующее: `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows Server® 2012

-   На экране **Пуск** введите **PowerShell** и выберите **Windows PowerShell (x86)**.

-   Выберите пункт **Windows PowerShell (x86)** в меню **Сервис** **диспетчера сервера**.

-   На рабочем столе переместите курсор в правый верхний угол, щелкните элемент **Поиск**, введите **PowerShell** и выберите **Windows PowerShell (x86)**.

-   В командной строке введите следующее: `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows® 8.1

-   На экране **Пуск** щелкните **Windows PowerShell (x86)**. Щелкните плитку **Windows PowerShell x86**.

-   Если вы используете [средства удаленного администрирования сервера](http://go.microsoft.com/fwlink/?LinkID=304145) для Windows 8.1, можно также открыть Windows PowerShell x86 из меню **Сервис** диспетчера сервера. Выберите **Windows PowerShell (x86)**.

-   На рабочем столе переместите курсор в правый верхний угол, щелкните элемент **Поиск**, введите **PowerShell x86** и выберите **Windows PowerShell (x86)**.
   
-   В командной строке введите следующее: `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows® 8

-   На экране **Пуск** переместите курсор в правый верхний угол, щелкните **Параметры**, **Плитки**, а затем переместите ползунок **Показать средства администрирования** в значение "Да". Введите **PowerShell** и выберите **Windows PowerShell (x86)**.

-   Если вы используете [средства удаленного администрирования сервера](http://www.microsoft.com/download/details.aspx?id=28972) для Windows 8, можно также открыть Windows PowerShell x86 из меню **Сервис** диспетчера сервера. Выберите **Windows PowerShell (x86)**.

-   На экране **Пуск** или рабочем столе введите **PowerShell (x86)** и выберите **Windows PowerShell (x86)**.

-   В командной строке введите следующее: `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`



<!--HONumber=Oct16_HO2-->


