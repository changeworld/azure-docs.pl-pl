---
title: 'Łączenie sieci wirtualnej z obwodem usługi ExpressRoute: programu PowerShell: klasyczny: Azure | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (Vnet) do obwodów usługi ExpressRoute za pomocą klasycznego modelu wdrażania i programu PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: ganesr
ms.openlocfilehash: e598249d0065bde8b3fe74883da8a0e39c9bc7c7
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308089"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute za pomocą programu PowerShell (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — witryna Azure portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł pomoże połączyć sieci wirtualne (Vnet) obwodów usługi ExpressRoute platformy Azure przy użyciu klasycznego modelu wdrażania i programu PowerShell. Sieci wirtualne mogą być w tej samej subskrypcji lub mogą być częścią innej subskrypcji.

Pojedynczej sieci wirtualnej można połączyć maksymalnie cztery obwodów usługi ExpressRoute. Wykonaj kroki w tym artykule, aby utworzyć nowe łącze, aby każdy obwód usługi ExpressRoute, z którą jest nawiązywane. Obwody usługi ExpressRoute może być w tej samej subskrypcji, w różnych subskrypcjach lub kombinacji obu.

Maksymalnie 10 sieciami wirtualnymi można połączyć z obwodem usługi ExpressRoute. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym. Możesz połączyć większej liczby sieci wirtualnych na obwód usługi ExpressRoute lub sieci wirtualne łącze, które znajdują się w innych regionów geopolitycznych, jeśli został włączony dodatek premium usługi ExpressRoute. Sprawdź [— często zadawane pytania](expressroute-faqs.md) więcej informacji na temat używania dodatku premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Przegląd [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Musisz mieć aktywny obwód usługi ExpressRoute.
   * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md) i poproś dostawcę połączenia Włącz obwodu.
   * Upewnij się, że prywatnej komunikacji równorzędnej Azure skonfigurowany dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-classic.md) artykuł, aby uzyskać instrukcje routingu.
   * Upewnij się, że skonfigurowano prywatnej komunikacji równorzędnej Azure i komunikację równorzędną BGP między siecią a Microsoft działa tak, aby umożliwić łączność end-to-end.
   * Konieczne jest posiadanie sieci wirtualnej i bramy sieci wirtualnej, utworzona i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Konfigurowanie sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Pobierz najnowsze polecenia cmdlet programu PowerShell

Konieczne jest najnowsza wersja modułów programu Azure PowerShell. Możesz pobrać najnowsze moduły programu PowerShell z sekcji PowerShell [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/). Postępuj zgodnie z instrukcjami w [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) wskazówki krok po kroku dotyczące sposobu konfigurowania komputera do modułów programu Azure PowerShell.

Ponadto należy pobrać moduł usługi ExpressRoute. Następujące przykładowe polecenia służy do pobierania modułów platformy Azure i usługi ExpressRoute. Korzystając z tych poleceń, należy pamiętać, że numer wersji (w tym przykładzie 5.1.1) zmieni się nowsze wersje poleceń cmdlet zostaną zwolnione.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem
Możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute za pomocą następującego polecenia cmdlet. Upewnij się, że brama sieci wirtualnej jest tworzony i jest gotowy do konsolidacji przed uruchomieniem polecenia cmdlet.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Usuwanie łącza sieci wirtualnej z obwodem
Możesz usunąć łącze sieci wirtualnej z obwodem usługi ExpressRoute za pomocą następującego polecenia cmdlet. Upewnij się, że bieżąca subskrypcja została wybrana danej sieci wirtualnej. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższej ilustracji pokazano prosty schemat działa jak udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każdy z mniejszych chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Do wdrażania ich usług — ale działów mogą udostępniać pojedynczy obwód usługi ExpressRoute do łączenia z powrotem do sieci lokalnej każdego z działów w organizacji, można użyć własnej subskrypcji. Jednego działu (w tym przykładzie: IT) mogą być właścicielami obwód usługi ExpressRoute. Inne subskrypcje w ramach organizacji, można użyć z obwodem usługi ExpressRoute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwód dedykowany będą stosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne współdzielą ten sam przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administracja
*Właściciela obwodu* jest administrator/współadministrator subskrypcji, w którym zostanie utworzona z obwodem usługi ExpressRoute. Właściciela obwodu może autoryzować administratorów/współadministratorów z innych subskrypcji, nazywane *circuit użytkowników*, aby użyć dedykowanego obwodu, której jest właścicielem. Obwód osób, które są autoryzowane do używania obwód usługi ExpressRoute w organizacji można połączyć sieci wirtualnej w ramach swojej subskrypcji z obwodem usługi ExpressRoute, po użytkownik jest uprawniony.

Właściciela obwodu ma uprawnienia do modyfikowania i odwołać autoryzacji w dowolnym momencie. Cofanie autoryzacji spowoduje wszystkie łącza usuwana z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Tworzenie autoryzacji**

Właściciela obwodu autoryzuje administratorom użycie określony obwód innych subskrypcji. W poniższym przykładzie administrator obwodu (Contoso IT) umożliwia administrator innej subskrypcji (Dev-Test), aby połączyć maksymalnie dwie sieci wirtualne z obwodem usługi. Administrator IT firmy Contoso umożliwia to przez określenie identyfikatora Microsoft Dev-Test. Polecenie cmdlet nie wysyłaj wiadomość e-mail do określonego identyfikatora firmy Microsoft. Właściciela obwodu musi jawnie powiadomić właściciela subskrypcji że autoryzacja została zakończona.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Przeglądanie autoryzacji**

Właściciela obwodu, można przejrzeć wszystkie autoryzacje są wydawane w ramach określonego obwodu, uruchamiając następujące polecenie cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Aktualizowanie autoryzacji**

Właściciela obwodu można zmodyfikować autoryzacji za pomocą następującego polecenia cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Usuwanie autoryzacji**

Właściciela obwodu można odwołać/usuwanie autoryzacji dla użytkownika, uruchamiając następujące polecenie cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

**Przeglądanie autoryzacji**

Użytkowników obwodu przejrzeć autoryzacji za pomocą następującego polecenia cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Zrealizowaniem autoryzacji łącza**

Użytkowników obwodu, można uruchomić następujące polecenie cmdlet, aby Zrealizuj autoryzację łącza:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

W nowo połączone subskrypcji w ramach sieci wirtualnej, uruchom następujące polecenie:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

