---
title: Usuwanie klastra usługi Service Fabric na platformie Azure | Microsoft Docs
description: W ramach tego samouczka dowiesz się, jak usunąć klaster usługi Service Fabric hostowany na platformie Azure razem ze wszystkimi zasobami. Istnieje możliwość usunięcia grupy zasobów zawierającej klaster lub selektywnego usuwania zasobów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 38e776a5398ea5217a0d7e385d6ebb45d2199d51
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044611"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Samouczek: usuwanie klastra usługi Service Fabric uruchomionego na platformie Azure

W tym samouczku znajduje się pięć częścią serii i dowiesz się, jak usunąć klaster usługi Service Fabric działających na platformie Azure. Aby całkowicie usunąć klaster usługi Service Fabric, musisz również usunąć wszystkie zasoby używane przez klaster. Dostępne są dwie opcje: usunięcie grupy zasobów, do której należy klaster (co powoduje usunięcie zasobu klastra i wszystkich innych zasobów w grupie zasobów) lub usunięcie konkretnie zasobu klastra i jego powiązanych zasobów (lecz nie innych zasobów w grupie zasobów).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie grupy zasobów i jej wszystkich zasobów
> * Selektywne usuwanie zasobów z grupy zasobów

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * Usuwanie klastra


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [programu Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) lub [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Usuwanie grupy zasobów zawierającej klaster usługi Service Fabric
Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Logowanie do platformy Azure i wybierz identyfikator subskrypcji, z którą chcesz usunąć klaster.  Identyfikator subskrypcji można uzyskać po zalogowaniu się do [witryny Azure Portal](https://portal.azure.com). Usuń grupę zasobów i wszystkie zasoby klastra, używając [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) polecenia cmdlet lub [usunięcie grupy az](/cli/azure/group?view=azure-cli-latest) polecenia.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Selektywne usuwanie zasobu klastra i skojarzonych zasobów
Jeśli grupa zasobów zawiera tylko zasoby powiązane z klastrem usługi Service Fabric do usunięcia, to łatwiej usunąć całą grupę zasobów. Jeśli chcesz selektywnie usunąć zasoby z grupy zasobów i zachować zasoby nieskojarzone z klastrem, wykonaj następujące kroki.

Wyświetl zasoby w grupie zasobów:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Dla każdego z zasobów do usunięcia uruchom następujący skrypt:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Aby usunąć zasób klastra, uruchom następujący skrypt:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie grupy zasobów i jej wszystkich zasobów
> * Selektywne usuwanie zasobów z grupy zasobów

Po ukończeniu tego samouczka spróbuj wykonać następujące czynności:
* Dowiedz się, jak przeprowadzić inspekcję klastra usługi Service Fabric i zarządzać nim przy użyciu programu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Dowiedz się, jak [zastosować poprawki systemu operacyjnego Windows](service-fabric-patch-orchestration-application.md) w węzłach klastra.
* Dowiedz się, jak agregować i zbierać zdarzenia z [klastrów systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md) oraz jak [skonfigurować usługę Log Analytics](service-fabric-diagnostics-oms-setup.md) pod kątem monitorowania zdarzeń klastra.
ms-setup.md) to monitor cluster events.
