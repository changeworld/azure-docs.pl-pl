---
title: 'Konfigurowanie usługi ExpressRoute, zasięgu globalnym: interfejs wiersza polecenia platformy Azure | Microsoft Docs'
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 28df8f59944ccac9d731e15a558d864beed2f3ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364218"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurowanie usługi ExpressRoute zasięgu globalnym, przy użyciu wiersza polecenia platformy Azure

Ten artykuł pomoże Ci skonfigurować zasięgu globalnym usługi ExpressRoute platformy Azure przy użyciu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Przed rozpoczęciem konfiguracji należy wykonać następujące wymagania:

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz artykuły [Install the Azure CLI (Instalowanie interfejsu wiersza polecenia platformy Azure) ](/cli/azure/install-azure-cli) i [Get started with Azure CLI (Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure)](/cli/azure/get-started-with-azure-cli).
* Omówienie usługi ExpressRoute — aprowizacji obwodu [przepływy pracy](expressroute-workflows.md).
* Upewnij się, że obwodów usługi ExpressRoute znajdują się w stanie Aprowizowana.
* Upewnij się, że prywatnej komunikacji równorzędnej Azure jest skonfigurowany na obwodów usługi ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Aby uruchomić konfigurację, zaloguj się do konta platformy Azure. Następujące polecenie spowoduje otwarcie domyślnej przeglądarki i wyświetli monit o podanie poświadczeń logowania dla konta platformy Azure:  

```azurecli
az login
```

Jeśli masz wiele subskrypcji platformy Azure, sprawdź subskrypcje dla konta:

```azurecli
az account list
```

Określ subskrypcję, dla której chcesz użyć:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identyfikowanie obwodów usługi ExpressRoute dla konfiguracji

Zasięgu globalnym usługi ExpressRoute można włączyć między wszystkie dwa obwody usługi ExpressRoute, tak długo, jak znajdują się w obsługiwane kraje i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem zarówno obwody, można wybrać obu obwodu, Uruchom konfigurację, zgodnie z opisem w dalszej części tego artykułu. Jeśli dwa obwody należą do różnych subskrypcji platformy Azure, musisz mieć autoryzacji z jedną subskrypcją platformy Azure i musi upłynąć w jego klucza autoryzacji, po uruchomieniu polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włącz łączność między sieci lokalnej

Uruchamiając polecenie, aby umożliwić łączność, należy uwzględnić następujące wymagania o wprowadzenie wartości parametrów:

* *obwodu równorzędnego* powinien być identyfikator zasobu pełne. Na przykład:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *Prefiks adresu* musi być "/ 29" podsieć IPv4 (na przykład "10.0.0.0/29"). Używamy adresów IP w tej podsieci, można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie można używać adresów w tej podsieci w sieci wirtualnej platformy Azure lub w sieci lokalnej.

Uruchom następujące polecenie interfejsu wiersza polecenia, aby połączyć dwa obwody usługi ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Dane wyjściowe interfejsu wiersza polecenia wygląda następująco:

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

Po zakończeniu tej operacji, będziesz mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Włącz łączność między obwodów usługi ExpressRoute w innej subskrypcji platformy Azure

Jeśli dwa obwody nie są w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. Następująca konfiguracja służy do generowania autoryzacji w ramach subskrypcji z obwodem 2 i polega na przekazaniu klucza autoryzacji do circuit 1.

1. Generowanie klucza autoryzacji:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Dane wyjściowe interfejsu wiersza polecenia wygląda następująco:

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

1. Zanotuj identyfikator zasobu i klucza autoryzacji dla obwodu. 2.

1. Uruchom następujące polecenie przed obwodu 1, przekazując obwodu 2 zasobów Identyfikatora i klucza autoryzacji:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po zakończeniu tej operacji, będziesz mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i Zweryfikuj konfigurację

Użyj następującego polecenia aby zweryfikować konfigurację w ramach obwodu, w którym konfiguracji dokonano (obwód 1 w powyższym przykładzie):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

W danych wyjściowych interfejsu wiersza polecenia, zobaczysz *CircuitConnectionStatus*. Informuje, czy jest łączność między dwa obwody nawiązać ("połączone") lub nie ustanowić ("Disconnected"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłącz łączność między sieci lokalnej

Aby wyłączyć połączenie, uruchom następujące polecenie względem obwodu, w którym konfiguracji dokonano (obwód 1 we wcześniejszym przykładzie).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Użyj ```show``` polecenie, aby sprawdzić stan.

Po zakończeniu tej operacji będzie nie masz już połączenie między sieci lokalnej za pośrednictwem obwodów usługi ExpressRoute.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-global-reach.md)
* [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Połącz obwodu usługi ExpressRoute z sieci wirtualnej](expressroute-howto-linkvnet-arm.md)
