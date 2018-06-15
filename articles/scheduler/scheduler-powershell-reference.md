---
title: Dokumentacja poleceń cmdlet programu PowerShell harmonogramu
description: Dokumentacja poleceń cmdlet programu PowerShell harmonogramu
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 141919ab4506b3de4c4a69670dcf54c60ee6409c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866051"
---
# <a name="scheduler-powershell-cmdlets-reference"></a>Dokumentacja poleceń cmdlet programu PowerShell harmonogramu
Poniższa tabela zawiera opis oraz łączy na stronie poszczególnych głównych poleceń cmdlet w harmonogramie Azure.

Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview). 

Aby uzyskać więcej informacji na temat [poleceń cmdlet usługi Azure Resource Manager](/powershell/azure/overview), zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

| Polecenie cmdlet | Opis polecenia cmdlet |
| --- | --- |
| [Wyłącz AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcji zadań. |
| [Włącz AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Umożliwia kolekcji zadań. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera harmonogram zadań. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera zadań kolekcji. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera Historia zadania. |
| [Nowe AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie HTTP. |
| [Nowe AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [Nowe AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) |Tworzy zadanie kolejki magistrali usług. |
| [Nowe AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematów magistrali usługi. |
| [Nowe AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Usuń AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadania harmonogramu. |
| [Usuń AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcji zadań. |
| [Zestaw AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadania harmonogramu HTTP. |
| [Zestaw AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcji zadań. |
| [Zestaw AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadania kolejki magistrali usług. |
| [Zestaw AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadania tematów magistrali usługi. |
| [Zestaw AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadanie kolejki magazynu. |

Aby uzyskać szczegółowe informacje można uruchomić dowolną z następujących poleceń cmdlet: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

