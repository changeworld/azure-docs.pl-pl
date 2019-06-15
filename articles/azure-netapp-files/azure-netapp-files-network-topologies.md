---
title: Wskazówki dotyczące usługi Azure Files NetApp sieci, planowanie | Dokumentacja firmy Microsoft
description: W tym artykule opisano wskazówki, które mogą pomóc Ci projektowania architektury sieci skuteczne przy użyciu usługi Azure Files NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826445"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Wytyczne dotyczące planowania sieci usługi Azure NetApp Files

Planowanie architektury sieci jest kluczowym elementem projektowania infrastruktury aplikacji. Ten artykuł ułatwia projektowanie architektury skuteczne sieci dla swoich obciążeń korzystać z zaawansowanych funkcji usługi Azure Files NetApp.

Azure plików NetApp woluminy są przeznaczone do być zawarty w podsieci specjalnego przeznaczenia [delegowane podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) w usłudze Azure Virtual Network. W związku z tym są dostępne woluminy bezpośrednio z sieci wirtualnej, wirtualne sieci równorzędne w tym samym regionie lub w środowisku lokalnym za pośrednictwem bramy sieci wirtualnej (ExpressRoute i VPN Gateway) zgodnie z potrzebami. Podsieć jest przeznaczone do usługi Azure Files NetApp, i nie ma łączności z innymi usługami platformy Azure lub z Internetem.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Należy zrozumieć kilka istotnych kwestii, podczas planowania związanego z plików NetApp platformy Azure w sieci.

### <a name="constraints"></a>Ograniczenia

Poniższe funkcje są obecnie obsługiwane dla usługi Azure Files NetApp: 

* Sieciowe grupy zabezpieczeń (NSG) w podsieci
* Trasy zdefiniowane przez użytkownika (Udr) z następnym etapem jako podsieć plików NetApp platformy Azure
* Zasady usługi Azure (na przykład niestandardowe zasady nazewnictwa) w interfejsie usługi Azure Files NetApp
* Moduły równoważenia obciążenia dla ruchu usługi Azure Files NetApp

Następujące ograniczenia sieciowe dotyczące usługi Azure Files NetApp:

* Liczba maszyn wirtualnych, które można nawiązać połączenia z woluminów (z sieci wirtualnej lub w wirtualnych sieciach równorzędnych) nie może przekraczać 1000.
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.


### <a name="supported-network-topologies"></a>Obsługiwane topologie sieci

W poniższej tabeli opisano topologie sieci, obsługiwane przez usługi Azure Files NetApp.  Omówiono także obejścia dla nieobsługiwanego topologii. 

|    Topologie    |    jest obsługiwany    |     Obejście    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Łączność z woluminu w lokalnej sieci wirtualnej    |    Yes    |         |
|    Łączność z woluminu w skomunikowanej równorzędnie sieci wirtualnej (tym samym regionie)    |    Tak    |         |
|    Łączność z woluminu w wirtualnych sieciach równorzędnych (Cross regionu lub globalnej komunikacji równorzędnej)    |    Nie    |    Brak    |
|    Łączność z woluminem przez bramę usługi ExpressRoute    |    Yes    |         |
|    Łączność ze środowiska lokalnego do woluminu w szprysze sieci wirtualnej przez bramę usługi ExpressRoute i wirtualną sieć równorzędną przy użyciu tranzyt przez bramę    |    Nie    |    Utwórz podsieć delegowanego w piastą (sieć wirtualną platformy Azure za pomocą bramy)    |
|    Łączność ze środowiska lokalnego do woluminu w szprysze sieci wirtualnej za pośrednictwem bramy sieci VPN    |    Tak    |         |
|    Łączność ze środowiska lokalnego do woluminu w szprysze sieci wirtualnej za pośrednictwem bramy sieci VPN i wirtualną sieć równorzędną przy użyciu tranzyt przez bramę    |    Tak    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Sieć wirtualna dla usługi Azure Files NetApp woluminów

W tej sekcji opisano pojęcia, które pomagają w planowaniu sieci wirtualnej.

### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Przed zainicjowaniem obsługi administracyjnej woluminie plików NetApp platformy Azure, musisz utworzyć sieć wirtualną platformy Azure (VNet) lub użyj jednego z nich, która już istnieje w Twojej subskrypcji. Sieć wirtualna definiuje granicę sieci woluminu.  Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentacja usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Podsieci

Podsieci segmentu sieci wirtualnej do oddzielnych przestrzeni adresowych, które są może być używany przez zasoby platformy Azure w nich.  Azure woluminy plików NetApp znajdują się w podsieci specjalnych [delegowane podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Delegowanie podsieci umożliwia jawne uprawnienia do usługi Azure NetApp Files, aby utworzyć zasoby specyficzne dla usługi w podsieci.  Używa ona Unikatowy identyfikator we wdrażaniu usługi. W tym przypadku interfejs sieciowy jest tworzony, aby umożliwić łączność z usługą Azure Files NetApp.

Korzystając z nowej sieci wirtualnej, można utworzyć podsieć i delegować podsieci do usługi Azure Files NetApp, postępując zgodnie z instrukcjami opisanymi w [delegować podsieci do usługi Azure Files NetApp](azure-netapp-files-delegate-subnet.md). Możesz również delegować istniejącą pustą podsieć, która nie jest jeszcze delegowana do innych usług.

Jeśli sieć wirtualna a jest połączona z inną siecią wirtualną, nie można rozszerzyć przestrzeni adresowej sieci wirtualnej. Z tego powodu nowej podsieci delegowanego musi zostać utworzona w ramach przestrzeni adresowej sieci wirtualnej. Jeśli chcesz rozszerzyć przestrzeń adresową, należy usunąć równorzędnej przed zwiększeniem rozmiaru przestrzeni adresowej.

### <a name="udrs-and-nsgs"></a>Tras zdefiniowanych przez użytkownika i sieciowymi grupami zabezpieczeń

Sieciowe grupy zabezpieczeń (NSG) z miejsca docelowego następnego skoku nie może służyć jako delegowanego podsieci dla usługi Azure Files NetApp. Podobnie trasy zdefiniowane przez użytkownika (Udr) również nie są obsługiwane. 

Jako obejście można zastosować sieciowe grupy zabezpieczeń do innych podsieci, które zezwalają na ruch do i z podsieci usługi Azure Files NetApp delegowany albo zezwolić na.  

## <a name="azure-native-environments"></a>Środowiska natywnego platformy Azure

Na poniższym diagramie przedstawiono środowiska macierzystego platformy Azure:

![Azure — natywne środowisko sieciowe](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

Podstawowy scenariusz jest tworzenie lub połączenie do usługi Azure Files NetApp woluminu z maszyny wirtualnej (VM) w tej samej sieci wirtualnej. Dla sieci wirtualnej 2 w powyższym diagramie 1 woluminu jest tworzony w podsieci delegowanego i mogą być instalowane na 1 maszyny Wirtualnej w podsieci domyślne.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Jeśli masz dodatkowe sieci wirtualne w tym samym regionie, którzy potrzebują dostępu do zasobów siebie nawzajem, sieci wirtualne mogą być połączone za pomocą [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) bezpieczną łączność za pośrednictwem infrastruktury platformy Azure. 

Na powyższym diagramie, należy rozważyć sieci wirtualnej 2 i 3 sieci wirtualnej. Jeśli 1 maszyna wirtualna musi połączyć się z 2 maszyn wirtualnych i 2 woluminu lub maszyny Wirtualnej 2 musi połączyć się woluminu 1 lub 1 maszyny Wirtualnej, należy włączyć wirtualną sieć równorzędną między siecią wirtualną 2 i 3 sieci wirtualnej. 

Ponadto należy wziąć pod uwagę scenariusz, w którym 1 sieć wirtualna a jest połączona z siecią wirtualną 2 i 2 w sieci wirtualnej jest połączona z siecią wirtualną 3 w tym samym regionie. Zasoby z zakresu od 1 do sieci wirtualnej mogą łączyć się z zasobami w sieci wirtualnej 2, ale nie można połączyć z zasobami w sieci wirtualnej 3, chyba że skomunikowane równorzędnie sieci wirtualnej 1 i 3 sieci wirtualnej. 

Na powyższym diagramie, mimo że 3 maszyny Wirtualnej mogą łączyć się woluminie 1 z 4 maszyn wirtualnych nie można nawiązać 2 woluminu.  Przyczyną jest to, że nie równorzędne sieci wirtualne będące szprychami, a _routing tranzytowy nie jest obsługiwany za pośrednictwem wirtualnej sieci równorzędnej_.

## <a name="hybrid-environments"></a>Środowiska hybrydowe

Na poniższym diagramie przedstawiono środowisko hybrydowe: 

![Hybrydowe środowisko sieciowe](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

W scenariuszu hybrydowym aplikacje z lokalnych centrów danych muszą mieć dostęp do zasobów na platformie Azure.  Jest to, czy chcesz rozszerzyć centrum danych na platformie Azure, którego chcesz użyć usługi natywnego platformy Azure lub na potrzeby odzyskiwania po awarii. Zobacz [bramy sieci VPN, opcje planowania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) o tym, jak wiele zasobów lokalnych łączenie się z zasobami na platformie Azure za pośrednictwem sieci VPN lokacja lokacja lub ExpressRoute.

W topologii piasty i szprych hybrydowego piastą platformy Azure działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieciami wirtualnymi jest połączona z Centrum i mogą być używane do izolowania obciążeń.

W zależności od konfiguracji. Możesz połączyć zasoby ze środowiska lokalnego do zasobów w Centrum i szprychy.

W topologii przedstawionym powyżej sieci lokalnej jest podłączone do koncentratora sieci wirtualnej na platformie Azure, a istnieją 2-satelity sieci wirtualne skomunikowane równorzędnie za pomocą sieci wirtualnej serwera centralnego.  W tym scenariuszu Opcje łączności, obsługiwane w przypadku usługi Azure Files NetApp woluminy są następujące:

* W środowisku lokalnym zasoby maszyny Wirtualnej 1 i 2 maszyn wirtualnych mogą łączyć się na woluminie 1 w Centrum za pośrednictwem sieci VPN lokacja lokacja lub ExpressRoute. 
* Zasobów lokalnych maszyn wirtualnych 1 i 2 maszyny Wirtualnej można nawiązać woluminu 2 lub 3 woluminu.
* 3 maszyny Wirtualnej w piaście sieci wirtualnej można nawiązać woluminie 2 w szprysze 1 sieć wirtualną i 3 woluminu w szprysze sieci wirtualnej 2.
* 4 maszyny Wirtualnej ze szprychy 1 sieci wirtualnej i 5 maszyn wirtualnych ze szprychy 2 w sieci wirtualnej mogą łączyć się na woluminie 1 w sieci wirtualnej serwera centralnego.

4 maszyny Wirtualnej w szprysze 1 sieć wirtualna nie może nawiązać 3 woluminu w szprysze sieci wirtualnej 2. Ponadto 5 maszyn wirtualnych w szprysze VNet2 nie można nawiązać połączenia 2 woluminu w szprysze 1 sieci wirtualnej. Dotyczy to sytuacji, ponieważ nie równorzędne sieci wirtualne będące szprychami i _routing tranzytowy nie jest obsługiwany za pośrednictwem wirtualnej sieci równorzędnej_.

## <a name="next-steps"></a>Kolejne kroki

[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
