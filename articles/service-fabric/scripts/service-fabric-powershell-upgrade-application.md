---
title: Przykładowy skrypt Azure PowerShell — uaktualnienia aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Przykładowy skrypt Azure PowerShell — uaktualnienia aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2f896e0ff8d05d9c77a4a11c97afd9c0ffc17b7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621761"
---
# <a name="upgrade-a-service-fabric-application"></a>Uaktualnianie aplikacji usługi Service Fabric

Ten przykładowy skrypt aktualizuje wersji 1.3.0 uruchomionego wystąpienia aplikacji usługi Service Fabric. Skrypt kopiuje nowy pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i usuwa pakiet niepotrzebnych aplikacji.  Skrypt uruchamia monitorowanych uaktualnienia i systematycznie sprawdza stan uaktualnienia, aż do ukończenia uaktualniania, lub wycofanie. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Pobiera wszystkie aplikacje w klastrze usługi Service Fabric lub konkretnej aplikacji.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Pobiera stan uaktualniania aplikacji usługi Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Pobiera typy aplikacji usługi Service Fabric, które są zarejestrowane w klastrze usługi Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Wyrejestrowuje typ aplikacji usługi Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopiuje pakiet aplikacji usługi Service Fabric do magazynu obrazów.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Rejestruje typ aplikacji usługi Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Uaktualnienie aplikacji usługi Service Fabric w wersji typu określonej aplikacji. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji usługi Service Fabric z magazynu obrazów.|


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Service Fabric programu PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
