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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045001"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tworzenie skryptów do tworzenia i zarządzania harmonogram zadania i kolekcje zadań, można użyć poleceń cmdlet programu PowerShell. W tym artykule wymieniono głównej [poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](/powershell/module/azurerm.scheduler) wraz z łączami do artykułów odwołania. Aby zainstalować program Azure PowerShell dla subskrypcji platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Aby uzyskać więcej informacji na temat [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview), zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

| Polecenie cmdlet | Opis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Pobiera harmonogram zadań. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Pobiera historię zadania. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Tworzy zadanie usługi HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Tworzy kolekcję zadań. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Tworzy zadanie z kolejki usługi Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Tworzy zadanie tematu usługi Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Usuwa zadanie usługi Scheduler. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Modyfikuje zadania usługi Scheduler HTTP. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Modyfikuje zadania kolejki usługi Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Modyfikuje zadania tematu usługi Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Modyfikuje zadania kolejki magazynu. |
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
