---
title: Projektowanie pod kątem odzyskiwania po awarii przy użyciu usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera architektury zalecenia dotyczące odzyskiwania po awarii podczas korzystania z usługi Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466070"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projektowanie pod kątem odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute

Usługa ExpressRoute jest przeznaczona dla wysokiej dostępności zapewnić operatora połączenie sieci prywatnej klasy korporacyjnej z zasobami firmy Microsoft. Innymi słowy nie ma żadnego pojedynczego punktu awarii w ścieżce usługi ExpressRoute w sieci firmy Microsoft. Zagadnienia dotyczące projektowania w celu zapewnienia maksymalnej dostępności obwodu usługi ExpressRoute, można zobaczyć [projektowanie pod kątem wysokiej dostępności przy użyciu usługi ExpressRoute][HA].

Jednak wykonanie firmy Murphy popularnych powiedzeniem —*Jeśli wszystko pójdzie źle, będzie*— pod uwagę, w tym artykule Daj nam skupić się na zastosowanie rozwiązań wykraczających poza błędy, które można rozwiązać za pomocą jednego obwodu usługi ExpressRoute. Innymi słowy w tym artykule Daj nam zapozna zagadnienia dotyczące architektury sieci do tworzenia niezawodnych zaplecza łącznością sieciową na potrzeby odzyskiwania po awarii przy użyciu magazynu geograficznie nadmiarowego obwodów usługi ExpressRoute.

## <a name="need-for-redundant-connectivity-solution"></a>Konieczność stosowania rozwiązań łączność nadmiarowa

Brak możliwości i wystąpienia, gdzie całą usługę regionalne (można go z firmy Microsoft, sieci dostawcy usług, klienta lub innych dostawców usług w chmurze) pobiera negatywny wpływ na dostępność. Główną przyczynę tego wpływu regionalna usługa szerokiego obejmują calamity fizycznych. W związku z tym ciągłość działalności biznesowej i misji kluczowych aplikacji biznesowych należy zaplanować odzyskiwanie po awarii.   

Niezależnie od tego, czy uruchomić usługi dla aplikacji o znaczeniu w regionie platformy Azure lub lokalnie lub dowolnej innej lokalizacji można użyć innego regionu platformy Azure jako lokacji trybu failover. Następujące artykuły adresy odzyskiwania po awarii z aplikacji i perspektywy dostępu do serwera sieci Web:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii SMB za pomocą usługi Azure Site Recovery][SMB DR]

Niezawodne połączenia usługi ExpressRoute między siecią lokalną i firmy Microsoft dotyczących krytycznych operacji o znaczeniu plan odzyskiwania po awarii powinny również obejmować łączności sieciowej geograficznie nadmiarowy. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Wyzwania związane z przy użyciu wielu obwodów usługi ExpressRoute

Gdy połączenie jest ten sam zestaw sieci za pomocą więcej niż jedno połączenie, wprowadzasz równoległe ścieżek między tymi sieciami. Równoległe ścieżek, jeśli nie zostało prawidłowo zaprojektowana, może prowadzić do routingu asymetrycznego. Jeśli masz jednostek stanowe (na przykład translatora adresów Sieciowych, zapory) w ścieżce routingu asymetrycznego może zablokować przepływu ruchu.  Zazwyczaj za pośrednictwem ścieżki prywatnej komunikacji równorzędnej usługi ExpressRoute nie będzie trafisz na stanowe jednostki, takie jak translatora adresów Sieciowych lub zaporami. W związku z tym asymetryczny routing za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute nie zawsze blokuje przepływu ruchu.
 
Jednak możesz Równoważenie obciążenia ruchem magazynu geograficznie nadmiarowego ścieżek równoległych, niezależnie od tego, czy masz stanowych jednostek lub nie będzie występować wydajność sieci niespójne. W tym artykule omówimy sposób rozwiązać te problemy.

## <a name="small-to-medium-on-premises-network-considerations"></a>Małe i zagadnienia dotyczące sieci średniej w środowisku lokalnym

Rozważmy przykładową sieć zilustrowane na poniższym diagramie. W tym przykładzie magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute zostanie nawiązane między firmy Contoso na lokalizację lokalną a siecią wirtualną firmy Contoso, w regionie platformy Azure. Na diagramie linia ciągła zielony oznacza preferowaną ścieżkę (za pośrednictwem usługi ExpressRoute 1), a jeden kropkowana reprezentuje ścieżkę zapasowego (za pośrednictwem usługi ExpressRoute 2).

[![1]][1]

Podczas projektowania połączenia usługi ExpressRoute na potrzeby odzyskiwania po awarii, należy wziąć pod uwagę:

- za pomocą magazynu geograficznie nadmiarowego obwodów usługi ExpressRoute
- za pomocą różnych usług dostawcy sieci dla innego obwodu usługi ExpressRoute
- Każdy obwód usługi ExpressRoute dla projektowania [wysokiej dostępności][HA]
- zakończenie innego obwodu usługi ExpressRoute w innej lokalizacji w sieci klientów

Domyślnie jeśli identycznie anonsować trasy za pośrednictwem wszystkich ścieżek usługi ExpressRoute platformy Azure zostanie równoważyć obciążenie ruchem w środowisku lokalnym powiązany we wszystkich ścieżkach usługi ExpressRoute za pomocą routing wielościeżkowego (ECMP) równy kosztów.

Jednak z magazynu geograficznie nadmiarowego obwodów usługi ExpressRoute należy wziąć pod uwagę wykonań inną sieć z innymi ścieżkami sieciowymi (zwłaszcza w przypadku opóźnienia sieci). Aby uzyskać bardziej spójną wydajność sieci podczas normalnego działania, warto preferowany obwód usługi ExpressRoute, który zapewnia minimalne opóźnienia.

Mogą mieć wpływ Azure preferowanie jeden obwód usługi ExpressRoute za pośrednictwem innej przy użyciu jednej z następujących technik (wymienione w kolejności skuteczności):

- anonsowane dokładniej określoną trasę przez preferowany obwód usługi ExpressRoute w porównaniu do innych obwody usługi ExpressRoute
- Konfigurowanie wyższa waga połączenia dla połączenia, które łączy sieć wirtualną preferowany obwód usługi ExpressRoute
- anonsowanie tras za pośrednictwem mniej preferowany obwód usługi ExpressRoute za pomocą dłuższej ścieżki AS (jak ścieżki jest dołączana)

### <a name="more-specific-route"></a>Dokładniej określoną trasę

Na poniższym diagramie przedstawiono wywieranie wybór ścieżki usługi ExpressRoute przy użyciu bardziej szczegółowe anons trasy. W tym przykładzie przedstawiono Contoso lokalnych/24 zakres adresów IP są anonsowane jako zakresów adresów dwóch /25 za pośrednictwem preferowaną ścieżką (usługa ExpressRoute 1) i prefiksie/24, za pośrednictwem ścieżki rezerwowej (2 usługi ExpressRoute).

[![2]][2]

Ponieważ /25 jest bardziej szczegółowe, w porównaniu do prefiksie/24, Azure prześle ruch kierowany do 10.1.11.0/24 za pośrednictwem usługi ExpressRoute 1 w normalnym stanie. W przypadku połączeń usługi ExpressRoute 1 przestaną działać, następnie sieci wirtualnej widział anons trasy 10.1.11.0/24 wyłącznie za pośrednictwem usługi ExpressRoute 2; i w związku z tym obwodu rezerwy jest używany w tym stanie błędu.

### <a name="connection-weight"></a>Waga połączenia

Poniższy zrzut ekranu przedstawia Konfigurowanie wagę połączenia usługi ExpressRoute za pośrednictwem witryny Azure portal.

[![3]][3]

Na poniższym diagramie przedstawiono wywieranie wybór ścieżki usługi ExpressRoute za pomocą wagi połączenia. Waga połączenia domyślne to 0. W poniższym przykładzie wagę połączenia dla usługi ExpressRoute 1 jest skonfigurowany jako 100. Gdy sieć wirtualną otrzymuje prefiks trasy anonsowane przez więcej niż jeden obwód usługi ExpressRoute, sieci wirtualnej zostanie wybrane połączenie o najwyższej wadze.

[![4]][4]

W przypadku połączeń usługi ExpressRoute 1 przestaną działać, następnie sieci wirtualnej widział anons trasy 10.1.11.0/24 wyłącznie za pośrednictwem usługi ExpressRoute 2; i w związku z tym obwodu rezerwy jest używany w tym stanie błędu.

### <a name="as-path-prepend"></a>JAKO ścieżki dołączana

Na poniższym diagramie przedstawiono wywieranie wybór ścieżki usługi ExpressRoute przy użyciu ścieżki jest dołączana. Na diagramie anons trasy za pośrednictwem usługi ExpressRoute 1 wskazuje, domyślne zachowanie eBGP. Na anonsowanie trasy za pośrednictwem usługi ExpressRoute, 2 numer ASN w sieci lokalnej jest poprzedzona dodatkowo na trasie jako ścieżka. Po otrzymaniu tej samej trasie za pomocą wielu obwodów usługi ExpressRoute dla procesu wyboru eBGP trasy sieci wirtualnej wolisz trasa o najkrótszym jako ścieżka. 

[![5]][5]

W przypadku połączeń usługi ExpressRoute 1 przestaną działać, sieć wirtualna widział anons trasy 10.1.11.0/24 wyłącznie za pośrednictwem usługi ExpressRoute 2. Consequentially tym dłużej, ponieważ ścieżka staną się nieodpowiednie. W związku z tym wstrzymania obwód będzie używana w tym stanie błędu.

Przy użyciu dowolnej z metod, jeśli mają wpływ na platformie Azure, aby wybrać jeden z usługi ExpressRoute za pośrednictwem innych, również należy upewnić się siecią lokalną również Preferuj tej samej ścieżce usługi ExpressRoute dla platformy Azure powiązane ruchu, aby uniknąć asymetrycznego przepływów. Zazwyczaj wartość preferencji lokalnej jest używana do wywierania wpływu na sieć lokalną preferowanie jeden obwód usługi ExpressRoute za pośrednictwem innych użytkowników. Preferencji lokalnej jest wewnętrzny metryki protokołu BGP (iBGP). Trasa protokołu BGP z najwyższą wartość preferencji lokalnej jest preferowana.

> [!IMPORTANT]
> Gdy używasz pewnych obwodów usługi ExpressRoute jako zapasowego, musisz aktywnie zarządzać nimi i okresowo testować operacji trybu failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Duże przedsiębiorstwo rozproszonej sieci

W przypadku sieci dużych przedsiębiorstw rozproszonych wszystko może mieć wiele obwodów usługi ExpressRoute. W tej sekcji Zobaczmy, jak projektować odzyskiwania po awarii przy użyciu obwodów usługi ExpressRoute aktywne aktywne, bez konieczności dodatkowego zapasowego obwodów. 

Rozważmy przykład zilustrowane na poniższym diagramie. W tym przykładzie firma Contoso ma dwa lokalizacjami lokalnymi połączyć dwa wdrożenia IaaS firmy Contoso w dwóch różnych regionach platformy Azure za pośrednictwem obwodów usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej. 

[![6]][6]

Jak tworzymy architekturę odzyskiwania po awarii ma wpływ na sposób krzyżowe regionalne do wielu lokalizacji (aby location2/location1 region1/region2) ruch jest kierowany. Rozważmy dwa architektur różnych po awarii, które kieruje ruchem między lokalizacja regionu inaczej.

### <a name="scenario-1"></a>Scenariusz 1

W pierwszego scenariusza umożliwia projektowanie odzyskiwania po awarii, tak, aby cały ruch między siecią platformy Azure region i lokalnych przepływać przez lokalnym obwodem usługi ExpressRoute w stanie stabilności. Jeśli nie powiedzie się z lokalnym obwodem usługi ExpressRoute, następnie zdalnym obwodem usługi ExpressRoute jest używany dla wszystkich przepływów ruchu sieciowego między platformą Azure i sieci lokalnej.

Scenariusz 1 przedstawiono na poniższym diagramie. Na diagramie zielony linie wskazują ścieżki dla przepływu ruchu sieci VNet1 i w środowisku lokalnym. Niebieskie linie wskazują ścieżki dla przepływu ruchu między sieciami VNet2 i lokalnych. Linia ciągła wskazać odpowiednią ścieżkę w stanie stabilności i linie przerywane wskazać ścieżkę ruchu odpowiedniego obwód usługi ExpressRoute, obsługująca przepływ ruchu stabilnym nie powiedzie się. 

[![7]][7]

Można zaprojektować scenariusza za pomocą wagi połączenia do wywierania wpływu na sieci wirtualne preferowanie połączenia do lokalnej lokalizacji komunikacji równorzędnej usługi ExpressRoute między siecią lokalną powiązać ruchu. Aby wykonać to rozwiązanie, należy się upewnić przepływu symetryczne ruch powrotny. Możesz użyć preferencji lokalnej sesji iBGP między poszczególne routery BGP (na których obwodów usługi ExpressRoute kończą stronie lokalnych) preferowanie obwód usługi ExpressRoute. Rozwiązanie to zilustrowane na poniższym diagramie. 

[![8]][8]

### <a name="scenario-2"></a>Scenariusz 2

2 scenariusz przedstawiono na poniższym diagramie. Na diagramie zielony linie wskazują ścieżki dla przepływu ruchu sieci VNet1 i w środowisku lokalnym. Niebieskie linie wskazują ścieżki dla przepływu ruchu między sieciami VNet2 i lokalnych. W stabilnym (linie ciągłe na diagramie) wszystkie ruchu między sieciami wirtualnymi i lokalnych lokalizacji przepływu za pośrednictwem sieci szkieletowej firmy Microsoft w większości przypadków i przechodzą przez wzajemne połączenie między lokalizacjami lokalnymi tylko w stanie awarii (linie kropkowana w diagram) z usługi ExpressRoute.

[![9]][9]

Rozwiązanie to zilustrowane na poniższym diagramie. Zgodnie z opisami, scenariusz można zaprojektować albo przy użyciu bardziej szczegółowe trasy (opcja 1) lub ścieżkę AS dołączana (opcja 2) w celu wywierania wpływu na wybór ścieżki sieci wirtualnej. Aby wpłynąć na wybór trasy sieci lokalnej dla ruchu związanego z platformy Azure, należy skonfigurować wzajemne połączenie między lokalizacją lokalną jako mniej preferowane. Howe połączeń łącza są konfigurowane jako preferowane jest zależna od protokół routingu używany w sieci lokalnej. Możesz użyć preferencji lokalnej za pomocą iBGP lub metryki za pomocą IGP (OSPF lub znajduje się).

[![10]][10]


## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono sposób projektowania do odzyskiwania po awarii połączenia usługi ExpressRoute circuit prywatnej komunikacji równorzędnej. Następujące artykuły adresy odzyskiwania po awarii z aplikacji i perspektywy dostępu do serwera sieci Web:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii SMB za pomocą usługi Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "małych i zagadnienia dotyczące sieci w środowisku lokalnym lub średnim rozmiarze"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "wywieranie wpływu na wybór ścieżki przy użyciu bardziej szczegółowe trasy"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Konfigurowanie waga połączenia za pośrednictwem witryny Azure portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "wywieranie wpływu na wybór ścieżki przy użyciu wagi połączenia"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "wywieranie wpływu na wybór ścieżki przy użyciu jako ścieżka dołączana"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "dużych rozproszonej sieci lokalnej zagadnienia"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenariusz 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktywne aktywne ExpressRoute circuits rozwiązanie 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "Scenariusz 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktywne aktywne ExpressRoute circuits rozwiązanie 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





