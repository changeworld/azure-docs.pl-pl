---
title: 'Konfigurowanie usługi ExpressRoute, zasięgu globalnym: Interfejs wiersza polecenia Azure | Dokumentacja firmy Microsoft'
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384064"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Konfigurowanie usługi ExpressRoute globalny zasięg przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)
Ten artykuł ułatwia konfigurowanie usługi ExpressRoute zasięgu globalnym przy użyciu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [zasięgu globalnym ExpressRouteRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Przed rozpoczęciem konfiguracji należy sprawdzić następujące wymagania.

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [zainstalować interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).
* Zrozumienie aprowizacji obwodu usługi ExpressRoute [przepływy pracy](expressroute-workflows.md).
* Upewnij się, że obwodów usługi ExpressRoute znajdują się w stanie Aprowizowana.
* Upewnij się, że prywatnej komunikacji równorzędnej Azure jest skonfigurowany na obwodów usługi ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Zaloguj się do konta platformy Azure
Aby uruchomić konfigurację, należy zalogować się do konta platformy Azure. Polecenie spowoduje to otworzenie domyślnej przeglądarki i wyświetlenie monitu o poświadczenia logowania dla konta platformy Azure.  

```azurecli
az login
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```azurecli
az account list
```

Wskaż subskrypcję, której chcesz użyć.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identyfikowanie obwodów usługi ExpressRoute dla konfiguracji
Aby umożliwić zasięgu globalnym usługi ExpressRoute między wszystkie dwa obwody usługi ExpressRoute tak długo, jak znajdują się one w obsługiwane kraje i są tworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem zarówno obwodów można albo obwodu, Uruchom konfigurację w poniższych sekcjach. Jeśli dwa obwody należą do różnych subskrypcji platformy Azure, należy autoryzacji z jedną subskrypcją platformy Azure, a przekazać klucza autoryzacji podczas uruchamiania polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włącz łączność między sieci lokalnej

Uruchamiając polecenie, aby umożliwić łączność, należy wziąć pod uwagę następujące wartości:

* *obwodu równorzędnego* powinien być identyfikator zasobu pełne. Na przykład: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix* musi mieć wartość/29 IPv4 podsieci, np. "10.0.0.0/29". Firma Microsoft użyje adresów IP w tej podsieci można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie można używać adresów w tej podsieci w sieci wirtualne platformy Azure lub w sieci lokalnej.

Uruchom poniższy interfejs wiersza polecenia i połączyć dwa obwody usługi ExpressRoute. Użyj następujące przykładowe polecenie:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Dane wyjściowe interfejsu wiersza polecenia wyglądają jak w poniższym przykładzie:

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Po ukończeniu powyższych operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. W następującej konfiguracji autoryzacji jest generowany w ramach subskrypcji z obwodem 2 i klucza autoryzacji jest przekazywana do obwodu 1.

Generowanie klucza autoryzacji. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

Dane wyjściowe interfejsu wiersza polecenia wygląda podobnie do poniższego.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Zanotuj obwodu 2 identyfikator zasobu, a także klucza autoryzacji.

Uruchom następujące polecenie przed 1 obwodu. Przekaż identyfikator zasobu 2 obwodu i klucza autoryzacji 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

Po ukończeniu powyższych operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i Zweryfikuj konfigurację

Użyj następującego polecenia aby zweryfikować konfigurację w ramach obwodu, w którym konfiguracji została wprowadzona, czyli obwodu 1 w powyższym przykładzie.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

W interfejsie wiersza polecenia platformy danych wyjściowych zostaną wyświetlone *CircuitConnectionStatus*. Jego temu wiadomo, czy łączność między dwa obwody zostanie nawiązane, "Połączono", czy nie, "Odłączone". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłącz łączność między sieci lokalnej

Aby je wyłączyć, uruchamiać polecenia obwodu gdzie konfiguracja została wprowadzona, czyli obwodu 1 w powyższym przykładzie.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Możesz uruchomić interfejs wiersza polecenia pokazują, aby sprawdzić stan. 

Po wykonaniu powyższych operacji już nie będą mieć łączność między sieci lokalnej za pomocą obwodów usługi ExpressRoute. 


## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-global-reach.md)
* [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Połącz obwód usługi ExpressRoute z sieci wirtualnej platformy Azure](expressroute-howto-linkvnet-arm.md)


