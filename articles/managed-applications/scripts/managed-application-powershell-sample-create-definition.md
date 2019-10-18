---
title: Utwórz definicję aplikacji zarządzanej — Azure PowerShell
description: Zawiera przykładowy skrypt Azure PowerShell, który tworzy definicję aplikacji zarządzanej w ramach subskrypcji platformy Azure.
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
ms.openlocfilehash: 4c0b06ed0e103a88f3f99e1c242dbfe15ba8fd2a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527262"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Tworzenie definicji aplikacji zarządzanej za pomocą programu PowerShell

Ten skrypt publikuje definicję aplikacji zarządzanej w katalogu usług.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia do utworzenia definicji aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Tworzy definicję aplikacji zarządzanej. Należy podać pakiet, który zawiera wymagane pliki. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
