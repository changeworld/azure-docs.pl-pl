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
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991100"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługi Azure Scheduler, która zostanie wycofana. Aby zaplanować prace, [zamiast tego spróbuj wykonać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Tworzenie skryptów do tworzenia i zarządzania harmonogram zadania i kolekcje zadań, można użyć poleceń cmdlet programu PowerShell. W tym artykule wymieniono głównej [poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](/powershell/module/azurerm.scheduler) wraz z łączami do artykułów odwołania. Aby zainstalować program Azure PowerShell dla subskrypcji platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Aby uzyskać więcej informacji na temat [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview), zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

| Polecenie cmdlet | Opis |
|--------|-------------|
| [Wyłącz AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Włącz AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera harmonogram zadań. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera historię zadania. |
| [Nowe AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie usługi HTTP. |
| [Nowe AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [Nowe AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Tworzy zadanie z kolejki usługi Service Bus. |
| [Nowe AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematu usługi Service Bus. |
| [Nowe AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Usuń AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadanie usługi Scheduler. |
| [Usuń AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Zestaw AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadania usługi Scheduler HTTP. |
| [Zestaw AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Zestaw AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadania kolejki usługi Service Bus. |
| [Zestaw AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadania tematu usługi Service Bus. |
| [Zestaw AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadania kolejki magazynu. |
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
