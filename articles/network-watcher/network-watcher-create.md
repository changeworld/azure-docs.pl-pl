---
title: Tworzenie wystąpienia obserwatora sieci platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć funkcję Kontrola sieci w regionie platformy Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191178"
---
# <a name="create-an-azure-network-watcher-instance"></a>Tworzenie wystąpienia usługi Azure Network Watcher

Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieciowego w, do i z platformy Azure. Monitorowanie na poziomie scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci od końca do końca. Narzędzia diagnostyczne i wizualizacji sieci dostępne za pomocą obserwatora sieci pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. Kontrola sieci jest włączona poprzez utworzenie zasobu Obserwatora sieci. Ten zasób umożliwia korzystanie z funkcji obserwatora sieci.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Funkcja Kontrola sieci jest włączana automatycznie
Podczas tworzenia lub aktualizowania sieci wirtualnej w ramach subskrypcji usługa Network Watcher zostanie automatycznie włączona w regionie sieci wirtualnej. Automatyczne włączanie usługi Network Watcher nie wpływa na zasoby i nie wiąże się z opłatami.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Rezygnacja z automatycznego włączania funkcji Obserwatora sieci
Jeśli chcesz zrezygnować z automatycznego włączania funkcji Network Watcher, możesz to zrobić, uruchamiając następujące polecenia:

> [!WARNING]
> Rezygnacja z automatycznego włączania funkcji Obserwatora sieciowego jest trwałą zmianą. Po rezygnacji nie możesz wyrazić zgody bez [kontaktowania się z pomocą techniczną](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Tworzenie obserwatora sieci w portalu

Przejdź do **pozycji Wszystkie usługi** > **Networking** > **Network Watcher**. Można wybrać wszystkie subskrypcje, dla których chcesz włączyć funkcję Obserwatora sieci. Ta akcja tworzy Obserwatora sieci w każdym regionie, który jest dostępny.

![tworzenie obserwatora sieci](./media/network-watcher-create/figure1.png)

Po włączeniu obserwatora sieciowego za pomocą portalu nazwa wystąpienia Obserwatora sieci jest automatycznie ustawiana *na NetworkWatcher_region_name,* w którym *region_name* odpowiada regionowi platformy Azure, w którym jest włączone wystąpienie. Na przykład obserwator sieci włączony w regionie Zachodnio-środkowe stany USA nosi nazwę *NetworkWatcher_westcentralus*.

Wystąpienie Obserwatora sieci jest automatycznie tworzone w grupie zasobów o nazwie *NetworkWatcherRG*. Grupa zasobów jest tworzona, jeśli jeszcze nie istnieje.

Jeśli chcesz dostosować nazwę wystąpienia obserwatora sieci i grupy zasobów, w której jest umieszczana, możesz użyć programu Powershell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST lub metod ARMClient opisanych w kolejnych sekcjach. W każdej opcji grupa zasobów musi istnieć przed utworzeniem w niej obserwatora sieci.  

## <a name="create-a-network-watcher-with-powershell"></a>Tworzenie obserwatora sieciowego za pomocą programu PowerShell

Aby utworzyć wystąpienie Obserwatora sieci, uruchom następujący przykład:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Tworzenie obserwatora sieci za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć wystąpienie Obserwatora sieci, uruchom następujący przykład:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Tworzenie obserwatora sieciowego za pomocą interfejsu API REST

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Tworzenie obserwatora sieci

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

## <a name="delete-a-network-watcher-in-the-portal"></a>Usuwanie obserwatora sieci w portalu

Przejdź do **pozycji Wszystkie usługi** > **Networking** > **Network Watcher**.

Wybierz kartę przegląd, jeśli jeszcze cię tam nie ma. Użyj listy rozwijanej, aby wybrać subskrypcję, w której chcesz wyłączyć obserwatora sieciowego.
Rozwiń listę regionów wybranej subskrypcji, klikając strzałkę. W przypadku dowolnych 3 punktów po prawej stronie można uzyskać dostęp do menu kontekstowego.
Kliknij "Wyłącz obserwatora sieci", aby rozpocząć wyłączanie. Zostaniesz poproszony o potwierdzenie tego kroku. Kliknij przycisk Tak, aby kontynuować.
Na portalu należy to zrobić indywidualnie dla każdego regionu w każdej subskrypcji.


## <a name="delete-a-network-watcher-with-powershell"></a>Usuwanie obserwatora sieciowego za pomocą programu PowerShell

Aby usunąć wystąpienie Obserwatora sieci, uruchom następujący przykład:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wystąpienie Obserwatora sieci, dowiedz się więcej o dostępnych funkcjach:

* [Topologia](network-watcher-topology-overview.md)
* [Przechwytywanie pakietów](network-watcher-packet-capture-overview.md)
* [Weryfikowanie przepływu adresów IP](network-watcher-ip-flow-verify-overview.md)
* [Następny przeskok](network-watcher-next-hop-overview.md)
* [Widok grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
* [Rejestrowanie przepływu nsg](network-watcher-nsg-flow-logging-overview.md)
* [Rozwiązywanie problemów z bramą sieci wirtualnej](network-watcher-troubleshoot-overview.md)
