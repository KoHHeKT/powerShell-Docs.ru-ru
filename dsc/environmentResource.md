---
title: "Ресурс Environment в DSC"
ms.date: 2016-05-16
keywords: powershell,DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: 6477ae8575c83fc24150f9502515ff5b82bc8198
ms.openlocfilehash: 20a7711604033b5ff1484dbb526df2642a9a1738

---

# Ресурс Environment в DSC

> Область применения: Windows PowerShell 4.0, Windows PowerShell 5.0

Ресурс __Environment__ в DSC Windows PowerShell предоставляет механизм управления системными переменными среды.

## Синтаксис
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

## Свойства

|  Свойство  |  Описание   | 
|---|---| 
| Название| Указывает имя переменной среды, для которой требуется обеспечить определенное состояние.| 
| Ensure| Указывает, существует ли переменная. Присвойте этому свойству значение __Present__, чтобы создать переменную среды, если она отсутствует, или убедиться, что ее значение соответствует значению свойства __Value__, если переменная уже существует. Задайте значение __Absent__, чтобы удалить переменную, если она существует.| 
| путь| Определяет настраиваемую переменную среды. Для переменной __Path__ присвойте этому свойству значение __$true__; для остальных переменных используйте значение __$false__. Значение по умолчанию — __$false__. Если настраивается переменная __Path__, к существующему значению прикрепляется значение свойства __Value__.| 
| DependsOn | Указывает, что перед настройкой этого ресурса необходимо запустить настройку другого ресурса. Например, если идентификатор первого запускаемого блока сценария для конфигурации ресурса — __ResourceName__, а его тип — __ResourceType__, то синтаксис использования этого свойства таков: `DependsOn = "[ResourceType]ResourceName"`.| 
| Значение| Значение, которое нужно присвоить переменной среды.| 

## Пример

В следующем примере проверяется, существует ли переменная __TestEnvironmentVariable__ и имеет ли она значение __TestValue__. Если переменная не существует, она создается.

```powershell
Environment EnvironmentExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Name = "TestEnvironmentVariable"
    Value = "TestValue"
}
```




<!--HONumber=Aug16_HO3-->


