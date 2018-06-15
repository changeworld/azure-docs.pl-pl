---
title: 'Tworzenie i modyfikowanie obwodu usługi Azure ExpressRoute: interfejs wiersza polecenia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób tworzenia, obsługi administracyjnej, sprawdź, aktualizacji, usuwania i anulowanie zastrzeżenia obwodu usługi ExpressRoute, przy użyciu interfejsu wiersza polecenia.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23933249"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute, przy użyciu interfejsu wiersza polecenia


W tym artykule opisano sposób tworzenia obwodu usługi Azure ExpressRoute przy użyciu interfejsu wiersza polecenia (CLI). W tym artykule przedstawiono również sposób sprawdzić stan, update lub delete i anulowanie zastrzeżenia obwodu. Jeśli chcesz użyć innej metody do pracy z obwody usługi ExpressRoute, możesz wybrać tego artykułu z poniższej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli).
* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

## <a name="create"></a>Tworzenie i przydzielanie obwodu usługi ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wyboru subskrypcji

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Użyj poniższych przykładach umożliwiające nawiązywanie połączeń:

```azurecli
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwodu usługi ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości

Przed utworzeniem obwodu usługi ExpressRoute, należy listę dostawców obsługiwanych łączności, lokalizacji i opcji przepustowości. Interfejs wiersza polecenia polecenie "az sieci express route listy--usługodawcy" zwraca informację, która będzie używana w dalszych krokach:

```azurecli
az network express-route list-service-providers
```

Odpowiedź jest podobny do poniższego przykładu:

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Sprawdź, czy dostawca połączenia jest wyświetlana odpowiedź. Zanotuj następujące informacje, konieczne będzie utworzenie obwodu:

* Name (Nazwa)
* PeeringLocations
* BandwidthsOffered

Teraz możesz utworzyć obwodu usługi ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)

> [!IMPORTANT]
> Jest on rozliczany od momentu jego wystawieniu klucz usługi obwodu usługi ExpressRoute. Tę operację należy wykonać, gdy dostawca łączności jest gotowy do udostępniania obwodu.
> 
> 

Jeśli nie masz już grupę zasobów, należy go utworzyć przed utworzeniem obwodu usługi ExpressRoute. Można utworzyć grupę zasobów, uruchamiając następujące polecenie:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

Poniższy przykład przedstawia sposób tworzenia obwodu ExpressRoute za pośrednictwem Equinix 200-MB/s w Dolinie Krzemowej. Jeśli używasz innego dostawcy i inne ustawienia, należy zastąpić te informacje po wprowadzeniu żądania. 

Upewnij się, należy określić prawidłowe jednostki SKU warstwy i rodzina jednostek SKU:

* Jednostka SKU warstwy określa, czy włączone jest standardem ExpressRoute lub dodatek usługi ExpressRoute w warstwie premium. Można określić Standard, aby uzyskać dodatek premium standardowy SKU lub "Premium".
* Rodzina jednostek SKU Określa typ rozliczeń. "Metereddata" można określić dla planu dane naliczane i "Unlimiteddata" planu dane nieograniczone. Można zmienić typ rozliczeń "Metereddata" do "Unlimiteddata", ale nie można zmienić typu z "Unlimiteddata" do "Metereddata".


Jest on rozliczany od momentu jego wystawieniu klucz usługi obwodu usługi ExpressRoute. Poniższy przykład jest to żądanie dla nowego klucza usługi:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpowiedź zawiera klucz usługi.

### <a name="4-list-all-expressroute-circuits"></a>4. Wyświetl listę wszystkich obwody usługi ExpressRoute

Aby uzyskać listę wszystkich obwody usługi ExpressRoute, które zostały utworzone, uruchom polecenie "Lista express route sieci az". Te informacje w dowolnym momencie można pobrać za pomocą tego polecenia. Aby wyświetlić listę wszystkich obwodów, należy wykonać wywołanie bez parametrów.

```azurecli
az network express-route list
```

Klucz usługi ma na liście *bindingTemplate* pole odpowiedzi.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Aby uzyskać szczegółowy opis wszystkich parametrów, działanie przy użyciu polecenia "-h" parametru.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcą połączenia do inicjowania obsługi

"ServiceProviderProvisioningState" zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie dostawcy usług. Stan udostępnia stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [artykułu przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu ExpressRoute obwodu znajduje się w następującym stanie:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Po dostawca połączenia trwa jej włączanie obwodu zmienia się na następujący:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Dla Ciebie można było używać obwodu usługi ExpressRoute musi być w następującym stanie:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Okresowo sprawdzać stan i stan klucz obwodu

Sprawdzanie stanu i stan klucz obwodu informuje gdy dostawca jest włączony obwodu. Po skonfigurowaniu obwodu "ServiceProviderProvisioningState" pojawia się jako "Obsługiwane administracyjnie", jak pokazano w poniższym przykładzie:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Odpowiedź jest podobny do poniższego przykładu:

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Tworzenie konfiguracji routingu

Aby uzyskać instrukcje, zobacz [obwodu ExpressRoute konfiguracji routingu](howto-routing-cli.md) artykułu do tworzenia i modyfikowania obwodu komunikacji równorzędnych.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwody, które zostały utworzone z dostawców usług, które oferują warstwy 2 łączności usługi. Jeśli używasz usługodawcy, który oferuje zarządzanych warstwy 3 usługi (zazwyczaj IP sieci VPN, takie jak MPLS), dostawca połączenia konfiguruje i zarządza nimi routingu dla Ciebie.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połączyć sieć wirtualną obwodu usługi ExpressRoute. Użyj [łączenia sieci wirtualne obwody usługi ExpressRoute](howto-linkvnet-cli.md) artykułu.

## <a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute

Można zmodyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność. Można dokonać następujących zmian bez przestojów:

* Można włączyć lub wyłączyć dodatek premium usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Można zwiększyć przepustowość obwodu ExpressRoute, pod warunkiem Brak dostępnej pojemności na porcie. Jednak przepustowość obwodu zmiana wersji na starszą nie jest obsługiwane. 
* Plan zliczania naliczane danych można zmienić na dane nieograniczone. Jednak zmiana zliczania planu z danych nieograniczone naliczane danych nie jest obsługiwana.
* Można włączyć lub wyłączyć *operacje klasycznego*.

Aby uzyskać więcej informacji o limity i ograniczenia, zobacz [ExpressRoute — często zadawane pytania](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek usługi ExpressRoute w warstwie premium

Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można włączyć za pomocą następującego polecenia:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Obwód ma teraz włączonymi funkcjami dodatek premium usługi ExpressRoute. Zaczniemy rozliczeń dla funkcji dodatku premium, jak polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek usługi ExpressRoute w warstwie premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone dla standardowych obwodu.
> 
> 

Przed wyłączeniem dodatek usługi ExpressRoute w warstwie premium, należy zrozumieć następujące kryteria:

* Przed obniżyć z premium standard użytkownik musi upewnić się, że masz mniej niż 10 sieciami wirtualnymi połączone z obwodem. Jeśli masz więcej niż 10 żądania aktualizacji zakończy się niepowodzeniem, a nas rachunku stawkami premium.
* Należy odłączyć wszystkie sieci wirtualne w różnych regionach geograficznymi. Jeśli nie można odłączyć wszystkie sieci wirtualne, żądanie aktualizacji nie powiedzie się i NAS rachunku stawkami premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000 tras, porzuca sesji BGP. Sesja nie będzie reenabled, dopóki nie zostanie liczby prefiksów anonsowanych poniżej 4000.

Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można wyłączyć za pomocą w poniższym przykładzie:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowości obwodu ExpressRoute

Dla opcji obsługiwanych przepustowości dla dostawcy, sprawdź [ExpressRoute — często zadawane pytania](expressroute-faqs.md). Można wybrać żadnych rozmiar większy niż rozmiar z istniejącym obwodem.

> [!IMPORTANT]
> Istniejącego portu jest nieodpowiedni pojemności, należy ponownie utworzyć obwodu usługi expressroute. Nie można uaktualnić obwodu, jeśli nie bez dodatkowej pojemności dostępnej w tej lokalizacji.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez zakłóceń. Zmiana wersji na starszą przepustowości wymaga anulowanie zastrzeżenia obwodu ExpressRoute, a następnie Udostępnij ponownie nowy obwód usługi ExpressRoute.
>

Po wybraniu rozmiar, należy użyć następującego polecenia, aby zmienić rozmiar obwodu:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Obwodu jest o rozmiarze po stronie firmy Microsoft. Następnie musi skontaktuj się z dostawcą połączenia można zaktualizować konfiguracji na ich strony, aby dopasować tę zmianę. Po wprowadzeniu tego powiadomienia, możemy rozpocząć rozliczeń można opcji zaktualizowane przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść SKU z naliczanego na nieograniczony

Jednostka SKU obwodu ExpressRoute można zmienić za pomocą w poniższym przykładzie:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Do kontrolowania dostępu do klasycznego i środowisk usługi Resource Manager

Zapoznaj się z instrukcjami wyświetlanymi w [obwody Przenieś usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Anulowania obsługi i usuwania obwodu usługi ExpressRoute

Anulowanie zastrzeżenia i usunąć obwodu usługi expressroute, upewnij się, że rozumiesz następujące kryteria:

* Należy odłączyć wszystkie sieci wirtualne z obwodem usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodu.
* Jeśli dostawca usługi obwodu ExpressRoute stan inicjowania obsługi jest **inicjowania obsługi administracyjnej** lub **obsługiwane administracyjnie**, należy skontaktować się z dostawcą usług na anulowanie zastrzeżenia obwód w bok. Będziemy nadal zarezerwować zasobów i obciążać Cię do czasu dostawcy usług wykonuje anulowania obsługi obwodu i powiadomienia NAS.
* Jeśli usługodawca została anulowana obwodu, można usunąć obwodu. Gdy obwód jest anulowana, ma ustawioną wartość usługodawcy stan inicjowania obsługi **nieudostępniane**. Powoduje to zatrzymanie rozliczeń dla obwodu.

Można usunąć obwodu usługi ExpressRoute, uruchamiając następujące polecenie:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu obwodu upewnij się, że wykonywanie następujących zadań:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](howto-routing-cli.md)
* [Link sieci wirtualnej do obwodu usługi ExpressRoute](howto-linkvnet-cli.md)