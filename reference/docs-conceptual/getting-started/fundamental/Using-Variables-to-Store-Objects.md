---
ms.date: 2017-06-05
keywords: "powershell,командлет"
title: "Использование переменных для хранения объектов"
ms.assetid: b1688d73-c173-491e-9ba6-6d0c1cc852de
ms.openlocfilehash: 067948d7c234fb70c7cf9966c9ae3e8df1f99757
ms.sourcegitcommit: 598b7835046577841aea2211d613bb8513271a8b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2017
---
# <a name="using-variables-to-store-objects"></a>Использование переменных для хранения объектов
Windows PowerShell работает с объектами. Можно создавать переменные, главным образом — именованные объекты, чтобы сохранять выходные данные для последующего использования. Если вы привыкли работать с переменными в других оболочках, помните, что переменные Windows PowerShell являются объектами, а не текстом.

Имена переменных всегда начинаются с символа $ и могут включать любые буквенно-цифровые символы или знаки подчеркивания.

### <a name="creating-a-variable"></a>Создание переменной
Чтобы создать переменную, можно ввести для нее допустимое имя:

```
PS> $loc
PS>
```

Это не возвращает никаких результатов, так как **$loc** не имеет значения. Создать переменную и присвоить ей значение можно в одном шаге. Windows PowerShell создает переменную, только если она не существует; в противном случае указанное значение назначается существующей переменной. Для сохранения текущего расположения в переменной **$loc** введите:

```
$loc = Get-Location
```

При вводе этой команды выходные данные не отображаются, поскольку они отправляются в $loc. В Windows PowerShell отображаемые выходные данные являются побочным эффектом той особенности, что данные, не ориентированные каким либо образом, всегда выводятся на экран. При вводе $loc отображается ваше текущее расположение:

```
PS> $loc

Path
----
C:\temp
```

**Get-Member** можно использовать для отображения сведений о содержимом переменных. Передача $loc в Get-Member покажет, что это объект **PathInfo**, так же как и выходные данные Get-Location:

```
PS> $loc | Get-Member -MemberType Property

   TypeName: System.Management.Automation.PathInfo

Name         MemberType Definition
----         ---------- ----------
Drive        Property   System.Management.Automation.PSDriveInfo Drive {get;}
Path         Property   System.String Path {get;}
Provider     Property   System.Management.Automation.ProviderInfo Provider {...
ProviderPath Property   System.String ProviderPath {get;}
```

### <a name="manipulating-variables"></a>Работа с переменными
Windows PowerShell предоставляет несколько команд для работы с переменными. Полный список в удобочитаемой форме можно получить, введя следующее:

```
Get-Command -Noun Variable | Format-Table -Property Name,Definition -AutoSize -Wrap
```

Кроме переменных, создаваемых в текущем сеансе Windows PowerShell, существует несколько системных переменных. Можно использовать командлет **Remove-Variable**, чтобы очистить все переменные, которые не управляются с помощью Windows PowerShell. Введите следующую команду для очистки всех переменных:

```
Remove-Variable -Name * -Force -ErrorAction SilentlyContinue
```

В результате выводится приведенный ниже запрос на подтверждение.

```
Confirm
Are you sure you want to perform this action?
Performing operation "Remove Variable" on Target "Name: Error".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):A
```

Если вы запустите командлет **Get-Variable**, то увидите остальные переменные Windows PowerShell. Поскольку существует и переменный диск Windows PowerShell, можно также отобразить все переменные Windows PowerShell, введя следующее:

```
Get-ChildItem variable:
```

### <a name="using-cmdexe-variables"></a>Использование переменных Cmd.exe
Хотя система Windows PowerShell — это не Cmd.exe, она выполняется в среде командной оболочки и может использовать переменные, доступные в любой среде в Windows. Эти переменные предоставляются посредством диска с именем **env:**. Эти переменные можно просмотреть, введя следующее:

```
Get-ChildItem env:
```

Хотя стандартные командлеты переменных не предназначены для работы с переменными **env:**, их все равно можно использовать, указав префикс **env:**. Например, для просмотра корневого каталога операционной системы можно использовать переменную **%SystemRoot%** командной оболочки из Windows PowerShell, введя следующее:

```
PS> $env:SystemRoot
C:\WINDOWS
```

В Windows PowerShell также можно создавать и изменять переменные среды. Переменные среды из Windows PowerShell соответствует обычным правилам для переменных среды в рамках всей системы Windows.

