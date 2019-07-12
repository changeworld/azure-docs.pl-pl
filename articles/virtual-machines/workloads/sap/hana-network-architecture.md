---
title: Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia) sieci | Dokumentacja firmy Microsoft
description: Architektura sieci sposób wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2359f1ea1d55b8ce153295cc71fdf78040e8e316
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707414"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architektura sieci platformy SAP HANA (duże wystąpienia)

Architektura usług sieci platformy Azure jest kluczowym elementem pomyślne wdrożenie aplikacji SAP na dużych wystąpień HANA. Zazwyczaj środowiska SAP HANA na wdrożeń platformy Azure (duże wystąpienia) mają większe środowisko SAP w kilku różnych rozwiązaniach SAP o różnych rozmiarach, między bazami danych, zużycia zasobów procesora CPU i użycie pamięci przez program. Istnieje prawdopodobieństwo, że nie wszystkie systemy IT znajdują się na platformie Azure już. Środowiska SAP jest często hybrydowe, także z punktu DBMS i SAP aplikacji punktu widzenia przy użyciu mieszanki NetWeaver i S/4HANA i SAP HANA i innymi bazami danych. Platforma Azure oferuje różne usługi, które umożliwiają uruchamianie różnych systemów DBMS, NetWeaver i S/4HANA na platformie Azure. Usługa Azure również sieci technologię Szukaj platforma Azure oferuje, takich jak centrum danych wirtualnego wdrożenia oprogramowania w środowisku lokalnym

Chyba że kompletny system INFORMATYCZNY są hostowane na platformie Azure. Funkcjonalność sieci platformy Azure jest używany do łączenia na świecie lokalnych z zasobami platformy Azure umożliwiają Azure wygląda jak wirtualnego centrum danych, której występuje. Funkcje sieci platformy Azure, używana jest: 

- Sieci wirtualne platformy Azure są połączone z [ExpressRoute](https://azure.microsoft.com/services/expressroute/) obwodu, który nawiązuje połączenie z zasobów sieciowych w środowisku lokalnym.
- Obwód usługi ExpressRoute, który nawiązuje połączenie środowiska lokalnego do platformy Azure mają minimalna przepustowość [1 GB/s lub wyższej](https://azure.microsoft.com/pricing/details/expressroute/). Tej minimalnej przepustowości umożliwia odpowiedniej przepustowości do transferu danych między systemami lokalnymi i systemów, które są uruchamiane na maszynach wirtualnych. Umożliwia także odpowiedniej przepustowości dla połączenia z systemami Azure od użytkowników w środowisku lokalnym.
- Wszystkie systemy SAP na platformie Azure są konfigurowane w sieciach wirtualnych do komunikowania się ze sobą.
- Usługa Active Directory i DNS hostowanych lokalnie zostały rozszerzone na platformę Azure za pośrednictwem usługi ExpressRoute ze środowiska lokalnego lub działające na platformie Azure.

W przypadku określonych integracji dużych wystąpień HANA w sieci szkieletowej centrum danych platformy Azure, usługi Azure ExpressRoute to technologia używana również


> [!NOTE] 
> Tylko jedną subskrypcję platformy Azure można połączyć tylko jednej dzierżawy w sygnaturze dużych wystąpień HANA, w określonym regionie platformy Azure. Z drugiej strony pojedynczej dzierżawy sygnatury dużych wystąpień HANA mogą być połączone tylko jedną subskrypcję platformy Azure. To wymaganie jest spójna z innymi obiektami płatnych na platformie Azure.

Jeśli oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jest wdrażana w wielu różnych regionach platformy Azure, oddzielna dzierżawa jest wdrażany w sygnaturze dużych wystąpień HANA. Można uruchomić zarówno w ramach tej samej subskrypcji platformy Azure, tak długo, jak te wystąpienia są częścią tej samej środowiskiem SAP. 

> [!IMPORTANT] 
> Metoda wdrażania usługi Azure Resource Manager jest obsługiwana przez oprogramowanie SAP HANA na platformie Azure (duże wystąpienia).

 

## <a name="additional-virtual-network-information"></a>Informacje dodatkowe sieci wirtualnej

Aby połączyć sieć wirtualną z usługi ExpressRoute, należy utworzyć bramę usługi ExpressRoute platformy Azure. Aby uzyskać więcej informacji, zobacz [dotyczących bramy usługi Expressroute dla usługi ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Brama usługi ExpressRoute systemu Azure jest używany przy użyciu usługi ExpressRoute do infrastruktury spoza platformy Azure lub sygnatury dużego wystąpienia platformy Azure. Możesz łączyć się z bramy usługi Azure ExpressRoute do maksymalnie czterech różnych obwodów usługi ExpressRoute, tak długo, jak te połączenia pochodzą z różnych routery graniczne enterprise firmy Microsoft. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Maksymalna przepływność, którą można osiągnąć przy użyciu bramy usługi ExpressRoute jest 10 GB/s za pośrednictwem połączenia usługi ExpressRoute. Kopiowanie plików między maszyną Wirtualną, która znajduje się w sieci wirtualnej i systemem lokalnego (jako strumień o pojedynczej kopii) nie osiągać pełną przepływność różne jednostki SKU bramy. Wykorzystanie przepustowości pełną bramę usługi ExpressRoute, należy użyć wielu strumieni. Lub należy skopiować różne pliki w strumieniach równoległe pojedynczego pliku.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla dużych wystąpień HANA
Architektura sieci dużych wystąpień HANA można podzielić na cztery elementy:

- Sieć lokalną i połączenie usługi ExpressRoute z platformą Azure. Ta część jest domeną klienta i jest połączony z platformą Azure za pośrednictwem usługi ExpressRoute. Ten obwód usługi Expressroute jest w pełni płatne przez Ciebie jako klient. Przepustowość powinien być wystarczająco duży, aby obsługiwać ruch sieciowy między swoje zasoby lokalne i regionu platformy Azure, w którym jest nawiązywane względem. Zobacz prawym dolnym rogu na poniższej ilustracji.
- Usługi sieci platformy Azure, jak wspomniano, dzięki sieciom wirtualnym ponownie potrzebujące bramy usługi ExpressRoute dodane. Ta część jest obszar, w którym trzeba znaleźć odpowiednie projekty dla wymagań aplikacji, zabezpieczeń i zgodności z wymaganiami. Czy są używane duże wystąpienie oprogramowania HANA jest inny punkt do należy wziąć pod uwagę pod względem liczby sieci wirtualnych i jednostki SKU bramy platformy Azure do wyboru. Zobacz prawym górnym rogu na rysunku.
- Łączność z dużych wystąpień HANA za pomocą usługi ExpressRoute technologii na platformie Azure. Ta część jest wdrażane i obsługiwane przez firmę Microsoft. To wszystko, co należy zrobić, podaj niektóre zakresy adresów IP po wdrożeniu Twoich zasobów w dużych wystąpień HANA połączenia obwodu usługi ExpressRoute z sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nie ma dodatkowych opłat za Ciebie jako klient korzystający z łączności między sieci szkieletowej sieci centrum danych platformy Azure i jednostkami dużych wystąpień HANA.
- Sieć, w ramach sygnatury dużych wystąpień HANA, który jest przede wszystkim przezroczyste dla Ciebie.

![Sieć wirtualna połączona z platformą SAP HANA na platformie Azure (duże wystąpienia) i w środowisku lokalnym](./media/hana-overview-architecture/image1-architecture.png)

Wymóg, aby połączyć z zasobów lokalnych za pośrednictwem usługi ExpressRoute na platformie Azure nie powoduje zmiany, ponieważ używasz dużych wystąpień HANA. Wymóg posiadania jednego lub wielu sieci wirtualnych, które uruchamiania maszyn wirtualnych, które hostują warstwy aplikacji, który nawiązuje połączenie z wystąpień HANA hostowanych w jednostkach dużych wystąpień HANA, również nie ulegnie zmianie. 

Dostępne są następujące różnice, by wdrożenia SAP na platformie Azure:

- Jednostki dużych wystąpień HANA dzierżawy klienta są połączone za pośrednictwem innego obwodu usługi ExpressRoute w sieciach wirtualnych. Do oddzielania warunkach obciążenia, lokalne obwodów usługi ExpressRoute w sieci wirtualnej platformy Azure i scalone między sieciami wirtualnymi platformy Azure i dużych wystąpień HANA nie udostępniaj tego samego routery.
- Przechowujących nieregularnie między warstwą aplikacji SAP i HANA, duże wystąpienie jest innego rodzaju, z wielu małych żądań i bursts, takie jak transfer danych (zestawy wyników) z platformy SAP HANA w warstwie aplikacji.
- Architektura aplikacji SAP jest bardziej wrażliwy na opóźnienia sieci niż typowych scenariuszy, w którym dane są wymieniane między lokalną i platformą Azure.
- Bramy usługi Azure ExpressRoute ma co najmniej dwóch połączeń usługi ExpressRoute. Jeden obwód, która jest połączona ze środowiska lokalnego i jedną, która jest połączona z dużymi wystąpieniami platformy HANA. Spowoduje to pozostawienie tylko miejsca dla dodatkowych obwodów w innym dwa z różnych Msee połączyć się na bramę usługi ExpressRoute. To ograniczenie jest niezależny od użycia ścieżki szybkiego do usługi ExpressRoute. Połączone obwodów udostępniać maksymalna przepustowość dla danych przychodzących z bram usługi ExpressRoute.

Opóźnienie sieci reprezentatywne między maszynami wirtualnymi i dużych wystąpień HANA jednostki mogą być większe niż typowy opóźnienie Rundy sieci maszyny Wirtualnej do maszyny Wirtualnej. Zależy od regionu platformy Azure, wartości mierzone może przekroczyć opóźnienie obustronne ms 0,7 sklasyfikowane jako poniżej średniej w [1100926 # Uwaga SAP — często zadawane pytania: Wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). Zależą od regionu platformy Azure oraz narzędzia do mierzenia obustronne opóźnienie sieciowe między maszyną Wirtualną platformy Azure i jednostka dużych wystąpień HANA, mierzonego opóźnienie można maksymalnie i wokół 2 milisekund. Niemniej jednak klienci wdrażać aplikacje SAP oparte na oprogramowanie SAP HANA produkcyjne pomyślnie na duże wystąpienie SAP HANA. Upewnij się, że należy dokładnie przetestować procesy biznesowe w dużych wystąpień HANA platformy Azure. Nowe funkcje, nazywane ExpressRoute szybka ścieżka, jest w stanie znacznego zmniejszenia opóźnienia sieci między dużymi wystąpieniami platformy HANA i warstwy aplikacji maszyn wirtualnych na platformie Azure (patrz poniżej). 

Aby zapewnić deterministyczne opóźnienie między maszynami wirtualnymi i HANA dużych wystąpień, wybór bramę usługi ExpressRoute jednostki SKU jest niezbędne. W przeciwieństwie do wzorców ruchu między magazynami lokalnymi i maszynami wirtualnymi wzorzec ruchu między maszynami wirtualnymi i dużych wystąpień HANA można tworzyć małe, ale wysokie wzrosty żądania i dane woluminów, które mają być przekazywane. Aby obsłużyć takie wzrosty dobrze, zdecydowanie zalecamy użycie jednostki SKU bramy UltraPerformance. Dla klasy typu II jednostek SKU platformy HANA duże wystąpienia korzystanie z jednostki SKU bramy UltraPerformance jako brama ExpressRotue jest obowiązkowe.

> [!IMPORTANT] 
> Biorąc pod uwagę ogólną ruch sieciowy między aplikacji SAP i warstwy bazy danych, tylko HighPerformance lub jednostki SKU bramy UltraPerformance dla sieci wirtualnych są obsługiwane w przypadku nawiązywania połączenia z platformą SAP HANA na platformie Azure (duże wystąpienia). Brama usługi ExpressRoute platformy HANA dużych wystąpień jednostek SKU typu II, obsługiwane są tylko jednostki SKU bramy UltraPerformance. Wyjątki mają zastosowanie w przypadku korzystania z usługi ExpressRoute szybka ścieżka (patrz poniżej)

### <a name="expressroute-fast-path"></a>Ścieżka Fast usługi ExpressRoute
Zmniejszyć opóźnienie, ExpressRoute szybka ścieżka stało się wprowadzone i opublikowane w maja 2019 określonych łączności dużych wystąpień HANA sieciami wirtualnymi platformy Azure obsługujących maszyny wirtualne aplikacji SAP. Główną różnicą rozwiązaniem wprowadzane do tej pory jest to, że przepływy danych między maszynami wirtualnymi i dużych wystąpień HANA nie są trasowane przez bramę usługi ExpressRoute już. Zamiast tego maszyny wirtualne przypisane w podsieci sieci wirtualnej platformy Azure bezpośrednio komunikują się z router brzegowy przedsiębiorstwa dedykowanych. 

> [!IMPORTANT] 
> Funkcje usługi ExpressRoute szybka ścieżka wymaga, że podsieci z uruchomionymi maszynami wirtualnymi aplikacji SAP znajdują się w tej samej platformy Azure sieci wirtualnej, który pobrano podłączony do dużych wystąpień HANA. Maszyn wirtualnych znajdujących się w sieciach wirtualnych platformy Azure, które się przy użyciu sieci wirtualnej platformy Azure, bezpośrednio połączone z jednostkami dużych wystąpień HANA są nie korzystających z usługi ExpressRoute szybka ścieżka. W wyniku typowe gwiazdy sieci wirtualnej projekty, gdy obwodów usługi ExpressRoute jest nawiązywane połączenie, względem centralnej sieci wirtualnej i uzyskiwanie połączenia równorzędnego sieci wirtualne zawierające warstwie aplikacji SAP (klienci dokonującymi) optymalizacji przez szybkie usługi ExpressRoute Ścieżka nie będzie działać. Oprócz ścieżkę Fast usługi ExpressRoute nie obsługuje reguł routingu zdefiniowanego przez użytkownika (UDR) już dziś. Aby uzyskać więcej informacji, zobacz [sieci wirtualnej usługi ExpressRoute, bramy i FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Aby uzyskać więcej informacji na temat konfigurowania usługi ExpressRoute szybka ścieżka, przeczytaj dokument [łączenie sieci wirtualnej do dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Bramy UltraPerformance usługi ExpressRoute jest wymagane do pracy usługi ExpressRoute szybka ścieżka


## <a name="single-sap-system"></a>Pojedynczy system SAP

Infrastruktury lokalnej z wcześniejszymi jest połączony za pośrednictwem usługi ExpressRoute na platformie Azure. Obwód usługi ExpressRoute łączy do routera brzegowego firmy Microsoft enterprise (MSEE). Aby uzyskać więcej informacji, zobacz [ExpressRoute — opis techniczny](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po nawiązaniu trasy łączy do sieci szkieletowej platformy Azure.

> [!NOTE] 
> Aby uruchomić krajobrazów SAP na platformie Azure, nawiązać najbliżej regionu platformy Azure w orientacji poziomej SAP router brzegowy przedsiębiorstwa. Sygnatury dużych wystąpień HANA są połączone za pomocą dedykowanych enterprise krawędzi routera urządzeń, aby zminimalizować opóźnienie sieci między maszynami wirtualnymi w modelu IaaS platformy Azure i sygnatur dużych wystąpień HANA.

Brama usługi ExpressRoute dla maszyn wirtualnych, w których są hostowane wystąpienia aplikacji SAP jest podłączony do jeden obwód usługi ExpressRoute, który nawiązuje połączenie z lokalnych. Tej samej sieci wirtualnej jest połączony z router brzegowy przedsiębiorstwa oddzielnych dedykowanych do nawiązywania połączenia z sygnaturami dużego wystąpienia. Przy użyciu usługi ExpressRoute szybko ścieżki przepływu danych z dużych wystąpień HANA do warstwy aplikacji SAP maszyny wirtualne nie są przesyłane za pośrednictwem bramy usługi ExpressRoute na już i który umożliwia zmniejszenie sieci, które \średni czas opóźnienia Rundy.

Ten system jest prosty przykład pojedynczego systemu SAP. Warstwa aplikacji SAP jest hostowany na platformie Azure. Uruchamia bazy danych SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia). Zakłada się, że przepustowość bramy usługi ExpressRoute, 2 GB lub 10 GB/s przepływności nie stanowią "wąskie gardło".

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

Wiele systemów SAP lub dużych systemów SAP są wdrażane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia), mogą stać się wąskim gardłem przez przepływność bramy ExpressRoute. Lub chcesz izolować produkcji i systemów nieprodukcyjnych w różnych sieciach wirtualnych platformy Azure. W takim przypadku należy podzielić warstw aplikacji na wielu sieci wirtualnych. Można również utworzyć specjalne sieci wirtualnej, która łączy się z dużych wystąpień HANA w przypadkach, takich jak:

- Wykonywanie kopii zapasowych bezpośrednio w wystąpieniach oprogramowania HANA w dużych wystąpień HANA do maszyny Wirtualnej na platformie Azure, która hostuje udziały NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z dużych wystąpień HANA jednostek do miejsca na dysku zarządzanego na platformie Azure.

Użyj oddzielnych sieci wirtualnej do hosta maszyny wirtualne, które zarządzania magazynem dla pamięci masowej transfer danych między dużymi wystąpieniami platformy HANA i platformą Azure. To rozwiązanie pozwala uniknąć dużych plików lub przesyłanie danych z dużych wystąpień HANA na platformie Azure w ramach bramy usługi ExpressRoute, pełniącą maszyn wirtualnych działających w warstwie aplikacji SAP. 

Aby uzyskać bardziej Skalowalna architektura sieci:

- Korzystać z wielu sieci wirtualnych w jednym, większy warstwy aplikacji SAP.
- Wdrożyć oddzielny jednej sieci wirtualnej dla każdego systemu SAP wdrożone, w porównaniu do łączenia tych systemów SAP w oddzielnych podsieciach w ramach tej samej sieci wirtualnej.

  Bardziej Skalowalna architektura sieci dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

![Wdrażanie warstwy aplikacji SAP za pośrednictwem wielu sieci wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

Zależne od reguł i ograniczeń, które chcesz zastosować między różnych sieciach wirtualnych hostingu maszyn wirtualnych z różnych systemów SAP, powinien połączenia tych sieci wirtualnych. Aby uzyskać więcej informacji na temat komunikacji równorzędnej sieci wirtualnej, zobacz [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing na platformie Azure

W domyślnym wdrożeniu trzy routingu zagadnienia dotyczące sieci są ważne w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) są dostępne wyłącznie za pośrednictwem maszyn wirtualnych platformy Azure i dedykowane połączenie usługi ExpressRoute nie są bezpośrednio ze środowiska lokalnego. Bezpośredni dostęp ze środowiska lokalnego do jednostek duże wystąpienie oprogramowania HANA w dostarczonym przez firmę Microsoft, nie jest możliwe natychmiast. Przechodnie ograniczenia routingu są spowodowane przez bieżący architektury sieci platformy Azure używane do duże wystąpienie SAP HANA. Niektórzy klienci administracji i wszelkie aplikacje, które muszą bezpośredni dostęp, takich jak SAP rozwiązania Manager uruchomiony w środowisku lokalnym, nie można nawiązać połączenia bazy danych SAP HANA. Wyjątki na ten temat można znaleźć w sekcji "Bezpośredni Routing do dużych wystąpień HANA".

* Jeśli masz jednostkach dużych wystąpień HANA wdrożone w dwóch różnych regionach platformy Azure do odzyskiwania po awarii, te same ograniczenia routingu przejściowy stosowanych w przeszłości. Innymi słowy adresy IP jednostki duże wystąpienie oprogramowania HANA w jednym regionie (na przykład zachodnie stany USA) nie były kierowane do jednostki dużych wystąpień HANA, wdrożyć w innym regionie (na przykład wschodnie stany USA). To ograniczenie jest niezależny od użycia sieci platformy Azure komunikację równorzędną między regionami lub wielu łączenie obwody usługi ExpressRoute jednostki dużych wystąpień HANA nawiązać połączenie z sieciami wirtualnymi. Aby uzyskać graficzną reprezentację zobacz rysunek w sekcji "Dużych wystąpień HANA użycia jednostek w wielu regionach." To ograniczenie, który został dostarczony z wdrożonej architektury, będzie to zabronione natychmiastowe korzystanie z replikacji systemu HANA jako funkcja odzyskiwania po awarii. Ostatnie zmiany wyszukując hasło sekcji "Dużych wystąpień HANA użycia jednostki w wielu regionach". 

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jednostki ma przypisanego adresu IP z zakresu adresów puli adresów IP serwera, który przesłane podczas wdrażania dużych wystąpień HANA żądania. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pośrednictwem subskrypcji platformy Azure i obwodu, który nawiązuje połączenie sieci wirtualnych platformy Azure do dużych wystąpień HANA. Przypisany adres IP z zakresu adresów puli adresów IP serwera jest przypisane bezpośrednio do jednostki sprzętu. Ma ona *nie* przypisane przy użyciu translatora adresów Sieciowych, jak w przypadku pierwszego wdrożenia tego rozwiązania. 

### <a name="direct-routing-to-hana-large-instances"></a>Bezpośredni Routing do dużych wystąpień HANA
Domyślnie przechodnim routingiem między jednostkami dużych wystąpień HANA i w środowisku lokalnym lub między dużych wystąpień HANA routingu, które są wdrażane w dwóch różnych regionach zakończy się niepowodzeniem. Istnieje kilka możliwości, aby umożliwić takie przechodnim routingiem.

- Zwrotnego serwera proxy do kierowania danych do i z. Na przykład F5 BIG-IP, serwer NGINX z usługą Traffic Manager wdrożone w sieci wirtualnej platformy Azure, łączy do dużych wystąpień HANA oraz lokalnego jako rozwiązanie zapory/ruch wirtualnej routingu.
- Za pomocą [reguły IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) na maszynie wirtualnej systemu Linux, aby umożliwić routing między lokalizacjami lokalnymi i dużych wystąpień HANA jednostki lub między jednostkami duże wystąpienie oprogramowania HANA w różnych regionach. Maszyny Wirtualnej z systemem IPTables musi zostać wdrożony w sieci wirtualnej platformy Azure, który nawiązuje połączenie z dużymi wystąpieniami platformy HANA i w środowisku lokalnym. Maszyna wirtualna wymaga za względu musisz odpowiednio dostosować, tak, czy przepustowość sieci dla maszyny Wirtualnej jest wystarczająca dla oczekiwanego natężenia ruchu sieciowego. Dla przepustowość sieci szczegółowe informacje na temat maszyny Wirtualnej, sprawdź artykuł [rozmiary Linux maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Zapora usługi Azure](https://azure.microsoft.com/services/azure-firewall/) będą inne rozwiązanie umożliwia bezpośrednie kierowanie ruchu między w środowisku lokalnym i HANA, duże wystąpienia jednostki. 

Cały ruch z tych rozwiązań będą kierowane za pośrednictwem sieci wirtualnej platformy Azure i jako takie może być dodatkowo ograniczona przez urządzenia nietrwałego, używane lub Azure sieciowych grup zabezpieczeń, w związku z określonych adresów IP lub adres IP od ruchu sieciowego w środowisku lokalnym, może zablokowane lub jawnie dozwolone, uzyskiwanie dostępu do dużych wystąpień HANA. 

> [!NOTE]  
> Należy pamiętać, że wdrożenia i obsługę niestandardowego rozwiązania innych firm urządzeń sieciowych lub IPTables nie jest obsługiwane przez firmę Microsoft. Obsługa musi być podana przez dostawcę składnik używany lub integrator. 

#### <a name="express-route-global-reach"></a>Express Route globalny zasięg
Firma Microsoft wprowadziła nową funkcję o nazwie [zasięgu globalnym ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Globalny zasięg, może służyć do dużych wystąpień HANA w przypadku dwóch scenariuszy:

- Włącz bezpośredni dostęp ze środowiska lokalnego do jednostek dużych wystąpień HANA wdrożonych w różnych regionach
- Włącz bezpośrednią komunikację między jednostek dużych wystąpień HANA wdrożonych w różnych regionach


##### <a name="direct-access-from-on-premise"></a>Bezpośredni dostęp ze środowiska lokalnego
W regionach platformy Azure, w których jest oferowany zasięgu globalnym możesz poprosić o włączenie funkcji zasięgu globalnym dla obwodów usługi ExpressRoute, który nawiązuje połączenie z siecią lokalną siecią wirtualną platformy Azure łączącej się z jednostek dużych wystąpień HANA także. Istnieją pewne efekty kosztów dla strony w lokalnym obwód usługi ExpressRoute. Ceny są dostępne ceny [globalnego dotrzeć do dodatku](https://azure.microsoft.com/pricing/details/expressroute/). Brak bez dodatkowych kosztów dla Ciebie związane z obwodem, łączącej jedn dużych wystąpień HANA na platformie Azure. 

> [!IMPORTANT]  
> W przypadku włączania bezpośredni dostęp między jednostkami dużych wystąpień HANA i zasobów lokalnych przy użyciu zasięgu globalnym, przepływ danych i kontrolki sieci jest **nie są kierowane za pośrednictwem sieci wirtualnych platformy Azure**, ale bezpośrednio od firmy Microsoft routery exchange Enterprise. W rezultacie wszystkie reguły sieciowej grupy zabezpieczeń lub grupie ASG lub dowolny typ zapory, urządzenia WUS lub serwera proxy, wdrożone w sieci wirtualnej platformy Azure są wprowadzenie nienaruszone. **Jeśli korzystasz z usługi ExpressRoute zasięgu globalnym włączyć bezpośredni dostęp ze środowiska lokalnego do platformy HANA, duże wystąpienia jednostki ograniczeń, a uprawnienia dostępu HANA dużych wystąpień jednostek muszą być zdefiniowane w zapór na stronie w środowisku lokalnym** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Łączenie z dużymi wystąpieniami platformy HANA w różnych regionach platformy Azure
W taki sam sposób jak zasięgu globalnym usługi ExpressRoute może służyć do łączenia lokalnych jednostek dużych wystąpień HANA może służyć do łączenia z tow, który dzierżawy dużych wystąpień HANA rozmieszczone w dwóch różnych regionach. Izolacja jest obwody usługi ExpressRoute używają dzierżaw dużych wystąpień HANA w celu połączenia z platformą Azure w obu regionach. Nie ma żadnych dodatkowych opłat za łączenia dwóch dzierżaw dużych wystąpień HANA, które są wdrażane w dwóch różnych regionach. 

> [!IMPORTANT]  
> Przepływ danych i przepływ sterowania ruchu sieciowego między różnych HANA duże wystąpienia dzierżawcy nie będą kierowane za pośrednictwem sieci platformy azure. W rezultacie nie można użyć funkcjonalność platformy Azure lub urządzenia WUS do wymuszania ograniczenia komunikacji między dwoma dzierżaw dużych wystąpień HANA. 

Aby uzyskać więcej informacji na temat uzyskiwania ExpressRoute zasięgu globalnym włączone odczytać dokument [łączenie sieci wirtualnej do dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Łączność z Internetem z dużych wystąpień HANA
Duże wystąpienie oprogramowania HANA jest *nie* mieć bezpośrednie połączenie z Internetem. Na przykład to ograniczenie może ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio z dostawcą systemu operacyjnego. Może być konieczne z lokalnego serwera SUSE Linux Enterprise Server subskrypcji zarządzania narzędzia lub Red Hat Enterprise Linux subskrypcji Menedżera.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi i dużych wystąpień HANA
Dane przesyłane między dużych wystąpień HANA i maszyny wirtualne nie są szyfrowane. Jednak służyć wyłącznie do programu exchange między po stronie platformy HANA DBMS i aplikacje oparte na JDBC/ODBC, można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tej dokumentacji SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Użyj jednostki duże wystąpienie oprogramowania HANA w wielu regionach

Do osiągnięcia ups zestaw odzyskiwania po awarii, musisz mieć SZANA duże wystąpienie jednostki w wielu regionach platformy Azure. Nawet w przypadku przy użyciu platformy Azure [globalnych wirtualnych sieci równorzędnych], przechodnim routingiem domyślnie nie działa między dzierżawami duże wystąpienie oprogramowania HANA w dwóch różnych regionach. Jednak zasięgu globalnym otwiera ścieżka komunikacji między jednostkami dużych wystąpień HANA, aprowizowanej w dwóch różnych regionach. Ten scenariusz użycia o zasięgu globalnym ExpressRoute umożliwia:

 - Replikacji systemu HANA bez żadnych dodatkowych serwerów proxy lub zapory
 - Skopiowanie kopii zapasowych między jednostkami duże wystąpienie oprogramowania HANA w dwóch różnych regionach do wykonania kopii systemu lub odświeża systemu


![Sieć wirtualna połączona sygnatury dużego wystąpienia platformy Azure w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Na rysunku przedstawiono, jak różnych sieciach wirtualnych w obu regionach są połączone z dwóch różnych obwodów usługi ExpressRoute, które są używane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia) w obu regionach platformy Azure (linie szary). Przyczyną tego dwa połączenia krzyżowego jest do ochrony przed awarią Msee po obu stronach. Przepływ komunikacji między dwiema sieciami wirtualnymi w dwóch regionach platformy Azure mają być obsługiwane za pośrednictwem [globalnej komunikacji równorzędnej](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) z dwiema sieciami wirtualnymi w dwóch różnych regionach (niebieska linia przerywana). Gruba linia czerwony opisuje połączenia ExpressRoute zasięgu globalnym, który umożliwia jednostek dużych wystąpień HANA dzierżawcy w dwóch różnych regionach do komunikowania się ze sobą. 

> [!IMPORTANT] 
> Jeśli używasz wielu obwodów usługi ExpressRoute, ustawienia lokalnej preferencji BGP i dołączania ścieżki AS powinny służyć do zapewnienia prawidłowego przekierowania ruchu.

**Następne kroki**
- Zapoznaj się [architektury magazynu oprogramowania SAP HANA (duże wystąpienia)](hana-storage-architecture.md)