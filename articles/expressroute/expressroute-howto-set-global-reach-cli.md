---
title: 'Usługa Azure ExpressRoute: konfigurowanie globalnego zasięgu usługi ExpressRoute: cli'
description: Ten artykuł ułatwia łączenie obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami lokalnymi i włączania globalnego zasięgu.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: a39cf4e09a70ca2b1225d699c84abf0e7f1d2eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476410"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurowanie globalnego zasięgu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure

Ten artykuł ułatwia konfigurowanie globalnego zasięgu usługi Azure ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Przed rozpoczęciem konfiguracji należy spełnić następujące wymagania:

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz artykuły [Install the Azure CLI (Instalowanie interfejsu wiersza polecenia platformy Azure) ](/cli/azure/install-azure-cli) i [Get started with Azure CLI (Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure)](/cli/azure/get-started-with-azure-cli).
* Zapoznaj się z [przepływami pracy](expressroute-workflows.md)inicjowania obsługi administracyjnej obwodów usługi ExpressRoute .
* Upewnij się, że obwody usługi ExpressRoute są w stanie aprowizowana.
* Upewnij się, że prywatna komunikacja równorzędna platformy Azure jest skonfigurowana w obwodach usługi ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Następujące polecenie otwiera domyślną przeglądarkę i monituje o poświadczenia logowania dla konta platformy Azure:  

```azurecli
az login
```

Jeśli masz wiele subskrypcji platformy Azure, sprawdź subskrypcje dla konta:

```azurecli
az account list
```

Określ subskrypcję, której chcesz użyć:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identyfikowanie obwodów usługi ExpressRoute w celu konfiguracji

Globalny zasięg usługi ExpressRoute można włączyć między dwoma obwodami usługi ExpressRoute, o ile znajdują się one w obsługiwanych krajach/regionach i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli subskrypcja jest właścicielem obu obwodów, można wybrać jeden z obwodów, aby uruchomić konfigurację, jak wyjaśniono w dalszej części tego artykułu. Jeśli dwa obwody znajdują się w różnych subskrypcjach platformy Azure, musisz mieć autoryzację z jednej subskrypcji platformy Azure i należy przekazać w kluczu autoryzacji po uruchomieniu polecenia konfiguracji w innej subskrypcji platformy Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włączanie łączności między sieciami lokalnymi

Podczas uruchamiania polecenia w celu włączenia łączności należy zwrócić uwagę na następujące wymagania dotyczące wartości parametrów:

* *obwód równorzędny* powinien być pełnym identyfikatorem zasobu. Przykład:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *prefiks adresu* musi być podsiecią IPv4 "/29" (na przykład "10.0.0.0/29"). Używamy adresów IP w tej podsieci do ustanawiania łączności między dwoma obwodami usługi ExpressRoute. Nie wolno używać adresów w tej podsieci w sieciach wirtualnych platformy Azure ani w sieciach lokalnych.

Uruchom następujące polecenie interfejsu wiersza polecenia, aby połączyć dwa obwody usługi ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Dane wyjściowe interfejsu wiersza polecenia wyglądają następująco:

```output
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

Po zakończeniu tej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Włączanie łączności między obwodami usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie są w tej samej subskrypcji platformy Azure, potrzebujesz autoryzacji. W poniższej konfiguracji generujesz autoryzację w subskrypcji obwodu 2 i przekazujesz klucz autoryzacji do obwodu 1.

1. Generowanie klucza autoryzacji:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Dane wyjściowe interfejsu wiersza polecenia wyglądają następująco:

   ```output
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

1. Zanotuj zarówno identyfikator zasobu, jak i klucz autoryzacji dla obwodu 2.

1. Uruchom następujące polecenie względem obwodu 1, przechodząc w obwodzie 2 identyfikator zasobu i klucz autoryzacji:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po zakończeniu tej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i sprawdź konfigurację

Użyj następującego polecenia, aby sprawdzić konfigurację obwodu, w którym dokonano konfiguracji (obwód 1 w poprzednim przykładzie):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

W wyjściu interfejsu wiersza polecenia zobaczysz *CircuitConnectionStatus*. Informuje, czy łączność między dwoma obwodami jest ustanowiona ("Połączony"), czy nie ustanowiona ("Rozłączone"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłączanie łączności między sieciami lokalnymi

Aby wyłączyć łączność, uruchom następujące polecenie względem obwodu, w którym została wykonana konfiguracja (obwód 1 we wcześniejszym przykładzie).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Użyj ```show``` polecenia, aby zweryfikować stan.

Po zakończeniu tej operacji nie będzie już mieć łączności między sieciami lokalnymi za pośrednictwem obwodów usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o globalnym zasięgu usługi ExpressRoute](expressroute-global-reach.md)
* [Weryfikowanie łączności usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Łączenie obwodu usługi ExpressRoute z siecią wirtualną](expressroute-howto-linkvnet-arm.md)
