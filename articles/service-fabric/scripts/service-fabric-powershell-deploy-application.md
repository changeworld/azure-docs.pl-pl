---
title: Skrypt programu Azure PowerShell przykładowy — wdrażanie aplikacji w klastrze | Dokumentacja firmy Microsoft
description: Skrypt programu Azure PowerShell przykładowy — wdrażanie aplikacji w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 6de7a721a5042a20537d3f09ea9b8de1cc0e7169
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622037"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Ten przykładowy skrypt kopiuje pakietu aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji w klastrze, usuwa pakiet zbędnych aplikacji i tworzy wystąpienie aplikacji na podstawie typu aplikacji.  Jeśli wszystkie usługi domyślne zostały zdefiniowane w manifeście aplikacji typ aplikacji docelowej, te usługi są tworzone w tej chwili. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu zostały, skrypt [usuwania aplikacji](service-fabric-powershell-remove-application.md) można usunąć wystąpienie aplikacji, wyrejestrować typ aplikacji i usunąć pakiet aplikacji z magazynu obrazów.

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Tworzy połączenie z klastrem usługi Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Przechowywanie kopii pakietu aplikacji do obrazu klastra.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Rejestruje typ i wersja aplikacji w klastrze. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Tworzy aplikację z typem zarejestrowanej aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji usługi Service Fabric z magazynu obrazów.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Service Fabric programu PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
