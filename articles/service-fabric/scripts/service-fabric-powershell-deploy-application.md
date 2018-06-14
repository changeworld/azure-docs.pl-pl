---
title: Skrypt programu PowerShell Azure Przykładowe — wdrożenie aplikacji do klastra | Dokumentacja firmy Microsoft
description: Skrypt programu PowerShell Azure przykładowe — wdrażanie aplikacji do klastra usługi sieć szkieletowa usług.
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
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c81514fb4b1c1da483ebd55deae149caf22d4b63
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927604"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Wdrażanie aplikacji do klastra sieci szkieletowej usług

Ten przykładowy skrypt kopiuje pakietu aplikacji do magazynu obrazu klastra, rejestruje typ aplikacji w klastrze, usuwa pakiet zbędnych aplikacji i tworzy wystąpienie aplikacji na podstawie typu aplikacji.  Jeśli wszystkie domyślne usługi zostały zdefiniowane w manifeście aplikacji typu aplikacji docelowej, te usługi są tworzone w tym momencie. Dostosuj parametry zgodnie z potrzebami. 

Jeśli to konieczne, Zainstaluj moduł programu PowerShell usługi Service Fabric [zestawu SDK usług sieci szkieletowej](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po przykładowym skrypcie zostało uruchomione, skrypt w [usuwania aplikacji](service-fabric-powershell-remove-application.md) można usunąć wystąpienia aplikacji, wyrejestrowywanie typu aplikacji i usuwanie pakietu aplikacji w magazynie obrazów.

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Tworzy połączenie z klastrem usługi sieć szkieletowa usług. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Przechowywanie kopii pakietu aplikacji do obrazu klastra.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Rejestruje typ i wersja aplikacji w klastrze. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Tworzy aplikację z typ zarejestrowanych aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji sieci szkieletowej usług z magazynu obrazów.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na modułu programu PowerShell usługi Service Fabric, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Dodatkowe przykłady środowiska Powershell dla usługi sieć szkieletowa usług Azure można znaleźć w [przykłady programu Azure PowerShell](../service-fabric-powershell-samples.md).
