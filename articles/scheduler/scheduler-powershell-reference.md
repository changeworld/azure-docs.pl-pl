---
title: Odwołanie do poleceń cmdlet programu PowerShell
description: Dowiedz się więcej o poleceniach cmdlet programu PowerShell dla harmonogramu platformy Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898494"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Odwołanie do poleceń cmdlet programu PowerShell dla harmonogramu platformy Azure

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

Aby utworzyć skrypty do tworzenia i zarządzania zadaniami harmonogramu i kolekcjami zadań, można użyć poleceń cmdlet programu PowerShell. W tym artykule wymieniono główne polecenia cmdlet programu PowerShell dla harmonogramu platformy Azure z łączami do ich artykułów referencyjnych. Aby zainstalować program Azure PowerShell dla subskrypcji platformy Azure, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview). Aby uzyskać więcej informacji na temat [poleceń cmdlet usługi Azure Resource Manager,](/powershell/azure/overview)zobacz [Korzystanie z programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Polecenie cmdlet | Opis |
|--------|-------------|
| [Wyłącz-AzSchedulerKolection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzSchedulerObob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera zadania harmonogramu. |
| [Get-AzSchedulerKolekcja](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera historię zadań. |
| [Nowy-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie HTTP. |
| [Nowa-AzSchedulerKolection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [Nowy-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Tworzy zadanie kolejki usługi Service Bus. |
| [Nowy-AzSchedulerServiceBusTopicOb](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematu usługi Service Bus. |
| [Nowy-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Usuń-AzSchedulerObob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadanie harmonogramu. |
| [Usuń-AzSchedulerKolekcja](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadanie HTTP harmonogramu. |
| [Zestaw-AzSchedulerKolection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Zestaw-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadanie kolejki usługi Service Bus. |
| [Set-AzSchedulerServiceBusTopicOb](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadanie tematu usługi Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadanie kolejki magazynu. |
||| 

Aby uzyskać więcej informacji, można uruchomić dowolną z tych poleceń cmdlet: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)