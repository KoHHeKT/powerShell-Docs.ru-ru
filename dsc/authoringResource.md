---
ms.date: 2017-06-12
author: eslesar
ms.topic: conceptual
keywords: "dsc,powershell,конфигурация,установка"
title: "Создание пользовательских ресурсов настройки требуемого состояния Windows PowerShell"
ms.openlocfilehash: 75b494db4ee6e381491decb11d35b60105217a0f
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="build-custom-windows-powershell-desired-state-configuration-resources" class="xliff"></a>
# Создание пользовательских ресурсов настройки требуемого состояния Windows PowerShell

> Область применения: Windows PowerShell 4.0, Windows PowerShell 5.0

Настройка требуемого состояния (DSC) Windows PowerShell включает встроенные ресурсы для настройки среды. (Дополнительные сведения см. в статье [Встроенные ресурсы настройки требуемого состояния (DSC) Windows PowerShell](builtInResource.md).) Этот раздел содержит сведения о разработке ресурсов и ссылок на разделы с описанием особенностей и примерами.

<a id="dsc-resource-components" class="xliff"></a>
## Компоненты ресурсов DSC

Ресурс DSC — это модуль Windows PowerShell. Модуль содержит схему (определение настраиваемых свойств) и реализацию (код, выполняющий заданную конфигурацией работу) для ресурса. Схема ресурсов DSC может быть определена в MOF-файле, а реализация выполняется модулем сценариев. С добавлением поддержки классов PowerShell в версии 5 появилась возможность определять схему и реализацию в классе. Более подробные инструкции по созданию ресурсов DSC см. в следующих статьях:

* [Написание пользовательских ресурсов DSC с использованием MOF](authoringResourceMOF.md) 
* [Реализация ресурса DSC на языке C#](authoringResourceMofCS.md) 
* [Написание пользовательских ресурсов DSC с использованием классов PowerShell](authoringResourceClass.md) 
* [Составные ресурсы: использование конфигурации DSC как ресурса](authoringResourceComposite.md) 
* [Использование конструктора ресурсов](authoringResourceMofDesigner.md) 

