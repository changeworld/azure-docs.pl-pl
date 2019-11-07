---
title: Skrypt programu PowerShell — wdrażanie środowiska Azure-SSIS Integration Runtime
description: Ten skrypt programu PowerShell tworzy środowisko Azure-SSIS Integration Runtime, które może uruchamiać pakiety usług SSIS w chmurze.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: beba0a8d01d7d10910716f03602116fe7673ffc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684361"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Skrypt programu PowerShell — wdrażanie środowiska Azure-SSIS Integration Runtime

Ten przykładowy skrypt programu PowerShell tworzy środowisko Azure-SSIS Integration Runtime, które może uruchamiać pakiety usług SSIS na platformie Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowego skryptu można użyć następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Aby usunąć fabrykę danych z grupy zasobów, uruchom następujące polecenie: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Tworzy środowisko Azure-SSIS Integration Runtime, które może uruchamiać pakiety usług SSIS w chmurze |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Uruchamia środowisko Azure-SSIS Integration Runtime. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Pobiera informacje o środowisku Azure-SSIS Integration Runtime. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Data Factory PowerShell można znaleźć w [przykładach Azure Data Factory programu PowerShell](../samples-powershell.md).
