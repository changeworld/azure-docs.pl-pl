---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 074d28f8144245247944f9c3409507d331c81166
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758566"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[Włączanie zestawów skalowania usługi Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz usługę Azure Monitor dla zestawów skalowania maszyny wirtualnej w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. Uwaga: jeśli uaktualnienie zestawu skalowaniaPolicy jest ustawiona na Ręcznie, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując uaktualnienie na nich. W cli byłoby az vmss update-wystąpień. |6 |1.0.1 |
|[Włączanie usługi Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz usługę Azure Monitor dla maszyn wirtualnych (maszyn wirtualnych) w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. |6 |1.0.1 |
