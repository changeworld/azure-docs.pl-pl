---
title: Przykładowy skrypt programu Azure PowerShell — konfigurowanie domeny niestandardowej | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — konfigurowanie domeny niestandardowej
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 12/14/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b120f3d2731601652d46f71206637bacdda023cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="set-up-custom-domain"></a>Konfigurowanie domeny niestandardowej

Ten przykładowy skrypt konfiguruje domenę niestandardową na serwerze proxy i punkt końcowy portalu usługi API Management.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/api-management/setup-custom-domain/setup_custom_domain.ps1 "Set up custom domain")]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

[!INCLUDE [api-management-custom-domain](../../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure API Management można znaleźć w [przykładach programu PowerShell](../powershell-samples.md).
