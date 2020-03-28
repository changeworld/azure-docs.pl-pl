---
title: Tworzenie klastra sieci szkieletowej usług w programie Powershell
description: Przykład skryptu programu Azure PowerShell — tworzenie klastra sieci szkieletowej usług zabezpieczone certyfikatem X.509.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: f8e1a0ca86f9346cf07c87a738d48cb56f6d7d57
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614778"
---
# <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric

Ten przykładowy skrypt tworzy klaster usługi Service Fabric z pięcioma węzłami zabezpieczony za pomocą certyfikatu X.509.  Polecenie tworzy certyfikat z podpisem własnym i przekazuje go do nowego magazynu kluczy. Certyfikat jest też kopiowany do katalogu lokalnego.  Ustaw parametr *-OS,* aby wybrać wersję systemu Windows lub Linux, która działa w węzłach klastra.  Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, klaster i wszystkie powiązane zasoby.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Nowy-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Tworzy nowy klaster usługi Service Fabric. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
