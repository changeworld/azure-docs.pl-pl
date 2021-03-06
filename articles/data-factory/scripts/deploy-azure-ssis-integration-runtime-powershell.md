---
title: Wdrażanie środowiska wykonawczego integracji usługi Azure SSIS przy użyciu programu PowerShell
description: Ten skrypt programu PowerShell tworzy środowisko uruchomieniowe integracji platformy Azure-SSIS, które można uruchamiać pakiety SSIS w chmurze.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929818"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Skrypt programu PowerShell — wdrażanie środowiska wykonawczego integracji usługi Azure-SSIS

Ten przykładowy skrypt programu PowerShell tworzy środowisko uruchomieniowe integracji platformy Azure-SSIS, które można uruchomić pakiety SSIS na platformie Azure.  

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
| [Zestaw-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Tworzy środowisko uruchomieniowe integracji platformy Azure-SSIS, które może uruchamiać pakiety SSIS w chmurze |
| [Czas uruchamiania AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Uruchamia środowisko uruchomieniowe integracji azure-SSIS. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Pobiera informacje o środowiskou wykonywania integracji azure-SSIS. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów usługi Azure Data Factory PowerShell można znaleźć w [przykładach usługi Azure Data Factory PowerShell.](../samples-powershell.md)
