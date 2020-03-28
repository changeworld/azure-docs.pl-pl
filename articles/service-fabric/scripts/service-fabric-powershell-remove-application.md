---
title: Usuwanie aplikacji z klastra w programie Powershell
description: Przykład skryptu programu Azure PowerShell — usuwanie aplikacji z klastra sieci szkieletowej usług.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610288"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Usuwanie aplikacji z klastra sieci szkieletowej usług przy użyciu programu Powershell

Ten przykładowy skrypt usuwa uruchomione wystąpienie aplikacji sieci szkieletowej usług i wyrejestrowyje typ aplikacji i wersję z klastra.  Usuwanie wystąpienia aplikacji spowoduje usunięcie wszystkich uruchomionych wystąpień usługi skojarzonych z aplikacją. Dostosuj parametry zgodnie z potrzebami. 

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Usuń-ServiceFabricAplikacja](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Usuwa uruchomione wystąpienie aplikacji sieci szkieletowej usług z klastra.  |
| [Wyrejestrowanie-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Wyrejestrowanie typu aplikacji sieci szkieletowej usług i wersji z klastra. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu powershell sieci szkieletowej usług, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
