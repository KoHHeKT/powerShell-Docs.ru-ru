---
ms.date: 2017-06-12
author: eslesar
ms.topic: conceptual
keywords: "dsc,powershell,конфигурация,установка"
title: "Ресурс nxPackage в DSC для Linux"
ms.openlocfilehash: 11019b1cd12f23b0b498b7cb9a06e02c46c3c279
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="dsc-for-linux-nxpackage-resource" class="xliff"></a>
# Ресурс nxPackage в DSC для Linux

Ресурс **nxPackage** в настройке требуемого состояния PowerShell предоставляет механизм управления пакетами на узле Linux.

<a id="syntax" class="xliff"></a>
## Синтаксис

```
nxPackage <string> #ResourceName
{
    Name = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ PackageManager = <string> { Yum | Apt | Zypper } ]
    [ PackageGroup = <bool>]
    [ Arguments = <string> ]
    [ ReturnCode = <uint32> ]
    [ LogPath = <string> ]
    [ DependsOn = <string[]> ]
    
}
```

<a id="properties" class="xliff"></a>
## Свойства

|  Свойство |  Описание | 
|---|---|
| Название| Указывает имя пакета, для которого требуется обеспечить определенное состояние.| 
| Ensure| Определяет, нужно ли проверять существование пакета. Чтобы убедиться, что пакет существует, укажите для этого свойства значение Present. Чтобы убедиться, что пакет не существует, укажите для этого свойства значение Absent. Значение по умолчанию — Present.|  
| PackageManager| Поддерживаются значения yum, apt и zypper. Указывает, какой диспетчер пакетов нужно использовать при установке пакетов. Если свойство **FilePath** указано, пакет будет установлен по указанному пути. В противном случае для установки пакета будет использоваться диспетчер пакетов из предварительно настроенного репозитория. Если ни одно из свойств **PackageManager** и **FilePath** не указано, будет использоваться диспетчер пакетов, предусмотренный для системы по умолчанию.| 
| FilePath| Путь к файлу пакета.| 
| PackageGroup| Если свойство имеет значение **$true**, в качестве параметра **Name** должно быть задано имя группы пакетов для использования с **PackageManager**. **PackageGroup** не используется, если задано свойство **FilePath**.| 
| Аргументы| Строка аргументов, которая будет передана в пакет в указанном виде.| 
| ReturnCode| Ожидаемый код возврата. Если фактический код возврата не соответствует указанному здесь значению, настройка вернет ошибку.| 
| DependsOn | Указывает, что перед настройкой этого ресурса необходимо запустить настройку другого ресурса. Например, если **идентификатор** первого запускаемого блока сценария для конфигурации ресурса — **ResourceName**, а его тип — **ResourceType**, то синтаксис использования этого свойства таков: `DependsOn = "[ResourceType]ResourceName"`.| 

<a id="example" class="xliff"></a>
## Пример

В следующем примере с помощью диспетчера пакетов Yum проверяется, установлен ли пакет с именем httpd на компьютере Linux.

```
Import-DSCResource -Module nx 

Node $node {
nxPackage httpd
{
    Name = "httpd"
    Ensure = "Present"
    PackageManager = "Yum"
}
}
```

