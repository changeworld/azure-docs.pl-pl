---
title: Uaktualnianie aplikacji sieci szkieletowej usług w programie Powershell
description: Przykład skryptu programu Azure PowerShell — uaktualnianie i monitorowanie aplikacji sieci szkieletowej usług Azure przy użyciu programu Powershell.
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
ms.openlocfilehash: 3a4ef9fad8567eb145d51c6fef61773cc3a00b11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614744"
---
# <a name="upgrade-a-service-fabric-application"></a>Uaktualnianie aplikacji sieci szkieletowej usług

Ten przykładowy skrypt uaktualnia uruchomione wystąpienie aplikacji sieci szkieletowej usług do wersji 1.3.0. Skrypt kopiuje nowy pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i usuwa niepotrzebny pakiet aplikacji.  Skrypt uruchamia monitorowane uaktualnienie i stale sprawdza stan uaktualnienia, dopóki uaktualnienie nie zostanie zakończone lub wycofane. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Wniosek o świadczenie Get-ServiceFabric](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Pobiera wszystkie aplikacje w klastrze sieci szkieletowej usług lub określonej aplikacji.  |
| [Get-ServiceFabricApplicationPoustuj](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Pobiera stan uaktualnienia aplikacji sieci szkieletowej usług. |
| [Get-ServiceFabricZastosowanieType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Pobiera typy aplikacji sieci szkieletowej usług zarejestrowane w klastrze sieci szkieletowej usług. |
| [Wyrejestrowanie-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Wyrejestrowaj typ aplikacji sieci szkieletowej usług.  |
| [Kopiuj-ServiceFabricApplicationPakiet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopiuje pakiet aplikacji sieci szkieletowej usług do magazynu obrazów.  |
| [Zarejestruj się-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Rejestruje typ aplikacji sieci szkieletowej usług. |
| [Start-ServiceFabricApplicationPoustuj](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Uaktualnia aplikację sieci szkieletowej usług do określonej wersji typu aplikacji. |
| [Usuń-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Usuwa pakiet aplikacji sieci szkieletowej usług z magazynu obrazów.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu powershell sieci szkieletowej usług, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
