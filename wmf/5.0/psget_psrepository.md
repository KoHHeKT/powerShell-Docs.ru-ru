---
ms.date: 06/12/2017
author: JKeithB
ms.topic: reference
keywords: wmf,powershell,установка
ms.openlocfilehash: 269f4112704067f291728e4c1d745d68ec6ccd6f
ms.sourcegitcommit: cf195b090b3223fa4917206dfec7f0b603873cdf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2018
---
# <a name="register-a-powershell-repository"></a>Регистрация репозитория PowerShell
Вы можете настроить работу PowerShellGet с внутренними репозиториями. Для этого были добавлены следующие компоненты.
- Register-PSRepository: регистрирует репозиторий для текущего пользователя.
- Unregister-PSRepository: удаляет зарегистрированный репозиторий для текущего пользователя.
- Set-PSRepository: задает значения для зарегистрированного репозитория.
- Get-PSRepository: возвращает все зарегистрированные репозитории для текущего пользователя.

После регистрации репозитория командлеты Find-Module и Install-Module можно настроить на работу с ним.

```powershell
\#Register a default repository
Register-PSRepository –Name DemoRepo –SourceLocation “https://www.myget.org/F/powershellgetdemo/api/v2” –PublishLocation “<https://www.myget.org/F/powershellgetdemo/api/v2>/package” –InstallationPolicy –Trusted

\#Get all of the registered repositories
Get-PSRepository
Name SourceLocation
---- --------------
PSGallery https://msconfiggallery.cloudapp...
DemoRepo https://www.myget.org/F/powershe...

\#Search only the new repository for modules
Find-Module -Repository DemoRepo
Repository Version Name
---------- ------- ----
DemoRepo 1.0.1 xActiveDirectory
DemoRepo 1.1.1 SomeModule

\#By default, PowerShellGet operates against all registered repositories when none is specified. In this example, the “SomeModule” module is installed from the DemoRepo.
Install-Module SomeModule

\#Removing a repository
Unregister-PSRepository DemoRepo
```