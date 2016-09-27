---
title: "Справка по командной строке PowerShell.exe"
ms.date: 2016-05-11
keywords: "powershell,командлет"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 1ab7b93b-6785-42c6-a1c9-35ff686a958f
translationtype: Human Translation
ms.sourcegitcommit: 763dc6bb0410ec09fc237d41b96842895b15d142
ms.openlocfilehash: c3b263110a908c28569cf3048a94d48da8316684

---

# Справка по командной строке PowerShell.exe
Запускает сеанс Windows PowerShell. Можно использовать PowerShell.exe для запуска сеанса Windows PowerShell из командной строки другого средства, такого как Cmd.exe, или использовать его в командной строке Windows PowerShell для запуска нового сеанса. Используйте указанные параметры для настройки сеанса.

## Синтаксис

```
PowerShell[.exe]
       [-EncodedCommand <Base64EncodedCommand>]
       [-ExecutionPolicy <ExecutionPolicy>]
       [-InputFormat {Text | XML}] 
       [-Mta]
       [-NoExit]
       [-NoLogo]
       [-NonInteractive] 
       [-NoProfile] 
       [-OutputFormat {Text | XML}] 
       [-PSConsoleFile <FilePath> | -Version <Windows PowerShell version>]
       [-Sta]
       [-WindowStyle <style>]
       [-File <FilePath> [<Args>]]
       [-Command { - | <script-block> [-args <arg-array>]
                     | <string> [<CommandParameters>] } ]

PowerShell[.exe] -Help | -? | /?
```

## Параметры

### -EncodedCommand <Base64EncodedCommand>
Принимает строковую версию команды в кодировке Base 64. Используйте этот параметр для отправки в Windows PowerShell команд, требующих сложных кавычек или фигурных скобок.

### -ExecutionPolicy <ExecutionPolicy>
Задает политику выполнения по умолчанию для текущего сеанса и сохраняет ее в переменной среды $env:PSExecutionPolicyPreference. Этот параметр не изменяет политику выполнения Windows PowerShell, заданную в реестре. Дополнительные сведения о политиках выполнения Windows PowerShell, включая список допустимых значений, см. в статье about_Execution_Policies (http://go.microsoft.com/fwlink/?LinkID=135170).

### -File <FilePath> \[<Parameters>]
Запускает указанный сценарий в локальной области ("с точкой"), чтобы создаваемые сценарием функции и переменные были доступны в текущем сеансе. Введите путь к файлу сценария и любые параметры. Параметр **File** должен быть последним в команде, поскольку все символы, введенные после параметра **File**, интерпретируются как путь к файлу сценария и следующие за ним параметры сценария.

Параметры сценария и их значения можно включить в значение параметра **File**. Например: `-File .\Get-Script.ps1 -Domain Central`

Обычно параметры-переключатели сценария включаются или опускаются. Например, приведенная ниже команда использует параметр **All** файла сценария Get-Script.ps1: `-File .\Get-Script.ps1 -All`

В редких случаях может потребоваться указать для параметра-переключателя логическое значение. Чтобы сделать это в значении параметра **File**, заключите его имя и значение в фигурные скобки, например: `-File .\Get-Script.ps1 {-All:$False}`

### -InputFormat {Text | XML}
Описывает формат данных, отправляемых в Windows PowerShell. Допустимые значения: "Text" (текстовые строки) или "XML" (сериализованный формат CLIXML).

### -Mta
Запускает оболочку с использованием многопотокового подразделения. Этот параметр впервые появился в Windows PowerShell 3.0. В Windows PowerShell 3.0 по умолчанию используется однопотоковое подразделение (STA). В Windows PowerShell 2.0 по умолчанию используется многопотоковое подразделение (STA).

### -NoExit
Не завершает работу после выполнения команд запуска.

### -NoLogo
Скрывает баннер авторских прав при запуске программы.

### -NonInteractive
Не предоставляет пользователю интерактивную командную строку.

### -NoProfile
Не загружает профиль Windows PowerShell.

### -OutputFormat {Text | XML}
Определяет формат выходных данных Windows PowerShell. Допустимые значения: "Text" (текстовые строки) или "XML" (сериализованный формат CLIXML).

### -PSConsoleFile <FilePath>
Загружает указанный файл консоли Windows PowerShell. Введите путь и имя файла консоли. Для создания файла консоли используйте командлет [Export-Console](https://technet.microsoft.com/en-us/library/4bab1c02-9e61-4aaf-9957-11d1934ef4ef) в Windows PowerShell.

### -Sta
Запускает Windows PowerShell с использованием многопотокового подразделения. В Windows PowerShell 3.0 по умолчанию используется однопотоковое подразделение (STA). В Windows PowerShell 2.0 по умолчанию используется многопотоковое подразделение (STA).

### -Version <Windows PowerShell Version>
Запускает заданную версию Windows PowerShell. Указанная версия должна быть установлена в системе. Если на компьютере установлен Windows PowerShell 3.0, допустимыми значениями являются "3.0" и "2.0". По умолчанию используется значение "3.0".

Если Windows PowerShell 3.0 не установлен, допустимо только значение "2.0". Другие значения игнорируются.

Дополнительные сведения см. в разделе "Установка Windows PowerShell" статьи [Начало работы с Windows PowerShell [СТАРЫЙ MSDN]](https://technet.microsoft.com/en-us/library/69555d95-b481-43e1-86e7-b46d68b3e2dd).

### -WindowStyle <Window style>
Задает стиль окна для сеанса. Допустимые значения: Normal, Minimized, Maximized и Hidden.

### -Command
Выполняет указанные команды (вместе с параметрами) как введенные в командной строке Windows PowerShell и завершает работу, если не указан параметр NoExit.

Значением Command может быть строка "-". или блок сценария. Если для Command задано значение "-", текст команды считывается из стандартного ввода.

Блоки сценариев должны быть заключены в фигурные скобки ({}). Указать блок сценария можно только при использовании PowerShell.exe в Windows PowerShell. Результаты сценария возвращаются родительской оболочке как десериализованные объекты XML, а не как активные объекты.

Если значением Command является строка, параметр **Command** должен быть последним в команде, так как любой знак, введенный после команды, интерпретируется как ее аргумент.

При написании строки команды Windows PowerShell используйте следующий формат:

```
"& {<command>}"
```

где кавычки отделяют строку, а оператор вызова (&) запускает выполнение команды.

### -Help, -?, /?
Показывает это сообщение. При вводе команды PowerShell.exe в Windows PowerShell добавляйте в начало параметров команды дефис (-), а не косую черту (/). В Cmd.exe можно использовать как дефис, так и косую черту.

> [!NOTE]
> Замечание по устранению неполадок. Обратите внимание, что в Windows PowerShell 2.0 запуск некоторых программ в Windows PowerShell завершается ошибкой с кодом LastExitCode 0xc0000142.

## ПРИМЕРЫ

```
PowerShell -PSConsoleFile sqlsnapin.psc1

PowerShell -Version 2.0 -NoLogo -InputFormat text -OutputFormat XML

PowerShell -Command "Get-EventLog -LogName security"

# in an existing PowerShell session that understands the curly braces mean a script block
PowerShell -Command {Get-EventLog -LogName security}

PowerShell -Command "& {Get-EventLog -LogName security}"

# To use the -EncodedCommand parameter:
$command = "dir 'c:\program files' "
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)
powershell.exe -encodedCommand $encodedCommand
```




<!--HONumber=Sep16_HO4-->


