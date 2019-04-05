---
title: Tworzenie wystąpienia usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć usługę Network Watcher w regionie platformy Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 601a3f273a8da9100d24dfdbd13bd598b0e48884
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051567"
---
# <a name="create-an-azure-network-watcher-instance"></a>Tworzenie wystąpienia usługi Azure Network Watcher

Network Watcher to regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na poziomie sieci, do i z platformy Azure. Monitorowania na poziomie scenariusza pozwala diagnozować problemy w widoku poziomu typu end to end sieci. Diagnostyka sieci i narzędzi do wizualizacji dostępne w usłudze Network Watcher pomagają zrozumieć, diagnozowanie i uzyskiwanie szczegółowych informacji do sieci na platformie Azure. Usługa Network Watcher jest włączana za pomocą utworzony zasób usługi Network Watcher. Ten zasób umożliwia korzystanie z funkcji usługi Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Usługa Network Watcher jest automatycznie włączone.
Podczas tworzenia lub aktualizowania sieci wirtualnej w ramach subskrypcji usługa Network Watcher zostanie automatycznie włączona w regionie sieci wirtualnej. Automatyczne włączanie usługi Network Watcher nie wpływa na zasoby i nie wiąże się z opłatami.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Zrezygnować z automatycznego włączania usługi Network Watcher
Jeśli chcesz zrezygnować z automatycznego włączania usługi Network Watcher, możesz to zrobić, uruchamiając następujące polecenia:

> [!WARNING]
> Rezygnacja z automatycznego włączania usługi Network Watcher jest trwałych zmian. Po użytkownik zrezygnować możesz nie wymaga zgody na uczestnictwo bez [kontaktując się z pomocą techniczną](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Tworzenie usługi Network Watcher w portalu

Przejdź do **wszystkich usług** > **sieć** > **Network Watcher**. Można wybrać wszystkie subskrypcje, które chcesz włączyć usługi Network Watcher dla. Ta akcja powoduje utworzenie usługi Network Watcher w każdym regionie, który jest dostępny.

![Tworzenie usługi network watcher](./media/network-watcher-create/figure1.png)

Po włączeniu usługi Network Watcher przy użyciu portalu, nazwę wystąpienia usługi Network Watcher jest automatycznie równa *NetworkWatcher_region_name* gdzie *region_name* odnosi się do regionu platformy Azure gdy wystąpienie jest włączone. Na przykład, nosi nazwę usługi Network Watcher w regionie zachodnio-środkowe stany USA włączone *NetworkWatcher_westcentralus*.

Automatycznie tworzone jest wystąpienie usługi Network Watcher w grupie zasobów o nazwie *NetworkWatcherRG*. Jeśli jeszcze nie istnieje, utworzona jest grupa zasobów.

Jeśli chcesz dostosować nazwę wystąpienia usługi Network Watcher i grupy zasobów jest umieszczana w, można użyć programu Powershell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST lub metody ARMClient opisane w kolejnych sekcjach. W przypadku poszczególnych opcji grupy zasobów musi istnieć przed utworzeniem usługi Network Watcher w nim.  

## <a name="create-a-network-watcher-with-powershell"></a>Tworzenie usługi Network Watcher przy użyciu programu PowerShell

Aby utworzyć wystąpienie usługi Network Watcher, uruchom poniższy przykład:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Tworzenie usługi Network Watcher przy użyciu wiersza polecenia platformy Azure

Aby utworzyć wystąpienie usługi Network Watcher, uruchom poniższy przykład:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Tworzenie usługi Network Watcher przy użyciu interfejsu API REST

ARMclient jest używane do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey na [ARMClient na narzędzia Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Zaloguj się przy użyciu ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Tworzenie usługi network watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wystąpienie usługi Network Watcher, Dowiedz się więcej o funkcjach dostępnych:

* [Topologia](network-watcher-topology-overview.md)
* [Przechwytywanie pakietów](network-watcher-packet-capture-overview.md)
* [Weryfikowanie przepływu adresów IP](network-watcher-ip-flow-verify-overview.md)
* [Następny przeskok](network-watcher-next-hop-overview.md)
* [Widok grup zabezpieczeń](network-watcher-security-group-view-overview.md)
* [Rejestrowanie przepływu sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md)
* [Rozwiązywanie problemów bramy sieci wirtualnej](network-watcher-troubleshoot-overview.md)

Po wystąpieniu usługi Network Watcher umożliwia przechwytywanie pakietów na maszynach wirtualnych. Aby dowiedzieć się więcej, zobacz temat [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)
