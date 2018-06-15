---
title: Skrypt programu PowerShell Azure przykładowe — Tworzenie definicji aplikacji zarządzanej | Dokumentacja firmy Microsoft
description: Skrypt programu PowerShell Azure przykładowe — Tworzenie definicji aplikacji zarządzanej
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
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940956"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Utwórz definicję aplikacji zarządzanej przy użyciu programu PowerShell

Ten skrypt publikuje definicję aplikacji zarządzanych do katalogu usług.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następującego polecenia do tworzenia definicji zarządzanej aplikacji. Każde polecenie w tabeli łącza do dokumentacji specyficzne dla polecenia.

| Polecenie | Uwagi |
|---|---|
| [Nowe AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | Utwórz definicję zarządzanej aplikacji. Podaj pakiet, który zawiera pliki wymagane. |


## <a name="next-steps"></a>Następne kroki

* Aby obejrzeć wprowadzenie do aplikacji zarządzanych, zobacz [zarządzanej aplikacji Azure — omówienie](../overview.md).
* Aby uzyskać więcej informacji dotyczących środowiska PowerShell, zobacz [dokumentacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
