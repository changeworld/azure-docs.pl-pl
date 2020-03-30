---
title: 'Tworzenie i modyfikowanie obwodu usługi ExpressRoute: narzędzie interfejsu wiersza polecenia platformy Azure'
description: W tym artykule pokazano, jak utworzyć, aprowizować, weryfikować, aktualizować, usuwać i usuwać aprowizję obwód usługi ExpressRoute przy użyciu interfejsu wiersza polecenia.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476155"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute za pomocą interfejsu wiersza polecenia


W tym artykule opisano sposób tworzenia obwodu usługi Azure ExpressRoute przy użyciu interfejsu wiersza polecenia (CLI). W tym artykule pokazano również, jak sprawdzić stan, zaktualizować lub usunąć i anulować aprowizję obwodu. Jeśli chcesz użyć innej metody do pracy z obwodami usługi ExpressRoute, możesz wybrać ten artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).
* Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i udostępnianie obwodu usługi ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz CloudShell "Try It", jesteś zalogowany automatycznie. Skorzystaj z poniższych przykładów, aby połączyć się:

```azurecli-interactive
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

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości

Przed utworzeniem obwodu usługi ExpressRoute potrzebna jest lista obsługiwanych dostawców łączności, lokalizacji i opcji przepustowości. Polecenie `az network express-route list-service-providers` CLI zwraca te informacje, które będą używane w późniejszych krokach:

```azurecli-interactive
az network express-route list-service-providers
```

Odpowiedź jest podobna do poniższego przykładu:

```output
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

Sprawdź odpowiedź, aby sprawdzić, czy dostawca łączności znajduje się na liście. Zanotuj następujące informacje, które będą potrzebne podczas tworzenia obwodu:

* Nazwa
* Alokacje komunikacji równorzędnej
* Oferowane przepustowości

Teraz możesz przystąpić do tworzenia obwodu usługi ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Tworzenie obwodu usługi ExpressRoute

> [!IMPORTANT]
> Twój obwód usługi ExpressRoute jest rozliczany od momentu wystawienia klucza usługi. Tę operację należy wykonać, gdy dostawca łączności jest gotowy do aprowizowania obwodu.
>
>

Jeśli nie masz jeszcze grupy zasobów, należy go utworzyć przed utworzeniem obwodu usługi ExpressRoute. Grupę zasobów można utworzyć, uruchamiając następujące polecenie:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

W poniższym przykładzie pokazano, jak utworzyć obwód 200 Mb/s usługi ExpressRoute przez Equinix w Dolinie Krzemowej. Jeśli używasz innego dostawcy i różnych ustawień, zastąp te informacje podczas składania wniosku.

Upewnij się, że określono poprawną warstwę jednostki SKU i rodzinę jednostek SKU:

* Warstwa SKU określa, czy obwód usługi ExpressRoute to [Lokalny,](expressroute-faqs.md#expressroute-local)Standardowy czy [Premium.](expressroute-faqs.md#expressroute-premium) Można określić *lokalne,* *standardowe* lub *premium*.
* Rodzina jednostek SKU określa typ rozliczeń. Można określić *Dane metereddata* dla taryfowego planu danych i *nieograniczone dane* dla nieograniczonego planu danych. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Obwód *lokalny* jest tylko *nieograniczonedane.*


Twój obwód usługi ExpressRoute jest rozliczany od momentu wystawienia klucza usługi. Poniższy przykład jest żądaniem nowego klucza usługi:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpowiedź zawiera klucz usługi.

### <a name="4-list-all-expressroute-circuits"></a>4. Lista wszystkich obwodów usługi ExpressRoute

Aby uzyskać listę wszystkich utworzonych obwodów usługi ExpressRoute, uruchom `az network express-route list` to polecenie. Te informacje można pobrać w dowolnym momencie za pomocą tego polecenia. Aby wyświetlić listę wszystkich obwodów, nawiązuj połączenie bez parametrów.

```azurecli-interactive
az network express-route list
```

Klucz usługi jest wymieniony w *servicekey* pola odpowiedzi.

```output
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

Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając polecenie za pomocą parametru '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcy łączności w celu inicjowania obsługi administracyjnej

"ServiceProviderProvisioningState" zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie usługodawcy. Stan zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [artykuł Przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Obwód zmienia się w następujący stan, gdy dostawca łączności jest w trakcie włączania go dla Ciebie:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Aby można było korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Okresowo sprawdzaj stan i stan przycisku obwodu

Sprawdzanie stanu i stanu klucza obwodu informuje o tym, kiedy dostawca włączył obwód. Po skonfigurowaniu obwodu "ServiceProviderProvisioningState" pojawia się jako "Aprowizowana", jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Odpowiedź jest podobna do poniższego przykładu:

```output
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

Aby uzyskać instrukcje krok po kroku, zobacz artykuł [konfiguracji routingu obwodu usługi ExpressRoute,](howto-routing-cli.md) aby utworzyć i zmodyfikować komunikację równorzędną obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych z dostawcami usług, którzy oferują usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje usługi zarządzanej warstwy 3 (zazwyczaj sieć VPN IP, taką jak MPLS), dostawca łączności konfiguruje i zarządza routingiem.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połącz sieć wirtualną z obwodem usługi ExpressRoute. Użyj artykułu [Łączenie sieci wirtualnych z obwodami usługi ExpressRoute.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute

Można zmodyfikować niektóre właściwości obwodu usługi ExpressRoute bez wpływu na łączność. Możesz wprowadzić następujące zmiany bez przestojów:

* Można włączyć lub wyłączyć dodatek Premium Usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Można zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność. Jednak obniżenie przepustowości obwodu nie jest obsługiwane.
* Można zmienić plan pomiaru z Dane taryfowe na nieograniczone dane. Jednak zmiana planu pomiaru z nieograniczonych danych na dane taryfowe nie jest obsługiwana.
* Można włączyć i wyłączyć *zezwalaj na operacje klasyczne*.

Aby uzyskać więcej informacji na temat ograniczeń i ograniczeń, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek Premium usługi ExpressRoute

Dodatek Premium usługi ExpressRoute premium dla istniejącego obwodu można włączyć za pomocą następującego polecenia:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Obwód ma teraz włączone funkcje dodatku Premium Usługi ExpressRoute. Rozpoczynamy rozliczanie się za funkcję dodatku premium, gdy tylko polecenie zostanie pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek Premium usługi ExpressRoute

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż to, co jest dozwolone dla obwodu standardowego.
>
>

Przed wyłączeniem dodatku premium usługi ExpressRoute należy zrozumieć następujące kryteria:

* Przed przejściem na starszą do standardową należy upewnić się, że masz mniej niż 10 sieci wirtualnych połączonych z obwódem. Jeśli masz więcej niż 10, żądanie aktualizacji nie powiedzie się, a my rozliczamy cię według stawek premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli nie odłączysz wszystkich sieci wirtualnych, żądanie aktualizacji nie powiedzie się, a my rozliczamy cię według stawek premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli marszruty jest większy niż 4000 tras, sesja BGP spada. Sesja nie zostanie ponownie zmiękczona, dopóki liczba anonsowanych prefiksów nie będzie niższa niż 4000.

Dodatek premium usługi ExpressRoute premium dla istniejącego obwodu można wyłączyć, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowość obwodu usługi ExpressRoute

Aby uzyskać obsługiwane opcje przepustowości dla dostawcy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi usługi ExpressRoute](expressroute-faqs.md). Można wybrać dowolny rozmiar większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Jeśli na istniejącym porcie jest niewystarczająca pojemność, może być trzeba ponownie utworzyć obwód usługi ExpressRoute. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania obsługi administracyjnej obwodu usługi ExpressRoute, a następnie ponownego obsługi administracyjnej nowego obwodu usługi ExpressRoute.
>

Po ominieniu rozmiaru należy użyć następującego polecenia, aby zmienić rozmiar obwodu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Twój obwód jest po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą łączności, aby zaktualizować konfiguracje po ich stronie, aby dopasować tę zmianę. Po dokonaniu tego powiadomienia, rozpoczynamy rozliczenia za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostkę SKU z licznika do nieograniczonego

Jednostkę SKU obwodu usługi ExpressRoute można zmienić, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do klasycznych i resource manager środowisk

Przejrzyj instrukcje w [usłudze Przenieś obwody usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Aby anulować aprowizję i usunąć obwód usługi ExpressRoute, upewnij się, że znasz następujące kryteria:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan inicjowania obsługi administracyjnej dostawcy obwodu usługi Usługi ExpressRoute to **inicjowanie obsługi administracyjnej** lub **inicjowanie obsługi administracyjnej,** należy współpracować z dostawcą usług w celu anulowania obsługi administracyjnej obwodu po ich stronie. Nadal rezerwujemy zasoby i rozliczamy cię do czasu zakończenia przez usługodawcę anulowania obsługi administracyjnej obwodu i powiadomi nas o tym.
* Można usunąć obwód, jeśli usługodawca wyrejestrował obwód. Gdy obwód jest wyrejestrowany, stan inicjowania obsługi administracyjnej dostawcy usług jest ustawiony na **Nie aprowied .** Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód usługi ExpressRoute można usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu upewnij się, że wykonujesz następujące zadania:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](howto-routing-cli.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](howto-linkvnet-cli.md)
