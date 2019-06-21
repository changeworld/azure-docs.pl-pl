---
title: 'Skonfiguruj komunikację równorzędną dla obwodu — ExpressRoute: Azure: klasyczny | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: a57681cc9f44593ceea6b2c1795274c1b16d3a94
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64726212"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Utworzyć i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Wideo — prywatna komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — publicznej komunikacji równorzędnej](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

W tym artykule przedstawiono kroki, aby tworzyć i zarządzać nimi konfiguracji komunikacji równorzędnej routing dla obwodu usługi ExpressRoute za pomocą programu PowerShell i klasycznego modelu wdrażania. W poniższych krokach opisano również, jak sprawdzać stan komunikacji równorzędnej, aktualizować ją, usuwać i wstrzymywać jej obsługę administracyjną dla obwodu usługi ExpressRoute. Można skonfigurować jeden, dwa lub wszystkie trzy komunikacje równorzędne (Azure prywatnej i publicznej Azure i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje zarządzane warstwy 3 usługi (zwykle IPVPN, np. MPLS), dostawca połączenia będzie Konfigurowanie routingu oraz zarządzanie nim za Ciebie.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md) i który powinien zostać włączony przez dostawcę połączenia przed kontynuowaniem. Obwód usługi ExpressRoute musi być zainicjowany i włączony, aby można było uruchamiać polecenia cmdlet opisane poniżej.

### <a name="download-the-latest-powershell-cmdlets"></a>Pobierz najnowsze polecenia cmdlet programu PowerShell

Zainstaluj najnowsze wersje modułu usługi ExpressRoute i modułów programu PowerShell usługi Azure Service Management (SM). Podczas korzystając z następującego przykładu, należy pamiętać, że numer wersji (w tym przykładzie 5.1.1) zmieni się nowsze wersje poleceń cmdlet zostaną zwolnione.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Aby uzyskać więcej informacji, zobacz [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) wskazówki krok po kroku dotyczące sposobu konfigurowania komputera do modułów programu Azure PowerShell.

### <a name="sign-in"></a>Logowanie

Aby zalogować się do konta platformy Azure, można użyć następujących przykładów:

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem.

   ```powershell
   Connect-AzAccount
   ```
2. Sprawdź subskrypcje dostępne na koncie.

   ```powershell
   Get-AzSubscription
   ```
3. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Następnie użyj następującego polecenia cmdlet, aby dodać subskrypcję platformy Azure do programu PowerShell dla klasycznego modelu wdrażania.

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Prywatna komunikacja równorzędna Azure

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. **Utwórz obwód usługi ExpressRoute.**

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby upewnić się, że jest zainicjowany.**
   
   Sprawdź, czy obwód usługi ExpressRoute jest Aprowizowany i włączona.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwróć:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Upewnij się, że obwód jest wyświetlany jako Aprowizowana i włączony. Jeśli nie, współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Skonfiguruj prywatną komunikację równorzędną dla obwodu.**

   Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:
   
   * Podsieć /30 dla połączenia podstawowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Sprawdź, że nie używasz 65515.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalnie**.
     
   Skorzystaj z następującego przykładu, aby skonfigurować prywatną komunikację równorzędną dla obwodu:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Jeśli chcesz używać skrótu MD5, skorzystaj z następującego przykładu, aby skonfigurować prywatną komunikację równorzędną dla obwodu:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Upewnij się, można określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 

### <a name="to-view-azure-private-peering-details"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić szczegóły konfiguracji za pomocą następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Zwróć:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet. W poniższym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany ze 100 do 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet. Upewnij się, że wszystkie sieci wirtualne zostały odłączone od obwodu usługi ExpressRoute, przed uruchomieniem tego polecenia cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Publiczna komunikacja równorzędna Azure

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji publicznej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

> [!NOTE]
> Publicznej komunikacji równorzędnej Azure jest przestarzała w przypadku obwodów.
>

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure

1. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie publicznej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby zweryfikować, że jest zainicjowany**

   Musisz najpierw sprawdzić, czy obwód usługi ExpressRoute jest zainicjowany i włączony.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwróć:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Upewnij się, że obwód jest wyświetlany jako Aprowizowana i włączony. Jeśli nie, współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Konfigurowanie publicznej komunikacji równorzędnej Azure dla obwodu**
   
   Upewnij się, że masz poniższe informacje przed kontynuowaniem:
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalnie**.

   > [!IMPORTANT]
   > Upewnij się, określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   >  
     
   Skorzystaj z następującego przykładu, aby skonfigurować publiczną komunikację równorzędną Azure dla obwodu:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Jeśli chcesz używać skrótu MD5, skorzystaj z następującego przykładu, aby skonfigurować obwodu:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure

Aby wyświetlić szczegóły konfiguracji, użyj następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Zwróć:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure

Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Upewnij się, że obwód jest wyświetlany jako Aprowizowana i włączony. 
### <a name="to-delete-azure-public-peering"></a>Aby usunąć publiczną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji komunikacji równorzędnej Microsoft dla obwodu usługi ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**
  
   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby zweryfikować, że jest zainicjowany**

   Upewnij się, że obwód jest wyświetlany jako Aprowizowana i włączony. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwróć:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Upewnij się, że obwód jest wyświetlany jako Aprowizowana i włączony. Jeśli nie, współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Skonfiguruj komunikację równorzędną Microsoft dla obwodu**
   
    Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: Musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * Numer ASN klienta: Jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane. **Opcjonalnie**.
   * Nazwa rejestru routingu: Możesz określić RIR / IRR, względem którego numer AS i prefiksy są.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalnie.**
     
   Uruchom następujące polecenie cmdlet, aby skonfigurować komunikację równorzędną Microsoft dla obwodu:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić szczegóły konfiguracji za pomocą następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Zwróć:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Kolejne kroki

Następnie [połączyć sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Aby uzyskać więcej informacji na temat przepływów pracy, zobacz [przepływy pracy ExpressRoute](expressroute-workflows.md).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
