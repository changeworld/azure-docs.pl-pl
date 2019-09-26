---
title: Dokumentacja poleceń cmdlet programu PowerShell — usługa Azure Scheduler
description: Informacje o poleceniach cmdlet programu PowerShell dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300880"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby tworzyć skrypty do tworzenia zadań harmonogramu i kolekcji zadań oraz zarządzania nimi, można użyć poleceń cmdlet programu PowerShell. Ten artykuł zawiera listę głównych poleceń cmdlet programu PowerShell dla usługi Azure Scheduler z linkami do artykułów referencyjnych. Aby zainstalować Azure PowerShell dla subskrypcji platformy Azure, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/overview). Więcej informacji o [poleceniach cmdlet Azure Resource Manager](/powershell/azure/overview)można znaleźć w temacie [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md).

| Polecenia cmdlet | Opis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera zadania usługi Scheduler. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera historię zadania. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Tworzy zadanie kolejki Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematu Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadanie usługi Scheduler. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadanie HTTP usługi Scheduler. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadanie Service Bus kolejki. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadanie tematu Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadanie kolejki magazynu. |
||| 

Aby uzyskać więcej informacji, możesz uruchomić dowolne z następujących poleceń cmdlet: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia, terminologia oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Utwórz i Zaplanuj pierwsze zadanie — Azure Portal](scheduler-get-started-portal.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
