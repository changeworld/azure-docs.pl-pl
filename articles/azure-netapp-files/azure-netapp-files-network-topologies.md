---
title: Wskazówki dotyczące planowania sieci usługi Azure NetApp Files | Dokumenty firmy Microsoft
description: W tym artykule opisano wskazówki, które mogą pomóc w projektowaniu efektywnej architektury sieci przy użyciu plików NetApp platformy Azure.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
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
ms.openlocfilehash: 12be766f36a0901079a5a26f20ea7dacc75268de
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667876"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Wytyczne dotyczące planowania sieci usługi Azure NetApp Files

Planowanie architektury sieci jest kluczowym elementem projektowania dowolnej infrastruktury aplikacji. Ten artykuł ułatwia projektowanie efektywnej architektury sieci dla obciążeń, aby korzystać z rozbudowanych możliwości plików NetApp platformy Azure.

Woluminy usługi Azure NetApp Files są przeznaczone do umieszczenia w podsieci specjalnego przeznaczenia o nazwie [delegowanej podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) w sieci wirtualnej platformy Azure. W związku z tym można uzyskać dostęp do woluminów bezpośrednio z sieci wirtualnej, z równorzędnych sieci wirtualnych w tym samym regionie lub z lokalnego za pośrednictwem bramy sieci wirtualnej (usługi ExpressRoute lub bramy sieci VPN), jeśli to konieczne. Podsieć jest dedykowana do plików NetApp platformy Azure i nie ma łączności z innymi usługami platformy Azure lub Internetem.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Należy zrozumieć kilka zagadnień podczas planowania dla sieci Azure NetApp Files.

### <a name="constraints"></a>Ograniczenia

Poniższe funkcje są obecnie nieobsługiwały dla usługi Azure NetApp Files: 

* Sieciowe grupy zabezpieczeń (NSG) stosowane do delegowanej podsieci
* Trasy zdefiniowane przez użytkownika (UDR) z prefiksem adresu jako podsieci plików NetApp platformy Azure
* Zasady platformy Azure (na przykład niestandardowe zasady nazewnictwa) w interfejsie plików NetApp platformy Azure
* Moduły równoważenia obciążenia dla ruchu plików NetApp platformy Azure
* Usługa Azure NetApp Files nie jest obsługiwana w wirtualnej sieci WAN platformy Azure

Następujące ograniczenia sieciowe dotyczą plików NetApp platformy Azure:

* Liczba obiektów IP używanych w sieci wirtualnej z plikami NetApp platformy Azure (w tym równorzędnych sieci wirtualnych) nie może przekraczać 1000. Pracujemy nad zwiększeniem tego limitu, aby sprostać wymaganiom klientów. 
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.


### <a name="supported-network-topologies"></a>Obsługiwane topologie sieci

W poniższej tabeli opisano topologie sieci obsługiwane przez usługi Azure NetApp Files.  Opisano w nim również obejścia nieobsługiwionych topologii. 

|    Topologie    |    Jest obsługiwany    |     Obejście    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Łączność z woluminem w lokalnej sieci wirtualnej    |    Tak    |         |
|    Łączność z woluminem w sieci wirtualnej równorzędnej (ten sam region)    |    Tak    |         |
|    Łączność z woluminem w równorzędnej sieci wirtualnej (region krzyżowy lub globalna komunikacja równorzędna)    |    Nie    |    Brak    |
|    Łączność z woluminem za 2000 usługi ExpressRoute    |    Tak    |         |
|    Łączność z lokalnego do woluminu w sieci wirtualnej szprychy za pomocą bramy usługi ExpressRoute i komunikacji równorzędnej sieci wirtualnej z przesyłaniem bramy    |    Tak    |        |
|    Łączność z lokalnego do woluminu w sieci wirtualnej szprychy za pośrednictwem bramy sieci VPN    |    Tak    |         |
|    Łączność z lokalnego do woluminu w sieci wirtualnej szprychy za pośrednictwem bramy sieci VPN i komunikacji równorzędnej sieci wirtualnej z przesyłaniem bramy    |    Tak    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Sieć wirtualna dla woluminów plików NetApp platformy Azure

W tej sekcji opisano pojęcia, które pomagają w planowaniu sieci wirtualnej.

### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Przed zainicjowaniem obsługi administracyjnej woluminu plików NetApp platformy Azure należy utworzyć sieć wirtualną platformy Azure lub użyć sieci wirtualnej, która już istnieje w ramach subskrypcji. Sieć wirtualna definiuje granicę sieci woluminu.  Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentację sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Podsieci

Podsieci segment sieci wirtualnej do oddzielnych przestrzeni adresowych, które są użyteczne przez zasoby platformy Azure w nich.  Woluminy usługi Azure NetApp Files znajdują się w podsieci specjalnego przeznaczenia zwanej [podsiecią delegowanej](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Delegowanie podsieci daje jawne uprawnienia do usługi Azure NetApp Files do tworzenia zasobów specyficznych dla usługi w podsieci.  Używa unikatowego identyfikatora podczas wdrażania usługi. W takim przypadku tworzony jest interfejs sieciowy, aby umożliwić łączność z plikami NetApp platformy Azure.

Jeśli używasz nowej sieci wirtualnej, możesz utworzyć podsieć i delegować podsieć do usługi Azure NetApp Files, postępując zgodnie z instrukcjami w [aplikacji Delegowanie podsieci do plików NetApp platformy Azure.](azure-netapp-files-delegate-subnet.md) Można również delegować istniejącą pustą podsieć, która nie jest jeszcze delegowana do innych usług.

Jeśli sieć wirtualna jest równorzędna z inną siecią wirtualną, nie można rozwinąć przestrzeni adresowej sieci wirtualnej. Z tego powodu nowa podsieć delegowane musi zostać utworzona w przestrzeni adresowej sieci wirtualnej. Jeśli chcesz rozszerzyć przestrzeń adresową, należy usunąć komunikację równorzędnych sieci wirtualnej przed rozwinięciem przestrzeni adresowej.

### <a name="udrs-and-nsgs"></a>UDR i NSG

Trasy zdefiniowane przez użytkownika (UDR) i sieciowe grupy zabezpieczeń (NSG) nie są obsługiwane w podsieciach delegowanych dla plików NetApp Usługi Azure.

Aby obejść ten problem, można zastosować sieciowe sieciowe do innych podsieci, które zezwalają lub odmawiają ruchu do i z podsieci delegowanej usługi Azure NetApp Files.  

## <a name="azure-native-environments"></a>Środowiska macierzyste platformy Azure

Na poniższym diagramie przedstawiono środowisko natywne platformy Azure:

![Środowisko sieci natywne platformy Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokalna sieć wirtualna

Podstawowym scenariuszem jest utworzenie lub połączenie z woluminem usługi Azure NetApp Files z maszyny wirtualnej (VM) w tej samej sieci wirtualnej. Dla sieci wirtualnej 2 na powyższym diagramie wolumin 1 jest tworzony w podsieci delegowanej i może być zainstalowany na maszynie Wirtualnej 1 w domyślnej podsieci.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Jeśli masz dodatkowe sieci wirtualne w tym samym regionie, które wymagają dostępu do zasobów innych, sieci wirtualne mogą być połączone przy użyciu [komunikacji równorzędnej sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) aby włączyć bezpieczną łączność za pośrednictwem infrastruktury platformy Azure. 

Należy wziąć pod uwagę sieci wirtualnej 2 i sieci wirtualnej 3 na powyższym diagramie. Jeśli maszyna wirtualna 1 musi połączyć się z maszyną wirtualną 2 lub woluminem 2 lub jeśli maszyna wirtualna 2 musi połączyć się z maszyną wirtualną 1 lub woluminem 1, należy włączyć komunikację równorzędnych sieci wirtualnej między siecią wirtualną 2 i siecią wirtualną 3. 

Ponadto należy wziąć pod uwagę scenariusz, w którym sieć wirtualna 1 jest równorzędna z siecią wirtualną 2, a sieć wirtualna 2 jest równorzędna z siecią wirtualną 3 w tym samym regionie. Zasoby z sieci wirtualnej 1 można połączyć się z zasobami w sieci wirtualnej 2, ale nie można połączyć się z zasobami w sieci wirtualnej 3, chyba że sieć wirtualna 1 i sieć wirtualna 3 są równorzędne. 

Na powyższym diagramie, chociaż maszyna wirtualna 3 może łączyć się z woluminem 1, maszyna wirtualna 4 nie może połączyć się z woluminem 2.  Powodem tego jest to, że szprychowe sieci wirtualne nie są równorzędne, a _routing tranzytowy nie jest obsługiwany przez komunikację równorzędnie sieci wirtualnej_.

## <a name="hybrid-environments"></a>Środowiska hybrydowe

Na poniższym diagramie przedstawiono środowisko hybrydowe: 

![Hybrydowe środowisko sieciowe](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

W scenariuszu hybrydowym aplikacje z lokalnych centrów danych potrzebują dostępu do zasobów na platformie Azure.  Ma to miejsce niezależnie od tego, czy chcesz rozszerzyć centrum danych na platformę Azure, czy chcesz użyć usług natywnych platformy Azure, czy do odzyskiwania po awarii. Zobacz [opcje planowania bramy sieci VPN,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) aby uzyskać informacje na temat łączenia wielu zasobów lokalnych z zasobami na platformie Azure za pośrednictwem sieci VPN typu lokacja lokacja lub usługi ExpressRoute.

W hybrydowej topologii szprychy koncentratora sieci wirtualnej centrum na platformie Azure działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieci wirtualne równorzędne z koncentratora i mogą być używane do izolowania obciążeń.

W zależności od konfiguracji można połączyć zasoby lokalne z zasobami w centrum i szprychami.

W topologii zilustrowane powyżej sieci lokalnej jest podłączony do sieci wirtualnej koncentratora na platformie Azure i istnieją 2 szprychy sieci wirtualne w tym samym regionie równorzędne z sieci wirtualnej centrum.  W tym scenariuszu opcje łączności obsługiwane dla woluminów plików NetApp usługi Azure są następujące:

* Zasoby lokalne Maszyny Wirtualne 1 i VM 2 mogą łączyć się z woluminem 1 w koncentratorze za pośrednictwem usługi VPN lokacja lokacja lub obwodu usługi ExpressRoute. 
* Zasoby lokalne Maszyny Wirtualne 1 i VM 2 mogą łączyć się z woluminem 2 lub woluminem 3 za pośrednictwem sieci VPN między lokacjami i regionalnej komunikacji równorzędnej sieci wirtualnej.
* Maszyna wirtualna 3 w sieci wirtualnej koncentratora może łączyć się z woluminem 2 w sieci wirtualnej szprychy 1 i woluminie 3 w sieci wirtualnej szprychy 2.
* Maszyna wirtualna 4 z szprychy VNet 1 i VM 5 z szprychy VNet 2 można połączyć się z woluminu 1 w sieci wirtualnej koncentratora.
* VM 4 w szprychowej sieci wirtualnej 1 nie może połączyć się z woluminem 3 w sieci wirtualnej szprychy 2. Ponadto VM 5 w szprychy VNet2 nie może połączyć się z woluminem 2 w szprychy VNet 1. Dzieje się tak, ponieważ sieci wirtualne szprychy nie są równorzędne, a _routing tranzytowy nie jest obsługiwany przez komunikację równorzędnie sieci wirtualnej_.
* W powyższej architekturze, jeśli istnieje brama w sieci wirtualnej szprychy, jak również, łączność z woluminem ANF z on-prem łączenia za dodatkową bramę w centrum zostaną utracone. Zgodnie z projektem preferowane będzie brama w sieci wirtualnej szprychy i tak tylko maszyny łączące się przez tę bramę można połączyć się z woluminem ANF.

## <a name="next-steps"></a>Następne kroki

[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
