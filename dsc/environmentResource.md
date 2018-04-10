---
ms.date: 06/12/2017
ms.topic: conceptual
keywords: dsc,powershell,конфигурация,установка
title: Ресурс Environment в DSC
ms.openlocfilehash: 4f024afe2d70c13e19406745ec7fd69821ab229b
ms.sourcegitcommit: cf195b090b3223fa4917206dfec7f0b603873cdf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2018
---
# <a name="dsc-environment-resource"></a>Ресурс Environment в DSC

> Область применения: Windows PowerShell 4.0, Windows PowerShell 5.0

Ресурс __Environment__ в DSC Windows PowerShell предоставляет механизм управления системными переменными среды.

## <a name="syntax"></a>Синтаксис
``` mof
Environment [string] #ResourceName
{
    Name = [string]
    [ Ensure = [string] { Absent | Present }  ]
    [ Path = [bool] ]
    [ DependsOn = [string[]] ]
    [ Value = [string] ]
}
```

## <a name="properties"></a>Свойства

|  Свойство  |  Описание   |
|---|---|
| Name| Указывает имя переменной среды, для которой требуется обеспечить определенное состояние.|
| Ensure| Указывает, существует ли переменная. Присвойте этому свойству значение __Present__, чтобы создать переменную среды, если она отсутствует, или убедиться, что ее значение соответствует значению свойства __Value__, если переменная уже существует. Задайте значение __Absent__, чтобы удалить переменную, если она существует.|
| путь| Определяет настраиваемую переменную среды. Для переменной __Path__ присвойте этому свойству значение __$true__; для остальных переменных используйте значение __$false__. Значение по умолчанию — __$false__. Если настраивается переменная __Path__, к существующему значению прикрепляется значение свойства __Value__.|
| DependsOn | Указывает, что перед настройкой этого ресурса необходимо запустить настройку другого ресурса. Например, если идентификатор первого запускаемого блока сценария для конфигурации ресурса — __ResourceName__, а его тип — __ResourceType__, то синтаксис использования этого свойства таков: `DependsOn = "[ResourceType]ResourceName"`.|
| Значение| Значение, которое нужно присвоить переменной среды.|

## <a name="example"></a>Пример

В следующем примере проверяется, существует ли переменная __TestEnvironmentVariable__ и имеет ли она значение __TestValue__. Если переменная не существует, она создается.

```powershell
Environment EnvironmentExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Name = "TestEnvironmentVariable"
    Value = "TestValue"
}
```