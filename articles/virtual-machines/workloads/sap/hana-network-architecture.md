---
title: Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia) sieci | Dokumentacja firmy Microsoft
description: Architektura sieci sposób wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
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
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835793"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architektura sieci platformy SAP HANA (duże wystąpienia)

Architektura usług sieci platformy Azure jest kluczowym elementem pomyślne wdrożenie aplikacji SAP na dużych wystąpień HANA. Zazwyczaj środowiska SAP HANA na wdrożeń platformy Azure (duże wystąpienia) mają większe środowisko SAP w kilku różnych rozwiązaniach SAP o różnych rozmiarach, między bazami danych, zużycia zasobów procesora CPU i użycie pamięci przez program. Istnieje prawdopodobieństwo, że nie wszystkie te systemy SAP są oparte na platformie SAP HANA. Środowiska SAP jest prawdopodobnie hybrydowego, który używa:

- Wdrażane oprogramowanie SAP systemów lokalnych. Ze względu na ich rozmiary te systemy aktualnie nie mogą być hostowane na platformie Azure. Przykładem jest system SAP ERP, który działa w programie SQL Server (co baza danych) i wymaga większej ilości zasobów procesora CPU lub pamięci niż są dostępne maszyny wirtualne w środowisku produkcyjnym.
- Wdrażane na podstawie platformy SAP HANA SAP systemów lokalnych.
- Wdrożonych systemów SAP na maszynach wirtualnych. Te systemy mogą być tworzenia/testowania piaskownicy, lub produkcji wystąpienia dla każdej aplikacji opartych na oprogramowanie SAP NetWeaver, które pomyślnie wdrożyć na platformie Azure (na maszynach wirtualnych), zgodnie z potrzebami zasobów zużycia i pamięci. Te systemy również może bazować na baz danych, takich jak SQL Server. Aby uzyskać więcej informacji, zobacz [SAP pomocy technicznej Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533/E). A te systemy mogą być oparte na baz danych, takich jak SAP HANA. Aby uzyskać więcej informacji, zobacz [platform IaaS z certyfikatem SAP HANA](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Wdrożone serwery aplikacji SAP na platformie Azure (na maszynach wirtualnych), korzystających z platformy SAP HANA na platformie Azure (duże wystąpienia) w sygnatury dużego wystąpienia platformy Azure.

Typowe jest hybrydowego środowiska SAP za pomocą co najmniej czterech różnych scenariuszy wdrażania. Istnieje również wiele przypadków klienta pełną SAP zapewniały realizację niezbędnych zadań, które działają na platformie Azure. W miarę bardziej wydajne maszyny wirtualne zwiększa liczby klientów, które przenoszą swoje rozwiązania SAP na platformie Azure.

Azure networking w kontekście systemów SAP wdrożonych na platformie Azure nie jest skomplikowane. Jest on oparty na następujące zasady:

- Sieci wirtualne platformy Azure musi być połączony z obwodem usługi ExpressRoute, który nawiązuje połączenie z siecią lokalną.
- Obwód usługi ExpressRoute, który nawiązuje połączenie środowiska lokalnego na platformę Azure zazwyczaj mają przepustowości 1 GB/s lub wyższej. Tej minimalnej przepustowości umożliwia odpowiedniej przepustowości do transferu danych między systemami lokalnymi i systemów, które są uruchamiane na maszynach wirtualnych. Umożliwia także odpowiedniej przepustowości dla połączenia z systemami Azure od użytkowników w środowisku lokalnym.
- Wszystkie systemy SAP na platformie Azure należy ustawić w sieciach wirtualnych, do komunikowania się ze sobą.
- Usługi Active Directory i DNS hostowanych lokalnie zostały rozszerzone na platformę Azure za pośrednictwem usługi ExpressRoute ze środowiska lokalnego.


> [!NOTE] 
> Z rozliczeń punktu widzenia tylko jedną subskrypcję platformy Azure można połączyć tylko jednej dzierżawy w sygnaturze dużych wystąpień, w określonym regionie platformy Azure. Z drugiej strony pojedynczej dzierżawy sygnatury duże wystąpienie można połączyć tylko jedną subskrypcję platformy Azure. To wymaganie jest spójna z innymi obiektami płatnych na platformie Azure.

Jeśli oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jest wdrażana w wielu różnych regionach platformy Azure, oddzielna dzierżawa jest wdrażany w sygnaturze dużego wystąpienia. Można uruchomić zarówno w ramach tej samej subskrypcji platformy Azure, tak długo, jak te wystąpienia są częścią tej samej środowiskiem SAP. 

> [!IMPORTANT] 
> Tylko wdrożenie usługi Azure Resource Manager jest obsługiwana przez oprogramowanie SAP HANA na platformie Azure (duże wystąpienia).

 

## <a name="additional-virtual-network-information"></a>Informacje dodatkowe sieci wirtualnej

Aby połączyć sieć wirtualną z usługi ExpressRoute, należy utworzyć bramę platformy Azure. Aby uzyskać więcej informacji, zobacz [o bramach sieci wirtualnej dla usługi ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Brama platformy Azure może służyć przy użyciu usługi ExpressRoute do infrastruktury spoza platformy Azure lub do sygnatury dużego wystąpienia platformy Azure. Brama platformy Azure można również nawiązać połączenia między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [skonfigurować połączenie do sieci dla usługi Resource Manager przy użyciu programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Brama platformy Azure do maksymalnie czterech różnych połączeń usługi ExpressRoute można połączyć, tak długo, jak te połączenia pochodzą z różnych routery graniczne enterprise firmy Microsoft. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Przepływność, brama platformy Azure zapewnia różni się dla obu przypadków użycia. Aby uzyskać więcej informacji, zobacz [VPN Gateway — informacje](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maksymalna przepływność, którą można uzyskać za pośrednictwem bramy sieci wirtualnej jest 10 GB/s za pośrednictwem połączenia usługi ExpressRoute. Kopiowanie plików między maszyną Wirtualną, która znajduje się w sieci wirtualnej i systemem lokalnego (jako strumień o pojedynczej kopii) nie osiągać pełną przepływność różne jednostki SKU bramy. Korzystanie z pełną przepustowość bramy sieci wirtualnej, należy użyć wielu strumieni. Lub należy skopiować różne pliki w strumieniach równoległe pojedynczego pliku.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla dużych wystąpień HANA
Architektura sieci dużych wystąpień HANA można podzielić na cztery elementy:

- Sieć lokalną i połączenie usługi ExpressRoute z platformą Azure. Ta część jest domeną klienta i jest połączony z platformą Azure za pośrednictwem usługi ExpressRoute. Zobacz prawym dolnym rogu na poniższej ilustracji.
- Usługi sieci platformy Azure, jak wspomniano, przy użyciu sieci wirtualnych, których ponownie bram. Ta część jest obszar, w którym trzeba znaleźć odpowiednie projekty dla wymagań aplikacji, zabezpieczeń i zgodności z wymaganiami. Czy są używane duże wystąpienie oprogramowania HANA jest inny punkt do należy wziąć pod uwagę pod względem liczby sieci wirtualnych i jednostki SKU bramy platformy Azure do wyboru. Zobacz prawym górnym rogu na rysunku.
- Łączność z dużych wystąpień HANA za pomocą usługi ExpressRoute technologii na platformie Azure. Ta część jest wdrażane i obsługiwane przez firmę Microsoft. To wszystko, co należy zrobić, podaj niektóre zakresy adresów IP po wdrożeniu Twoich zasobów w dużych wystąpień HANA połączenia obwodu usługi ExpressRoute z sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Sieć w dużych wystąpień HANA, która jest głównie przezroczyste dla Ciebie.

![Sieć wirtualna połączona z platformą SAP HANA na platformie Azure (duże wystąpienia) i w środowisku lokalnym](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Wymóg, aby połączyć z zasobów lokalnych za pośrednictwem usługi ExpressRoute na platformie Azure nie powoduje zmiany, ponieważ używasz dużych wystąpień HANA. Wymóg posiadania jednego lub wielu sieci wirtualnych, które uruchamiania maszyn wirtualnych, które hostują warstwy aplikacji, który nawiązuje połączenie z wystąpień HANA hostowanych w jednostkach dużych wystąpień HANA, również nie ulegnie zmianie. 

Dostępne są następujące różnice, by wdrożenia SAP na platformie Azure:

- Jednostki dużych wystąpień HANA dzierżawy klienta są połączone za pośrednictwem innego obwodu usługi ExpressRoute w sieciach wirtualnych. Do oddzielania warunkach obciążenia, lokalnej sieci wirtualnej usługi ExpressRoute łączy i łącza między sieciami wirtualnymi i dużych wystąpień HANA nie udostępniaj tego samego routery.
- Przechowujących nieregularnie między warstwą aplikacji SAP i HANA, duże wystąpienie jest innego rodzaju, z wielu małych żądań i bursts, takie jak transfer danych (zestawy wyników) z platformy SAP HANA w warstwie aplikacji.
- Architektura aplikacji SAP jest bardziej wrażliwy na opóźnienia sieci niż typowych scenariuszy, w którym dane są wymieniane między lokalną i platformą Azure.
- Brama sieci wirtualnej ma co najmniej dwóch połączeń usługi ExpressRoute. Zarówno połączenia współużytkują maksymalna przepustowość dla danych przychodzących bramy sieci wirtualnej.

Opóźnienie sieci reprezentatywne między maszynami wirtualnymi i dużych wystąpień HANA jednostki mogą być większe niż typowy opóźnienie Rundy sieci maszyny Wirtualnej do maszyny Wirtualnej. Zależy od regionu platformy Azure, wartości mierzone może przekroczyć opóźnienie obustronne ms 0,7 sklasyfikowane jako poniżej średniej w [1100926 # Uwaga SAP — często zadawane pytania: Wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). Zależą od regionu platformy Azure oraz narzędzia do mierzenia obustronne opóźnienie sieciowe między maszyną Wirtualną platformy Azure i jednostka dużych wystąpień HANA, mierzonego opóźnienie można maksymalnie i wokół 2 milisekund. Niemniej jednak klienci wdrażać aplikacje SAP oparte na oprogramowanie SAP HANA produkcyjne pomyślnie na duże wystąpienie SAP HANA. Upewnij się, że należy dokładnie przetestować procesy biznesowe w dużych wystąpień HANA platformy Azure.
 
Aby zapewnić deterministyczne opóźnienie między maszynami wirtualnymi i dużych wystąpień HANA, wybór bramy sieci wirtualnej jednostka SKU jest niezbędne. W przeciwieństwie do wzorców ruchu między magazynami lokalnymi i maszynami wirtualnymi wzorzec ruchu między maszynami wirtualnymi i dużych wystąpień HANA można tworzyć małe, ale wysokie wzrosty żądania i dane woluminów, które mają być przekazywane. Aby obsłużyć takie wzrosty dobrze, zdecydowanie zalecamy użycie jednostki SKU bramy UltraPerformance. W klasie typu II jednostek SKU platformy HANA duże wystąpienie użycie jednostki SKU bramy UltraPerformance jako brama sieci wirtualnej jest obowiązkowe.

> [!IMPORTANT] 
> Biorąc pod uwagę ogólną ruch sieciowy między aplikacji SAP i warstwy bazy danych, tylko HighPerformance lub jednostki SKU bramy UltraPerformance dla sieci wirtualnych są obsługiwane w przypadku nawiązywania połączenia z platformą SAP HANA na platformie Azure (duże wystąpienia). Brama sieci wirtualnej platformy HANA dużych wystąpień jednostek SKU typu II, obsługiwane są tylko jednostki SKU bramy UltraPerformance.



## <a name="single-sap-system"></a>Pojedynczy system SAP

Infrastruktury lokalnej z wcześniejszymi jest połączony za pośrednictwem usługi ExpressRoute na platformie Azure. Obwód usługi ExpressRoute łączy do router brzegowy przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [ExpressRoute — opis techniczny](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po nawiązaniu trasy łączy do sieci szkieletowej platformy Azure, a wszystkie regiony platformy Azure są dostępne.

> [!NOTE] 
> Aby uruchomić krajobrazów SAP na platformie Azure, nawiązać najbliżej regionu platformy Azure w orientacji poziomej SAP router brzegowy przedsiębiorstwa. Azure sygnatury dużych wystąpień są połączone za pośrednictwem urządzenia routera brzegowe dedykowanej, enterprise, aby zminimalizować opóźnienie sieci między maszynami wirtualnymi w modelu IaaS platformy Azure i duże wystąpienie sygnatury.

Brama sieci wirtualnej dla maszyn wirtualnych, w których są hostowane wystąpienia aplikacji SAP jest połączona z obwodem usługi ExpressRoute. Tej samej sieci wirtualnej jest połączony z router brzegowy przedsiębiorstwa oddzielnych dedykowanych do nawiązywania połączenia z sygnaturami dużego wystąpienia.

Ten system jest prosty przykład pojedynczego systemu SAP. Warstwa aplikacji SAP jest hostowany na platformie Azure. Uruchamia bazy danych SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia). Zakłada się, że przepustowość bramy sieci wirtualnej 2 GB lub 10 GB/s przepływności nie stanowią "wąskie gardło".

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

Wiele systemów SAP lub dużych systemów SAP są wdrażane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia), mogą stać się wąskim gardłem przez przepływność bramy sieci wirtualnej. W takim przypadku należy podzielić warstw aplikacji na wielu sieci wirtualnych. Można również utworzyć specjalne sieci wirtualnej, która łączy się z dużych wystąpień HANA w przypadkach, takich jak:

- Wykonywanie kopii zapasowych bezpośrednio w wystąpieniach oprogramowania HANA w dużych wystąpień HANA do maszyny Wirtualnej na platformie Azure, która hostuje udziały NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z dużych wystąpień HANA jednostek do miejsca na dysku zarządzanego na platformie Azure.

Użyj oddzielnych sieci wirtualnej do hosta maszyny wirtualne, które zarządzania magazynem. To rozwiązanie pozwala uniknąć dużych plików lub przesyłanie danych z dużych wystąpień HANA na platformie Azure w bramie sieci wirtualnej, pełniącą maszyn wirtualnych działających w warstwie aplikacji SAP. 

Aby uzyskać bardziej Skalowalna architektura sieci:

- Korzystać z wielu sieci wirtualnych w jednym, większy warstwy aplikacji SAP.
- Wdrożyć oddzielny jednej sieci wirtualnej dla każdego systemu SAP wdrożone, w porównaniu do łączenia tych systemów SAP w oddzielnych podsieciach w ramach tej samej sieci wirtualnej.

  Bardziej Skalowalna architektura sieci dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

![Wdrażanie warstwy aplikacji SAP za pośrednictwem wielu sieci wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

Na rysunku przedstawiono warstwy aplikacji SAP, czyli składników, wdrażanych za pośrednictwem wielu sieci wirtualnych. Ta konfiguracja wprowadzona nieuniknione opóźnienie, który wystąpił podczas komunikacji między aplikacjami, hostowane w tych sieciach wirtualnych. Domyślnie ruch sieciowy między maszynami wirtualnymi w różnych sieciach wirtualnych Rozsyłanie za pomocą routerami granicznymi enterprise w tej konfiguracji. Sposób, aby optymalizować i zmniejszyć opóźnienia w komunikacji między dwiema sieciami wirtualnymi jest komunikacji równorzędnej sieci wirtualnych w tym samym regionie. Ta metoda działa, nawet jeśli te sieci wirtualne należą do różnych subskrypcji. Za pomocą komunikacji równorzędnej sieci wirtualnej, komunikacja między maszynami wirtualnymi w dwóch różnych sieci wirtualnych można użyć sieci szkieletowej sieci platformy Azure może komunikować się bezpośrednio ze sobą. Opóźnienie pokazuje tak, jakby maszyny wirtualne znajdują się w tej samej sieci wirtualnej. Ruch, odnoszący się do zakresów adresów IP, które są połączone za pośrednictwem bramy sieci wirtualnej platformy Azure odbywa się za pośrednictwem bramy sieci wirtualnej poszczególnych sieci wirtualnej. 

Aby uzyskać więcej informacji na temat komunikacji równorzędnej sieci wirtualnej, zobacz [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing na platformie Azure

Trzy routingu zagadnienia dotyczące sieci są ważne w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) są dostępne wyłącznie za pośrednictwem maszyny wirtualne i dedykowane połączenie usługi ExpressRoute nie są bezpośrednio ze środowiska lokalnego. Bezpośredni dostęp ze środowiska lokalnego do jednostek duże wystąpienie oprogramowania HANA w dostarczonym przez firmę Microsoft, nie jest możliwe natychmiast. Przechodnie ograniczenia routingu są spowodowane przez bieżący architektury sieci platformy Azure używane do duże wystąpienie SAP HANA. Niektórzy klienci administracji i wszelkie aplikacje, które muszą bezpośredni dostęp, takich jak SAP rozwiązania Manager uruchomiony w środowisku lokalnym, nie można nawiązać połączenia bazy danych SAP HANA.

* W przypadku dużych wystąpień HANA jednostki wdrożona w dwóch różnych regionach platformy Azure w celu odzyskiwania po awarii, mają zastosowanie te same ograniczenia routingu przejściowy. Innymi słowy adresy IP jednostki duże wystąpienie oprogramowania HANA w jednym regionie (na przykład zachodnie stany USA) nie są kierowane do jednostki dużych wystąpień HANA, wdrożyć w innym regionie (na przykład wschodnie stany USA). To ograniczenie jest niezależny od użycia sieci platformy Azure komunikację równorzędną między regionami lub wielu łączenie obwody usługi ExpressRoute jednostki dużych wystąpień HANA nawiązać połączenie z sieciami wirtualnymi. Aby uzyskać graficzną reprezentację zobacz rysunek w sekcji "Dużych wystąpień HANA użycia jednostek w wielu regionach." To ograniczenie, którego dołączono wdrożonej architektury, zabrania natychmiastowe korzystanie z replikacji systemu HANA jako funkcja odzyskiwania po awarii.

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jednostki ma przypisanego adresu IP z zakresu adresów puli adresów IP serwera, przesłane. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pośrednictwem subskrypcji platformy Azure i usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnych do platformy HANA na platformie Azure (duże wystąpienia). Przypisany adres IP z zakresu adresów puli adresów IP serwera jest przypisane bezpośrednio do jednostki sprzętu. Ma ona *nie* przypisane przy użyciu translatora adresów Sieciowych, jak w przypadku pierwszego wdrożenia tego rozwiązania. 

> [!NOTE]
> Aby wyeliminować ograniczeń w routingu przejściowy, jak wyjaśniono w elementy: pierwsze dwie listy, należy użyć dodatkowych składników routingu. Składniki, które mogą służyć do pokonania ograniczenia może być:
> 
> * Zwrotnego serwera proxy do kierowania danych do i z. Na przykład F5 BIG-IP, serwer NGINX z usługą Traffic Manager wdrożonych na platformie Azure jako rozwiązanie zapory/ruch wirtualnej routingu.
> * Za pomocą [reguły IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) na maszynie wirtualnej systemu Linux, aby umożliwić routing między lokalizacjami lokalnymi i dużych wystąpień HANA jednostki lub między jednostkami duże wystąpienie oprogramowania HANA w różnych regionach.
> 
> Należy pamiętać, że wdrożenia i obsługę niestandardowego rozwiązania innych firm urządzeń sieciowych lub IPTables nie jest obsługiwane przez firmę Microsoft. Obsługa musi być podana przez dostawcę składnik używany lub integrator. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Łączność z Internetem z dużych wystąpień HANA
Duże wystąpienie oprogramowania HANA jest *nie* mieć bezpośrednie połączenie z Internetem. Na przykład to ograniczenie może ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio z dostawcą systemu operacyjnego. Może być konieczne z lokalnego serwera SUSE Linux Enterprise Server subskrypcji zarządzania narzędzia lub Red Hat Enterprise Linux subskrypcji Menedżera.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi i dużych wystąpień HANA
Dane przesyłane między dużych wystąpień HANA i maszyny wirtualne nie są szyfrowane. Jednak służyć wyłącznie do programu exchange między po stronie platformy HANA DBMS i aplikacje oparte na JDBC/ODBC, można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tej dokumentacji SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Użyj jednostki duże wystąpienie oprogramowania HANA w wielu regionach

Konieczne może być powodów wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia) w wielu regionach platformy Azure innych niż do odzyskiwania po awarii. Chcecie dostępu do dużych wystąpień HANA w każdej z maszyn wirtualnych wdrożonych w różnych sieciach wirtualnych w regionach. Adresy IP przypisane do różnych jednostek dużych wystąpień HANA nie są przenoszone poza sieci wirtualne, które są podłączone bezpośrednio za pośrednictwem ich bramy do wystąpień. W rezultacie to niewielkie zmiany wprowadzane do projektu sieci wirtualnej. Brama sieci wirtualnej może obsługiwać czterech różnych obwodów usługi ExpressRoute z routerami granicznymi różnych enterprise. Każdej sieci wirtualnej podłączonej do jednej z sygnaturami duże wystąpienie może być połączona z sygnaturą duże wystąpienie w innym regionie platformy Azure.

![Sieć wirtualna połączona sygnatury dużego wystąpienia platformy Azure w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Na rysunku przedstawiono sposób połączenia różnych sieciach wirtualnych w obu regionach do dwóch różnych obwodów usługi ExpressRoute, które są używane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia), w obu regionach platformy Azure. Nowo wprowadzonych połączenia są prostokątne czerwone linie. W przypadku tych połączeń z sieciami wirtualnymi maszyn wirtualnych uruchomionych w jednym z tych sieci wirtualnych są dostępne różne jednostki dużych wystąpień HANA wdrożona w dwóch regionach. Jak pokazano na rysunku, zakłada się, że masz dwa połączenia usługi ExpressRoute ze środowiska lokalnego do dwóch regionach platformy Azure. Taki układ jest zalecane w przypadku przyczyny odzyskiwania po awarii.

> [!IMPORTANT] 
> Jeśli używasz wielu obwodów usługi ExpressRoute, ustawienia lokalnej preferencji BGP i dołączania ścieżki AS powinny służyć do zapewnienia prawidłowego przekierowania ruchu.

**Następne kroki**
- Zapoznaj się [architektury magazynu oprogramowania SAP HANA (duże wystąpienia)](hana-storage-architecture.md)