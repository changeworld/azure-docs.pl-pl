---
title: Wdrażanie aplikacji w klastrze w programie Powershell
description: Przykład skryptu programu Azure PowerShell — wdrażanie aplikacji w klastrze sieci szkieletowej usług.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610305"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Ten przykładowy skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji w klastrze, usuwa niepotrzebny pakiet aplikacji i tworzy wystąpienie aplikacji z typu aplikacji.  Jeśli wszystkie usługi domyślne zostały zdefiniowane w manifeście aplikacji typu aplikacji docelowej, usługi te są tworzone w tej chwili. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładu skryptu skrypt w [usuń aplikację](service-fabric-powershell-remove-application.md) może służyć do usunięcia wystąpienia aplikacji, wyrejestrowania typu aplikacji i usunięcia pakietu aplikacji z magazynu obrazów.

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Tworzy połączenie z klastrem sieci szkieletowej usług. |
|[Kopiuj-ServiceFabricApplicationPakiet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopiuje pakiet aplikacji do magazynu obrazów klastra.  |
|[Zarejestruj się-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Rejestruje typ aplikacji i wersję w klastrze. |
|[Nowa usługaFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Tworzy aplikację z zarejestrowanego typu aplikacji. |
| [Usuń-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji sieci szkieletowej usług z magazynu obrazów.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu powershell sieci szkieletowej usług, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
