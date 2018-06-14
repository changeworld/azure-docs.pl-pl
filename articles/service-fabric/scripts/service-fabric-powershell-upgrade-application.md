---
title: Przykładowy skrypt programu PowerShell Azure - uaktualniania aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Azure przykładowy skrypt programu PowerShell — uaktualnienie aplikacji sieci szkieletowej usług.
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
ms.openlocfilehash: 889e1bbb71f6eaa1871556b3b9a7da1c28cf16ee
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927917"
---
# <a name="upgrade-a-service-fabric-application"></a>Uaktualnianie aplikacji sieci szkieletowej usług

Ten przykładowy skrypt uaktualnia uruchomione wystąpienie aplikacji sieci szkieletowej usług do wersji 1.3.0. Skrypt kopiuje nowy pakiet aplikacji do magazynu obrazu klastra, rejestruje typ aplikacji i usuwa pakiet niepotrzebnych aplikacji.  Skrypt uruchamia monitorowanych uaktualnienia i stale sprawdza stan uaktualnienia, aż do uaktualnienia ukończenia lub wycofuje. Dostosuj parametry zgodnie z potrzebami. 

Jeśli to konieczne, Zainstaluj moduł programu PowerShell usługi Service Fabric [zestawu SDK usług sieci szkieletowej](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Pobiera wszystkie aplikacje w klastrze usługi sieć szkieletowa usług lub określonych aplikacji.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Pobiera stan uaktualniania aplikacji sieci szkieletowej usług. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Pobiera typy aplikacji usługi sieć szkieletowa zarejestrowany w klastrze usługi sieć szkieletowa usług. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Wyrejestrowuje typ aplikacji sieci szkieletowej usług.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopiuje pakiet aplikacji usługi sieci szkieletowej magazynu obrazów.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Rejestruje typ aplikacji sieci szkieletowej usług. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Uaktualnia aplikacji usługi Service Fabric, do wersji typu określonej aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji sieci szkieletowej usług z magazynu obrazów.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na modułu programu PowerShell usługi Service Fabric, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Dodatkowe przykłady środowiska Powershell dla usługi sieć szkieletowa usług Azure można znaleźć w [przykłady programu Azure PowerShell](../service-fabric-powershell-samples.md).
