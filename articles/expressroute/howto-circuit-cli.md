---
title: 'Tworzenie i modyfikowanie obwodu usługi ExpressRoute: interfejs wiersza polecenia platformy Azure | Microsoft Docs'
description: Ten artykuł pokazuje, jak tworzenie, aprowizować, sprawdź, aktualizowanie, usuwanie i anulować aprowizację obwodu ExpressRoute za pomocą interfejsu wiersza polecenia platformy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.reviewer: anzaman
ms.openlocfilehash: e42190814b9365c7db054eb2b5f1842581b64009
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657067"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Tworzenie i modyfikowanie obwodu ExpressRoute za pomocą interfejsu wiersza polecenia


W tym artykule opisano, jak utworzyć obwód usługi ExpressRoute systemu Azure przy użyciu interfejsu wiersza polecenia (CLI). W tym artykule przedstawiono również sposób Sprawdź stan, update lub delete i anulować aprowizację obwodu. Jeśli chcesz użyć innej metody do pracy z obwodów usługi ExpressRoute, można wybrać artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).
* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

## <a name="create"></a>Tworzenie i aprowizowanie obwodu usługi ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz CloudShell, "Try It", użytkownik jest zalogowany automatycznie. Poniższe przykłady umożliwiają łatwiejszego nawiązania połączenia:

```azurecli
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli-interactive
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobieranie listy obsługiwanych dostawców, lokalizacji i przepustowości

Przed przystąpieniem do tworzenia obwodu usługi ExpressRoute, należy listę dostawców łączności obsługiwanych, lokalizacji i opcje przepustowości. Polecenia interfejsu wiersza polecenia `az network express-route list-service-providers` zwraca te informacje, która będzie używana w dalszych krokach:

```azurecli-interactive
az network express-route list-service-providers
```

Odpowiedź jest podobna do poniższego przykładu:

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

Sprawdź odpowiedzi, aby zobaczyć, czy dostawca połączenia są dostępne. Zanotuj następujące informacje, które będą potrzebne podczas tworzenia obwodu:

* Name (Nazwa)
* PeeringLocations
* BandwidthsOffered

Teraz możesz utworzyć obwód usługi ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczana w momencie utworzenia klucza usługi. Tę operację należy wykonać, gdy dostawca połączenia jest gotowy do obsługi administracyjnej obwodu.
>
>

Jeśli nie masz jeszcze grupy zasobów, należy utworzyć jedną przed utworzeniem obwód usługi ExpressRoute. Aby utworzyć grupę zasobów, należy uruchomić następujące polecenie:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Poniższy przykład pokazuje, jak utworzyć obwód usługi ExpressRoute za pośrednictwem Equinix 200-MB/s w Dolinie Krzemowej. Jeśli używasz innego dostawcy i inne ustawienia, należy zastąpić te informacje podczas Prześlij żądanie.

Upewnij się, że podajesz poprawne warstwa jednostki SKU i rodzina jednostek SKU:

* Warstwa jednostki SKU Określa, czy włączono standard usługi ExpressRoute lub dodatek ExpressRoute premium. Można określić "Standardowa", aby uzyskać dodatek premium standardowe jednostki SKU lub "Premium".
* Rodzina jednostek SKU Określa typ rozliczeń. "Metereddata" plan taryfowy z danymi i "Unlimiteddata" można określić dla plan z nieograniczonymi danymi. Można zmienić typ rozliczeń z "Metereddata" do "Unlimiteddata", ale nie można zmienić typu z "Unlimiteddata" do "Metereddata".


Obwód usługi ExpressRoute jest rozliczana w momencie utworzenia klucza usługi. Poniższy przykład przedstawia żądanie nowego klucza usługi:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpowiedź zawiera klucz usługi.

### <a name="4-list-all-expressroute-circuits"></a>4. Lista wszystkich obwodów usługi ExpressRoute

Aby uzyskać listę wszystkich obwodów usługi ExpressRoute, które zostały utworzone, uruchom `az network express-route list` polecenia. Możesz pobrać te informacje w dowolnym momencie za pomocą tego polecenia. Aby wyświetlić listę wszystkich obwodów, należy wykonać wywołanie bez parametrów.

```azurecli-interactive
az network express-route list
```

Klucz usługi znajduje się w *klucza ServiceKey* pole odpowiedzi.

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

Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając polecenie, używając "-h" parametru.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi dostawcy łączności dla inicjowania obsługi administracyjnej

"ServiceProviderProvisioningState" zawiera informacje dotyczące bieżącego stanu aprowizacji po stronie dostawcy usług. Stan zawiera stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [artykułu przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

```azurecli-interactive
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Po dostawcy połączenia Trwa włączanie go dla Ciebie, obwodu zmienia się w następującym stanie:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Należy mieć możliwość użycia obwodu usługi ExpressRoute musi być w następującym stanie:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Okresowo sprawdzać stan i stan klawisza obwodu

Sprawdzanie stanu i stan klawisza obwodu informuje o tym, kiedy Twój dostawca włączył obwodu. Po skonfigurowaniu obwód "ServiceProviderProvisioningState" pojawia się jako "Aprowizowana", jak pokazano w poniższym przykładzie:

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zobacz [obwód usługi ExpressRoute, konfiguracji routingu](howto-routing-cli.md) artykuł, aby tworzyć i modyfikować komunikacja równorzędna obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są utworzonych przy pomocy dostawców oferujących usługi łączności 2 warstwy. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zazwyczaj IP sieci VPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie łączenie sieci wirtualnej na obwód usługi ExpressRoute. Użyj [łączenie sieci wirtualnych obwodów usługi ExpressRoute](howto-linkvnet-cli.md) artykułu.

## <a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute

Można modyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność. Można wprowadzić następujące zmiany bez przerw w dostępności:

* Można włączyć lub wyłączyć dodatek premium usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Możesz zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że pojemność dostępna na porcie. Obniżenie przepustowości obwodu nie jest jednak obsługiwane.
* Do danych bez ograniczeń, można zmienić planu zliczania z plan taryfowy z danymi. Jednak zmiana zliczania planu z danymi nieograniczonymi na plan taryfowy z danymi nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalaj na klasyczne operacje*.

Aby uzyskać więcej informacji na temat limity i ograniczenia, zobacz [ExpressRoute — często zadawane pytania](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek premium usługi ExpressRoute

Należy włączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu za pomocą następującego polecenia:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Obwód ma teraz funkcje dodatku premium usługi ExpressRoute, włączone. Zaczniemy rozliczenia dla funkcji dodatku premium tak szybko, jak polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek ExpressRoute premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone w przypadku obwód standardowy.
>
>

Przed wyłączeniem dodatek ExpressRoute premium, należy zrozumieć następujące kryteria:

* Przed obniżanie poziomu z wersji premium na warstwę standardowa, należy się upewnić, że masz mniej niż 10 sieci wirtualne, połączone z obwodem usługi danych. Jeśli masz więcej niż 10, żądania aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli nie możesz odłączyć wszystkie sieci wirtualne, Twoje żądanie aktualizacji nie powiedzie się, a opłaty są naliczane według stawek premium.
* Tabela tras muszą być mniej niż 4000 tras do prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000, odrzuca sesji protokołu BGP. Sesja nie będzie reenabled, dopóki liczba prefiksów anonsowanych znajduje się poniżej 4000.

Aby wyłączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowości obwodu usługi ExpressRoute

Obsługiwane opcje przepustowości dla dostawcy, można sprawdzić [ExpressRoute — często zadawane pytania](expressroute-faqs.md). Możesz wybrać dowolnej wielkości większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Jeśli jest niewystarczająca pojemność istniejącego portu, na może być konieczne ponownie utworzyć obwód usługi ExpressRoute. Nie można uaktualnić obwodu, jeśli w tej lokalizacji jest dostępna nie dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez przerw w działaniu. Obniżenie przepustowości, należy anulować aprowizację obwodu usługi ExpressRoute, a następnie ponownie udostępnić nowego obwodu usługi ExpressRoute.
>

Po podjęciu decyzji rozmiar, czego potrzebujesz, użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Twój obwód ma rozmiar po stronie firmy Microsoft. Następnie musisz skontaktować się z dostawcą połączenia, można zaktualizować konfiguracji po ich stronie odpowiadający tej zmiany. Po wprowadzeniu tego powiadomienia, możemy rozpocząć rozliczanie opcji zaktualizowane przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostki SKU z naliczanego na nieograniczony

Aby zmienić jednostki SKU obwodu usługi ExpressRoute, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do klasycznej sieci wirtualnej i środowiska usługi Resource Manager

Zapoznaj się z instrukcjami wyświetlanymi w [obwodów ExpressRoute przenieść z klasycznego modelu wdrażania usługi Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute, upewnij się, że rozumiesz następujące kryteria:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, jeśli wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli dostawca usług obwodu usługi ExpressRoute, w stanie inicjowania obsługi **aprowizacji** lub **Aprowizowana**, należy skontaktować się z dostawcą usługi, aby anulować aprowizację obwodu po ich stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Można usunąć obwodu, gdy dostawca usług ma anulowanie aprowizacji obwodu. Gdy anulowanie aprowizacji obwodu stan inicjowania obsługi dostawcy usług jest równa **nie zainicjowano obsługi administracyjnej**. Spowoduje to zatrzymanie naliczania opłat za obwód.

Możesz usunąć obwód usługi ExpressRoute, uruchamiając następujące polecenie:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu obwodu, upewnij się, że wykonywanie następujących zadań:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](howto-routing-cli.md)
* [Łączenie sieci wirtualnej na obwód usługi ExpressRoute](howto-linkvnet-cli.md)
