---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: f06a78039217d7bc6b03c9196ac6824477ea977e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79381903"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[Włączanie zestawów skalowania usługi Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz usługę Azure Monitor dla zestawów skalowania maszyny wirtualnej w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. Uwaga: jeśli uaktualnienie zestawu skalowaniaPolicy jest ustawiona na Ręcznie, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując uaktualnienie na nich. W cli byłoby az vmss update-wystąpień. |6 |1.0.1 |
|[Włączanie usługi Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz usługę Azure Monitor dla maszyn wirtualnych (maszyn wirtualnych) w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. |6 |1.0.1 |
