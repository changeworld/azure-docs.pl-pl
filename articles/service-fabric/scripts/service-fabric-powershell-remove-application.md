---
title: Przykładowy skrypt programu PowerShell Azure - Usuń aplikację z klastra | Dokumentacja firmy Microsoft
description: Azure przykładowy skrypt programu PowerShell — Usuń aplikację z klastra usługi sieć szkieletowa usług.
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
ms.openlocfilehash: ea7037fc3655298fc4c03b8d9f988a55e42c9fe9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927866"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Usuń aplikację z klastra sieci szkieletowej usług

Ten przykładowy skrypt usuwa uruchomione wystąpienie aplikacji sieci szkieletowej usług i wyrejestrowuje typ i wersja aplikacji z klastra.  Usuwanie wystąpienia aplikacji spowoduje również usunięcie wszystkich uruchomionych wystąpień skojarzoną z daną aplikacją usługi. Dostosuj parametry zgodnie z potrzebami. 

Jeśli to konieczne, Zainstaluj moduł programu PowerShell usługi Service Fabric [zestawu SDK usług sieci szkieletowej](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Usuwa uruchomione wystąpienie aplikacji sieci szkieletowej usług z klastra.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Wyrejestrowuje sieci szkieletowej usług typ i wersja aplikacji z klastra. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na modułu programu PowerShell usługi Service Fabric, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Dodatkowe przykłady środowiska Powershell dla usługi sieć szkieletowa usług Azure można znaleźć w [przykłady programu Azure PowerShell](../service-fabric-powershell-samples.md).
