---
title: Program Azure PowerShell skrypt przykładowy — Pobierz grupę zasobów zarządzanych i rozmiar maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Azure przykładowy skrypt programu PowerShell — Pobierz grupę zasobów zarządzanych i zmień rozmiar maszyny wirtualne
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: poweshell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f549f26cb3f9fdb2d805d2efb2c0e1706abe3edb
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940942"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Pobieranie zasobów w grupie zasobów zarządzanych, a następnie zmień rozmiar maszyn wirtualnych przy użyciu programu PowerShell

Ten skrypt pobiera zasoby w grupie zasobów zarządzanych i zmienia rozmiar maszyn wirtualnych w danej grupie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń do wdrażania aplikacji zarządzanych. Każde polecenie w tabeli łącza do dokumentacji specyficzne dla polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Wyświetl listę zarządzanych aplikacji. Podaj nazwę grupy zasobów, aby skupić się wyniki. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Lista zasobów. Podaj grupę zasobów i typ zasobu skupić się wynik. |
| [AzureRmVM aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Zaktualizować rozmiaru maszyny wirtualnej. |


## <a name="next-steps"></a>Następne kroki

* Aby obejrzeć wprowadzenie do aplikacji zarządzanych, zobacz [zarządzanej aplikacji Azure — omówienie](../overview.md).
* Aby uzyskać więcej informacji dotyczących środowiska PowerShell, zobacz [dokumentacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
