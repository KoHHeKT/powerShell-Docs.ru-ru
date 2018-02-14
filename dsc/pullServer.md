---
ms.date: 2018-02-02
ms.topic: conceptual
keywords: "dsc,powershell,конфигурация,установка"
title: "Опрашивающая служба DSC"
ms.openlocfilehash: d5e24dcc093c73d8ebbaa618517193dacc4f2aaf
ms.sourcegitcommit: 755d7bc0740573d73613cedcf79981ca3dc81c5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-pull-service"></a>Опрашивающая служба Desired State Configuration

> Область применения: Windows PowerShell 5.0

Управление локальным диспетчером конфигураций можно централизировать с помощью опрашивающей службы.
При использовании такого подхода управляемый узел регистрируется в службе, и ему в параметрах локального диспетчера конфигураций назначается конфигурация.
Конфигурация и все ресурсы DSC, которые необходимы в качестве зависимостей конфигурации, загружаются на компьютер и используются локальным диспетчером конфигураций для управления конфигурацией.
Сведения о состоянии управляемого компьютера отправляются в службу для отчетности.
Эта концепция называется "опрашивающей службой".

Текущие варианты опрашивающей службы:

- служба Desired State Configuration в службе автоматизации Azure;
- Опрашивающая служба в Windows Server
- Решения с открытым исходным кодом, поддерживаемые сообществом
- Общий ресурс SMB

**Рекомендуемое решение** и вариант с наибольшим числом доступных функций — [DSC в службе автоматизации Azure](https://docs.microsoft.com/en-us/azure/automation/automation-dsc-getting-started).

Служба Azure может управлять узлами в локальных частных центрах обработки данных или в общедоступных облаках, таких как Azure и AWS.
Для частных сред, где серверы не могут напрямую подключаться к Интернету, рекомендуем ограничить исходящий трафик только опубликованным диапазоном IP-адресов Azure (см. сведения о [диапазонах IP-адресов для центров обработки данных Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653)).

Функции веб-службы, которые сейчас недоступны в опрашивающей службе в Windows Server:
- Шифрование всех данных при передаче и хранении.
- Автоматическое создание и обслуживание клиентских сертификатов.
- Хранение секретов с централизованным управлением [паролями и учетными данными](https://docs.microsoft.com/en-us/azure/automation/automation-credentials) или [переменными](https://docs.microsoft.com/en-us/azure/automation/automation-variables), такими как имена серверов или строки подключения.
- Централизованное управление [конфигурацией LCM](metaConfig.md#basic-settings) в узле.
- Централизованное назначение конфигураций клиентским узлам.
- Выпуск изменений конфигурации для небольших групп пользователей, позволяющий протестировать решение перед реализацией в рабочей среде.
- Графические отчеты:
  - сведения о состоянии на уровне ресурсов DSC;
  - подробные сообщения об ошибках с клиентских компьютеров для устранения неполадок.
- [Интеграция с Azure Log Analytics](https://docs.microsoft.com/en-us/azure/automation/automation-dsc-diagnostics) для отправки предупреждений, автоматизации задач, а также выполнения приложений Android или iOS для создания отчетов и оповещений.

## <a name="dsc-pull-service-in-windows-server"></a>Опрашивающая служба DSC в Windows Server

Опрашивающую службу можно настроить для работы в Windows Server.
Учтите, что опрашивающая служба в составе Windows Server включает только возможности хранения конфигураций и модулей для скачивания и записи данных отчетов в базу данных.
Она не включает многих функций, предоставляемых службой в Azure, и поэтому не слишком хорошо подходит для оценки потенциального использования службы.

Опрашивающая служба в Windows Server — это веб-служба в составе IIS, которая использует интерфейс OData для создания файлов конфигурации DSC, доступных целевым узлам по запросу.

Требования к использованию опрашивающего сервера:

- Сервер под управлением:
  - WMF/PowerShell 5.0 или более поздней версии
  - Роль сервера служб IIS
  - Служба DSC
- Рекомендуется использовать средства создания сертификата для защиты учетных данных, передаваемых в локальный диспетчер конфигураций (LCM) на целевых узлах

Для настройки размещения опрашивающей службы в Windows Server лучше всего воспользоваться конфигурацией DSC.
Пример сценария приведен ниже.

### <a name="using-the-xdscwebservice-resource"></a>Использование ресурса xDSCWebService

Самый простой способ настроить опрашивающий веб-сервер — использовать ресурс xWebService, включенный в модуль xPSDesiredStateConfiguration.
В следующих действиях показано, как использовать ресурс в конфигурации, которая настраивает веб-службу.

1. Вызовите командлет [Install-Module](https://technet.microsoft.com/en-us/library/dn807162.aspx), чтобы установить модуль **xPSDesiredStateConfiguration**. **Примечание**. **Install-Module** включен в модуль **PowerShellGet**, содержащийся в PowerShell 5.0. Вы можете скачать модуль **PowerShellGet**для PowerShell 3.0 и 4.0 в разделе [Предварительная версия модулей PackageManagement PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=49186). 
1. Получите SSL-сертификат для опрашивающего сервера DSC из доверенного центра сертификации (общедоступного или входящего в состав вашей организации). Полученный из центра сертификат обычно имеет формат PFX. Установите сертификат на узле, который должен стать опрашивающим сервером DSC, в расположении по умолчанию (CERT:\LocalMachine\My). Запишите отпечаток сертификата.
1. Выберите идентификатор GUID для использования в качестве ключа регистрации. Для его создания с помощью PowerShell введите следующие команды в командной строке PS и нажмите клавишу ВВОД: "``` [guid]::newGuid()```" или "```New-Guid```". Этот ключ будет использоваться клиентскими узлами в качестве общего ключа для проверки подлинности во время регистрации. Дополнительные сведения см. в разделе "Ключ регистрации" ниже.
1. В PowerShell ISE запустите (нажав клавишу F5) следующий сценарий конфигурации (включенный в пример папки модуля **xPSDesiredStateConfiguration** в качестве Sample_xDscWebService.ps1). Этот сценарий настраивает опрашивающий сервер.

```powershell
    configuration Sample_xDscPullServer
    {
        param
        (
                [string[]]$NodeName = 'localhost',

                [ValidateNotNullOrEmpty()]
                [string] $certificateThumbPrint,

                [Parameter(Mandatory)]
                [ValidateNotNullOrEmpty()]
                [string] $RegistrationKey
         )

         Import-DSCResource -ModuleName xPSDesiredStateConfiguration
         Import-DSCResource –ModuleName PSDesiredStateConfiguration

         Node $NodeName
         {
             WindowsFeature DSCServiceFeature
             {
                 Ensure = 'Present'
                 Name   = 'DSC-Service'
             }

             xDscWebService PSDSCPullServer
             {
                 Ensure                   = 'Present'
                 EndpointName             = 'PSDSCPullServer'
                 Port                     = 8080
                 PhysicalPath             = "$env:SystemDrive\inetpub\PSDSCPullServer"
                 CertificateThumbPrint    = $certificateThumbPrint
                 ModulePath               = "$env:PROGRAMFILES\WindowsPowerShell\DscService\Modules"
                 ConfigurationPath        = "$env:PROGRAMFILES\WindowsPowerShell\DscService\Configuration"
                 State                    = 'Started'
                 DependsOn                = '[WindowsFeature]DSCServiceFeature'
                 UseSecurityBestPractices = $false
             }

            File RegistrationKeyFile
            {
                Ensure          = 'Present'
                Type            = 'File'
                DestinationPath = "$env:ProgramFiles\WindowsPowerShell\DscService\RegistrationKeys.txt"
                Contents        = $RegistrationKey
            }
        }
    }

```

1. Запустите конфигурацию, передав отпечаток SSL-сертификата в виде параметра **certificateThumbPrint** и ключ регистрации GUID в виде параметра **RegistrationKey**:

```powershell
    # To find the Thumbprint for an installed SSL certificate for use with the pull server list all certificates in your local store 
    # and then copy the thumbprint for the appropriate certificate by reviewing the certificate subjects
    dir Cert:\LocalMachine\my

    # Then include this thumbprint when running the configuration
    Sample_xDSCPullServer -certificateThumbprint 'A7000024B753FA6FFF88E966FD6E19301FAE9CCC' -RegistrationKey '140a952b-b9d6-406b-b416-e0f759c9c0e4' -OutputPath c:\Configs\PullServer

    # Run the compiled configuration to make the target node a DSC Pull Server
    Start-DscConfiguration -Path c:\Configs\PullServer -Wait -Verbose

```

#### <a name="registration-key"></a>Ключ регистрации

Чтобы разрешить клиентским узлам регистрироваться на сервере для использования имен конфигурации вместо идентификаторов конфигурации, созданный конфигурацией ключ конфигурации сохраняется в файле `RegistrationKeys.txt` и `C:\Program Files\WindowsPowerShell\DscService`. Ключ регистрации работает как общий секрет, используемый во время первоначальной регистрации клиента с опрашивающим сервером. Клиент создает самозаверяющий сертификат, который используется для уникальной проверки подлинности на опрашивающем сервере после успешного завершения регистрации. Отпечаток этого сертификата сохраняется локально и связывается с URL-адресом опрашивающего сервера.
> **Примечание**. Ключи регистрации не поддерживаются в PowerShell 4.0. 

Для настройки проверки подлинности узла на опрашивающем сервере необходимо поместить ключ регистрации в метаконфигурацию всех целевых узлов, которые будут регистрироваться на этом опрашивающем сервере. Обратите внимание, что **RegistrationKey** в метаконфигурации ниже удаляется после успешной регистрации целевого компьютера и значение "140a952b-b9d6-406b-b416-e0f759c9c0e4" должно соответствовать значению в файле RegistrationKeys.txt на опрашивающем сервере. Значение ключа регистрации должно оставаться конфиденциальным, так как с ним любой целевой компьютер сможет зарегистрироваться на опрашивающем сервере.

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode          = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded   = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL          = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey    = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
            ConfigurationNames = @('ClientConfig')
        }

        ReportServerWeb CONTOSO-PullSrv
        {
            ServerURL       = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
        }
    }
}

PullClientConfigID -OutputPath c:\Configs\TargetNodes


```

> **Примечание**. Раздел **ReportServerWeb** позволяет отправлять данные отчетов на опрашивающий сервер.

Отсутствие свойства **ConfigurationID** в файле метаконфигурации неявно означает, что опрашивающий сервер поддерживает версию 2 протокола опрашивающего сервера и требуется начальная регистрация.
Наоборот, наличие свойства **ConfigurationID** означает, что используется версия 1 протокола опрашивающего сервера и регистрация не выполняется.

>**Примечание**. В сценарии PUSH в текущем выпуске есть ошибка, из-за которой необходимо определять свойство ConfigurationID в файле метаконфигурации для узлов, которые никогда не регистрировались на опрашивающем сервере. Это приведет к принудительному использованию версии 1 протокола опрашивающего сервера и позволит избежать сообщений об ошибках регистрации.

## <a name="placing-configurations-and-resources"></a>Размещение конфигураций и ресурсов

После завершения установки опрашивающего сервера папки для размещения модулей и конфигурации, которые будут доступны целевым узлам, задаются свойствами **ConfigurationPath** и **ModulePath** в конфигурации опрашивающего сервера.
Для правильной обработки опрашивающим сервером эти файлы должны иметь специальный формат.

### <a name="dsc-resource-module-package-format"></a>Формат пакета модуля для ресурсов DSC

Каждый модуль ресурса необходимо упаковать в ZIP-архив и переименовать согласно следующему шаблону: `{Module Name}_{Module Version}.zip`.
Например, модуль с именем xWebAdminstration с версией модуля 3.1.2.0 будет иметь имя "xWebAdministration_3.2.1.0.zip".
Каждая версия модуля должна находиться в собственном ZIP-файле.
Поскольку в каждом ZIP-файле существует только одна версия ресурса, формат модулей с поддержкой нескольких версий модуля в одном каталоге, который появился в WMF 5.0, не поддерживается.
Это означает, что перед упаковкой модулей ресурсов DSC для опрашивающего сервера необходимо внести небольшое изменение в структуру каталогов.
Формат модулей, содержащих ресурсы DSC, в WMF 5.0 по умолчанию таков: "{папка_модуля}\{{версия_модуля}\DscResources\{{папка_ресурса_DSC}\'.
Перед упаковкой для опрашивающего сервера просто удалите папку **{версия_модуля}**, чтобы путь стал таким: "{папка_модуля}\DscResources\{{папка_ресурса_DSC}\'.
Выполнив это изменение, упакуйте папку в ZIP-архив, как описано выше, и поместите ZIP-архивы в папку **ModulePath**.

Используйте `new-dscchecksum {module zip file}`, чтобы создать файл контрольной суммы для только что добавленного модуля.

### <a name="configuration-mof-format"></a>Формат файлов конфигурации MOF

MOF-файл конфигурации необходимо сопоставить с файлом контрольной суммы, чтобы LCM на целевом узле мог проверить конфигурацию.
Чтобы создать контрольную сумму, вызовите командлет [New-DSCCheckSum](https://technet.microsoft.com/en-us/library/dn521622.aspx).
Командлет принимает параметр **Path**, указывающий папку, в которой располагается MOF-файл конфигурации.
Командлет создает файл контрольной суммы `ConfigurationMOFName.mof.checksum`, где `ConfigurationMOFName` — имя MOF-файла конфигурации.
Если в указанной папке есть несколько MOF-файлов конфигурации, контрольная сумма создается для каждой конфигурации в папке.
Поместите файлы MOF и их файлы контрольных сумм в папку **ConfigurationPath**.

>**Примечание**. Если вы измените MOF-файл конфигурации каким-либо образом, потребуется повторно создать файл контрольной суммы.

### <a name="tooling"></a>Инструменты

Для упрощения настройки, проверки опрашивающего сервера и управления им в последнюю версию модуля xPSDesiredStateConfiguration в качестве примеров включены следующие инструменты:

1. Модуль, который помогает упаковывать модули ресурсов и конфигурационные файлы DSC для использования на опрашивающем сервере. [PublishModulesAndMofsToPullServer.psm1](https://github.com/PowerShell/xPSDesiredStateConfiguration/blob/dev/DSCPullServerSetup/PublishModulesAndMofsToPullServer.psm1). Примеры ниже:

    ```powershell
        # Example 1 - Package all versions of given modules installed locally and MOF files are in c:\LocalDepot
         $moduleList = @("xWebAdministration", "xPhp") 
         Publish-DSCModuleAndMof -Source C:\LocalDepot -ModuleNameList $moduleList 

         # Example 2 - Package modules and mof documents from c:\LocalDepot
         Publish-DSCModuleAndMof -Source C:\LocalDepot -Force
    ```

1. Сценарий, который проверяет, что опрашивающий сервер настроен правильно. [PullServerSetupTests.ps1](https://github.com/PowerShell/xPSDesiredStateConfiguration/blob/dev/DSCPullServerSetup/PullServerDeploymentVerificationTest/PullServerSetupTests.ps1).

## <a name="community-solutions-for-pull-service"></a>Решения сообщества для опрашивающей службы

Сообщество DSC разработало несколько решений для реализации протокола опрашивающей службы.
В случае локальных сред эти решения предоставляют функции опрашивающей службы и дают возможность поделиться с сообществом своими улучшениями.

- [Tug](https://github.com/powershellorg/tug)
- [DSC-TRÆK](https://github.com/powershellorg/dsc-traek)

## <a name="pull-client-configuration"></a>Настройка опрашивающего клиента

В следующих разделах настройка опрашивающих клиентов описывается более подробно:

- [Настройка опрашивающего клиента DSC с помощью идентификатора конфигурации](pullClientConfigID.md)
- [Настройка опрашивающего клиента DSC с помощью имен конфигурации](pullClientConfigNames.md)
- [Частичные конфигурации](partialConfigs.md)

## <a name="see-also"></a>См. также:

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](overview.md)
- [Активированные конфигурации](enactingConfigurations.md)
- [Использование сервера отчетов DSC](reportServer.md)
