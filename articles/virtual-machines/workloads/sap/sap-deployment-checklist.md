---
title: Lista planowania i wdrażania obciążeń SAP | Dokumentacja firmy Microsoft
description: Lista kontrolna dotycząca planowania i wdrażania obciążeń SAP na platformie Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fef2d42282291bb0ea6afeea03e60234d3d47a4d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878727"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Obciążeń SAP na Azure Lista kontrolna dotycząca planowania i wdrażania 

Ta lista kontrolna jest przeznaczona dla klientów przenoszących ich oprogramowanie SAP NetWeaver, S/4HANA i Hybris aplikacji do infrastruktury platformy Azure jako usługa.  Ta lista kontrolna, należy poddać przeglądowi przez klienta i/lub partnera SAP w czasie trwania projektu. Jest ważne, wiele testy są przeprowadzane na początku projektu i w fazie planowania. Po zakończeniu wdrożenia podstawowe zmiany na wdrożonym infrastruktury platformy Azure lub wersji oprogramowania SAP, może być skomplikowane. Przejrzeć niniejszą listę kontrolną na kluczowe punkty kontrolne w całym projekcie.  Może zostać wykryte drobne problemy, zanim obejmą duże i wystarczająco dużo czasu istnieje, aby ponownie opracowywać i testować wszystkie wymagane zmiany. Lista kontrolna nie względami oświadczeń zakończenie. Zależy od konkretnej sytuacji poszczególnych, może istnieć wiele więcej testów, które muszą być przeprowadzane. 

Listy kontrolnej, zebranych nie zawiera zadania, które są niezależne od platformy Azure.  Przykład: SAP zmiany interfejsy aplikacji podczas przenoszenia do chmury publicznej platformy Azure lub dostawcy usług hostingowych.    

Ta lista kontrolna może również dla już wdrożonych systemów. Nowe funkcje takie jak akceleratorem zapisu, strefy dostępności i nowych typów maszyny Wirtualnej mogły zostać dodane, ponieważ została wdrożona.  W związku z tym zaleca się przejrzenie listy kontrolnej okresowo, aby upewnić się, że masz świadomość nowych funkcji na platformie Azure. 

## <a name="project-preparation-and-planning-phase"></a>Projekt przygotowania i fazy planowania
W tej fazie migracji obciążeń SAP do chmury publicznej platformy Azure jest planowane. Minimalny zestaw obiektów i elementów omówionych i zdefiniowane listy, takich jak:

1. Dokument projektowy wysokiego poziomu — w tym dokumencie powinny zawierać:
    1. Bieżący spis aplikacji i składniki SAP oraz Magazyn aplikacji docelowej, na platformie Azure
    2. Utwórz i trzymać się kolejności odpowiedzialność przypisania macierzy (RACI) definiujący zakres odpowiedzialności i przydziały różnych zainteresowanych stron. Uruchom na wysokim poziomie i działać na bardziej szczegółowym poziomów przepływności pierwszy i planowania wdrożenia
    2. Architektura wysokiego poziomu rozwiązania.
    3. Decyzja w sprawie regiony platformy Azure do wdrożenia w. Aby uzyskać listę regionów świadczenia usługi Azure, sprawdź [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Usługi jest dostępny we wszystkich regionach platformy Azure, można znaleźć w artykule [produktów dostępnych na region](https://azure.microsoft.com/global-infrastructure/services/)
    4. Sieć architektury, aby połączyć się ze środowiska lokalnego na platformę Azure. Rozpocznij z siebie uczynić zapoznać się z [planu wirtualnego centrum danych platformy Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Zasady zabezpieczeń dla uruchamiania danych znaczenie biznesowe wysoki na platformie Azure. Do odczytywania materiału rozpoczyna się od [dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/)
2.  Projekt dokumentu technicznego — która zawiera:
    1.  Diagram blokowy rozwiązania 
    2.  Zmiana rozmiaru obliczeń, magazynowania i składników sieciowych na platformie Azure. Aby uzyskać SAP rozmiarów maszyn wirtualnych platformy Azure, zapoznaj się uwagę techniczną [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Architektura ciągłość biznesową i odzyskiwanie po awarii
    4.  Szczegółowe systemu operacyjnego, bazy danych, jądra i SAP obsługuje wersje pakietu. Nie biorąc pod uwagę, że dowolnej wersji systemu operacyjnego, który jest obsługiwany przez oprogramowanie SAP NetWeaver i S/4HANA, jest obsługiwane w maszynach wirtualnych platformy Azure. Dotyczy to także wersji systemu DBMS. Jest to konieczne, że przed wyrównywanie i w razie potrzeby uaktualnić wersje SAP, wersji systemu DBMS lub wersji systemu operacyjnego, aby w systemie SAP zaznaczyć Pobierz następujące źródła i Azure obsługiwane w oknie. Jest to konieczne, że jesteś w obrębie SAP i platformy Azure obsługiwane kombinacje wersji, aby uzyskać pełną pomoc techniczną, SAP i Microsoft. Jeśli to konieczne, należy zaplanować dla uaktualnienie niektórych składników oprogramowania. Szczegółowe informacje na temat obsługiwanych oprogramowania SAP, systemu operacyjnego i DBMS jest udokumentowany w następujących lokalizacjach:
        1.  Uwaga pomocy technicznej SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Ta uwaga definiuje minimalnej wersji systemu operacyjnego, obsługiwane w maszynach wirtualnych platformy Azure. Definiuje również wersje minimalna bazy danych wymagane dla większości innych HANA bazy danych. Notatki prezentuje też rozmiaru SAP różnych typów maszyn wirtualnych platformy Azure obsługiwane SAP.
        2.  Uwaga pomocy technicznej SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619). Notatki definiuje macierz obsługi bazy danych Oracle na platformie Azure. Należy pamiętać, że bazy danych Oracle obsługuje tylko Windows i Oracle Linux jako system operacyjny gościa na platformie Azure w przypadku obciążeń SAP. Dotyczy to stwierdzenie systemem SAP wystąpienia warstwy aplikacji SAP. Jednak Oracle nie obsługuje wysoką dostępność dla usług SAP Central Services w systemie Oracle Linux przez program Pacemaker. Jeśli potrzebujesz wysokiej dostępności dla ASCS w systemie Oracle Linux, musisz wykorzystać pakiet ochronny oprogramowanie SIOS dla systemu Linux. Szczegółowe dane certyfikacji SAP na ten temat można znaleźć w pomocy technicznej uwagę [1662610 # - szczegóły pomocy technicznej, oprogramowanie SIOS Suite Protection dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610). Windows SAP obsługiwanych tryb Failover klastra pracy awaryjnej Windows rozwiązania usług SAP Central Services jest obsługiwane w połączeniu z bazą danych Oracle jako warstwa DBMS. 
        3.  Uwaga pomocy technicznej SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) można pobrać macierzy obsługi dla wersji oprogramowania SAP HANA w różnych systemach operacyjnych
        4.  SAP HANA obsługiwanych maszynach wirtualnych platformy Azure i [dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) należą [tutaj](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Macierz dostępności produktów SAP](https://support.sap.com/en/)
        6.  Inne uwagi SAP dla innych produktów SAP  
    5.  Zalecamy ścisłe projekty 3-warstwowej dla systemów produkcyjnych SAP. Łącząc ASCS + aplikacji serwerów w tej samej maszyny Wirtualnej nie jest zalecane.  Za pomocą konfiguracje klastra — wiele identyfikatorów SID dla usług SAP Central Services jest obsługiwana przy użyciu Windows jako systemu operacyjnego gościa na platformie Azure. Natomiast konfiguracje klastra — wiele identyfikatorów SID usług SAP Central Services nie są obsługiwane w systemach operacyjnych Linux na platformie Azure. Dokumentacja w przypadku systemu operacyjnego gościa Windows można znaleźć w:
        1.  [SAP ASCS/SCS wystąpienia — wiele identyfikatorów SID wysokiej dostępności z systemu Windows Server Failover Clustering i udostępnionego dysku na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS wystąpienia — wiele identyfikatorów SID wysokiej dostępności z systemu Windows Server Failover Clustering i udział plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Wysoka dostępność i odzyskiwanie po awarii architektury odzyskiwania
        1.  Definiowanie zależności od RTO i RPO wysoką dostępność i odzyskiwanie po awarii architektury odzyskiwania musi wyglądać
        2.  Wysokiej dostępności w ramach tej samej strefie Sprawdź, co żądany DBMS ma do zaoferowania na platformie Azure. Większość DBMS oferują synchroniczne metody synchronicznej rezerwy, które firma Microsoft zaleca dla systemów produkcyjnych. Również Sprawdź SAP związane z dokumentacji dotyczącej różnych baz danych, rozpoczynając od [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) i powiązanych dokumentów
            1.  Przy użyciu usługę klastra pracy awaryjnej Windows za pomocą konfiguracji dysku udostępnionego dla warstwy DBMS, jak na przykład opisane dla programu SQL Server [tutaj](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) jest **nie** obsługiwane. Zamiast tego rozwiązania, takie jak:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replikacji systemu HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Do odzyskiwania po awarii w różnych regionach platformy Azure Sprawdź, jakie możliwości są oferowane przez różnych dostawców DBMS. Większość z nich obsługuje replikację asynchroniczną ani wysyłania dziennika
        4.  Dla warstwy aplikacji SAP należy określić, czy należy uruchomić usługi biznesowe regresji systemy testowe, które są najlepiej replik wdrożenia produkcyjne, w tym samym regionie platformy Azure lub w Twoim regionie odzyskiwania po awarii. W drugim przypadku możesz wybrać docelową tego systemu regresji firm jako cel odzyskiwania po awarii dla w środowisku produkcyjnym
        5.  Jeśli zdecydujesz się na umieszczenie systemów nieprodukcyjnych w lokacji odzyskiwania po awarii, Szukaj w usłudze Azure Site Recovery jako możliwego do użycia metody replikowania warstwie aplikacji SAP w regionie odzyskiwania po awarii z platformy Azure. Zobacz też [Konfigurowanie odzyskiwania po awarii dla wdrożenia obejmujące wiele warstw aplikacji SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Jeśli zdecydujesz się używać połączone konfiguracji o wysokiej dostępności i odzyskiwania po awarii, wykorzystując [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) przyznać sobie zapoznać się z regionów platformy Azure w przypadku, gdy dostępnych stref dostępności i z ograniczeniami, które mogą zostać wprowadzony przez opóźnienia sieciowe większą od dwóch strefach dostępności  
3.  Klientowi i partnerowi, należy utworzyć spis wszystkich interfejsów SAP (SAP i inne niż SAP). 
4.  Projekt projekt usług Foundation — ten projekt obejmuje elementy, takie jak
    1.  Projektowanie usługi Active Directory i DNS
    2.  Topologia sieci w systemie Azure lub przypisanie różnych systemów SAP
    3.  [Dostęp oparty na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) struktury dla zespołów różnych zarządzających infrastrukturą i aplikacjami SAP na platformie Azure
    3.  Topologia grupy zasobów 
    4.  [Strategia znakowania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Nazewnictwa, Konwencji dla maszyn wirtualnych i innych składników infrastruktury i/lub nazwy logiczne
5.  Umowę na pomoc techniczną Premium firmy Microsoft — zidentyfikować Menedżera konta MS Technical Preview (TAM). Do obsługi wymagań SAP odczytu Uwaga pomocy technicznej SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Umożliwia zdefiniowanie liczby subskrypcji platformy Azure i limit przydziału rdzeni dla różnych subskrypcji. [Otwierać żądania pomocy technicznej, aby zwiększyć limity przydziału subskrypcji platformy Azure,](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) zgodnie z potrzebami 
7.  Redukcja danych i migrację danych Zaplanuj migrację danych SAP na platformie Azure. Dla systemów SAP NetWeaver SAP ma wskazówki dotyczące sposobu na utrzymanie woluminu dużej liczby danych jest ograniczony. SAP opublikowane [przewodniku głęboki](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) o zarządzanie danymi w usłudze systemów SAP ERP. Jednak część zawartości ma zastosowania do systemów NetWeaver i S/4HANA, ogólnie rzecz biorąc.
8.  Definiowanie i zdecyduj, metoda automatycznego wdrażania. Celem usługi automation za wdrożenia infrastruktury na platformie Azure jest do wdrożenia w sposób deterministyczny i uzyskuj wyniki deterministyczna. Wielu klientów używa powłoki Power Shell lub interfejsu wiersza polecenia, na podstawie skryptów. Istnieją różne technologie typu open source, które mogą służyć do wdrażania infrastruktury platformy Azure dla rozwiązania SAP i nawet instalować oprogramowanie SAP. Przykłady można znaleźć w witrynie GitHub:
    1.  [Wdrożeń zautomatyzowanych SAP w chmurze platformy Azure](https://github.com/Azure/sap-hana)
    2.  [Instalacja programu SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Zdefiniuj regularne projektu i tempa Przegląd wdrażania między Tobą jako klient korzystający z integratora systemów, Microsoft i inne zaangażowane strony

 
## <a name="pilot-phase-strongly-recommended"></a>Fazy pilotażowej (zdecydowanie zalecane)
 
Przed lub w sposób równoległy do projektu, planowania i przygotowania, można uruchomić programu pilotażowego. Fazy może również służyć do testowania, metod i projektowania, wprowadzone w fazie planowania i przygotowywania. Fazy pilotażowej może być rozciągnięty do rzeczywistego weryfikacji koncepcji. Zaleca się konfigurowanie i weryfikowanie pełne rozwiązanie o wysokiej dostępności i odzyskiwania po awarii oraz projektowania zabezpieczeń podczas wdrożenia pilotażowego. W niektórych przypadkach klient skalowalność badania również może zostać przeprowadzone na tym etapie. Pozostali klienci powinni używać wdrożenie systemów piaskownicy SAP jako fazy pilotażowej. Dlatego przyjęto założenie, że wszystkie podane systemu, w którym chcesz przeprowadzić migrację na platformę Azure na potrzeby programu pilotażowego.

1. Optymalizuj transferu danych na platformę Azure. Stopniu zależą od klienta przypadków transfer za pośrednictwem [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ze środowiska lokalnego zostało najszybszy, jeśli próba zawierała wystarczającą przepustowość obwodu usługi Express. Z innymi klientami przechodzenia przez internet się okazało, aby szybciej
2. W przypadku migracji heterogenicznej platformy SAP, który obejmuje eksportowanie i importowanie danych w bazie danych, przetestować, optymalizacji eksportowania i importowania fazy. Dla dużych migracji dotyczących programu SQL Server jako platformy docelowej, można znaleźć zaleceń [tutaj](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Podejście migracji Monitor/SWPM można wykonać w przypadku, gdy nie ma potrzeby uaktualniania wersji połączone lub [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) przetwarzania podczas łączenia migracji uaktualnianie wersji SAP i spełnienia pewnych źródłowa i docelowa platforma DBMS kombinacje, zgodnie z opisem, na przykład w [opcji migracji bazy danych (DMO) z SUMĄ 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Eksportuj do źródła, przekazywanie pliku eksportu do platformy Azure i importowania wydajności.  Maksymalizuj nakładanie się eksportu i importu
   2.  Oceń objętości bazy danych między obiekt docelowy oraz docelowy platformy, aby odzwierciedlały zmiany rozmiaru infrastruktury    
   3.  Sprawdzanie poprawności i zoptymalizować chronometrażu 
3. Sprawdzanie poprawności Technical Preview 
   1. Typy maszyn wirtualnych
      1.  Sprawdź poprawność zasobów na informacje o pomocy technicznej SAP, SAP HANA sprzętu katalogu i SAP PAM ponownie, aby upewnić się, że było żadnych zmian na obsługiwanych maszynach wirtualnych platformy Azure obsługiwane wersje systemu operacyjnego dla tych typów maszyn wirtualnych i obsługiwane wersje SAP i DBMS
      2.  Ponownie sprawdź rozmiar aplikacji i infrastruktury, wdrażanej na platformie Azure. W przypadku przenoszenia istniejących aplikacji, często można uzyskać niezbędnych protokoły SAP z infrastrukturą użyjesz i [SAP testów wydajności strony sieci Web](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) i porównać go z listy numerów protokoły SAP w nocie pomocy technicznej SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Przechowuje też [w tym artykule](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) pamiętać
      3.  Oceny i testowania zmiany rozmiaru maszyn wirtualnych platformy Azure dotyczące przepływności pamięci masowej i przepływność sieci o różnych typach maszyn wirtualnych, który został wybrany w fazie planowania. Dane można znaleźć w:
          1.  [Rozmiary maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Należy wziąć pod uwagę **maksymalnej bez buforowania przepustowości dysku** w przypadku ustalania rozmiaru
          2.  [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) należy wziąć pod uwagę **maksymalnej bez buforowania przepustowości dysku** w przypadku ustalania rozmiaru
   2. Magazyn
      1.  Użyj [SSD w warstwie standardowa usługi Azure storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) minimalną dla maszyn wirtualnych reprezentujących warstw aplikacji SAP i innych wydajności poufnych wdrażania systemu DBMS
      2.  Zalecamy, aby nie korzystała z [dysków standardowych dysków Twardych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) ogólnie rzecz biorąc
      2.  Użyj [usługi Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) dla maszyn wirtualnych systemu DBMS, które zdalnie są wrażliwe na wydajność
      2.  Użyj [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Akcelerator zapisu platformy Azure na użytek dyski dziennika systemu DBMS z serii M. Należy pamiętać o limity akcelerator zapisu i użycia zgodnie z opisem w [akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Dla różnych typów DBMS, sprawdź [ogólnego SAP pokrewnej dokumentacji DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) i dokumentacje dotyczącą określonych DBMS dokumentu ogólnego punktów do
      5.  For SAP HANA i szczegółowe informacje są udokumentowane w artykule [konfiguracje infrastruktury SAP HANA i operacji na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Nigdy nie instalowanie usługi Azure dysków z danymi na maszynie Wirtualnej systemu Linux platformy Azure przy użyciu identyfikatora urządzenia. Zamiast tego należy użyć powszechnie Unikatowy identyfikator (UUID). Użyj narzędzi graficznych z dyskami danych Instalowanie usługi Azure, na przykład, należy zachować ostrożność. Dokładnie sprawdź wpisy w/etc/fstab, aby upewnić się, czy dyski są instalowane przy użyciu identyfikator UUID
          1.  Więcej szczegółów można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Networking
      1.  Przetestuj i oceń infrastruktury sieci wirtualnej i stopień rozproszenia swoje aplikacje SAP w lub w różnych sieciach wirtualnych platformy Azure
          1.  Podejście Centrum i architektura gwiazdy na sieć wirtualną lub microsegmentation w ramach jednej sieci wirtualnej platformy Azure na podstawie oceny
              1.  Koszty ze względu na wymianę danych między [nawiązać komunikacji równorzędnej między sieciami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Koszty można znaleźć [cennik usługi Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Zaletą fast rozłączyć komunikacji równorzędnej między sieciami wirtualnymi platformy Azure w porównaniu ze zmian sieciowej grupy zabezpieczeń w celu odizolowania podsieci w sieci wirtualnej w przypadkach, w którym aplikacje lub maszyny wirtualne hostowane w podsieci sieci wirtualnej stało się to zagrożenie bezpieczeństwa
              3.  Centralna rejestrowanie i przeprowadzanie inspekcji ruchu sieciowego między w środowisku lokalnym, światem i wirtualne centrum danych, do konstruowania na platformie Azure
          2.  Oceny i testowania ścieżki danych między aplikacji SAP i SAP DBMS warstwy. 
              1.  Wszelkie położenie [wirtualne urządzenia sieciowe Azure](https://azure.microsoft.com/solutions/network-appliances/) ścieżki komunikacji między aplikacji SAP i warstwy system DBMS, oprogramowanie SAP NetWeaver, Hybris lub S/4HANA systemów SAP nie jest obsługiwany na wszystkich
              2.  Wprowadzenie do warstwy aplikacji SAP i SAP DBMS w różnych sieciach wirtualnych platformy Azure, które nie są równorzędne nie jest obsługiwane.
              3.  [Reguły ASG i sieciowej grupy zabezpieczeń usługi Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) są obsługiwane w przypadku definiowania tras między warstwy aplikacji SAP i SAP DBMS warstwy
          3.  Upewnij się, że [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszyny wirtualne na warstwie aplikacji SAP i SAP DBMS warstwy. Należy pamiętać, że różne poziomy systemu operacyjnego są potrzebne do obsługi przyspieszonej sieci na platformie Azure:
              1.  Windows Server 2012 R2 lub nowszych wersjach
              2.  SUSE Linux 12 z dodatkiem SP3 lub nowszej wersji
              3.  RHEL w wersji 7.4 lub nowszej wersji
              4.  Oracle Linux w wersji 7.5. Jądro RHCKL wersji musi być 3.10.0-862.13.1.el7. Za pomocą Oracle UEK jądra w wersji 5 jest wymagana
          4.   Przetestuj i oceń opóźnienie sieciowe między warstwy aplikacji SAP maszyn wirtualnych i maszyn wirtualnych z systemem DBMS zgodnie z Uwaga pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i zwróć uwagę, pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Ocena wyników na podstawie wskazówek dotyczących opóźnienia sieci notatki pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinna należeć do zakresu moderuj i dobre. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i dużych wystąpień HANA jednostek zgodnie z opisem [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Upewnij się, że wdrożenia wewnętrznego modułu równoważenia obciążenia są konfigurowane do użycia bezpośredniego zwrotu serwera. To ustawienie będzie zmniejszyć opóźnienia w przypadkach, w której Ilb platformy Azure są używane w konfiguracjach wysokiej dostępności na warstwie systemu DBMS
          6.   Jeśli używasz usługi Azure Load Balancer w połączeniu z systemu Linux gościa systemów operacyjnych sprawdzanie, czy Linux sieci parametr **net.ipv4.tcp_timestamps** ustawiono **0**. Z zaleceniami w starszych wersjach programu SAP Pamiętaj [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Uwaga SAP w międzyczasie aktualizowany w celu odzwierciedlenia fakt, że parametr musi być równa 0 działają w połączeniu z modułami równoważenia obciążenia platformy Azure.
   4. Wysoka dostępność i odzyskiwanie po awarii odzyskiwania wdrożenia. 
      1. W przypadku warstwy aplikacji SAP wdrożenia bez definiowania określonej strefy dostępności platformy Azure, upewnij się, że wszystkie maszyny wirtualne uruchomione wystąpienie okna dialogowego SAP lub wystąpienia oprogramowania pośredniczącego pojedynczego systemu SAP są wdrażane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   W przypadku, gdy nie wymagają wysokiej dostępności dla usług SAP Central Services i DBMS, te maszyny wirtualne można wdrożyć na tym samym zestawie dostępności jako warstwa aplikacji SAP
      2. Jeśli chronisz usług SAP Central Services i warstwy system DBMS wysokiej dostępności z replikami pasywny ma dwa węzły dla usług SAP Central Services w jedną osobnym zestawie dostępności i dwoma węzłami systemu DBMS w innym zestawie dostępności
      3. Jeśli wdrażasz do strefy dostępności platformy Azure, nie może korzystać z zestawów dostępności. Jednak będziesz potrzebować upewnić się, wdrażanie usług centralnej węzłami aktywnym i pasywnym w dwóch różnych strefach dostępności, które wskazują najmniejszego opóźnienia między strefami.
         1.   Należy pamiętać, że należy użyć [standardowa usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) w przypadku ustanawiania Windows lub klastry pracy awaryjnej program Pacemaker w strefach dostępności dla warstwy system DBMS i usług SAP Central Services. [Podstawowy moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) nie może być używane do wdrożeń strefowej 
   5. Ustawienia limitu czasu
      1. Ślady dla deweloperów oprogramowania SAP NetWeaver różnymi wystąpieniami SAP i upewnij się, podano przerwy połączenie między serwerem umieścić w kolejce i procesów roboczych SAP. Podziały tych połączeń można uniknąć przez ustawienie rejestru dwóch parametrów:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 — Zobacz też [w tym artykule](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 — Zobacz też [w tym artykule](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. W celu uniknięcia przekroczenia limitów czasu graficznego interfejsu użytkownika między jednym lokalnym wdrożeniu SAP graficznego interfejsu użytkownika i warstwy aplikacji SAP wdrożonych na platformie Azure, należy sprawdzić, czy następujące parametry są ustawiane w default.pfl lub profil wystąpienie:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. W przypadku konfiguracji klastra pracy awaryjnej Windows, upewnij się, że czas reakcji na węzłach przestanie odpowiadać jest ustawione prawidłowo dla platformy Azure. W artykule firmy Microsoft [dostrajania progów sieci klastra trybu Failover](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) zawiera listę parametrów i wpływ tych wrażliwość trybu failover. Z parametrami wymienionymi należy ustawić te dwa parametry z wartościami:
         1.   SameSubNetDelay = 2
         2.   SameSubNetThreshold = 15
4. Testowanie usługi wysokiej procedury odzyskiwania dostępność i odzyskiwanie po awarii
   1. Aby symulować sytuacje awaryjne zamykania maszyn wirtualnych (system operacyjny gościa Windows) lub umieszczając systemów operacyjnych w trybie alarm (systemu operacyjnego gościa Linux), aby ustalić, czy konfiguracje trybu failover działa zgodnie z założeniami. 
   2. Zmierz swoje czasu potrzebnego do wykonania pracy w trybie failover. Jeśli czas, w którym trwa zbyt długo, należy wziąć pod uwagę:
      1.   W przypadku systemu SUSE Linux przy użyciu interwencja urządzeń zamiast agenta Odgradzania Azure przyspieszyć tryb failover
      2.   Dla oprogramowania SAP HANA Jeśli ponowne załadowanie danych trwa zbyt długo, należy wziąć pod uwagę do aprowizowania większej przepustowości magazynu
   3. Testowanie sekwencja/przywracania kopii zapasowej i czasu i dostrajania, jeśli to konieczne. Upewnij się, że nie tylko godziny tworzenia kopii zapasowej są wystarczające. Przetestować przywracania i podejmij czas przywracania działania. Upewnij się, że czas przywracania, w którym znajdują się w umowach SLA RTO, gdzie Twój czas RTO korzysta z bazy danych lub w procesie przywracania maszyny Wirtualnej
   4. Testowanie w regionie odzyskiwania po awarii, funkcji i architektury
5. Sprawdzanie zabezpieczeń
   1.  Test ważności rolę na platformie Azure na podstawie architektury dostępu (RBAC), w których zaimplementowano. Celem jest oddzielne i ograniczenie dostępu i uprawnień różnych zespołów. Na przykład członkowie zespołu podstawy SAP powinien móc wdrażać maszyny wirtualne i przypisać dysków z usługi Azure storage w danej sieci wirtualnej platformy Azure. Zespół podstawy SAP nie należy jednak możliwość utworzenia własnych sieci wirtualnych lub zmienić ustawienia istniejących sieciach wirtualnych. Po drugiej stronie Członkowie zespołu sieci, nie należy wdrażać maszyny wirtualne w sieciach wirtualnych, na którym uruchomiona jest aplikacja SAP i systemu DBMS na maszynach wirtualnych. Ani członków zespołu sieci należy można zmienić atrybuty maszyn wirtualnych lub nawet usunąć maszyny wirtualne lub dysków.  
   2.  Sprawdź [sieciowej grupy zabezpieczeń i ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) zasady działają zgodnie z oczekiwaniami i włączyć osłony dla chronionych zasobów
   3.  Upewnij się, że wszystkie zasoby, które muszą być szyfrowane są szyfrowane. Definiowanie i wykonywanie procesów Utwórz kopię zapasową certyfikaty, przechowywać, dostęp do tych certyfikatów i przywracania zaszyfrowanych jednostek. 
   4.  Użyj [usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) i/lub dysków systemu operacyjnego, gdzie to możliwe z systemem operacyjnym obsługują punktu widzenia
   5.  Sprawdź używano nie ma zbyt wielu warstw szyfrowania. Sensowne ograniczone do używania usługi Azure Disk encryption i następnie na pierwszych jednej z metod przezroczyste szyfrowanie bazy danych systemu DBMS
6. Testowanie wydajnościowe
   1.  W systemie SAP na podstawie śledzenia SAP i pomiary porównania najważniejsze 10 raporty online, aby bieżąca implementacja parametru, jeśli ma to zastosowanie 
   2.  W systemie SAP na podstawie śledzenia SAP i pomiary Porównaj 10 pierwszych zadań wsadowych, aby bieżąca implementacja parametru, jeśli ma to zastosowanie 
   3.  W systemie SAP na podstawie śledzenia SAP i pomiary Porównaj transfery danych za pośrednictwem interfejsów w systemie SAP. Skup się na interfejsach, gdy wiadomo, że transfer posłuży między różnymi lokalizacjami, jak ze środowiska lokalnego do platformy Azure 


## <a name="non-production-phase"></a>Faza nieprodukcyjnych 
W tej fazie przyjęto założenie, że po pilotażowego lub weryfikacji koncepcji, uruchamiasz do wdrażania systemów SAP spoza środowiska produkcyjnego na platformie Azure. Wszystkie informacje i środowisk poza weryfikacji koncepcji należy dostosować do takiej instalacji. W tym typie wdrożenia, a także mają zastosowanie wszystkie kryteria i kroki opisane w środowisku weryfikacji koncepcji. Na tym etapie zwykle wdrażania systemów rozwoju, testów jednostkowych systemów i regresji firm testy systemów na platformie Azure. Zaleca się że co najmniej jeden system nieprodukcyjnych w jednym wierszu aplikacji SAP ma konfigurację pełnej wysokiej dostępności, zgodnie z systemu produkcyjnego w przyszłości będzie to miało. Dostępne są następujące dodatkowe czynności, które należy wziąć pod uwagę podczas tej fazy:  

1.  Przed przeniesieniem systemy ze starego platform na platformie Azure zbieranie danych użycia zasobów, takich jak użycie procesora CPU, przepływności i operacje We/Wy danych. Zwłaszcza z bazami danych jednostki z warstwy, ale także z jednostek warstwy aplikacji. Również pomiaru opóźnienia sieci i magazynu.
2.  Możesz rejestrować wzorce czas użycia dostępności systemów. Dowiesz się, jak ustalić, czy systemów nieprodukcyjnych muszą być dostępne 7 x 24, czy istnieją systemów nieprodukcyjnych, które może zostać wyłączony podczas niektórych faz tygodnia lub miesiąca
3.  Przetestuj i zdefiniuj, czy chcesz utworzyć własne obrazy systemu operacyjnego dla maszyn wirtualnych na platformie Azure lub tego, czy chcesz użyć obrazu z galerii obrazów platformy Azure. Jeśli używasz obrazu z galerii platformy Azure, upewnij się, czy wykonujesz prawidłowy obraz, który odzwierciedla umowę pomocy technicznej dostawcy systemu operacyjnego. Dla niektórych dostawców systemu operacyjnego galerii Azure oferują użyć własnych obrazów licencji. Dla innych obrazów systemu operacyjnego, pomocy technicznej znajduje się w podanej ceny przez platformę Azure. Jeśli zdecydujesz się utworzyć własne obrazy systemu operacyjnego, dokumentację można znaleźć w następujących artykułach:
    1.  Możesz tworzyć uogólnionego obrazu maszyny wirtualnej Windows wdrożonymi na platformie Azure na podstawie [tej dokumentacji](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Możesz tworzyć uogólnionego obrazu systemu Linux maszyny Wirtualnej wdrożonej na platformie Azure na podstawie [tej dokumentacji](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Jeśli używasz SUSE i Red Hat Linux obrazów z galerii maszyn wirtualnych platformy Azure, musisz użyć obrazy dla rozwiązania SAP, dostarczone przez dostawców systemu Linux w galerii maszyn wirtualnych platformy Azure
4.  Upewnij się, że spełniają wymagania w zakresie obsługi, które firma SAP ma dotyczące umowy dotyczące pomocy technicznej firmy Microsoft. Informacje można znaleźć w uwadze pomocy technicznej SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553). HANA, duże wystąpienia można znaleźć w dokumencie [wymagań przechodzenia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Upewnij się, że odpowiednie osoby uzyskują [Planned maintenance notifications zaplanowanej](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), dlatego, że możesz wybrać przestoju i ponowny rozruch maszyn wirtualnych w czasie
5.  Intensywność w dokumentacji platformy Azure prezentacji firmy Microsoft za pośrednictwem kanałów, takich jak [Channel9](https://channel9.msdn.com/) dla nowych funkcji, które mogą być stosowane do wdrożenia
6.  Wyboru SAP notes związane z platformą Azure, takich jak obsługa Uwaga [#1928533](https://launchpad.support.sap.com/#/notes/1928533) dla nowych jednostek SKU maszyn wirtualnych lub nowo obsługiwanych wersji systemu operacyjnego i bazami danych. Porównaj nowe typy maszyn wirtualnych starsze maszynę Wirtualną pod kątem typów w zmienionych cennikach, dlatego, że jesteś w stanie wdrożyć maszyny wirtualne za pomocą najlepszych stosunek ceny do wydajności
7.  Sprawdzanie zasobów na informacje o pomocy technicznej SAP, SAP HANA sprzętu katalogu i ponownie PAM SAP upewnij się, że nie wprowadzono żadnych zmian na obsługiwanych maszynach wirtualnych platformy Azure obsługiwanego systemu operacyjnego zwalnia w tych maszyn wirtualnych i obsługiwanych SAP i DBMS wersji
8.  Sprawdź [tutaj](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) dla jednostki SKU na platformie Azure i porównaj ceny te planowane i po pewnym czasie zmiany można pobrać jednostki z lepszą cenę wydajności racja z certyfikatem HANA nowe 
9.  Skrypty wdrażania w taki sposób, aby korzystać z nowych typów maszyny Wirtualnej i uwzględnić nowe funkcje platformy Azure, której chcesz użyć do dostosowania
10. Po wdrożeniu infrastruktury, testowania i oceny opóźnienie sieciowe między warstwy aplikacji SAP maszyn wirtualnych i maszyn wirtualnych z systemem DBMS zgodnie z Uwaga pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i zwróć uwagę, pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Ocena wyników na podstawie wskazówek dotyczących opóźnienia sieci notatki pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinna należeć do zakresu moderuj i dobre. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i dużych wystąpień HANA jednostek zgodnie z opisem [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Upewnij się, że żadne ograniczenia wymienione w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) i [konfiguracje infrastruktury SAP HANA i operacji na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) zastosowanie dla konkretnego wdrożenia
11. Wykonaj inne kontrole, zgodnie z opisem w fazie weryfikacji koncepcji przed zastosowaniem obciążenia
12. Ponieważ obciążenie ma zastosowanie, rejestrowanie użycia zasobów w tych systemów na platformie Azure i porównać z uzyskana z platformą stare rekordy. Jeśli widzisz, że masz większe różnice, należy dostosować zmianę rozmiaru maszyny Wirtualnej przyszłych wdrożeń. Należy pamiętać, że w przypadku redukcję zatrudnienia, magazynu i przepustowości sieci maszyny wirtualnej zostanie ona zredukowana także:
    1.  [Rozmiary maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Działa w systemie funkcji kopiowania i procesów. Celem jest, to ułatwia umożliwiające kopiowanie systemie deweloperskim lub system testowy tak, które zespoły projektu można nowych systemów szybkiego uzyskania. Należy wziąć pod uwagę [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) jako narzędzie do wykonywania takich zadań.
14. Optymalizuj i doskonalenie zespołu uzyskuje dostęp do platformy Azure opartej na rolach, uprawnienia i procesów w celu upewnij się, że separacji należności na jednej stronie. Na tej stronie chcesz mieć wszystkie zespoły włączona w celu wykonywania zadań w ramach infrastruktury platformy Azure.
15. Wykonywania, testów i procedur dokumentu wysoką dostępność i odzyskiwanie po awarii odzyskiwania aby umożliwić pracownikom wykonywanie takich zadań. Identyfikowanie braków i dostosowania są integrowanie Twoich wdrożeń na nowych funkcji platformy Azure

 
## <a name="production-preparation-phase"></a>Etapu przygotowywania w środowisku produkcyjnym 
Na tym etapie, dla których chcesz zbierać wszystkie środowiska i wnioski dotyczące wdrożeń nieprodukcyjnych, a następnie zastosuj je w przyszłości wdrożeń produkcyjnych. Ponadto do faz przed, również należy przygotować pracy transfer danych między Twojej bieżącej lokalizacji hostingu i platformą Azure. 

1.  Pracę niezbędne uaktualnienia wersji SAP systemów produkcyjnych przed przejściem na platformę Azure
2.  Akceptuję właściciele firm na funkcjonalności i testy biznesowych, które muszą zostać przeprowadzone po zakończeniu migracji do systemu produkcyjnego
    1.  Upewnij się, że te testy są wykonywane za pomocą systemów źródeł w bieżącej lokalizacji hostowania. Aby uniknąć testy przeprowadzane po raz pierwszy po system jest przenoszony do platformy Azure
2.  Przetestuj proces migracji w środowisku produkcyjnym na platformie Azure. W przypadku nie przenoszenie wszystkich systemów produkcyjnych na platformie Azure, w tym samym przedziale czasu, tworzenie grup systemów produkcyjnych, które muszą być w tej samej lokalizacji hostowania. Migracja danych wykonywania i testowania. Typowych listy metod, takich jak:
    1.  Używanie metod DBMS, takich jak/przywracania kopii zapasowej w połączeniu z wysyłania dziennika, replikacji systemu HANA i programu SQL Server AlwaysOn umieszczenia i zsynchronizować zawartość bazy danych na platformie Azure
    2.  / Przywracania kopii zapasowej na użytek mniejszych baz danych
    3.  Użyj SAP Monitor migracji wdrożona w narzędziu SAP SWPM przeprowadzić migrację z heterogenicznych
    4.  Użyj [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) przetwarzania, jeśli chcesz połączyć z uaktualnienia wersji SAP. Należy pamiętać, że nie wszystkie kombinacje między źródłem a celem bazami danych są obsługiwane. Więcej informacji można znaleźć w określonych informacji pomocy technicznej SAP dla różnych wersji DMO. Na przykład [opcji migracji bazy danych (DMO) z SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Sprawdź, czy lepszą przepustowość jest transfer danych za pośrednictwem Internetu lub za pośrednictwem usługi ExpressRoute, w przypadku, gdy trzeba przenieść kopie zapasowe lub SAP eksportowania plików. W przypadku przenoszenia danych za pośrednictwem sieci internet konieczne może być zmiana niektórych reguł zabezpieczeń sieciowych grup zabezpieczeń/ASG wymagających jest dostępna dla systemów produkcyjnych w przyszłości
3.  Przed przeniesieniem systemy ze starego platform na platformie Azure zbieranie danych użycia zasobów, takich jak użycie procesora CPU, przepływności i operacje We/Wy danych. Zwłaszcza z bazami danych jednostki z warstwy, ale także z jednostek warstwy aplikacji. Również pomiaru opóźnienia sieci i magazynu.
4.  Sprawdzanie zasobów na informacje o pomocy technicznej SAP, SAP HANA sprzętu katalogu i ponownie PAM SAP upewnij się, że nie wprowadzono żadnych zmian na obsługiwanych maszynach wirtualnych platformy Azure obsługiwanego systemu operacyjnego zwalnia w tych maszyn wirtualnych i obsługiwanych SAP i DBMS wersji 
4.  Dostosowania skryptów wdrażania do najnowsze zmiany, które decyzji o typach maszyn wirtualnych i funkcjonalność platformy Azure
5.  Po wdrożeniu infrastruktury i aplikacji przechodzą przez szereg kontroli w celu weryfikacji:
    1.  Prawidłowe typy maszyn wirtualnych zostały wdrożone za pomocą prawidłowe atrybuty i rozmiaru magazynu
    2.  Sprawdź, czy maszyny wirtualne są poprawne i żądanej wersji systemu operacyjnego i poprawek i są jednolite
    3.  Sprawdź, czy zabezpieczenia maszyn wirtualnych są tak wymagany i jednolitego
    4.  Sprawdź, czy właściwej aplikacji, wersje i poprawki masz zainstalowany i wdrożony
    5.  Maszyny wirtualne zostały wdrożone w zestawach dostępności platformy Azure, zgodnie z planem
    6.  Usługa Azure Premium Storage została użyta dla dysków poufnych opóźnienia lub gdzie [pojedyncza maszyna wirtualna SLA na poziomie 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) jest wymagana
    7.  Sprawdzanie poprawności wdrożenia akcelerator zapisu platformy Azure
        1.  Upewnij się, że na maszynie wirtualnej, miejsca do magazynowania lub rozłożone został poprawnie zbudowany na dyskach, wymagających pomocy technicznej akcelerator zapisu platformy Azure
            1.  Sprawdź [Konfigurowanie programowej macierzy RAID w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Sprawdź [skonfigurować LVM na Maszynę wirtualną systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Usługa Azure managed disks](https://azure.microsoft.com/services/managed-disks/) były używane w trybie wyłączności
    9.  Maszyny wirtualne zostały wdrożone na prawidłowe zestawy dostępności i strefy dostępności
    10. Upewnij się, że [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszyny wirtualne na warstwie aplikacji SAP i SAP DBMS warstwy
    11. Nie umieszcza [wirtualne urządzenia sieciowe Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP DBMS warstwa SAP NetWeaver Hybris lub S/4HANA na podstawie systemów SAP
    12. ASG i sieciowej grupy zabezpieczeń reguły Zezwalaj na komunikację oraz odpowiednią planowane i zablokowanie komunikacji w przypadku, gdy wymagane
    13. Ustawienia limitu czasu zgodnie z wcześniejszym opisem zostały ustawione prawidłowo
    14. Przetestuj i oceń opóźnienie sieciowe między warstwy aplikacji SAP maszyn wirtualnych i maszyn wirtualnych z systemem DBMS zgodnie z Uwaga pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i zwróć uwagę, pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Ocena wyników na podstawie wskazówek dotyczących opóźnienia sieci notatki pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinna należeć do zakresu moderuj i dobre. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i dużych wystąpień HANA jednostek zgodnie z opisem [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Sprawdź, czy szyfrowanie zostały wdrożone, gdy jest to konieczne i niezbędne metodą szyfrowania
    16. Sprawdź, czy inne aplikacje i interfejsy mogą się łączyć nowo wdrożonym infrastruktury
6.  Tworzenie elementu playbook umożliwiająca reagowanie na planowana konserwacja platformy Azure. Zdefiniować kolejność systemów i maszyn wirtualnych, należy ponownie uruchomić w przypadku planowanej konserwacji
    

## <a name="go-live-phase"></a>Przejdź na żywo fazy
Na etapie gotowej do pracy musisz upewnij się, że należy postępować zgodnie z elementów playbook, który opracowany na wcześniejszych etapach. Wykonaj kroki, które przetestowane i skonfigurowanych pod kątem. Nie akceptuj najnowsze zmiany w konfiguracji i przetwarzania. Poza tym, które mają zastosowanie następujące miary:

1. Sprawdź, czy działają monitorowania portalu platformy Azure i innych narzędzi do monitorowania.  Zalecane narzędzia są monitora wydajności (Windows) lub SAR (Linux): 
    1.  Liczniki procesora CPU 
        1.  Średni czas procesora CPU — łącznie (wszystkie procesor CPU)
        2.  Średniego czasu procesora CPU — każdy procesor poszczególnych (dlatego 128 procesorów na maszynie Wirtualnej m128)
        3.  Procesor czas jądra — każdego poszczególnych procesora
        4.  Użytkownik czasu procesora CPU — każdego poszczególnych procesora
    5.  Memory (Pamięć) 
        1.  Wolna pamięć
        2.  Strony pamięci/s
        3.  Strony pamięci wychodzące/s
    4.  Dysk 
        1.  Odczyt z dysku kb/s — na poszczególnych dysków 
        2.  Odczyty dysku/s — na poszczególnych dysków
        3.  Odczyt z dysku ms odczytu — na poszczególnych dysków
        4.  Kb zapisu dysku/s — na poszczególnych dysków 
        5.  Zapisu dysku/s — na poszczególnych dysków
        6.  Ms zapisu dysku/odczytu — na poszczególnych dysków
    5.  Sieć 
        1.  Pakietów sieciowych/s
        2.  Pakiety sieciowe wychodzące/s
        3.  Sieć kb/s
        4.  Kb sieci wychodzące/s 
2.  Po zakończeniu migracji danych należy wykonać wszystkie testy sprawdzania poprawności, które uzgodnione z właściciele firm. Zaakceptuj, tylko wyniki testów weryfikacyjnych, w którym masz wyniki dla oryginalnego systemów źródłowych
3.  Sprawdź, czy działają interfejsy i tego, czy inne aplikacje mogą komunikować się z systemami produkcyjnymi nowo wdrożonym
4.  Sprawdź system transportu i korektę za pomocą transakcji SAP STMS
5.  Wykonaj kopie zapasowe bazy danych po udostępnieniu systemu w środowisku produkcyjnym
6.  Wykonaj kopie zapasowe maszyn wirtualnych warstwy aplikacji SAP maszyn wirtualnych po udostępnieniu systemu w środowisku produkcyjnym
7.  Dla rozwiązania SAP systemów, które nie były częścią bieżącego gotowej do pracy z fazy, ale komunikują się z systemami SAP, że przeniesione na platformę Azure w tej fazie gotowej do pracy, musisz zresetować Bufor nazwy hosta w SM51. W tym kroku zostanie pozbyć się starej pamięci podręcznej adresy IP skojarzone z nazwami wystąpień aplikacji, przeniesione na platformę Azure  


## <a name="post-production"></a>Wpis w środowisku produkcyjnym
Na tym etapie należy przede wszystkim chodzi o monitorowanie, operacyjne oraz administrowania systemu. Z punktu widzenia SAP mają zastosowanie zwykłe zadania, które należało wykonać przy użyciu starej lokalizacji hostowania. Azure określonych zadań, które chcesz zrobić to:

1. Analizowanie faktury platformy Azure dla wysokiej systemy opłat
2. Optymalizowanie wydajności ceny do wydajności po stronie magazynu i maszyn wirtualnych
3. Optymalizowanie razem, gdy systemy może zostać wyłączony.  


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

