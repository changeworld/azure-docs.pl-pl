---
title: 'Azure ExpressRoute: Konfigurowanie komunikacji równorzędnej: interfejs wiersza polecenia'
description: Ten artykuł ułatwia tworzenie i aprowizowanie prywatnej, publicznej i komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbda73e022ebaad283641ce2f54a5962aeb4cb60
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361704"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Utworzyć i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute za pomocą interfejsu wiersza polecenia

Ten artykuł ułatwia tworzenie i zarządzanie nimi routingu konfiguracji/komunikację równorzędną dla obwodu usługi ExpressRoute w modelu wdrażania usługi Resource Manager przy użyciu interfejsu wiersza polecenia. Możesz również sprawdzić stan, update lub delete i anulowanie aprowizacji komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z Twojego obwodu, wybierz artykułu z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Przed rozpoczęciem konfiguracji upewnij się, że zostały sprawdzone wymagania [wstępne](expressroute-prerequisites.md) [dotyczące wymagań routingu](expressroute-routing.md)i stron [przepływu pracy](expressroute-workflows.md) .
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony, aby można było uruchomić polecenia w tym artykule.

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje zarządzane warstwy 3 usługi (zwykle IPVPN, np. MPLS), dostawca połączenia będzie Konfigurowanie routingu oraz zarządzanie nim za Ciebie.

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

## <a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizacji i usuwania konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Użyj najnowszej wersji interfejsu wiersza polecenia platformy Azure (CLI). * Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

   ```azurecli-interactive
   az login
   ```

   Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute. Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](howto-circuit-cli.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić firmie Microsoft komunikacji równorzędnej. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków. 

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route list
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

   Uruchom poniższy przykład, aby skonfigurować komunikację równorzędną Microsoft dla obwodu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zobaczysz "AdvertisedPublicPrefixesState" w danych wyjściowych powyższego polecenia. 
> 
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez Otwieranie biletu pomocy technicznej, jak pokazano poniżej. 
> 
>

Dane wyjściowe są podobne do poniższego przykładu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz zaktualizować dowolną część konfiguracji. Anonsowane prefiksy obwodu są aktualizowane z 123.1.0.0/24 do 124.1.0.0/24 w następującym przykładzie:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Aby dodać ustawienia komunikacji równorzędnej Microsoft IPv6 do istniejącej konfiguracji IPv4

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Prywatna Komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Musisz użyć najnowszej wersji interfejsu wiersza polecenia platformy Azure (CLI). * Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

   ```azurecli-interactive
   az login
   ```

   Wybierz subskrypcję, w ramach której chcesz utworzyć obwód usługi ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute. Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](howto-circuit-cli.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić prywatną komunikację równorzędną Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

   Aby skonfigurować prywatną komunikację równorzędną dla obwodu, skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Jeśli zdecydujesz się używać skrótu MD5, skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   > 

### <a name="getprivate"></a>Aby wyświetlić szczegóły prywatnej komunikacji równorzędnej Azure

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Dane wyjściowe są podobne do poniższego przykładu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany ze 100 do 500.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

> [!WARNING]
> Należy się upewnić, czy wszystkie połączenia zasięgu globalnym usługi ExpressRoute i sieci wirtualne zostały usunięte przed uruchomieniem tego przykładu. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Następne kroki

Następny krok: [Link a VNet to an ExpressRoute circuit](howto-linkvnet-cli.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
