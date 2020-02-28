---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: 1bc83a4c93d4fbd252a99cb801e7484a8e15a689
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780154"
---
|Name (Nazwa) |Opis |Zasady |Wersja |
|---|---|---|---|
|[Włącz Azure Monitor dla Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz Azure Monitor dla Virtual Machine Scale Sets w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. Uwaga: Jeśli zestaw skalowania upgradePolicy jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia będzie to AZ VMSS Update-Instances. |6 |1.0.0 — wersja zapoznawcza |
|[Włącz Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz Azure Monitor dla Virtual Machines (maszyn wirtualnych) w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. |6 |1.0.0 — wersja zapoznawcza |
