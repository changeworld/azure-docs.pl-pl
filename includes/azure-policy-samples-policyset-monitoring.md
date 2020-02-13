---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8fe3dca69974f1df09ffb9ca4e1ece5a614f61f5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172968"
---
|Name (Nazwa) |Opis |Zasady |Wersja |
|---|---|---|---|
|[Włącz Azure Monitor dla VM Scale Sets (VMSS)](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz Azure Monitor dla VM Scale Sets w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. Uwaga: Jeśli zestaw skalowania upgradePolicy jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia będzie to AZ VMSS Update-Instances. |6 |1.0.0 — wersja zapoznawcza |
|[Włącz Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz Azure Monitor dla Virtual Machines (maszyn wirtualnych) w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. |6 |1.0.0 — wersja zapoznawcza |
