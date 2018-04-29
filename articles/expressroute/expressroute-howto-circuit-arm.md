---
title: 'Tworzenie i modyfikowanie obwodu usługi ExpressRoute: środowiska PowerShell: usługi Azure Resource Manager | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób tworzenia, obsługi administracyjnej, sprawdź, aktualizacji, usuwania i anulowanie zastrzeżenia obwodu usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 0d45e97cc42826375a99df16a73c9a7b0c359224
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute, przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule opisano sposób tworzenia obwodu usługi Azure ExpressRoute przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania usługi Azure Resource Manager. W tym artykule przedstawiono również sposób Sprawdź stan obwodu, zaktualizować lub usunąć i anulowanie zastrzeżenia go.

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie programu Azure PowerShell](/powershell/azure/overview).
* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.


## <a name="create"></a>Tworzenie i przydzielanie obwodu usługi ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wyboru subskrypcji
Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Użyj poniższych przykładach umożliwiające nawiązywanie połączeń:

```powershell
Connect-AzureRmAccount
```

Sprawdź subskrypcje dla konta:

```powershell
Get-AzureRmSubscription
```

Wybierz subskrypcję, którą chcesz utworzyć obwodu usługi ExpressRoute dla:

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości
Przed utworzeniem obwodu usługi ExpressRoute, należy listę dostawców obsługiwanych łączności, lokalizacji i opcji przepustowości.

Polecenia cmdlet programu PowerShell **Get AzureRmExpressRouteServiceProvider** zwraca informację, która będzie używana w dalszych krokach:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Sprawdź, czy dostawca połączenia znajduje się tam. Zanotuj następujące informacje, które będą potrzebne później utworzyć obwód:

* Name (Nazwa)
* PeeringLocations
* BandwidthsOffered

Teraz możesz utworzyć obwodu usługi ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)
Jeśli nie masz już grupę zasobów, należy go utworzyć przed utworzeniem obwodu usługi ExpressRoute. Można to zrobić, uruchamiając następujące polecenie:

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


Poniższy przykład przedstawia sposób tworzenia obwodu ExpressRoute za pośrednictwem Equinix 200-MB/s w Dolinie Krzemowej. Jeśli używasz innego dostawcy i inne ustawienia, należy zastąpić te informacje po wprowadzeniu żądania. Skorzystaj z następującego przykładu, aby zażądać nowego klucza usługi:

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Upewnij się, należy określić prawidłowe jednostki SKU warstwy i rodzina jednostek SKU:

* Jednostka SKU warstwy określa, czy włączone jest standardem ExpressRoute lub dodatek usługi ExpressRoute w warstwie premium. Można określić *standardowe* można pobrać wersji standard lub *Premium* dla dodatku premium.
* Rodzina jednostek SKU Określa typ rozliczeń. Można określić *Metereddata* planu dane naliczane i *Unlimiteddata* planu nieograniczone danych. Można zmienić typ rozliczeń z *Metereddata* do *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* do *Metereddata*.

> [!IMPORTANT]
> Jest on rozliczany od momentu jego wystawieniu klucz usługi obwodu usługi ExpressRoute. Upewnij się, gdy dostawca łączności jest gotowy do udostępniania obwodu podczas wykonywania tej operacji.
> 
> 

Odpowiedź zawiera klucz usługi. Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Wyświetl listę wszystkich obwody usługi ExpressRoute
Aby uzyskać listę wszystkich obwody usługi ExpressRoute, które zostały utworzone, uruchom **Get-AzureRmExpressRouteCircuit** polecenia:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do poniższego przykładu:

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

Te informacje w dowolnym momencie można pobrać za pomocą `Get-AzureRmExpressRouteCircuit` polecenia cmdlet. Wywołania bez parametrów wyświetla listę wszystkich obwodów. Klucz usługi ma na liście *bindingTemplate* pola:

```powershell
Get-AzureRmExpressRouteCircuit
```


Odpowiedź wygląda podobnie do poniższego przykładu:

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


Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcą połączenia do inicjowania obsługi
*ServiceProviderProvisioningState* zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie dostawcy usług. Stan udostępnia stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji na temat obwodu inicjowania obsługi administracyjnej stanów zobacz [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu ExpressRoute obwodu znajduje się w następującym stanie:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Po dostawca połączenia trwa jej włączanie obwodu zmienia się na następujący:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Dla Ciebie można było używać obwodu usługi ExpressRoute musi być w następującym stanie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Okresowo sprawdzać stan i stan klucz obwodu
Sprawdzanie stanu i stan klucz obwodu informuje gdy dostawca jest włączony obwodu. Po skonfigurowaniu obwodu *ServiceProviderProvisioningState* pojawia się jako *obsługiwane administracyjnie*, jak pokazano w poniższym przykładzie:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpowiedź wygląda podobnie do poniższego przykładu:

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
Aby uzyskać instrukcje, zobacz [obwodu ExpressRoute konfiguracji routingu](expressroute-howto-routing-arm.md) artykułu do tworzenia i modyfikowania obwodu komunikacji równorzędnych.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwody, które zostały utworzone z dostawców usług, które oferują warstwy 2 łączności usługi. Jeśli używasz usługodawcy, który oferuje zarządzanych warstwy 3 usługi (zazwyczaj IP sieci VPN, takie jak MPLS), dostawca połączenia konfiguruje i zarządza nimi routingu dla Ciebie.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
Następnie połączyć sieć wirtualną obwodu usługi ExpressRoute. Użyj [łączenia sieci wirtualne obwody usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) artykuł podczas pracy z modelu wdrażania usługi Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Pobieranie stanu obwodu usługi ExpressRoute
Te informacje w dowolnym momencie można pobrać za pomocą **Get-AzureRmExpressRouteCircuit** polecenia cmdlet. Wywołania bez parametrów wyświetla listę wszystkich obwodów.

```powershell
Get-AzureRmExpressRouteCircuit
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


Wyświetlane są informacje dotyczące określonego obwodu ExpressRoute, przekazując nazwy grupy zasobów i nazwy obwodu jako parametru do wywołania:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpowiedź wygląda podobnie do poniższego przykładu:

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


Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute
Można zmodyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność.

Można wykonać następujące zadania bez przestojów:

* Włącz lub Wyłącz dodatek premium usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu ExpressRoute dostarczane na port jest dostępna pojemność. Przepustowości obwodu zmiana wersji na starszą nie jest obsługiwane. 
* Zmiana zliczania planu naliczane danych nieograniczone danych. Zmiany planu zliczania dane nieograniczone naliczane danych nie jest obsługiwana.
* Można włączyć lub wyłączyć *operacje klasycznego*.

Aby uzyskać więcej informacji o limity i ograniczenia, zobacz [ExpressRoute — często zadawane pytania](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek usługi ExpressRoute w warstwie premium
Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można włączyć za pomocą następującego fragmentu kodu programu PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód ma teraz włączonymi funkcjami dodatek premium usługi ExpressRoute. Zaczniemy rozliczeń dla funkcji dodatku premium, jak polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek usługi ExpressRoute w warstwie premium
> [!IMPORTANT]
> Jeśli używasz zasobów, które są większe niż co to jest dozwolone dla standardowych obwodu ta operacja może zakończyć się niepowodzeniem.
> 
> 

Należy uwzględnić następujące informacje:

* Przed obniżyć z premium ze standardem, należy się upewnić, że liczba sieci wirtualnych, które są połączone z obwodem jest mniejsza niż 10. Jeśli nie zostanie żądania aktualizacji nie powiedzie się i NAS rachunku stawkami premium.
* Należy odłączyć wszystkie sieci wirtualne w różnych regionach geograficznymi. Jeśli tego nie zrobisz, żądanie aktualizacji zakończy się niepowodzeniem, a nas rachunku stawkami premium.
* Tabela tras musi być mniejsza niż 4000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000 tras, sesji BGP porzuca i nie będzie reenabled, aż przejdzie liczby prefiksów anonsowanych poniżej 4000.

Dodatek usługi ExpressRoute w warstwie premium dla istniejącego obwodu można wyłączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowości obwodu ExpressRoute
Dla opcji obsługiwanych przepustowości dla dostawcy, sprawdź [ExpressRoute — często zadawane pytania](expressroute-faqs.md). Można wybrać żadnych rozmiar większy niż rozmiar z istniejącym obwodem.

> [!IMPORTANT]
> Może być konieczne ponowne utworzenie obwodu usługi expressroute w przypadku niewystarczającego pojemności na istniejącego portu. Nie można uaktualnić obwodu, jeśli nie bez dodatkowej pojemności dostępnej w tej lokalizacji.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez zakłóceń. Zmiana wersji na starszą przepustowości wymaga anulowanie zastrzeżenia obwodu ExpressRoute, a następnie Udostępnij ponownie nowy obwód usługi ExpressRoute.
> 

Po wybraniu rozmiar, jaki należy Użyj następującego polecenia, aby zmienić rozmiar obwodu:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Obwodu będzie ustalany po stronie firmy Microsoft. Następnie musi skontaktuj się z dostawcą połączenia można zaktualizować konfiguracji na bok odpowiadające tej zmiany. Po wprowadzeniu tego powiadomienia, rozpocznie się rozliczeń można opcji zaktualizowane przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść SKU z naliczanego na nieograniczony
Jednostka SKU obwodu ExpressRoute można zmienić za pomocą następującego fragmentu kodu programu PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Do kontrolowania dostępu do klasycznego i środowisk usługi Resource Manager
Zapoznaj się z instrukcjami wyświetlanymi w [obwody Przenieś usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Anulowania obsługi i usuwania obwodu usługi ExpressRoute
Należy uwzględnić następujące informacje:

* Należy odłączyć wszystkie sieci wirtualne z obwodem usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodu.
* Jeśli dostawca usługi obwodu ExpressRoute stan inicjowania obsługi jest **inicjowania obsługi administracyjnej** lub **obsługiwane administracyjnie** należy skontaktować się z dostawcą usług na anulowanie zastrzeżenia obwód w bok. Będziemy nadal zarezerwować zasobów i obciążać Cię do czasu dostawcy usług wykonuje anulowania obsługi obwodu i powiadomienia NAS.
* Jeśli usługodawca została anulowana obwodu (ustawiono dostawcę usługi stan inicjowania obsługi **nieudostępniane**), można usunąć obwodu. Powoduje to zatrzymanie rozliczeń dla obwodu.

Można usunąć obwodu usługi ExpressRoute, uruchamiając następujące polecenie:

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu obwodu, upewnij się, należy wykonać następujące czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Link sieci wirtualnej do obwodu usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
