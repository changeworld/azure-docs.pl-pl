---
title: Dokumentacja poleceń cmdlet programu PowerShell — usługa Azure Scheduler
description: Więcej informacji na temat poleceń cmdlet programu PowerShell dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b179c50af8b1ffc4313a49da978f178915ec9cc
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489900"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tworzenie skryptów do tworzenia i zarządzania harmonogram zadania i kolekcje zadań, można użyć poleceń cmdlet programu PowerShell. W tym artykule wymieniono główne poleceń cmdlet programu PowerShell dla usługi Azure Scheduler, wraz z łączami do artykułów odwołania. Aby zainstalować program Azure PowerShell dla subskrypcji platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Aby uzyskać więcej informacji na temat [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview), zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

| Polecenie cmdlet | Opis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera harmonogram zadań. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera historię zadania. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie usługi HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Tworzy zadanie z kolejki usługi Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematu usługi Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadanie usługi Scheduler. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadania usługi Scheduler HTTP. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadania kolejki usługi Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadania tematu usługi Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadania kolejki magazynu. |
||| 

Aby uzyskać więcej informacji możesz uruchomić dowolne z tych poleceń cmdlet: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa Azure Scheduler?](scheduler-intro.md)
* [Pojęcia, terminologia oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Tworzenie i planowanie pierwszego zadania — Azure portal](scheduler-get-started-portal.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
