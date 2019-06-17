---
title: Łączność Konfigurowanie z sieci wirtualnej do platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Łączność ustawiane z sieci wirtualnej, aby używać platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239463"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Łączenie sieci wirtualnej do dużych wystąpień HANA

Po utworzeniu sieci wirtualnej platformy Azure, możesz połączyć tę sieć do platformy SAP HANA na platformie Azure. Utwórz bramę usługi ExpressRoute systemu Azure w sieci wirtualnej. Ta brama umożliwia łączenie sieci wirtualnej z obwodem usługi ExpressRoute, który nawiązuje połączenie z dzierżawy klienta w sygnaturze dużych wystąpień HANA.

> [!NOTE] 
> W tym kroku może potrwać do 30 minut. Nowa brama jest utworzony w wyznaczonym subskrypcji platformy Azure, a następnie jest podłączone do określonej sieci wirtualnej platformy Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Jeśli ta brama już istnieje, sprawdź, czy jest brama usługi ExpressRoute. Jeśli nie jest brama usługi ExpressRoute, usunąć bramę, a następnie utworzyć ją ponownie jako brama usługi ExpressRoute. Jeśli brama usługi ExpressRoute jest już ustanowione, zobacz sekcję następujące części tego artykułu "Łącze sieci wirtualne". 

- Użyj jednej [witryny Azure portal](https://portal.azure.com/) lub programu PowerShell do tworzenia bramy sieci VPN usługi ExpressRoute jest podłączony do sieci wirtualnej.
  - Jeśli używasz witryny Azure portal, Dodaj nowy **bramy sieci wirtualnej**, a następnie wybierz pozycję **ExpressRoute** jako typ bramy.
  - Jeśli używasz programu PowerShell, należy najpierw pobrać i użyć najnowszej wersji [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Następujące polecenia Utwórz bramę usługi ExpressRoute. Teksty poprzedzone _$_ zmiennych zdefiniowanych przez użytkownika, które powinien być zaktualizowany o konkretnych informacji.

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

W tym przykładzie użyto bramy jednostki SKU HighPerformance. Opcje są HighPerformance lub UltraPerformance jako bramę tylko jednostki SKU, które są obsługiwane w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia).

> [!IMPORTANT]
> Dla dużych wystąpień HANA klasy jednostek SKU typu II należy użyć jednostki SKU bramy UltraPerformance.

## <a name="link-virtual-networks"></a>Link sieci wirtualnych

Usługa Azure virtual network ma teraz bramę usługi ExpressRoute. Za pomocą informacji o autoryzacji, obsługiwane przez firmę Microsoft, aby połączyć bramę usługi ExpressRoute z obwodem usługi ExpressRoute wystąpień dużych SAP HANA. Możesz połączyć się przy użyciu witryny Azure portal lub programu PowerShell. Poniżej znajdują się instrukcje dotyczące programu PowerShell. 

Uruchom następujące polecenia dla każdej bramy usługi ExpressRoute za pomocą różnych AuthGUID dla każdego połączenia. Pierwsze dwie pozycje pokazano w poniższym skrypcie pochodzą z informacji podanych przez firmę Microsoft. Ponadto AuthGUID jest specyficzne dla każdej sieci wirtualnej i bramy. Jeśli chcesz dodać kolejną sieć wirtualną platformy Azure, musisz uzyskać inny AuthID obwód usługi ExpressRoute łączący dużych wystąpień HANA na platformie Azure firmy Microsoft. 

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
> Ostatni parametr w poleceniu New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** nowy parametr umożliwiająca ścieżkę Fast usługi ExpressRoute. Funkcja, która zmniejsza opóźnienie sieci między jednostkami dużych wystąpień HANA i maszyn wirtualnych platformy Azure. Funkcje stało się dodane w maja 2019 r. Aby uzyskać więcej informacji, zapoznaj się z tego artykułu [architektura SAP HANA (duże wystąpienia) sieci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Upewnij się, że używasz najnowszej wersji poleceń cmdlet programu PowerShell, przed uruchomieniem polecenia.

Aby połączyć z bramy do więcej niż jeden obwód usługi ExpressRoute skojarzonych z subskrypcją, konieczne może być więcej niż jeden raz uruchom ten krok. Na przykład prawdopodobnie zamierzasz do łączenia z tą samą bramą sieci wirtualnej z obwodem usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnej do sieci lokalnej.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Stosowanie usługi ExpressRoute szybka ścieżka istniejącego HANA duże wystąpienie obwodami usługi ExpressRoute
Do tej pory dokumentacji wyjaśniono, jak połączyć nowy obwód usługi ExpressRoute, która została utworzona przy użyciu dużych wystąpień HANA wdrożenia bramy usługi Azure ExpressRoute w jednej sieci wirtualnej platformy Azure. Jednak wielu klientów już ich konfiguracji obwodów usługi ExpressRoute już i sieciach wirtualnych nawiązano połączenie z dużymi wystąpieniami platformy HANA już. Zgodnie z nowej usługi ExpressRoute szybko ścieżki jest zmniejszenie opóźnienia sieci, zalecane jest, że zastosujesz zmiany, aby używać tej funkcji. Polecenia do łączenia z nowego obwodu ExpreesRoute i zmienić istniejący obwód usługi ExpressRoute są takie same. W rezultacie musisz uruchomić tej sekwencji poleceń programu PowerShell, aby zmienić istniejący obwód do użycia 

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

Jest ważne, dodaj ostatni parametr, wyświetlane powyżej, aby włączyć funkcje usługi ExpressRoute szybka ścieżka


## <a name="expressroute-global-reach"></a>Globalny zasięg usługi ExpressRoute
Jak chcesz włączyć zasięgu globalnym dla jednej lub obu z dwóch scenariuszy:

 - Replikacji systemu HANA bez żadnych dodatkowych serwerów proxy lub zapory
 - Skopiowanie kopii zapasowych między jednostkami duże wystąpienie oprogramowania HANA w dwóch różnych regionach do wykonania kopii systemu lub odświeża systemu

należy rozważyć, które:

- Należy podać zakres przestrzeni adresów / 29 przestrzeń adresowa. Czy zakres adresów nie nakładają ze wszystkimi innymi zakresów przestrzeni adresów używany do tej pory łączenia dużych wystąpień HANA na platformie Azure i nie może nakładać się z żadnym z zakresów adresów IP można używać gdzie indziej na platformie Azure lub lokalnie.
- Brak limitu ASN (numer systemu autonomicznego), których można użyć, aby anonsować lokalne trasy do dużych wystąpień HANA. Lokalne musi anonsuje, wszystkie trasy z prywatnych numerów ASN w zakresie 65000 — 65020 lub 65515. 
- Scenariusz łączenia bezpośredni dostęp do środowiska lokalnego do platformy HANA, duże wystąpienia musisz obliczyć opłaty dla obwodu, łączącej się z platformy Azure. Ceny są dostępne ceny [globalnego dotrzeć do dodatku](https://azure.microsoft.com/pricing/details/expressroute/).

Aby pobrać jeden lub oba scenariusze stosowane do wdrożenia, Otwórz wiadomość pomocy technicznej za pomocą platformy Azure, zgodnie z opisem w [Otwórz żądanie pomocy technicznej dla dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Dane, które są potrzebne i słów kluczowych, które należy użyć dla firmy Microsoft, aby można było kierować do wykonania na żądanie, wygląda następująco:

- Usługa: Duże wystąpienie SAP HANA
- Typ problemu: Konfiguracja i instalacja
- Podtyp problemu: Mojego problemu nie ma na powyższej liście
- Podmiotu "Modyfikowanie sieci — Dodaj zasięgu globalnym"
- Szczegóły: "Dodaj zasięgu globalnym do dużych wystąpień HANA do dużych wystąpień HANA dzierżawy lub" Dodaj zasięgu globalnym do środowiska lokalnego do platformy HANA, duże wystąpienie dzierżawy.
- Dodatkowe szczegóły dla dużych wystąpień HANA, w przypadku dzierżawy dużych wystąpień HANA: Należy zdefiniować **dwóch regionach platformy Azure** gdzie znajdują się dwa dzierżawcom możliwości połączenia **i** należy przesłać   **/29 zakres adresów IP**
- Dodatkowe szczegóły dla środowiska lokalnego do przypadku dzierżawy dużych wystąpień HANA: Należy zdefiniować **region świadczenia usługi Azure** w przypadku, gdy wdrożono dzierżawy dużych wystąpień HANA, którą chcesz połączyć się bezpośrednio. Ponadto konieczne będzie podanie **GUID uwierzytelniania** i **pakietem zawartości Circuit ID elementu równorzędnego** otrzymane podczas ustanawiania obwód usługi ExpressRoute między w środowisku lokalnym i platformą Azure. Ponadto należy nazwać swoje **ASN**. Ostatni dostarczany jest **/29 zakres adresów IP** dla usługi ExpressRoute zasięgu globalnym.

> [!NOTE]
> Jeśli chcesz mieć w obu przypadkach obsługiwane, należy podać dwa różne/29 zakresów adresów IP, które nie nakładają się z innym adresem IP adresów zakres używany do tej pory. 




## <a name="next-steps"></a>Kolejne kroki

- [Wymagania dodatkowe sieci dla HLI](hana-additional-network-requirements.md)
