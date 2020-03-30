---
title: 'Azure ExpressRoute: Łączenie sieci wirtualnej z obwód: klasyczny'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualnych) z obwodami usługi ExpressRoute przy użyciu klasycznego modelu wdrażania i programu PowerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930030"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Podłączanie sieci wirtualnej do obwodu usługi ExpressRoute przy użyciu programu PowerShell (klasyczny)
> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł pomoże Ci połączyć sieci wirtualne (sieci wirtualne) z obwodami usługi Azure ExpressRoute przy użyciu programu PowerShell. Pojedyncza sieci wirtualnej mogą być połączone z maksymalnie czterech obwodów usługi ExpressRoute. Czynności opisane w tym artykule umożliwiają utworzenie nowego łącza do każdego obwodu usługi ExpressRoute, z którego następuje połączenie. Obwody usługi ExpressRoute mogą być w tej samej subskrypcji, różnych subskrypcji lub kombinacji obu. Ten artykuł dotyczy sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania.

Do obwodu usługi ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym. Po włączeniu dodatku Premium usługi ExpressRoute można połączyć większą liczbę sieci wirtualnych z obwodem usługi ExpressRoute lub połączyć sieci wirtualne w innych regionach geopolitycznych. Więcej informacji na temat dodatku premium można znaleźć w często [zadawanych](expressroute-faqs.md) pytaniach.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Przed rozpoczęciem konfiguracji należy zapoznać się [z wymaganiami wstępnymi,](expressroute-prerequisites.md) [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy.](expressroute-workflows.md)
* Musisz mieć aktywny obwód usługi ExpressRoute.
   * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md) i włączyć obwód dostawcy łączności.
   * Upewnij się, że masz skonfigurowany private peering platformy Azure dla obwodu. Zobacz artykuł [Konfigurowanie routingu](expressroute-howto-routing-classic.md) dla instrukcji routingu.
   * Upewnij się, że prywatna komunikacja równorzędna platformy Azure jest skonfigurowana, a komunikacja równorzędna BGP między siecią a firmą Microsoft jest w górę, dzięki czemu można włączyć łączność end-to-end.
   * Musisz mieć sieć wirtualną i bramę sieci wirtualnej utworzone i w pełni aprowizowana. Postępuj zgodnie z instrukcjami, aby [skonfigurować sieć wirtualną dla usługi ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Pobieranie najnowszych poleceń cmdlet programu PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem
Sieć wirtualną można połączyć z obwodem usługi ExpressRoute przy użyciu następującego polecenia cmdlet. Przed uruchomieniem polecenia cmdlet upewnij się, że brama sieci wirtualnej została utworzona i jest gotowa do łączenia.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Usuwanie łącza sieci wirtualnej do obwodu
Łącze sieci wirtualnej do obwodu usługi ExpressRoute można usunąć przy użyciu następującego polecenia cmdlet. Upewnij się, że bieżąca subskrypcja jest wybrana dla danej sieci wirtualnej. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w dużej chmurze jest używana do reprezentowania subskrypcji należących do różnych działów w organizacji. Każdy z działów w organizacji może używać własnej subskrypcji do wdrażania swoich usług — ale działy mogą udostępniać jeden obwód usługi ExpressRoute, aby połączyć się z powrotem do sieci lokalnej. Jeden dział (w tym przykładzie: IT) może być właścicielem obwodu usługi ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu usługi ExpressRoute.

> [!NOTE]
> Opłaty za łączność i przepustowość dla dedykowanego obwodu zostaną zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administracja
*Właścicielem obwodu* jest administrator/współadministrator subskrypcji, w której tworzony jest obwód usługi ExpressRoute. Właściciel obwodu może autoryzować administratorów / współadministratorów innych subskrypcji, zwanych *użytkownikami obwodów,* do korzystania z dedykowanego obwodu, który są ich własnością. Użytkownicy obwodów, którzy są upoważnieni do korzystania z obwodu usługi ExpressRoute organizacji, mogą połączyć sieć wirtualną w ramach subskrypcji z obwodem usługi ExpressRoute po ich autoryzacji.

Właściciel obwodu ma prawo do modyfikowania i cofania autoryzacji w dowolnym momencie. Cofnięcie autoryzacji spowoduje usunięcie wszystkich łączy z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Tworzenie autoryzacji**

Właściciel obwodu upoważnia administratorów innych subskrypcji do korzystania z określonego obwodu. W poniższym przykładzie administrator obwodu (Contoso IT) umożliwia administratorowi innej subskrypcji (Dev-Test) łączenie maksymalnie dwóch sieci wirtualnych z obwodem. Administrator IT contoso włącza to, określając identyfikator Microsoft Dev-Test. Polecenie cmdlet nie wysyła wiadomości e-mail do określonego identyfikatora Microsoft. Właściciel obwodu musi jawnie powiadomić innego właściciela subskrypcji, że autoryzacja została ukończona.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Zwracają:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Przeglądanie autoryzacji**

Właściciel obwodu może przejrzeć wszystkie uprawnienia wydane na określonym obwodzie, uruchamiając następujące polecenie cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Zwracają:

  ```powershell
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
  ```

**Aktualizowanie autoryzacji**

Właściciel obwodu może modyfikować autoryzacje przy użyciu następującego polecenia cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Zwracają:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Usuwanie autoryzacji**

Właściciel obwodu może odwołać/usunąć autoryzacje do użytkownika, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

**Przeglądanie autoryzacji**

Użytkownik obwodu może przeglądać autoryzacje przy użyciu następującego polecenia cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Zwracają:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Realizacja autoryzacji łączy**

Użytkownik obwodu może uruchomić następujące polecenie cmdlet, aby zrealizować autoryzację łącza:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Zwracają:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Uruchom to polecenie w nowo połączonej subskrypcji sieci wirtualnej:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
