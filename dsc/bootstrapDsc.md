---
title: "Настройка виртуальных машин при начальной загрузке с помощью DSC"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 77ca52b130a432f79b9b4399afa5480e0daec983
ms.openlocfilehash: 471684ffe62edfb10005f0ade162222eef4c9a32

---

>Область применения: Windows PowerShell 5.0

>**Примечание.** Раздел реестра **DSCAutomationHostEnabled**, описанный в этом разделе, недоступен в PowerShell 4.0. Сведения о настройке новых виртуальных машин при начальной загрузке в PowerShell 4.0 см. в разделе [Требуется автоматически настроить машины с помощью DSC при начальной загрузке?](https://blogs.msdn.microsoft.com/powershell/2014/02/28/want-to-automatically-configure-your-machines-using-dsc-at-initial-boot-up/).

# <a name="configure-a-virtual-machines-at-initial-bootup-by-using-dsc"></a>Настройка виртуальных машин при начальной загрузке с помощью DSC

## <a name="requirements"></a>Требования

Для выполнения этих примеров требуется следующее.

- Загрузочный VHD. ISO-файл с пробной версией Windows Server 2016 можно скачать в центре   [TechNet Evaluation Center](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Инструкции по созданию VHD из ISO-образа см. в разделе [Создание загрузочных виртуальных жестких дисков](https://technet.microsoft.com/en-us/library/gg318049.aspx).
- Компьютер с включенным Hyper-V. Дополнительные сведения см. в статье [Обзор Hyper-V](https://technet.microsoft.com/library/hh831531.aspx).

С помощью DSC можно автоматизировать установку и настройку программного обеспечения для компьютера при начальной загрузке. Для этого необходимо добавить документ MOF конфигурации или метаконфигурацию на загрузочный носитель (например, VHD), чтобы они выполнялись при начальной загрузке. Такое поведение контролируется [разделом реестра DSCAutomationHostEnabled](DSCAutomationHostEnabled.md) в разделе **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies**. По умолчанию значение этого раздела — 2, что позволяет DSC запускаться во время загрузки.

Если запускать DSC во время загрузки не следует, задайте для [раздела реестра DSCAutomationHostEnabled](DSCAutomationHostEnabled.md) значение 0.


- [Добавление документа MOF конфигурации в VHD](##Inject-a-configuration-MOF-document-into-a-VHD)
- [Добавление метаконфигурации DSC в VHD](##Inject-a-DSC-metaconfiguration-into-a-VHD)
- [Отключение DSC при загрузке](##Disable-DSC-at-boot-time)

>**Примечание.** На компьютер можно одновременно добавить `Pending.mof` и `MetaConfig.mof`. Если присутствуют оба файла, более высокий приоритет имеют параметры, указанные в `MetaConfig.mof`.


## <a name="inject-a-configuration-mof-document-into-a-vhd"></a>Добавление документа MOF конфигурации в VHD

Для применения конфигурации при начальной загрузке добавьте скомпилированный документ MOF конфигурации в VHD в качестве его файла `Pending.mof`. Если раздел реестра **DSCAutomationHostEnabled** имеет значение 2 (значение по умолчанию), DSC применяет конфигурацию, определенную в `Pending.mof`, при первой загрузке компьютера.

В этом примере используется следующая конфигурация, которая устанавливает службы IIS на новом компьютере.

```powershell
Configuration SampleIISInstall
{
    Import-DscResource -ModuleName 'PSDesiredStateConfiguration'

    node ('localhost')
    {
        WindowsFeature IIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server' 
        }
    }
}
```
### <a name="to-inject-the-configuration-mof-document-on-the-vhd"></a>Добавление документа MOF конфигурации в VHD

1. Подключите VHD, в который нужно добавить конфигурацию, вызвав командлет [Mount-VHD](). Пример:  `Mount-VHD -Path C:\users\public\documents\vhd\Srv16.vhd`
1. На компьютере с PowerShell 5.0 или более поздней версией сохраните указанную выше конфигурацию (**SampleIISInstall**) как файл сценария PowerShell (.ps1).
1. В консоли PowerShell перейдите в папку, где был сохранен PS1-файл.
1. Выполните следующие команды PowerShell для компиляции документа MOF (дополнительные сведения о компиляции конфигураций DSC см. в разделе [Конфигурации DSC](configurations.md)).
    ```powershell
    . .\SampleIISInstall.ps1
    SampleIISInstall
    ```
1. Будет создан файл `localhost.mof` в новой папке с именем `SampleIISInstall`. Переименуйте и переместите этот файл в нужное место на VHD как `Pending.mof` 
    с помощью командлета []Move-Item] (https://technet.microsoft.comlibrary/hh849852.aspx). Например:

        `Move-Item -Path C:\DSCTest\SampleIISInstall\localhost.mof -Destination E:\Windows\Sytem32\Configuration\Pending.mof`
1. Отключите VHD, вызвав командлет [Dismount-VHD](https://technet.microsoft.com/library/hh848562.aspx). Пример:  `Dismount-VHD -Path C:\users\public\documents\vhd\Srv16.vhd`
1. Создайте виртуальную машину с помощью VHD, на который установлен документ MOF DSC. После начальной загрузки и установки операционной системы будут установлены службы IIS.
    Это можно проверить, вызвав командлет [Get-WindowsFeature](https://technet.microsoft.com/library/jj205469.aspx).

## <a name="inject-a-dsc-metaconfiguration-into-a-vhd"></a>Добавление метаконфигурации DSC в VHD

Также можно настроить на компьютере извлечение конфигурации при начальной загрузке, добавив метаконфигурацию (см. раздел [Настройка локального диспетчера конфигурации [LCM]](metaConfig.md)) в VHD в качестве его файла `MetaConfig.mof`. Если раздел реестра **DSCAutomationHostEnabled** имеет значение 2 (значение по умолчанию), DSC применяет к локальному диспетчеру конфигурации метаконфигурацию, определенную в `MetaConfig.mof` при первой загрузке компьютера. Если в метаконфигурации указано, что локальный диспетчер конфигурации должен извлекать конфигурации с опрашивающего сервера, компьютер попытается извлечь конфигурации с этого сервера при начальной загрузке. Сведения о настройке опрашивающего сервера DSC см. в разделе [Настройка опрашивающего веб-сервера DSC](pullServer.md).

В этом примере используется конфигурация, описанная в предыдущем разделе (**SampleIISInstall**), и следующая метаконфигурация.

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientBootstrap
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }
        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
            ConfigurationNames = @('SampleIISInstall')
            
        }      
    }
}
```
### <a name="to-inject-the-metaconfiguration-mof-document-on-the-vhd"></a>Добавление документа MOF метаконфигурации в VHD

1. Подключите VHD, в который нужно добавить метаконфигурацию, вызвав командлет [Mount-VHD](). Пример:  `Mount-VHD -Path C:\users\public\documents\vhd\Srv16.vhd`
1. [Настройте опрашивающий веб-сервер DSC](pullServer.md) и сохраните конфигурацию **SampleIISInistall** в соответствующую папку.
1. На компьютере с PowerShell 5.0 или более поздней версией сохраните указанную выше метаконфигурацию (**PullClientBootstrap**) как файл сценария PowerShell (.ps1).
1. В консоли PowerShell перейдите в папку, где был сохранен PS1-файл.
1. Выполните следующие команды PowerShell для компиляции документа MOF метаконфигурации (дополнительные сведения о компиляции конфигураций DSC см. в разделе [Конфигурации DSC](configurations.md)).
    ```powershell
    . .\PullClientBootstrap.ps1
    PullClientBootstrap
    ```
1. Будет создан файл `localhost.meta.mof` в новой папке с именем `PullClientBootstrap`. Переименуйте и переместите этот файл в нужное место на VHD как `MetaConfig.mof` 
    с помощью командлета []Move-Item] (https://technet.microsoft.comlibrary/hh849852.aspx). Пример:  `Move-Item -Path C:\DSCTest\PullClientBootstrap\localhost.meta.mof -Destination E:\Windows\Sytem32\Configuration\MetaConfig.mof`
1. Отключите VHD, вызвав командлет [Dismount-VHD](https://technet.microsoft.com/library/hh848562.aspx). Пример:  `Dismount-VHD -Path C:\users\public\documents\vhd\Srv16.vhd`
1. Создайте виртуальную машину с помощью VHD, на который установлен документ MOF DSC. После начальной загрузки и установки операционной системы DSC извлекает конфигурацию из опрашивающего сервера и устанавливаются службы IIS. Это можно проверить, вызвав командлет [Get-WindowsFeature](https://technet.microsoft.com/library/jj205469.aspx).

## <a name="disable-dsc-at-boot-time"></a>Отключение DSC при загрузке.

По умолчанию раздел **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DSCAutomationHostEnabled** имеет значение 2, что позволяет запускать конфигурацию DSC, если компьютер находится в состоянии ожидания или в текущем состоянии. Если запускать конфигурацию при начальной загрузке не следует, необходимо задать для этого раздела значение 0.

1. Подключите VHD, вызвав командлет [Mount-VHD](). Пример:  `Mount-VHD -Path C:\users\public\documents\vhd\Srv16.vhd`
2. Загрузите подраздел реестра **HKLM\Software** с VHD, вызвав `reg load`. Например, если  `reg load HKLM\Vhd E:\Windows\System32\Config\Software`.
3. Перейдите в раздел **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\*** с помощью поставщика реестра PowerShell. Пример:  `Set-Location HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies`
4. Измените значение `DSCAutomationHostEnabled` на 0:  `Set-ItemProperty -Path . -Name DSCAutomationHostEnabled -Value 0`.
5. Выгрузите реестр, выполнив следующие команды.
    ```powershell
    [gc]::Collect()
    reg unload HKLM\Vhd
    ```
## <a name="see-also"></a>См. также

- [Конфигурации DSC](configurations.md)
- [Раздел реестра DSCAutomationHostEnabled](DSCAutomationHostEnabled.md)
- [Настройка локального диспетчера конфигураций (LCM)](metaConfig.md)
- [Настройка опрашивающего веб-сервера DSC](pullServer.md)















<!--HONumber=Oct16_HO4-->

