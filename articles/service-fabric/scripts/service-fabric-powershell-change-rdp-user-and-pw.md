---
title: Przykładowy skrypt programu Azure PowerShell — aktualizowanie nazwy użytkownika i hasła protokołu RDP | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — aktualizowanie nazwy użytkownika i hasła protokołu RDP dla wszystkich węzłów klastra usługi Service Fabric określonego typu węzła.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ff9cfabc4ac7b759a916ddaaeb3f4c95ceecd452
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aktualizowanie nazwy użytkownika i hasła administratora maszyn wirtualnych w klastrze

Każdy [typ węzła](../service-fabric-cluster-nodetypes.md) w klastrze usługi Service Fabric jest zestawem skalowania maszyn wirtualnych. Ten przykładowy skrypt aktualizuje nazwę użytkownika i hasło dla maszyn wirtualnych klastra w określonym typie węzła.  Dodaj rozszerzenie VMAccessAgent do zestawu skalowania, ponieważ hasło administratora nie jest wartością zestawu skalowania, którą można modyfikować.  Zmiany nazwy użytkownika i hasła dotyczą wszystkich węzłów w zestawie skalowania. Dostosuj parametry zgodnie z potrzebami.

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji dostępnej w [przewodniku programu Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Pobiera właściwości typu węzła klastra (zestaw skalowania maszyn wirtualnych).   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Dodaje rozszerzenie do zestawu skalowania maszyn wirtualnych.|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Aktualizuje stan zestawu skalowania maszyn wirtualnych do stanu obiektu lokalnego VMSS.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
