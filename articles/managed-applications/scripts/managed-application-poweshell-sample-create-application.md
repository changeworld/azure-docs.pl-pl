---
title: Skrypt programu PowerShell Azure przykładowe — wdrażanie zarządzanych aplikacji | Dokumentacja firmy Microsoft
description: Skrypt programu PowerShell Azure przykładowe — wdrażanie definicji zarządzanej aplikacji
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2017
ms.locfileid: "23941250"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Wdrażanie zarządzanych aplikacji katalogu usług, przy użyciu programu PowerShell

Ten skrypt wdraża definicję zarządzanych aplikacji z katalogu usług.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujące polecenie do wdrażania aplikacji zarządzanych. Każde polecenie w tabeli łącza do dokumentacji specyficzne dla polecenia.

| Polecenie | Uwagi |
|---|---|
| [Nowe AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | Utwórz zarządzaną aplikację. Podaj identyfikator definicji i parametrów szablonu. |


## <a name="next-steps"></a>Następne kroki

* Aby obejrzeć wprowadzenie do aplikacji zarządzanych, zobacz [zarządzanej aplikacji Azure — omówienie](../overview.md).
* Aby uzyskać więcej informacji dotyczących środowiska PowerShell, zobacz [dokumentacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
