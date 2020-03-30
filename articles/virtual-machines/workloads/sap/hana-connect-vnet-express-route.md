---
title: Konfiguracja łączności z sieci wirtualnej do SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Łączność skonfigurowana z sieci wirtualnej do korzystania z sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617201"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Łączenie sieci wirtualnej z dużymi wystąpieniami HANA

Po utworzeniu sieci wirtualnej platformy Azure można połączyć tę sieć z sap HANA na platformie Azure dużych wystąpień. Utwórz bramę usługi Azure ExpressRoute w sieci wirtualnej. Ta brama umożliwia łączenie sieci wirtualnej z obwodem usługi ExpressRoute, który łączy się z dzierżawą klienta na znaczeku dużego wystąpienia HANA.

> [!NOTE] 
> Ten krok może potrwać do 30 minut. Nowa brama jest tworzona w wyznaczonej subskrypcji platformy Azure, a następnie połączona z określoną siecią wirtualną platformy Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Jeśli brama już istnieje, sprawdź, czy jest bramą usługi ExpressRoute, czy nie. Jeśli nie jest bramą usługi ExpressRoute, usuń bramę i ponownie utwórz ją jako bramę usługi ExpressRoute. Jeśli brama usługi ExpressRoute została już ustanowiona, zobacz następującą sekcję tego artykułu "Połącz sieci wirtualne". 

- Użyj [witryny Azure portal](https://portal.azure.com/) lub programu PowerShell, aby utworzyć bramę sieci VPN usługi ExpressRoute połączoną z siecią wirtualną.
  - Jeśli używasz portalu Azure, dodaj nową **bramę sieci wirtualnej**, a następnie wybierz **expressroute** jako typ bramy.
  - Jeśli używasz programu PowerShell, najpierw pobierz i użyj najnowszego [sdk programu Azure PowerShell.](https://azure.microsoft.com/downloads/) 
 
Poniższe polecenia tworzą bramę usługi ExpressRoute. Teksty poprzedzone _$_ są zmiennymi zdefiniowanymi przez użytkownika, które powinny być aktualizowane o konkretne informacje.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

W tym przykładzie użyto jednostki SKU bramy Wysokiej Wydajności. Opcje są HighPerformance lub UltraPerformance jako tylko jednostek SKU bramy, które są obsługiwane dla SAP HANA na platformie Azure (duże wystąpienia).

> [!IMPORTANT]
> W przypadku dużych wystąpień jednostki SKU klasy typu II należy użyć jednostki SKU bramy UltraPerformance.

## <a name="link-virtual-networks"></a>Łączenie sieci wirtualnych

Sieć wirtualna platformy Azure ma teraz bramę usługi ExpressRoute. Użyj informacji o autoryzacji dostarczonych przez firmę Microsoft, aby połączyć bramę usługi ExpressRoute z obwódem usługi Sap HANA Large Instances ExpressRoute. Można połączyć za pomocą witryny Azure portal lub programu PowerShell. Instrukcje programu PowerShell są następujące. 

Uruchom następujące polecenia dla każdej bramy usługi ExpressRoute przy użyciu innego AuthGUID dla każdego połączenia. Pierwsze dwa wpisy pokazane w poniższym skrypcie pochodzą z informacji dostarczonych przez firmę Microsoft. Ponadto AuthGUID jest specyficzne dla każdej sieci wirtualnej i jej bramy. Jeśli chcesz dodać inną sieć wirtualną platformy Azure, musisz uzyskać inny AuthID dla obwodu usługi ExpressRoute, który łączy duże wystąpienia HANA z platformą Azure od firmy Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Ostatni parametr w poleceniu New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** jest nowym parametrem, który umożliwia szybką ścieżkę usługi ExpressRoute. Funkcja zmniejszająca opóźnienia sieci między jednostkami dużych wystąpień HANA a maszynami wirtualnymi platformy Azure. Funkcjonalność została dodana w maju 2019. Aby uzyskać więcej informacji, zapoznaj się z artykułem [SAP HANA (Duże wystąpienia) architektura sieci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Przed uruchomieniem poleceń upewnij się, że używasz najnowszej wersji poleceń cmdlet programu PowerShell.

Aby połączyć bramę z więcej niż jednym obwodem usługi ExpressRoute skojarzonym z subskrypcją, może być konieczne uruchomienie tego kroku więcej niż jeden raz. Na przykład prawdopodobnie połączysz tę samą bramę sieci wirtualnej z obwodem usługi ExpressRoute, który łączy sieć wirtualną z siecią lokalną.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Stosowanie szybkiej ścieżki usługi ExpressRoute do istniejących obwodów usługi ExpressRoute dużych wystąpień HANA
W dokumentacji do tej pory wyjaśniono, jak połączyć nowy obwód usługi ExpressRoute, który został utworzony przy wdrażaniu dużego wystąpienia HANA do bramy usługi Azure ExpressRoute jednej z sieci wirtualnych platformy Azure. Ale wielu klientów ma już konfigurację obwodów usługi ExpressRoute i ich sieci wirtualne są już podłączone do dużych wystąpień HANA. Ponieważ nowa szybka ścieżka usługi ExpressRoute zmniejsza opóźnienia w sieci, zaleca się zastosowanie zmiany w celu korzystania z tej funkcji. Polecenia, aby podłączyć nowy obwód ExpreesRoute i zmienić istniejący obwód usługi ExpressRoute są takie same. W związku z tym należy uruchomić tę sekwencję poleceń programu PowerShell, aby zmienić istniejący obwód do użycia 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Ważne jest, aby dodać ostatni parametr wyświetlany powyżej, aby włączyć funkcję Szybkiej ścieżki usługi ExpressRoute


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Jak chcesz włączyć globalny zasięg dla jednego lub obu scenariuszy:

 - Replikacja systemu HANA bez żadnych dodatkowych serwerów proxy lub zapór
 - Kopiowanie kopii zapasowych między jednostkami dużych wystąpień HANA w dwóch różnych regionach w celu wykonywania kopii systemowych lub odświeżania systemu

należy wziąć pod uwagę, że:

- Należy podać zakres przestrzeni adresowej /29 przestrzeni adresowej. Ten zakres adresów nie może pokrywać się z żadnym z innych zakresów przestrzeni adresowej, które były używane do tej pory podczas łączenia dużych wystąpień HANA na platformie Azure i nie mogą pokrywać się z żadnymi zakresami adresów IP używanymi w innym miejscu na platformie Azure lub lokalnie.
- Istnieje ograniczenie dotyczące sieci ASN (numer systemu autonomicznego), które mogą służyć do reklamowania lokalnych tras do dużych wystąpień HANA. Lokalnie nie wolno reklamować żadnych tras z prywatnymi sieciami ASN w zakresie 65000 – 65020 lub 65515. 
- W scenariuszu łączenia lokalnego bezpośredniego dostępu do wystąpień HANA Large należy obliczyć opłatę za obwód, który łączy cię z platformą Azure. W przypadku cen sprawdź ceny dodatku [Global Reach](https://azure.microsoft.com/pricing/details/expressroute/).

Aby uzyskać jeden lub oba scenariusze zastosowane do wdrożenia, otwórz komunikat pomocy technicznej z platformą Azure zgodnie z opisem w [otwórz żądanie pomocy technicznej dla dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Dane, które są potrzebne i słowa kluczowe, których należy użyć, aby firma Microsoft mogła kierować i wykonywać na żądanie, wygląda następująco:

- Usługa: SAP HANA Duże wystąpienie
- Typ problemu: Konfiguracja i konfiguracja
- Podtyp problemu: Mój problem nie jest wymieniony powyżej
- Temat "Zmodyfikuj moją sieć - dodaj globalny zasięg"
- Szczegóły: "Dodaj globalny zasięg do wystąpienia dużego HANA do dzierżawy dużych wystąpień HANA lub "Dodaj globalny zasięg do lokalnego do dzierżawy dużych wystąpień HANA.
- Dodatkowe szczegóły dotyczące przypadku dzierżawy dużych wystąpień HANA do lokalizacji dużych wystąpień HANA: należy zdefiniować **dwa regiony platformy Azure,** w których znajdują się dwie dzierżawy do nawiązania połączenia **i** musisz przesłać **zakres adresów IP /29**
- Dodatkowe szczegóły dotyczące lokalnego przypadku dzierżawy wystąpienia dużych hana: należy zdefiniować **region platformy Azure,** w którym dzierżawa dużych wystąpień HANA jest wdrażana bezpośrednio. Ponadto należy podać **identyfikator guid auth** i **identyfikator równorzędny obwodu,** który został odebrany podczas ustanawiania obwodu usługi ExpressRoute między środowiskiem lokalnym a platformą Azure. Ponadto należy nazwać nazwę **ASN**. Ostatnim rezultatem jest **zakres adresów IP /29** dla globalnego zasięgu usługi ExpressRoute.

> [!NOTE]
> Jeśli chcesz mieć obie sprawy obsługiwane, należy podać dwa różne /29 zakresy adresów IP, które nie pokrywają się z innych zakres adresów IP używane do tej pory. 




## <a name="next-steps"></a>Następne kroki

- [Dodatkowe wymagania sieciowe dla HLI](hana-additional-network-requirements.md)
