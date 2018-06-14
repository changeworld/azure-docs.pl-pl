---
title: 'Połączyć sieć wirtualną z obwodem usługi ExpressRoute: środowiska PowerShell: klasycznym: Azure | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualne) do obwody usługi ExpressRoute, przy użyciu klasycznego modelu wdrażania i programu PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 2f0fed77a676bc46e8daa9c41efd533dffe72d8b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875001"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Połączyć sieć wirtualną z obwodem usługi ExpressRoute przy użyciu programu PowerShell (klasyczne)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł pomoże Ci połączyć sieci wirtualnych (sieci wirtualne) obwody usługi ExpressRoute Azure przy użyciu klasycznego modelu wdrażania i programu PowerShell. Sieci wirtualne może być w tej samej subskrypcji lub może być częścią innej subskrypcji.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji
1. Należy najnowszej wersji modułów programu Azure PowerShell. Możesz pobrać najnowsze moduły programu PowerShell z sekcji PowerShell [Azure pobiera strony](https://azure.microsoft.com/downloads/). Postępuj zgodnie z instrukcjami [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) wskazówki krok po kroku dotyczące sposobu konfigurowania komputera do modułów programu Azure PowerShell.
2. Należy przejrzeć [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.
3. Musisz mieć aktywny obwód usługi ExpressRoute.
   * Postępuj zgodnie z instrukcjami, aby [utworzyć obwodu usługi ExpressRoute](expressroute-howto-circuit-classic.md) i dostawcą połączenia włączyć obwodu.
   * Upewnij się, że masz prywatnej komunikacji równorzędnej platformy Azure skonfigurowane dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-classic.md) artykułu instrukcje routingu.
   * Upewnij się, że Azure prywatnej komunikacji równorzędnej jest skonfigurowana i komunikację równorzędną BGP między siecią a Microsoft jest uruchomiony, dzięki czemu można włączyć łączność end-to-end.
   * Musi mieć sieci wirtualnej i Brama sieci wirtualnej utworzone i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Skonfiguruj sieć wirtualną w przypadku połączeń ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Maksymalnie 10 sieciami wirtualnymi można połączyć z obwodem usługi ExpressRoute. Wszystkie sieci wirtualne muszą być w tym samym regionie geograficznymi. Możesz połączyć większą liczbę sieci wirtualnych do obwodu ExpressRoute lub łącze sieci wirtualne, które są w różnych regionach geograficznymi włączona dodatek usługi ExpressRoute w warstwie premium. Sprawdź [— często zadawane pytania](expressroute-faqs.md) uzyskać więcej informacji dotyczących dodatek w warstwie premium.

Pojedynczy sieci wirtualnej może odnosić się do maksymalnie cztery obwody usługi ExpressRoute. Umożliwia utworzenie nowego łącza do każdego obwodu ExpressRoute, który jest nawiązywane z procesem opisanym niżej. Obwody usługi ExpressRoute, można w tej samej subskrypcji, różnych subskrypcji lub kombinację obu.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Połączyć się z obwodem sieci wirtualnej w tej samej subskrypcji
Możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute, za pomocą następującego polecenia cmdlet. Upewnij się, że bramy sieci wirtualnej jest tworzona i jest gotowy do konsolidacji przed uruchomieniem polecenia cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwodu usługi ExpressRoute mogą udostępniać między wieloma subskrypcjami. Na poniższej ilustracji przedstawiono prosty przedstawienie sposobu udostępniania prac dla obwody usługi ExpressRoute między wieloma subskrypcjami.

Każdy z mniejszym chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Do wdrażania ich usług — ale działów może udostępniać pojedynczy obwodu ExpressRoute do nawiązywania ponownego połączenia z siecią lokalną każdego z działów w organizacji, można użyć własnych subskrypcji. Jednego działu (w tym przykładzie: IT) może być właścicielem obwodu usługi expressroute. Inne subskrypcje w organizacji można używać obwodu usługi expressroute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwód dedykowany zostaną zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne udostępnianie tego samego przepustowości.
> 
> 

![Łącznością między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administracja
*Właściciela obwodu* jest administratorem/coadministrator subskrypcji, w której jest tworzona obwodu usługi expressroute. Właściciel obwodu może autoryzować Administratorzy/coadministrators innych subskrypcji, określany jako *obwodu użytkowników*, aby używać obwodu dedykowanego, której jest właścicielem. Obwód użytkowników, którzy są autoryzowane do używania obwodu ExpressRoute organizacji można połączyć sieci wirtualnej w swoich subskrypcji obwodu ExpressRoute po użytkownik jest uprawniony.

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływanie autoryzacje w dowolnym momencie. Odwoływanie autoryzacji spowoduje wszystkie linki usuwany z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Tworzenie autoryzacji**

Właściciel obwodu autoryzuje administratorom używanie określony obwód inne subskrypcje. W poniższym przykładzie administrator obwodu (Contoso IT) umożliwia administrator innej subskrypcji (deweloperów-Test), aby utworzyć link do dwóch sieci wirtualnych do obwodu. Administrator IT firmy Contoso umożliwia to przez określenie identyfikatora testu dla deweloperów firmy Microsoft. Polecenie cmdlet nie Wyślij wiadomość e-mail do określonego identyfikatora firmy Microsoft. Właściciel obwodu musi jawnie powiadomienie właściciel subskrypcji, że autoryzacja jest pełny.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Przegląd zezwolenia**

Właściciel obwodu można przejrzeć wszystkie autoryzacje, które są wydawane w szczególności obwód, uruchamiając następujące polecenie cmdlet:

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


**Aktualizacja zezwoleń**

Właściciela obwodu można zmodyfikować autoryzacje za pomocą następującego polecenia cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Usuwanie zezwolenia**

Właściciel obwodu można odwołać/usuwania zezwolenia użytkownikowi, uruchamiając następujące polecenie cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operacje użytkownik obwodu

**Przegląd zezwolenia**

Użytkownik obwodu można przejrzeć autoryzacje za pomocą następującego polecenia cmdlet:

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

**Realizowanie autoryzacje łącza**

Użytkownik obwodu można uruchomić następujące polecenie cmdlet, aby zrealizować autoryzacji łącza:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Uruchom następujące polecenie w nowo połączonej subskrypcji dla sieci wirtualnej:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

