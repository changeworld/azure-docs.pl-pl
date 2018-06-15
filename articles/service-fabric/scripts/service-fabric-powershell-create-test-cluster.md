---
title: Przykład skryptu programu Azure PowerShell — tworzenie klastra usługi Service Fabric | Microsoft Docs
description: Przykład skryptu programu Azure PowerShell — tworzenie klastra testowego usługi Service Fabric z trzema węzłami.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 169f68d179c7f895078fe649d0e2a69e58d148cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597507"
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Tworzenie klastra testowego usługi Service Fabric z trzema węzłami

Ten przykładowy skrypt tworzy klaster testowy usługi Service Fabric z trzema węzłami zabezpieczony za pomocą certyfikatu X.509. Konfiguracja klastra z trzema węzłami jest obsługiwana na potrzeby tworzenia i testowania, ponieważ można bezpiecznie wykonywać uaktualnienia i przetrwać awarie pojedynczych węzłów (o ile nie będą się zdarzać jednocześnie). Klaster produkcyjny wymaga co najmniej pięciu węzłów, aby był odporny na jednoczesne awarie.  

Polecenie tworzy certyfikat z podpisem własnym i przekazuje go do nowego magazynu kluczy, który jest tworzony w tej samej grupie zasobów co klaster. Certyfikat jest też kopiowany do katalogu lokalnego.  Ustaw parametr *-OS*, aby wybrać wersję systemu Windows lub Linux działającego w węzłach klastra.  Dostosuj parametry zgodnie z potrzebami.

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, klaster i wszystkie powiązane zasoby.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Tworzy nowy klaster usługi Service Fabric. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
