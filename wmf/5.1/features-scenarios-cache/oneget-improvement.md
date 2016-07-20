---
title: "Улучшения PackageManagement (OneGet)"
contributor: jianyunt, quoctruong
translationtype: Human Translation
ms.sourcegitcommit: 4c1b57f221d0f502313eecb21dd36b5e85c2de4d
ms.openlocfilehash: e2646a59c7a241491ef934c62fdfb6d649d16191

---

>Примечание. Предоставьте предложенное описательное название и краткое описание.

## Улучшения PackageManagement (OneGet) ##
Ниже перечислены исправления, внесенные в WMF 5.1 с целью устранить ряд проблем, возникавших у пользователей при работе с версией WMF 5.0. 

1. **Псевдоним версии**

**Сценарий**. Предположим, что в системе установлены версии 1.0 и 2.0 пакета P1 и вы хотите удалить версию 1.0, для чего выполняете команду "uninstall-package -name P1 -version 1.0". Вы ожидаете, что после выполнения командлета будет удалена версия 1.0. Но в результате удаляется версия 2.0. 
    
Причина этой проблемы в том, что параметр -version является псевдонимом параметра -minimumversion. Когда модуль OneGet ищет подходящий пакет с минимальной версией 1.0, он возвращает последнюю версию. Такое поведение является нормальным в большинстве случаев, так как обычно требуется найти именно последнюю версию. Но в случае с удалением пакета ситуация иная.
    
**Решение**. Псевдоним -version был полностью удален в OneGet и PowerShellGet. 

2. **Несколько запросов на начальную загрузку поставщика NuGet**

**Сценарий**. При первом выполнении командлета Find-Module, Install-Module или других командлетов OneGet на компьютере модуль OneGet пытается выполнить начальную загрузку поставщика NuGet. Связано это с тем, что поставщик PowershellGet также использует поставщик NuGet для скачивания модулей PowerShell. Затем модуль OneGet запрашивает у пользователя разрешение на установку поставщика NuGet. После того как пользователь разрешает начальную загрузку, устанавливается последняя версия поставщика NuGet. 
    
Но если на компьютере установлена старая версия поставщика NuGet, она иногда может загружаться первой в сеанс PowerShell (так как в OneGet возникает состояние гонки). Но модуль PowerShellGet требует, чтобы работала последняя версия поставщика NuGet, поэтому он еще раз запрашивает начальную загрузку поставщика NuGet у модуля OneGet. Вот почему выводится несколько запросов на начальную загрузку поставщика NuGet.

**Решение**. Модуль OneGet теперь загружает последнюю версию поставщика NuGet во избежание вывода нескольких запросов на начальную загрузку поставщика NuGet.

Также имеется обходной путь: вы можете вручную удалить старую версию поставщика NuGet (NuGet-Anycpu.exe), если она существует, из папок $env:ProgramFiles\PackageManagement\ProviderAssemblies и $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies


3. **Компьютер с доступом только к интрасети**

**Сценарий**. В корпоративных средах у пользователей может быть доступ только к интрасети, но не к Интернету. Модуль OneGet не поддерживал такой сценарий в WMF 5.0.

**Решение**.
- Вы можете скачать поставщик NuGet с помощью другого компьютера, имеющего подключение к Интернету, выполнив команду "Install-PackageProvider NuGet".

- Поставщик NuGet находится в папке $env:ProgramFiles\PackageManagement\ProviderAssemblies\nuget или $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies\nuget. 

- Скопируйте двоичные файлы в папку или сетевую папку, к которой есть доступ у вашего компьютера (без подключения к Интернету), и установите поставщик NuGet, выполнив команду "Install-PackageProvider NuGet -Source <Path to folder>".


4. **Журнал событий**

При установке пакетов состояние компьютера меняется. В целях диагностики модуль OneGet теперь записывает в журнал событий Windows события, связанные с установкой, удалением и сохранением пакетов. Канал событий тот же, что и для PowerShell, то есть Microsoft-Windows-PowerShell, Operational.

5. **Поддержка проверки подлинности**. Модуль OneGet теперь поддерживает поиск и установку пакетов из репозитория, требующего обычной проверки подлинности. Вы можете указывать учетные данные для командлетов Find-Package и Install-Package. Например:
``` PowerShell
Find-Package -Source <SourceWithCredential> -Credential (Get-Credential)
```
6. **Использование OneGet за прокси-сервером**

Модуль OneGet теперь принимает параметры прокси-сервера: -ProxyCredential и -Proxy. С помощью этих параметров можно указывать URL-адрес и учетные данные прокси-сервера для командлетов OneGet (по умолчанию используются системные настройки прокси-сервера). Например:
``` PowerShell
Find-Package -Source http://www.nuget.org/api/v2/ -Proxy http://www.myproxyserver.com -ProxyCredential (Get-Credential)
```



<!--HONumber=Jul16_HO1-->

