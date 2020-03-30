---
title: 'Azure ExpressRoute: Projektowanie do odzyskiwania po awarii'
description: Ta strona zawiera zalecenia architektury odzyskiwania po awarii podczas korzystania z usługi Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076700"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projektowanie do odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute

Usługa ExpressRoute została zaprojektowana z myślą o wysokiej dostępności w celu zapewnienia łączności sieci prywatnej klasy operatora z zasobami firmy Microsoft. Innymi słowy nie ma jednego punktu awarii w ścieżce usługi ExpressRoute w sieci Microsoft. Aby zapoznać się z zagadnieniami projektowymi, aby zmaksymalizować dostępność obwodu usługi ExpressRoute, zobacz [Projektowanie w celu zapewnienia wysokiej dostępności za pomocą usługi ExpressRoute][HA].

Jednak biorąc popularne powiedzenie Murphy'ego -*jeśli coś może pójść nie tak, będzie to*- pod uwagę, w tym artykule skupmy się na rozwiązaniach, które wykraczają poza błędy, które można rozwiązać za pomocą jednego obwodu Usługi ExpressRoute. Innymi słowy w tym artykule przyjrzyjmy się zagadnieniom architektury sieci do tworzenia niezawodnej łączności sieci wewnętrznej bazy danych do odzyskiwania po awarii przy użyciu obwodów usługi ExpressRoute geobekbowanych.

## <a name="need-for-redundant-connectivity-solution"></a>Potrzeba nadmiarowego rozwiązania w zakresie łączności

Istnieją możliwości i przypadki, w których cała usługa regionalna (czy to usługi firmy Microsoft, dostawców usług sieciowych, klientów lub innych dostawców usług w chmurze) ulega degradacji. Główną przyczyną takiego wpływu na usługi w całej gamie regionalnej są naturalne nieszczęście. W związku z tym dla ciągłości biznesowej i aplikacji o znaczeniu krytycznym ważne jest, aby zaplanować odzyskiwanie po awarii.   

Niezależnie od tego, czy uruchamiasz aplikacje o znaczeniu krytycznym w regionie platformy Azure, lokalnie, czy gdziekolwiek indziej, możesz użyć innego regionu platformy Azure jako lokacji trybu failover. Następujące artykuły dotyczą odzyskiwania po awarii z aplikacji i frontend perspektywy dostępu:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii dla małych i średnich firm za pomocą usługi Azure Site Recovery][SMB DR]

Jeśli korzystasz z łączności usługi ExpressRoute między siecią lokalną a firmą Microsoft w przypadku operacji o znaczeniu krytycznym, plan odzyskiwania po awarii powinien również obejmować łączność sieciową z nadmiarową geograficznie. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Wyzwania związane z używaniem wielu obwodów usługi ExpressRoute

Po połączeniu tego samego zestawu sieci przy użyciu więcej niż jednego połączenia, należy wprowadzić równoległe ścieżki między sieciami. Ścieżki równoległe, jeśli nie są odpowiednio zaprojektowany, może prowadzić do routingu asymetrycznego. Jeśli masz jednostki stanowe (na przykład NAT, firewall) w ścieżce, routing asymetryczny może blokować przepływ ruchu.  Zazwyczaj za pomocą prywatnej ścieżki równorzędnej usługi ExpressRoute nie natkniesz się na jednostki stanowe, takie jak NAT lub Zapory. W związku z tym routing asymetryczny za ich dotarcie do usługi ExpressRoute private peering nie musi blokować przepływu ruchu.
 
Jednak jeśli ładujysz ruch równoważenia między geograficznie nadmiarowymi ścieżkami równoległymi, niezależnie od tego, czy masz jednostki stanowe, czy nie, wydajność sieci będzie niespójna. W tym artykule omówimy, jak rozwiązać te problemy.

## <a name="small-to-medium-on-premises-network-considerations"></a>Zagadnienia dotyczące sieci lokalnej w małych i średnich środowiskach

Rozważmy przykładową sieć zilustrowane na poniższym diagramie. W tym przykładzie geograficznie nadmiarowa łączność usługi ExpressRoute jest ustanawiana między lokalizacją lokalną firmy Contoso a siecią wirtualną contoso w regionie platformy Azure. Na diagramie zielona zielona linia wskazuje preferowaną ścieżkę (za pośrednictwem usługi ExpressRoute 1), a kropkowana reprezentuje ścieżkę czuwania (za pośrednictwem usługi ExpressRoute 2).

[![1]][1]

Podczas projektowania usługi ExpressRoute łączności odzyskiwania po awarii, należy wziąć pod uwagę:

- korzystanie z geob nadmiarowych obwodów usługi ExpressRoute
- korzystanie z różnych sieci dostawców usług dla różnych obwodów usługi ExpressRoute
- zaprojektowanie każdego obwodu usługi ExpressRoute w [celu zapewnienia wysokiej dostępności][HA]
- zakończenie innego obwodu usługi ExpressRoute w innej lokalizacji w sieci klienta

Domyślnie, jeśli anonsujesz trasy identycznie we wszystkich ścieżkach usługi ExpressRoute, platforma Azure będzie równoważyć ruch lokalny we wszystkich ścieżkach usługi ExpressRoute przy użyciu routingu współbraciowego (ECMP) równego kosztu.

Jednak w przypadku obwodów usługi ExpressRoute geobeksysuj musimy wziąć pod uwagę różne wydajności sieci z różnymi ścieżkami sieciowymi (szczególnie w przypadku opóźnień sieci). Aby uzyskać bardziej spójną wydajność sieci podczas normalnej pracy, można preferować obwód usługi ExpressRoute, który oferuje minimalne opóźnienia.

Możesz mieć wpływ na platformę Azure, aby preferować jeden obwód usługi ExpressRoute nad innym przy użyciu jednej z następujących technik (wymienionych w kolejności skuteczności):

- reklamowanie bardziej szczegółowej trasy w preferowanym obwodzie usługi ExpressRoute w porównaniu z innymi obwodami usługi ExpressRoute
- konfigurowanie wyższej wagi połączenia w połączeniu łączącym sieć wirtualną z preferowanym obwodem usługi ExpressRoute
- reklamowanie tras w stosunku do mniej preferowanego obwodu usługi ExpressRoute z dłuższym AS Path (as path prepend)

### <a name="more-specific-route"></a>Bardziej szczegółowa trasa

Na poniższym diagramie przedstawiono wpływ na wybór ścieżki usługi ExpressRoute przy użyciu bardziej szczegółowej anonsu marszruty. W zilustrowanym przykładzie contoso lokalnie /24 zakres adresów IP jest anonsowany jako dwa /25 zakresów adresów za pośrednictwem preferowanej ścieżki (ExpressRoute 1) i jako /24 za pośrednictwem ścieżki gotowości (ExpressRoute 2).

[![2]][2]

Ponieważ /25 jest bardziej szczegółowe, w porównaniu do /24, Platforma Azure będzie wysyłać ruch przeznaczony do 10.1.11.0/24 za pośrednictwem usługi ExpressRoute 1 w stanie normalnym. Jeśli oba połączenia usługi ExpressRoute 1 ujrzą w dół, w sieci wirtualnej zostanie wyświetlenie anonsu trasy 10.1.11.0/24 tylko za pośrednictwem usługi ExpressRoute 2; w tym stanie awarii.

### <a name="connection-weight"></a>Masa połączenia

Poniższy zrzut ekranu ilustruje konfigurowanie wagi połączenia usługi ExpressRoute za pośrednictwem witryny Azure portal.

[![3]][3]

Na poniższym diagramie przedstawiono wpływ na wybór ścieżki usługi ExpressRoute przy użyciu wagi połączenia. Domyślna waga połączenia wynosi 0. W poniższym przykładzie waga połączenia dla usługi ExpressRoute 1 jest skonfigurowana jako 100. Gdy sieć wirtualna odbiera prefiks trasy anonsowane za pośrednictwem więcej niż jednego obwodu usługi ExpressRoute, sieć wirtualna preferuje połączenie o najwyższej wadze.

[![4]][4]

Jeśli oba połączenia usługi ExpressRoute 1 ujrzą w dół, w sieci wirtualnej zostanie wyświetlenie anonsu trasy 10.1.11.0/24 tylko za pośrednictwem usługi ExpressRoute 2; w tym stanie awarii.

### <a name="as-path-prepend"></a>As path prepend (poprzedzanie ścieżki AS)

Na poniższym diagramie przedstawiono wpływ na wybór ścieżki usługi ExpressRoute przy użyciu as path prepend. Na diagramie anons trasy zaokrętowany przez expressRoute 1 wskazuje domyślne zachowanie eBGP. W anonsie trasy za pośrednictwem usługi ExpressRoute 2 sieć lokalna ASN jest dołączana dodatkowo na ścieżce AS trasy. Gdy ta sama trasa jest odbierana przez wiele obwodów usługi ExpressRoute, zgodnie z procesem wyboru trasy eBGP sieć wirtualna wolałaby trasę z najkrótszą ścieżką AS. 

[![5]][5]

Jeśli oba połączenia usługi ExpressRoute 1 ujrzą w dół, w sieci wirtualnej zostanie wyświetlenie anonsu trasy 10.1.11.0/24 tylko za pośrednictwem usługi ExpressRoute 2. W konsekwencji dłuższa ścieżka AS stałaby się nieistotna. W związku z tym obwód czuwania będzie używany w tym stanie awarii.

Przy użyciu dowolnej z technik, jeśli wpływ platformy Azure, aby preferować jeden z usługi ExpressRoute nad innymi, należy również upewnić się, że sieć lokalna również preferować tę samą ścieżkę usługi ExpressRoute dla ruchu związanego z platformą Azure, aby uniknąć przepływów asymetrycznych. Zazwyczaj lokalna wartość preferencji jest używana do wpływania na sieć lokalną, aby preferować jeden obwód usługi ExpressRoute w stosunku do innych. Preferencja lokalna jest metryką wewnętrzną BGP (iBGP). Preferowana jest trasa BGP o najwyższej wartości preferencji lokalnej.

> [!IMPORTANT]
> Podczas korzystania z niektórych obwodów usługi ExpressRoute jako stand-by, należy aktywnie zarządzać nimi i okresowo testować pracę awaryjną. 
> 

## <a name="large-distributed-enterprise-network"></a>Duża rozproszona sieć przedsiębiorstwa

Jeśli masz dużą rozproszoną sieć przedsiębiorstwa, prawdopodobnie masz wiele obwodów usługi ExpressRoute. W tej sekcji zobaczmy, jak zaprojektować odzyskiwanie po awarii przy użyciu aktywnych aktywnych obwodów usługi ExpressRoute, bez konieczności dodatkowych obwodów czuwania. 

Rozważmy przykład zilustrowany na poniższym diagramie. W przykładzie Contoso ma dwie lokalizacje lokalne połączone z dwoma wdrożeniami IaaS contoso w dwóch różnych regionach platformy Azure za pośrednictwem obwodów usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej. 

[![6]][6]

Sposób eksformowania odzyskiwania po awarii ma wpływ na sposób przecinania ruchu między lokalizacjami regionalnymi (region1/region2 do location2/location1). Rozważmy dwie różne architektury awarii, które trasy przecinają ruch lokalizacji regionu inaczej.

### <a name="scenario-1"></a>Scenariusz 1

W pierwszym scenariuszu zaprojektujmy odzyskiwanie po awarii w taki sposób, aby cały ruch między regionem platformy Azure a siecią lokalną przepływał przez lokalny obwód usługi ExpressRoute w stanie stacjonarnym. Jeśli lokalny obwód usługi ExpressRoute ulegnie awarii, zdalny obwód usługi ExpressRoute jest używany dla wszystkich przepływów ruchu między platformą Azure a siecią lokalną.

Scenariusz 1 jest zilustrowany na poniższym diagramie. Na diagramie zielone linie wskazują ścieżki przepływu ruchu między siecią wirtualną1 a sieciami lokalnymi. Niebieskie linie wskazują ścieżki przepływu ruchu między siecią wirtualną2 a sieciami lokalnymi. Linie ciągłe wskazują żądaną ścieżkę w stanie stacjonarnym, a linie przerywane wskazują ścieżkę ruchu w przypadku awarii odpowiedniego obwodu usługi ExpressRoute, który przenosi przepływ ruchu w stanie stacjonarnym. 

[![7]][7]

Można zaprojektować scenariusz przy użyciu wagi połączenia, aby wpłynąć na sieci wirtualne, aby preferować połączenie z lokalną lokalizacją komunikacji równorzędnej ExpressRoute dla lokalnego ruchu związanego z siecią. Aby ukończyć rozwiązanie, należy zapewnić symetryczny przepływ ruchu wstecznego. Preferencji lokalnych można używać w sesji iBGP między routerami BGP (na których obwody usługi ExpressRoute są kończone po stronie lokalnej), aby preferować obwód usługi ExpressRoute. Rozwiązanie jest zilustrowane na poniższym diagramie. 

[![8]][8]

### <a name="scenario-2"></a>Scenariusz 2

Scenariusz 2 jest zilustrowany na poniższym diagramie. Na diagramie zielone linie wskazują ścieżki przepływu ruchu między siecią wirtualną1 a sieciami lokalnymi. Niebieskie linie wskazują ścieżki przepływu ruchu między siecią wirtualną2 a sieciami lokalnymi. W stanie stacjonarnym (linie stałe na diagramie) cały ruch między sieciami wirtualnymi a lokalizacjami lokalnymi przepływa przez szkielet firmy Microsoft w przeważającej części i przepływa przez połączenia między lokalizacjami lokalnymi tylko w stanie awarii (linie przerywane w diagramu) usługi ExpressRoute.

[![9]][9]

Rozwiązanie jest zilustrowane na poniższym diagramie. Jak pokazano, można zaprojektować scenariusz przy użyciu bardziej konkretnej trasy (opcja 1) lub as-path prepend (opcja 2), aby wpłynąć na wybór ścieżki sieci wirtualnej. Aby wpłynąć na wybór trasy sieci lokalnej dla ruchu związanego z platformą Azure, należy skonfigurować wzajemne połączenia między lokalizacją lokalną jako mniej korzystne. Howe skonfigurować łącze połączenia jako preferowane zależy od protokołu routingu używanego w sieci lokalnej. Można użyć preferencji lokalnych z iBGP lub metryki z IGP (OSPF lub IS-IS).

[![10]][10]


## <a name="next-steps"></a>Następne kroki

W tym artykule omówiliśmy sposób projektowania odzyskiwania po awarii prywatnej łączności równorzędnej obwodu usługi ExpressRoute. Następujące artykuły dotyczą odzyskiwania po awarii z aplikacji i frontend perspektywy dostępu:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii dla małych i średnich firm za pomocą usługi Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "zagadnienia dotyczące sieci lokalnej o małych i średnich rozmiarach"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "wpływanie na wybór ścieżki przy użyciu bardziej szczegółowych tras"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurowanie wagi połączenia za pośrednictwem witryny Azure portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "wpływanie na wybór ścieżki za pomocą masy połączenia"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "wpływanie na wybór ścieżki przy użyciu as path prepend"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "dużych rozproszonych zagadnień sieci lokalnych"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenariusz 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktywnych i aktywnych obwodów expressroute rozwiązanie 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenariusz 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktywnych i aktywnych obwodów expressroute rozwiązanie 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





