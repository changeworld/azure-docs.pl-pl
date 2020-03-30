---
title: 'Usługa Azure ExpressRoute: modyfikowanie obwodu: program PowerShell'
description: Tworzenie, inicjowanie obsługi administracyjnej, weryfikowanie, aktualizowanie, usuwanie i usuwanie obsługi administracyjnej obwodu usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770972"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Tworzenie i modyfikowanie obwodu ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł ułatwia tworzenie obwodu usługi ExpressRoute przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania usługi Azure Resource Manager. Można również sprawdzić stan, zaktualizować, usunąć lub anulować aprowizję obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem zapoznaj się z [wymaganiami wstępnymi](expressroute-prerequisites.md) i [przepływami pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i udostępnianie obwodu usługi ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości
Przed utworzeniem obwodu usługi ExpressRoute potrzebna jest lista obsługiwanych dostawców łączności, lokalizacji i opcji przepustowości.

Polecenie cmdlet programu PowerShell **Get-AzExpressRouteServiceProvider** zwraca te informacje, które będą używane w późniejszych krokach:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Sprawdź, czy dostawca łączności znajduje się na liście. Zanotuj następujące informacje, które są potrzebne później podczas tworzenia obwodu:

* Nazwa
* Alokacje komunikacji równorzędnej
* Oferowane przepustowości

Teraz możesz przystąpić do tworzenia obwodu usługi ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Tworzenie obwodu usługi ExpressRoute
Jeśli nie masz jeszcze grupy zasobów, należy go utworzyć przed utworzeniem obwodu usługi ExpressRoute. Można to zrobić, uruchamiając następujące polecenie:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

W poniższym przykładzie pokazano, jak utworzyć obwód 200 Mb/s usługi ExpressRoute przez Equinix w Dolinie Krzemowej. Jeśli używasz innego dostawcy i różnych ustawień, zastąp te informacje podczas składania wniosku. Użyj następującego przykładu, aby zażądać nowego klucza usługi:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Upewnij się, że określono poprawną warstwę jednostki SKU i rodzinę jednostek SKU:

* Warstwa SKU określa, czy obwód usługi ExpressRoute to [Lokalny,](expressroute-faqs.md#expressroute-local)Standardowy czy [Premium.](expressroute-faqs.md#expressroute-premium) Można określić *lokalne,* *standardowe* lub *premium*.
* Rodzina jednostek SKU określa typ rozliczeń. Można określić *Dane metereddata* dla taryfowego planu danych i *nieograniczone dane* dla nieograniczonego planu danych. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Lokalny *Local* obwód jest zawsze *Unlimiteddata*.

> [!IMPORTANT]
> Twój obwód usługi ExpressRoute jest rozliczany od momentu wystawienia klucza usługi. Upewnij się, że ta operacja jest wykonywane, gdy dostawca łączności jest gotowy do aprowizowania obwodu.
>
>

Odpowiedź zawiera klucz usługi. Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Lista wszystkich obwodów usługi ExpressRoute
Aby uzyskać listę wszystkich utworzonych obwodów usługi ExpressRoute, uruchom polecenie **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do następującego przykładu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Te informacje można pobrać w dowolnym `Get-AzExpressRouteCircuit` momencie za pomocą polecenia cmdlet. Wykonywanie połączenia bez parametrów zawiera listę wszystkich obwodów. Klucz usługi znajduje się w polu *ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpowiedź wygląda podobnie do następującego przykładu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcy łączności w celu inicjowania obsługi administracyjnej
*ServiceProviderProvisioningState* zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie dostawcy usług. Stan zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji na temat stanów inicjowania obsługi administracyjnej [obwodów,](expressroute-workflows.md#expressroute-circuit-provisioning-states)zobacz Przepływy pracy .

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Obwód zmienia się w następujący stan, gdy dostawca łączności jest w trakcie włączania go dla Ciebie:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Aby można było korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Okresowo sprawdzaj stan i stan przycisku obwodu
Sprawdzanie stanu i stanu klucza obwodu informuje o tym, kiedy dostawca włączył obwód. Po skonfigurowaniu obwodu *ServiceProviderProvisioningState* jest wyświetlany jako *aprowizowany*, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpowiedź wygląda podobnie do następującego przykładu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Tworzenie konfiguracji routingu
Aby uzyskać instrukcje krok po kroku, zobacz artykuł [konfiguracji routingu obwodu usługi ExpressRoute,](expressroute-howto-routing-arm.md) aby utworzyć i zmodyfikować komunikację równorzędną obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych z dostawcami usług, którzy oferują usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje usługi zarządzanej warstwy 3 (zazwyczaj sieć VPN IP, taką jak MPLS), dostawca łączności konfiguruje i zarządza routingiem.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
Następnie połącz sieć wirtualną z obwodem usługi ExpressRoute. Podczas pracy z modelem wdrażania Menedżera zasobów należy użyć artykułu Łączenie sieci wirtualnych z [obwodami usługi ExpressRoute.](expressroute-howto-linkvnet-arm.md)

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Uzyskiwanie stanu obwodu usługi ExpressRoute
Te informacje można pobrać w dowolnym momencie za pomocą polecenia cmdlet **Get-AzExpressRouteCircuit.** Wykonywanie połączenia bez parametrów zawiera listę wszystkich obwodów.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpowiedź jest podobna do poniższego przykładu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Informacje o określonym obwodzie usługi ExpressRoute można uzyskać, przekazując nazwę grupy zasobów i nazwę obwodu jako parametr do wywołania:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpowiedź wygląda podobnie do następującego przykładu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute
Można zmodyfikować niektóre właściwości obwodu usługi ExpressRoute bez wpływu na łączność.

Możesz wykonać następujące zadania bez przestojów:

* Włącz lub wyłącz dodatek Premium Usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Zwiększ przepustowość obwodu usługi ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan pomiaru z Dane taryfowe na Nieograniczone dane. Zmiana planu pomiaru z nieograniczonych danych na dane taryfowe nie jest obsługiwana.
* Można włączyć i wyłączyć *zezwalaj na operacje klasyczne*.

Aby uzyskać więcej informacji na temat ograniczeń i ograniczeń, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek Premium usługi ExpressRoute
Dodatek Premium usługi ExpressRoute premium dla istniejącego obwodu można włączyć przy użyciu następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód ma teraz włączone funkcje dodatku Premium Usługi ExpressRoute. Rozpoczynamy rozliczanie się za funkcję dodatku premium, gdy tylko polecenie zostanie pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek Premium usługi ExpressRoute
> [!IMPORTANT]
> Jeśli używasz zasobów, które są większe niż to, co jest dozwolone dla obwodu standardowego, ta operacja może zakończyć się niepowodzeniem.
>
>

Zanotuj następujące informacje:

* Przed przejściem na starszą do standardu należy upewnić się, że liczba sieci wirtualnych połączonych z obwódem jest mniejsza niż 10. Jeśli tego nie zrobisz, żądanie aktualizacji nie powiedzie się, a my rozliczamy cię według stawek premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli tego nie zrobisz, żądanie aktualizacji nie powiedzie się, a my rozliczamy cię według stawek premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli marszruty jest większy niż 4000 tras, sesja BGP zostanie porzucona i nie zostanie ponownie przypisana, dopóki liczba anonsowanych prefiksów nie spadnie poniżej 4000.

Dodatek premium usługi ExpressRoute premium dla istniejącego obwodu można wyłączyć przy użyciu następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowość obwodu usługi ExpressRoute
Aby uzyskać obsługiwane opcje przepustowości dla dostawcy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi usługi ExpressRoute](expressroute-faqs.md). Można wybrać dowolny rozmiar większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Może być trzeba ponownie utworzyć obwód usługi ExpressRoute, jeśli istnieje niewystarczająca pojemność na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania obsługi administracyjnej obwodu usługi ExpressRoute, a następnie ponownego obsługi administracyjnej nowego obwodu usługi ExpressRoute.
>

Po podjęciu decyzji, jaki rozmiar jest potrzebny, użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Twój obwód będzie miał rozmiar po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą łączności, aby zaktualizować konfiguracje po ich stronie, aby dopasować tę zmianę. Po dokonaniu tego powiadomienia, rozpoczniemy rozliczenia za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostkę SKU z licznika do nieograniczonego
Jednostkę SKU obwodu usługi ExpressRoute można zmienić przy użyciu następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do klasycznych i resource manager środowisk
Przejrzyj instrukcje w [usłudze Przenieś obwody usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute
Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan inicjowania obsługi administracyjnej dostawcy usług obwodu usługi ExpressRoute to **inicjowanie obsługi administracyjnej** lub **inicjowana obsługa,** należy współpracować z dostawcą usług w celu anulowania obsługi administracyjnej obwodu po ich stronie. Nadal rezerwujemy zasoby i rozliczamy cię do czasu zakończenia przez usługodawcę anulowania obsługi administracyjnej obwodu i powiadomi nas o tym.
* Jeśli dostawca usług wyrejestrował obwód (stan inicjowania obsługi administracyjnej dostawcy usług jest ustawiony na **Nie aprowizowana),** można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód usługi ExpressRoute można usunąć, uruchamiając następujące polecenie:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu wykonaj następujące czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
