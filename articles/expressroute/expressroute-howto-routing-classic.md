---
title: 'Azure ExpressRoute: Konfigurowanie komunikacji równorzędnej: klasyczne'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 05602538f206032d924b39a7dd8f4325c48a5224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931372"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute (klasyczny)
> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Wideo - Prywatne peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo - Komunikacja publiczna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

W tym artykule otwór przechodzi przez kroki tworzenia i zarządzania konfiguracją komunikacji równorzędnej/routingu dla obwodu usługi ExpressRoute przy użyciu programu PowerShell i klasycznego modelu wdrażania. W poniższych krokach opisano również, jak sprawdzać stan komunikacji równorzędnej, aktualizować ją, usuwać i wstrzymywać jej obsługę administracyjną dla obwodu usługi ExpressRoute. Można skonfigurować jeden, dwa lub wszystkie trzy komunikacji równorzędnej (azure private, Azure public, i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. 

Te instrukcje dotyczą tylko obwodów utworzonych u dostawców usług, którzy oferują usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje zarządzane usługi warstwy 3 (zazwyczaj ipvpn, takie jak MPLS), dostawca łączności skonfiguruje routing i zarządza nim.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md) i mieć włączony obwód przez dostawcę łączności przed kontynuowaniem. Obwód usługi ExpressRoute musi być zainicjowany i włączony, aby można było uruchamiać polecenia cmdlet opisane poniżej.

### <a name="download-the-latest-powershell-cmdlets"></a>Pobieranie najnowszych poleceń cmdlet programu PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Prywatna komunikacja równorzędna Azure

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. **Utwórz obwód usługi ExpressRoute.**

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby upewnić się, że jest aprowidyzowana.**
   
   Sprawdź, czy obwód usługi ExpressRoute jest aprowizowana, a także włączone.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwracają:

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
   
   Upewnij się, że obwód jest wyświetlany jako Aprowizowana i Włączona. Jeśli tak nie jest, skontaktuj się z dostawcą łączności, aby uzyskać wymagany stan i stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu.**

   Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:
   
   * Podsieć /30 dla połączenia podstawowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, czy żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Sprawdź, czy nie używasz 65515.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalnie**.
     
   Aby skonfigurować prywatną komunikację równorzędnych platformy Azure dla obwodu, można użyć następującego przykładu:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Jeśli chcesz użyć skrótu MD5, użyj następującego przykładu, aby skonfigurować prywatną komunikację równorzędną dla obwodu:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Sprawdź, czy numer AS został określony jako numer ASN komunikacji równorzędnej, a nie asn klienta.
   > 

### <a name="to-view-azure-private-peering-details"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Szczegóły konfiguracji można wyświetlić przy użyciu następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Zwracają:

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

Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet. W poniższym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany od 100 do 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet. Przed uruchomieniem tego polecenia cmdlet należy upewnić się, że wszystkie sieci wirtualne są odłączone od obwodu usługi ExpressRoute.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Publiczna komunikacja równorzędna Azure

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji publicznej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

> [!NOTE]
> Publiczne komunikacji równorzędnej platformy Azure jest przestarzałe dla nowych obwodów.
>

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure

1. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie publicznej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby sprawdzić, czy jest aprowidyfikowany**

   Musisz najpierw sprawdzić, czy obwód usługi ExpressRoute jest zainicjowany i włączony.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwracają:

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
   
   Sprawdź, czy obwód jest wyświetlany jako Aprowizowana i Włączona. Jeśli tak nie jest, skontaktuj się z dostawcą łączności, aby uzyskać wymagany stan i stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Konfigurowanie publicznej komunikacji równorzędnej platformy Azure dla obwodu**
   
   Przed kontynuowaniem upewnij się, że masz następujące informacje:
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, czy żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalnie**.

   > [!IMPORTANT]
   > Upewnij się, że określasz swój numer AS jako peering ASN, a nie numer ASN klienta.
   >  
     
   Poniższy przykład służy do konfigurowania publicznej komunikacji równorzędnej platformy Azure dla obwodu:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Jeśli chcesz użyć skrótu MD5, użyj następującego przykładu, aby skonfigurować obwód:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure

Aby wyświetlić szczegóły konfiguracji, użyj następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Zwracają:

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

Sprawdź, czy obwód jest wyświetlany jako Aprowizowana i Włączona. 
### <a name="to-delete-azure-public-peering"></a>Aby usunąć publiczną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji komunikacji równorzędnej Microsoft dla obwodu usługi ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**
  
   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
2. **Sprawdź obwód usługi ExpressRoute, aby sprawdzić, czy jest aprowidyfikowany**

   Sprawdź, czy obwód jest wyświetlany jako Aprowizowana i Włączona. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Zwracają:
   
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
   
   Sprawdź, czy obwód jest wyświetlany jako Aprowizowana i Włączona. Jeśli tak nie jest, skontaktuj się z dostawcą łączności, aby uzyskać wymagany stan i stan obwodu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurowanie komunikacji równorzędnej firmy Microsoft dla obwodu**
   
    Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Sprawdź, czy żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłać zestaw prefiksów, można wysłać listę oddzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * Numer ASN klienta: jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane. **Opcjonalnie**.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Opcjonalne.**
     
   Uruchom następujące polecenie cmdlet, aby skonfigurować komunikację równorzędną firmy Microsoft dla obwodu:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Szczegóły konfiguracji można wyświetlić przy użyciu następującego polecenia cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Zwracają:

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

Dowolną część konfiguracji można zaktualizować za pomocą następującego polecenia cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Następne kroki

Następnie [połącz z siecią wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Aby uzyskać więcej informacji o przepływach pracy, zobacz [Przepływy pracy usługi ExpressRoute](expressroute-workflows.md).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
