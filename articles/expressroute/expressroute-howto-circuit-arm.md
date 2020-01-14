---
title: 'Azure ExpressRoute: modyfikowanie obwodu: PowerShell'
description: Tworzenie, aprowizować, sprawdź, aktualizacji, usuwania i anulować aprowizację obwodu usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770972"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Tworzenie i modyfikowanie obwodu ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł pomoże Ci utworzyć obwodu ExpressRoute za pomocą poleceń cmdlet programu PowerShell i modelu wdrażania usługi Azure Resource Manager. Można również sprawdzić stan, aktualizacji, usunięcia lub anulować aprowizację obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem Sprawdź [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości
Przed przystąpieniem do tworzenia obwodu usługi ExpressRoute, należy listę dostawców łączności obsługiwanych, lokalizacji i opcje przepustowości.

Polecenie cmdlet **Get-AzExpressRouteServiceProvider** programu PowerShell zwraca te informacje, które będą używane w kolejnych krokach:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Sprawdź, czy dostawca połączenia znajduje się tam. Zanotuj następujące informacje, które będą potrzebne później, po utworzeniu obwodu:

* Name (Nazwa)
* PeeringLocations
* BandwidthsOffered

Teraz możesz utworzyć obwód usługi ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Tworzenie obwodu ExpressRoute
Jeśli nie masz jeszcze grupy zasobów, należy utworzyć jedną przed utworzeniem obwód usługi ExpressRoute. Możesz to zrobić, uruchamiając następujące polecenie:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Poniższy przykład pokazuje, jak utworzyć obwód usługi ExpressRoute za pośrednictwem Equinix 200-MB/s w Dolinie Krzemowej. Jeśli używasz innego dostawcy i inne ustawienia, należy zastąpić te informacje podczas Prześlij żądanie. Skorzystaj z następującego przykładu, aby zażądać nowego klucza usługi:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Upewnij się, że podajesz poprawne warstwa jednostki SKU i rodzina jednostek SKU:

* Warstwa SKU określa, czy obwód ExpressRoute jest [lokalny](expressroute-faqs.md#expressroute-local), standardowy czy [Premium](expressroute-faqs.md#expressroute-premium). Możesz określić *Local*, *Standard* lub *Premium*.
* Rodzina jednostek SKU Określa typ rozliczeń. Możesz określić *Metereddata* dla mierzonego planu taryfowego i *Unlimiteddata* dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Obwód *lokalny* jest zawsze *Unlimiteddata*.

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczana w momencie utworzenia klucza usługi. Upewnij się, gdy dostawca połączenia jest gotowy do obsługi administracyjnej obwodu podczas wykonywania tej operacji.
>
>

Odpowiedź zawiera klucz usługi. Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Wyświetl wszystkie obwody usługi ExpressRoute
Aby uzyskać listę wszystkich utworzonych obwodów usługi ExpressRoute, uruchom polecenie **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

Te informacje można pobrać w dowolnym momencie za pomocą polecenia cmdlet `Get-AzExpressRouteCircuit`. Wywołania bez parametrów wyświetla listę wszystkich obwodów. Twój klucz usługi jest wymieniony w polu *ServiceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
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


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji
*ServiceProviderProvisioningState* zawiera informacje o bieżącym stanie aprowizacji po stronie Dostawca usług. Stan zawiera stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Po dostawcy połączenia Trwa włączanie go dla Ciebie, obwodu zmienia się w następującym stanie:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Należy mieć możliwość użycia obwodu usługi ExpressRoute musi być w następującym stanie:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. okresowo sprawdza stan i stan klucza obwodu.
Sprawdzanie stanu i stan klawisza obwodu informuje o tym, kiedy Twój dostawca włączył obwodu. Po skonfigurowaniu obwodu *ServiceProviderProvisioningState* pojawia się zgodnie z *zainicjowaną obsługą*, jak pokazano w następującym przykładzie:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

### <a name="7-create-your-routing-configuration"></a>7. Utwórz konfigurację routingu
Aby uzyskać instrukcje krok po kroku, zobacz artykuł [Konfiguracja routingu obwodów usługi ExpressRoute](expressroute-howto-routing-arm.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są utworzonych przy pomocy dostawców oferujących usługi łączności 2 warstwy. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zazwyczaj IP sieci VPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem ExpressRoute
Następnie łączenie sieci wirtualnej na obwód usługi ExpressRoute. Podczas pracy z modelem wdrażania Menedżer zasobów należy użyć artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Uzyskiwanie stanu obwodu usługi ExpressRoute
Te informacje można pobrać w dowolnym momencie za pomocą polecenia cmdlet **Get-AzExpressRouteCircuit** . Wywołania bez parametrów wyświetla listę wszystkich obwodów.

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


Można uzyskać informacji na temat określonego obwodu usługi ExpressRoute przez przekazanie nazwy grupy zasobów i nazwy obwodu jako parametr do wywołania:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modify"></a>Modyfikowanie obwodu ExpressRoute
Można modyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność.

Możesz wykonać następujące zadania bez przerw w dostępności:

* Włącz lub Wyłącz dodatek ExpressRoute premium dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że pojemność dostępna na porcie. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan zliczania z plan taryfowy z danymi na dane nieograniczone. Zmiana planu zliczania z danymi nieograniczonymi plan taryfowy z danymi nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.

Aby uzyskać więcej informacji o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek premium usługi ExpressRoute
Należy włączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu, przy użyciu następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód ma teraz funkcje dodatku premium usługi ExpressRoute, włączone. Zaczniemy rozliczenia dla funkcji dodatku premium tak szybko, jak polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek ExpressRoute premium
> [!IMPORTANT]
> Jeśli używasz zasobów, które są większe niż co to jest dozwolone w przypadku obwód standardowy, ta operacja może zakończyć się niepowodzeniem.
>
>

Zanotuj następujące informacje:

* Przed obniżanie poziomu z wersji premium na warstwę standardowa, należy się upewnić, że liczba sieci wirtualnych, które są połączone z obwodem usługi jest mniejsza niż 10. Jeśli nie, Twoje żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli tego nie zrobisz, Twoje żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek premium.
* Tabela tras muszą być mniej niż 4000 tras do prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większa niż 4000, sesji protokołu BGP spada i nie będzie reenabled, dopóki liczba prefiksów anonsowanych spadnie poniżej 4000.

Możesz wyłączyć dodatek premium usługi ExpressRoute dla istniejącego obwodu za pomocą następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowości obwodu usługi ExpressRoute
W przypadku opcji obsługiwanych przepustowości dla dostawcy zapoznaj się z tematem [często zadawanych pytań](expressroute-faqs.md)dotyczących usługi ExpressRoute. Możesz wybrać dowolnej wielkości większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Może być konieczne odtworzenie obwód usługi ExpressRoute, jeśli istnieje niewystarczająca wydajność przy użyciu istniejącego portu. Nie można uaktualnić obwodu, jeśli w tej lokalizacji jest dostępna nie dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez przerw w działaniu. Obniżenie przepustowości wymaga anulować aprowizację obwodu usługi ExpressRoute, a następnie ponownie udostępnić nowego obwodu usługi ExpressRoute.
>

Po podjęciu decyzji rozmiar, jakiego potrzebujesz, użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Obwód usługi będą mieć rozmiar po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą połączenia, można zaktualizować konfiguracji po ich stronie odpowiadający tej zmiany. Po wprowadzeniu tego powiadomienia, firma Microsoft rozpocznie się rozliczanie opcji zaktualizowane przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostki SKU z naliczanego na nieograniczony
Jednostka SKU obwodu usługi ExpressRoute można zmienić za pomocą następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do klasycznej sieci wirtualnej i środowiska usługi Resource Manager
Zapoznaj się z instrukcjami w temacie [Move ExpressRoute Circuits z klasycznego modelu wdrażania Menedżer zasobów](expressroute-howto-move-arm.md).

## <a name="delete"></a>Cofanie aprowizacji i usuwanie obwodu ExpressRoute
Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, jeśli wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

Możesz usunąć obwód usługi ExpressRoute, uruchamiając następujące polecenie:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu, upewnij się, że czynności zostały wykonane następujące dalej:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
