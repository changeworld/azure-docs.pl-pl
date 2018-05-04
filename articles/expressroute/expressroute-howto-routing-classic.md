---
title: 'Jak skonfigurować obwód (równorzędna) dla usługi routingu: Azure: klasyczny | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 9cebb196bd91da704798fb001763a76e6d090472
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute (klasyczne)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Video - prywatnej komunikacji równorzędnej](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - publicznej komunikacji równorzędnej](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - komunikacji równorzędnej firmy Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

W tym artykule przedstawiono kroki, aby utworzyć i zarządzać konfiguracją routingu dla obwodu usługi ExpressRoute, przy użyciu programu PowerShell i klasycznym modelu wdrażania. W poniższych krokach opisano również, jak sprawdzać stan komunikacji równorzędnej, aktualizować ją, usuwać i wstrzymywać jej obsługę administracyjną dla obwodu usługi ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji
* Konieczne będzie najnowszą wersję poleceń cmdlet programu PowerShell Azure usługi zarządzania (ko). Aby uzyskać więcej informacji, zobacz [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview).  
* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami, aby [utworzyć obwodu usługi ExpressRoute](expressroute-howto-circuit-classic.md) i mieć obwodu włączane przez dostawcą połączenia, aby kontynuować. Obwód usługi ExpressRoute musi być zainicjowany i włączony, aby można było uruchamiać polecenia cmdlet opisane poniżej.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli korzystasz z usług dostawcy oferującego zarządzane usługi warstwy 3 (zwykle IPVPN, np. MPLS), dostawca połączenia skonfiguruje routing i będzie nim zarządzać.
> 
> 

Można skonfigurować jedną komunikację równorzędną, dwie lub trzy (prywatną Azure, publiczną Azure i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Zaloguj się do konta platformy Azure i wybierz subskrypcję
1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

        Connect-AzureRmAccount

2. Sprawdź subskrypcje dostępne na koncie.

        Get-AzureRmSubscription

3. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Następnie użyj następującego polecenia cmdlet można dodać subskrypcji platformy Azure do środowiska PowerShell dla klasycznym modelu wdrażania.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Prywatna komunikacja równorzędna Azure
Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną
1. **Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.**
   
    Aby rozpocząć korzystanie z poleceń cmdlet usługi ExpressRoute, należy zaimportować moduły Azure i usługi ExpressRoute w sesji programu PowerShell. Uruchom następujące polecenia, aby zaimportować moduły Azure i usługi ExpressRoute do sesji programu PowerShell. Wersja może się różnić.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Utworzyć obwodu usługi ExpressRoute.**
   
    Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami. 
3. **Sprawdź obwodu ExpressRoute, aby upewnić się, że jego obsługa została zainicjowana.**
   
    Musisz najpierw sprawdzić, czy obwód usługi ExpressRoute jest zainicjowany i włączony. Zobacz przykład poniżej.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Upewnij się, że obwód jest pokazywana jako obsługiwane administracyjnie i włączone. W przeciwnym razie należy współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Skonfiguruj prywatnej komunikacji równorzędnej platformy Azure dla obwodu.**
   
    Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:
   
   * Podsieć /30 dla połączenia podstawowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Nie może ona być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Jest to opcjonalne**.
     
    Możesz uruchomić następujące polecenie cmdlet, aby skonfigurować prywatną komunikację równorzędną Azure dla obwodu.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Możesz użyć poniższego polecenia cmdlet, jeśli zdecydujesz się używać skrótu MD5.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure
Możesz pobrać szczegóły dotyczące konfiguracji przy użyciu następującego polecenia cmdlet.

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

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


### <a name="to-update-azure-private-peering-configuration"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure
Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet. W poniższym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany ze 100 do 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Aby usunąć prywatną komunikację równorzędną Azure
Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet.

> [!WARNING]
> Przed uruchomieniem tego polecenia cmdlet upewnij się, że wszystkie sieci wirtualne zostały odłączone od obwodu usługi ExpressRoute. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Publiczna komunikacja równorzędna Azure
Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji publicznej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure
1. **Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.**
   
    Aby rozpocząć korzystanie z poleceń cmdlet usługi ExpressRoute, należy zaimportować moduły Azure i usługi ExpressRoute w sesji programu PowerShell. Uruchom następujące polecenia, aby zaimportować moduły Azure i usługi ExpressRoute do sesji programu PowerShell. Wersja może się różnić.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**
   
    Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie publicznej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
3. **Sprawdź obwodu ExpressRoute, aby upewnić się, że jego obsługa została zainicjowana**
   
    Musisz najpierw sprawdzić, czy obwód usługi ExpressRoute jest zainicjowany i włączony. Zobacz przykład poniżej.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Upewnij się, że obwód jest pokazywana jako obsługiwane administracyjnie i włączone. W przeciwnym razie należy współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Skonfiguruj publicznej komunikacji równorzędnej platformy Azure dla obwodu**
   
    Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Jest to opcjonalne**.
     
    Możesz uruchomić następujące polecenie cmdlet, aby skonfigurować publiczną komunikację równorzędną Azure dla obwodu.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Możesz użyć poniższego polecenia cmdlet, jeśli zdecydujesz się używać skrótu MD5.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure
Możesz pobrać szczegóły dotyczące konfiguracji przy użyciu następującego polecenia cmdlet.

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

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


### <a name="to-update-azure-public-peering-configuration"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure
Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet.

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

W przykładzie powyżej identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

### <a name="to-delete-azure-public-peering"></a>Aby usunąć publiczną komunikację równorzędną Azure
Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet.

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft
Ta sekcja zawiera instrukcje dotyczące tworzenia, pobierania, aktualizowania i usuwania konfiguracji komunikacji równorzędnej Microsoft dla obwodu usługi ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft
1. **Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.**
   
    Aby rozpocząć korzystanie z poleceń cmdlet usługi ExpressRoute, należy zaimportować moduły Azure i usługi ExpressRoute w sesji programu PowerShell. Uruchom następujące polecenia, aby zaimportować moduły Azure i usługi ExpressRoute do sesji programu PowerShell. Wersja może się różnić.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)**
   
    Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-classic.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić go o włączenie prywatnej komunikacji równorzędnej Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu postępuj zgodnie z poniższymi instrukcjami.
3. **Sprawdź obwodu ExpressRoute, aby upewnić się, że jego obsługa została zainicjowana**
   
    Musisz najpierw sprawdzić, czy obwodu ExpressRoute jest w stanie obsługiwane administracyjnie i włączone.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Upewnij się, że obwód jest pokazywana jako obsługiwane administracyjnie i włączone. W przeciwnym razie należy współpracować z dostawcą połączenia, aby uzyskać wymagany stan i Stan obwodu.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurowanie komunikacji równorzędnej dla obwodu firmy Microsoft**
   
    Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.
   
   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę oddzielaną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * Numer ASN klienta: jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane. **Jest to opcjonalne**.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * Skrót MD5, jeśli zdecydujesz się go użyć. **Jest to opcjonalne.**
     
    Można uruchomić następujące polecenie cmdlet, aby skonfigurować pering firmy Microsoft dla obwodu
     
        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft
Możesz pobrać szczegóły dotyczące konfiguracji przy użyciu następującego polecenia cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

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


### <a name="to-update-microsoft-peering-configuration"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft
Możesz zaktualizować dowolną część konfiguracji za pomocą następującego polecenia cmdlet.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Aby usunąć komunikację równorzędną firmy Microsoft
Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Kolejne kroki
Następnie [połączyć sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Aby uzyskać więcej informacji o przepływach pracy, zobacz [przepływy pracy usługi ExpressRoute](expressroute-workflows.md).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).

