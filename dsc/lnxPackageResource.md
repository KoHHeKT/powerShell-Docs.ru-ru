---
title: "Ресурс nxPackage в DSC для Linux"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
ms.openlocfilehash: 31867cc7af96a3d8d527f5906d77bed5206940b4
ms.sourcegitcommit: c732e3ee6d2e0e9cd8c40105d6fbfd4d207b730d
translationtype: HT
---
# <a name="dsc-for-linux-nxpackage-resource"></a>Ресурс nxPackage в DSC для Linux

Ресурс **nxPackage** в настройке требуемого состояния PowerShell предоставляет механизм управления пакетами на узле Linux.

## <a name="syntax"></a>Синтаксис

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

## <a name="properties"></a>Свойства

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

## <a name="example"></a>Пример

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

