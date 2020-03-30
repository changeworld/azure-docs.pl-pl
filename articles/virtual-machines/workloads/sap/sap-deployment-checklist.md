---
title: Lista kontrolna planowania i wdrażania obciążeń SAP | Dokumenty firmy Microsoft
description: Lista kontrolna planowania wdrożeń obciążeń SAP na platformie Azure i wdrażania obciążeń
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060064"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Obciążenia SAP na platformie Azure: lista kontrolna planowania i wdrażania

Ta lista kontrolna jest przeznaczona dla klientów przenoszących aplikacje SAP NetWeaver, S/4HANA i Hybris do infrastruktury platformy Azure jako usługi. Przez cały czas trwania projektu klient i/lub partner SAP powinni przejrzeć listę kontrolną. Należy pamiętać, że wiele kontroli jest zakończonych na początku projektu i na etapie planowania. Po zakończeniu wdrażania proste zmiany w wdrożonej infrastrukturze platformy Azure lub wersjach oprogramowania SAP mogą stać się złożone.

Przejrzyj listę kontrolną w kluczowych punktach kontrolnych podczas projektu. W ten sposób można wykryć małe problemy, zanim staną się one duże problemy. Będziesz mieć również wystarczająco dużo czasu, aby przeprojektować i przetestować wszelkie niezbędne zmiany. Nie uważaj, że ta lista kontrolna jest kompletna. W zależności od sytuacji może być konieczne przeprowadzenie o wiele więcej kontroli.

Lista kontrolna nie zawiera zadań, które są niezależne od platformy Azure. Na przykład interfejsy aplikacji SAP zmieniają się podczas przenoszenia na platformę Azure lub do dostawcy hostingu.

Ta lista kontrolna może być również używana dla systemów, które są już wdrożone. Od momentu wdrożenia dodano nowe funkcje, takie jak Akcelerator zapisu i strefy dostępności oraz nowe typy maszyn wirtualnych. Dlatego warto okresowo przeglądać listę kontrolną, aby upewnić się, że znasz nowe funkcje na platformie Azure.

## <a name="project-preparation-and-planning-phase"></a>Faza przygotowania i planowania projektu
W tej fazie planujesz migrację obciążenia SAP do platformy Azure. Co najmniej na tym etapie należy utworzyć następujące dokumenty i zdefiniować i omówić następujące elementy migracji:

1. Dokument projektowy wysokiego poziomu. Dokument ten powinien zawierać:
    - Bieżący spis składników i aplikacji SAP oraz spis aplikacji docelowych dla platformy Azure.
    - Macierz przypisania odpowiedzialności (RACI), która określa obowiązki i zadania zaangażowanych stron. Rozpocznij na wysokim poziomie i pracuj na bardziej szczegółowych poziomach podczas planowania i pierwszych wdrożeń.
    - Architektura rozwiązań wysokiego poziomu.
    - Decyzja o regionach platformy Azure do wdrożenia. Zobacz [listę regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Aby dowiedzieć się, które usługi są dostępne w każdym regionie, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/).
    - Architektura sieci do łączenia się z lokalnego do platformy Azure. Zacznij zapoznać się z [planem wirtualnego centrum danych dla platformy Azure.](https://docs.microsoft.com/azure/architecture/vdc/)
    - Zasady zabezpieczeń dotyczące uruchamiania danych biznesowych o dużym wpływie na platformie Azure. Aby dowiedzieć się więcej o zabezpieczeniach danych, zacznij od [dokumentacji zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/).
2.  Dokument techniczny projektu. Dokument ten powinien zawierać:
    - Diagram blokowy dla rozwiązania.
    - Rozmiar składników obliczeniowych, magazynu i sieci na platformie Azure. Aby uzyskać rozmiary maszyn wirtualnych platformy Azure w sap, zobacz [uwaga pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Ciągłość działania i architektura odzyskiwania po awarii.
    - Szczegółowe informacje o wersjach pakietu os, bazy danych, jądra i sap. Nie musi być prawdą, że każda wersja systemu operacyjnego obsługiwana przez SAP NetWeaver lub S/4HANA jest obsługiwana na maszynach wirtualnych platformy Azure. To samo dotyczy wydań DBMS. Sprawdź następujące źródła, aby wyrównać i w razie potrzeby uaktualnić wersje SAP, wersje DBMS i wersje systemu operacyjnego, aby zapewnić obsługę sap i platformy Azure. Aby uzyskać pełną pomoc techniczną od sap i firmy Microsoft, musisz mieć kombinacje wersji obsługiwane przez sap i platformę Azure. W razie potrzeby należy zaplanować uaktualnienie niektórych składników oprogramowania. Więcej szczegółów na temat obsługiwanego oprogramowania SAP, OS i DBMS opisano tutaj:
        - [Uwaga dotycząca obsługi sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ta uwaga definiuje minimalne wersje systemu operacyjnego obsługiwane na maszynach wirtualnych platformy Azure. Definiuje również minimalne wydania bazy danych wymagane dla większości baz danych innych niż HANA. Na koniec zapewnia rozmiar SAP dla typów maszyn wirtualnych platformy Azure obsługiwanych przez SAP.
        - [Uwaga dotycząca obsługi sap #2015553](https://launchpad.support.sap.com/#/notes/2015553). Ta uwaga definiuje zasady pomocy technicznej dotyczące usługi Azure storage i relacji pomocy technicznej potrzebnej firmie Microsoft.
        - [Uwaga dotycząca obsługi sap #2039619](https://launchpad.support.sap.com/#/notes/2039619). Ta uwaga definiuje macierz obsługi Oracle dla platformy Azure. Oracle obsługuje tylko systemy Windows i Oracle Linux jako systemy operacyjne gościa na platformie Azure dla obciążeń SAP. Ta instrukcja pomocy technicznej dotyczy również warstwy aplikacji SAP, która uruchamia wystąpienia SAP. Jednak Oracle nie obsługuje wysokiej dostępności usług SAP Central w systemie Oracle Linux za pośrednictwem producenta symulatora. Jeśli potrzebujesz wysokiej dostępności dla ASCS w systemie Oracle Linux, musisz użyć pakietu SIOS Protection Suite dla systemu Linux. Szczegółowe dane certyfikacji SAP można znaleźć w informacji o obsłudze sap [#1662610 — szczegóły dotyczące pomocy technicznej dla pakietu SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610). W systemie Windows obsługiwane przez system SAP rozwiązanie klastrowania trybu failover dla usług SAP Central services jest obsługiwane w połączeniu z oracle jako warstwą DBMS.
        - [Uwaga dotycząca obsługi sap #2235581](https://launchpad.support.sap.com/#/notes/2235581). Ta uwaga zawiera macierz obsługi sap HANA w różnych wersjach systemu operacyjnego.
        - Maszyny wirtualne platformy Azure obsługiwane przez system SAP HANA i [duże wystąpienia HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) są wymienione w witrynie SAP w [sieci Web.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        - [Macierz dostępności produktów SAP](https://support.sap.com/en/).
        - [Uwaga dotycząca obsługi SAP #2555629 — dynamiczne warstwowanie SAP HANA 2.0 — obsługa hipernadzorcy i chmury](https://launchpad.support.sap.com/#/notes/2555629)
        - [Uwaga dotycząca pomocy technicznej SAP #1662610 — szczegóły dotyczące pomocy technicznej dla pakietu SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Uwagi SAP dla innych produktów specyficznych dla SAP.     
    - Zalecamy ścisłe trzywarstwowe projekty dla systemów produkcyjnych SAP. Nie zaleca się łączenia serwerów ASCS i/lub DBMS i/lub aplikacji na jednej maszynie wirtualnej. Korzystanie z konfiguracji klastra z wieloma identyfikatorami SID dla usług SAP Central Services jest obsługiwane w systemach operacyjnych gościa systemu Windows na platformie Azure. Ale ta konfiguracja nie jest obsługiwana dla usług SAP Central Services w systemach operacyjnych Linux na platformie Azure. Dokumentację dotyczącą scenariusza systemu operacyjnego gościa systemu Windows można znaleźć w następujących artykułach:
        - [Wysoka dostępność wystąpienia SAP ASCS/SCS z klastrem trybu failover systemu Windows Server i dyskiem współdzielonym na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Sap ASCS/SCS wystąpienie wielu identyfikatorów SID wysokiej dostępności z klastrów trybu failover systemu Windows Server i udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Wysoka dostępność i architektura odzyskiwania po awarii.
        - Na podstawie obiektów RTO i celu rpo należy zdefiniować, jak musi wyglądać architektura wysokiej dostępności i odzyskiwania po awarii.
        - Aby uzyskać wysoką dostępność w strefie, sprawdź, co ma do zaoferowania żądany program DBMS na platformie Azure. Większość pakietów DBMS oferuje synchroniczne metody synchroniczowego gorąca rezerwy, które zalecamy dla systemów produkcyjnych. Sprawdź również dokumentację związaną z SAP dla różnych baz danych, począwszy od [zagadnień dotyczących wdrażania dbms maszyn wirtualnych platformy Azure dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) i powiązanych dokumentów.
           Korzystanie z klastra trybu failover systemu Windows Server z konfiguracją dysku udostępnionego dla warstwy DBMS, jak na przykład [opisane dla programu SQL Server,](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)nie jest obsługiwane. Zamiast tego należy użyć rozwiązań, takich jak:
           - [Opcja Zawsze włączone programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replikacja systemu HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Aby uzyskać odzyskiwanie po awarii w regionach platformy Azure, przejrzyj rozwiązania oferowane przez różnych dostawców usługi DBMS. Większość z nich obsługuje replikację asynchroniza lub wysyłkę dziennika.
        - W przypadku warstwy aplikacji SAP należy określić, czy uruchomisz systemy testowe regresji biznesowej, które idealnie są replikami wdrożeń produkcyjnych w tym samym regionie platformy Azure lub w regionie odzyskiwania po awarii. W drugim przypadku można kierować ten system regresji biznesowej jako miejsce docelowe odzyskiwania po awarii dla wdrożeń produkcyjnych.
        - Jeśli zdecydujesz się nie umieszczać systemów nieprodukcyjnych w lokacji odzyskiwania po awarii, zajrzyj do usługi Azure Site Recovery jako metody replikowania warstwy aplikacji SAP do regionu odzyskiwania po awarii platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia aplikacji SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap).
        - Jeśli zdecydujesz się użyć połączonej konfiguracji HADR przy użyciu [stref dostępności platformy Azure,](https://docs.microsoft.com/azure/availability-zones/az-overview)zapoznaj się z regionami platformy Azure, w których dostępne są strefy dostępności. Należy również wziąć pod uwagę ograniczenia, które mogą być wprowadzone przez zwiększone opóźnienia sieci między dwiema strefami dostępności.  
3.  Spis wszystkich interfejsów SAP (SAP i innych niż SAP).
4.  Projektowanie usług fundacji. Ten projekt powinien zawierać następujące elementy:
    - Projekt usługi Active Directory i DNS.
    - Topologia sieci na platformie Azure i przypisywanie różnych systemów SAP.
    - Struktura [dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) dla zespołów, które zarządzają infrastrukturą i aplikacjami SAP na platformie Azure.
    - Topologia grupy zasobów.
    - [Strategia tagowania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Konwencje nazewnictwa maszyn wirtualnych i innych składników infrastruktury i/lub nazw logicznych.
5.  Umowa pomocy technicznej Microsoft Premier. Zidentyfikuj swojego menedżera konta technicznego Microsoft (TAM). Aby zapoznać się z wymaganiami dotyczącymi obsługi sap, zobacz [uwaga dotycząca pomocy technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Liczba subskrypcji platformy Azure i podstawowy przydział dla subskrypcji. [Otwórz żądania pomocy technicznej, aby zwiększyć przydziały subskrypcji platformy Azure w](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) razie potrzeby.
7.  Plan redukcji danych i migracji danych do migracji danych SAP na platformę Azure. W przypadku systemów SAP NetWeaver firma SAP ma wytyczne dotyczące ograniczania ilości dużych ilości danych. Zobacz [ten przewodnik SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) dotyczący zarządzania danymi w systemach SAP ERP. Niektóre treści dotyczą również systemów NetWeaver i S/4HANA w ogóle.
8.  Podejście do wdrażania automatycznego. Celem automatyzacji wdrożeń infrastruktury na platformie Azure jest wdrożenie w sposób deterministyczny i uzyskanie deterministycznych wyników. Wielu klientów używa skryptów opartych na programie PowerShell lub CLI. Istnieją jednak różne technologie typu open source, których można użyć do wdrożenia infrastruktury platformy Azure dla systemu SAP, a nawet zainstalowania oprogramowania SAP. Przykłady można znaleźć na GitHub:
    - [Zautomatyzowane wdrożenia SAP w chmurze azure](https://github.com/Azure/sap-hana)
    - [Instalacja SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Zdefiniuj regularny harmonogram przeglądu projektu i wdrożenia między tobą jako klientem, integratorem systemu, firmą Microsoft i innymi zaangażowanymi stronami.

 
## <a name="pilot-phase-strongly-recommended"></a>Faza pilotażowa (zdecydowanie zalecana)
 
Można uruchomić pilotażu przed lub w trakcie planowania i przygotowania projektu. Można również użyć fazy pilotażowej do testowania podejść i projektów wykonanych w fazie planowania i przygotowania. I można rozszerzyć fazę pilotażową, aby uczynić go prawdziwym dowodem koncepcji.

Zaleca się skonfigurowanie i sprawdzenie poprawności pełnego rozwiązania HADR i projektu zabezpieczeń podczas wdrażania pilotażowego. Niektórzy klienci wykonują testy skalowalności w tej fazie. Inni klienci używają wdrożeń systemów piaskownicy SAP jako fazy pilotażowej. Zakładamy, że masz już zidentyfikowany system, który chcesz przeprowadzić migrację na platformę Azure dla programu pilotażowego.

1. Optymalizuj transfer danych na platformę Azure. Optymalny wybór zależy w dużym stopniu od konkretnego scenariusza. Transfer z lokalnego za pośrednictwem [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) jest najszybszy, jeśli obwód usługi ExpressRoute ma wystarczającą przepustowość. W innych scenariuszach przesyłanie przez Internet jest szybsze.
2. W przypadku migracji heterogenicznych platform SAP, która obejmuje eksport i import danych, przetestuj i optymalizuj fazy eksportu i importu. W przypadku dużych migracji, w których program SQL Server jest platformą docelową, można znaleźć [zalecenia](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Monitor migracji/SWPM można użyć, jeśli nie potrzebujesz połączonego uaktualnienia wersji. Proces SAP [DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) można użyć po połączeniu migracji z uaktualnieniem wersji SAP. Aby to zrobić, należy spełnić pewne wymagania dotyczące kombinacji platformy źródłowej i docelowej dbms. Ten proces jest udokumentowany w [opcji migracji bazy danych (DMO) sumy 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Eksportuj do źródła, eksportuj przekazywanie plików na platformę Azure i importuj wydajność. Maksymalizuj nakładanie się między eksportem a importem.
   2.  Oceń wolumin bazy danych na platformach docelowych i docelowych na potrzeby zmiany rozmiaru infrastruktury.
   3.  Sprawdzanie poprawności i optymalizacja czasu.
1. Walidacja techniczna.
   1. Typy maszyn wirtualnych.
        - Przejrzyj zasoby w uwagach do obsługi SAP, w katalogu sprzętu SAP HANA i ponownie w uprężonej ujrzycie SAP. Upewnij się, że nie ma żadnych zmian w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwanych wersjach systemu operacyjnego dla tych typów maszyn wirtualnych i obsługiwanych wersjach SAP i DBMS.
        - Ponownie sprawdź poprawność rozmiaru aplikacji i infrastruktury, którą wdrażasz na platformie Azure. Jeśli przenosisz istniejące aplikacje, często możesz wyprowadzić niezbędny SAPS z używanej infrastruktury i [strony internetowej testu porównawczego SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) i porównać ją z numerami SAPS wymienionymi w [notatce technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Również zachować [ten artykuł na saps ocen](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) na uwadze.
        - Oceny i testowania rozmiaru maszyn wirtualnych platformy Azure w odniesieniu do maksymalnej przepływności magazynu i przepływności sieci typów maszyn wirtualnych wybranych w fazie planowania. Dane można znaleźć tutaj:
           -  [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Należy wziąć pod uwagę *maksymalną przepływność dysku nieskłutowanego* do zmiany rozmiaru.
           -  [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Należy wziąć pod uwagę *maksymalną przepływność dysku nieskłutowanego* do zmiany rozmiaru.
   2. Pamięć.
        - Co najmniej należy użyć [magazynu SSD standard platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) dla maszyn wirtualnych, które reprezentują warstwy aplikacji SAP i do wdrażania dbmss, które nie są zależne od wydajności.
        - Ogólnie rzecz biorąc, nie zaleca się używania [dysków Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Użyj [usługi Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) dla wszystkich maszyn wirtualnych dbms, które są zdalnie zależne od wydajności.
        - Użyj [dysków zarządzanych platformy Azure](https://azure.microsoft.com/services/managed-disks/).
        - Użyj akceleratora zapisu azure dla dysków dziennika DBMS z serii M. Należy pamiętać o limitach akceleratora zapisu i użycia, zgodnie z dokumentami w [Akceleratorze zapisów](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - W przypadku różnych typów systemu dbms sprawdź [ogólną dokumentację systemu DBMS związaną z sap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) i dokumentację specyficzną dla systemu DBMS, na którą wskazuje dokument ogólny.
        - Aby uzyskać więcej informacji na temat sap hana, zobacz [KONFIGURACJE infrastruktury SAP HANA infrastruktury i operacji na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Nigdy nie należy montować dysków danych platformy Azure na maszynie Wirtualnej systemu Azure z systemem Linux przy użyciu identyfikatora urządzenia. Zamiast tego należy użyć uniwersalnego unikatowego identyfikatora (UUID). Należy zachować ostrożność podczas korzystania z narzędzi graficznych do instalowania dysków danych platformy Azure, na przykład. Sprawdź dokładnie wpisy w /etc/fstab, aby upewnić się, że UUID jest używany do montażu dysków. Więcej informacji można znaleźć w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Sieci.
        - Przetestuj i oceń infrastrukturę sieci wirtualnej i dystrybucję aplikacji SAP w różnych sieciach wirtualnych platformy Azure lub w nich.
        -  Oceń podejście architektury sieci wirtualnej lub podejście do mikrosegmentacji w ramach jednej sieci wirtualnej platformy Azure. Oparcie tej oceny na:
               1. Koszty wymiany danych między [równorzędnych sieci wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Aby uzyskać informacje o kosztach, zobacz [Ceny sieci wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Zalety szybkiego rozłączenia komunikacji równorzędnej między sieciami wirtualnymi platformy Azure, w przeciwieństwie do zmiany sieciowej grupy zabezpieczeń w celu izolowania podsieci w sieci wirtualnej. Ta ocena dotyczy przypadków, gdy aplikacje lub maszyny wirtualne hostowane w podsieci sieci wirtualnej stały się zagrożeniem bezpieczeństwa.
                3. Centralne rejestrowanie i inspekcja ruchu sieciowego między lokalnymi, zewnętrznymi i wirtualnym centrum danych zbudowanym na platformie Azure.
        - Oceń i przetestuj ścieżkę danych między warstwą aplikacji SAP a warstwą SAP DBMS.
            -  Umieszczenie [wirtualnych urządzeń sieciowych platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemów SAP opartych na SAP NetWeaver, Hybris lub S/4HANA nie jest obsługiwane.
            -  Umieszczenie warstwy aplikacji SAP i SAP DBMS w różnych sieciach wirtualnych platformy Azure, które nie są równorzędne, nie jest obsługiwane.
            -  Za pomocą [reguł grupy zabezpieczeń aplikacji i sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) można definiować trasy między warstwą aplikacji SAP a warstwą SAP DBMS.
        - Upewnij się, że [platforma Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i warstwie SAP DBMS. Należy pamiętać, że do obsługi przyspieszonej sieci na platformie Azure potrzebne są różne poziomy systemu operacyjnego:
            - Windows Server 2012 R2 lub nowszy.
            - SUSE Linux 12 SP3 lub nowszych.
            - RHEL 7.4 lub nowszych.
            - Oracle Linux 7.5. Jeśli używasz jądra RHCKL, wymagane jest zwolnienie 3.10.0-862.13.1.el7. Jeśli używasz jądra Oracle UEK, wymagane jest wydanie 5.
        - Przetestuj i oceń opóźnienie sieci między maszynami wirtualnymi z warstwą aplikacji SAP a maszynami wirtualnymi dbms zgodnie z uwagami obsługi SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki w wskazówki dotyczące opóźnienia sieci w [#1100926 w nocie pomocy technicznej SAP](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno znajdować się w umiarkowanym lub dobrym zakresie. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień HANA, zgodnie z opisem w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Upewnij się, że wdrożenia równoważenia obciążenia sieciowego są skonfigurowane do korzystania z funkcji Direct Server Return. To ustawienie zmniejszy opóźnienie, gdy platformy Azure ILBs są używane do konfiguracji wysokiej dostępności w warstwie DBMS.
        - Jeśli używasz programu Azure Load Balancer wraz z systemami operacyjnymi dla gości linuksowych, sprawdź, czy parametr sieciowy systemu Linux **net.ipv4.tcp_timestamps** jest ustawiony na **0**. To zalecenie jest sprzeczne z zaleceniami w starszych wersjach [notatki SAP #2382421](https://launchpad.support.sap.com/#/notes/2382421). Uwaga SAP jest teraz aktualizowana, aby stwierdzić, że ten parametr musi być ustawiony na **0,** aby pracować z modułami równoważenia obciążenia platformy Azure.
        - Należy rozważyć użycie [grup umieszczania zbliżeniowego platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) aby uzyskać optymalne opóźnienie sieci. Aby uzyskać więcej informacji, zobacz [Grupy miejsc docelowych zbliżeniowych platformy Azure, aby uzyskać optymalne opóźnienie sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
   4. Wysoka dostępność i odzyskiwanie po awarii wdrożeń.
        - Jeśli wdrożysz warstwę aplikacji SAP bez definiowania określonej strefy dostępności platformy Azure, upewnij się, że wszystkie maszyny wirtualne uruchamiające wystąpienia okien dialogowych SAP lub wystąpienia oprogramowania pośredniczącego pojedynczego systemu SAP są wdrażane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Jeśli nie potrzebujesz wysokiej dostępności usług SAP Central Services i DBMS, możesz wdrożyć te maszyny wirtualne w tym samym zestawie dostępności, co warstwa aplikacji SAP.
        - Jeśli chronisz usługi SAP Central Services i warstwę DBMS pod kątem wysokiej dostępności przy użyciu replikacji pasywnej, umieść dwa węzły usług SAP Central w jednym oddzielnym zestawie dostępności i dwa węzły DBMS w innym zestawie dostępności.
        - Jeśli wdrożysz w strefach dostępności platformy Azure, nie można używać zestawów dostępności. Należy jednak upewnić się, że wdrożono aktywne i pasywne węzły usług centralnych w dwóch różnych strefach dostępności. Użyj stref dostępności, które mają najniższe opóźnienie między nimi.
          Należy pamiętać, że należy użyć [azure standard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) dla przypadku użycia ustanowienia klastrów trybu failover systemu Windows lub rozrusznika dla dbms i SAP central services warstwy w strefach dostępności. Nie można użyć [podstawowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) dla wdrożeń strefowych.
   5. Ustawienia limitu czasu.
        - Sprawdź ślady dewelopera SAP NetWeaver wystąpień SAP, aby upewnić się, że nie ma żadnych przerw w połączeniu między serwerem enqueue i procesami roboczymi SAP. Można uniknąć tych przerw w połączeniu, ustawiając te dwa parametry rejestru:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Aby uzyskać więcej informacji, zobacz [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Aby uzyskać więcej informacji, zobacz [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Aby uniknąć limitów czasu gui między lokalnymi interfejsami GUI SAP i warstwami aplikacji SAP wdrożonymi na platformie Azure, sprawdź, czy te parametry są ustawione w default.pfl lub profilu wystąpienia:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Aby zapobiec zakłóceniom nawiązanych połączeń między procesem sap enqueue a procesami roboczymi SAP, należy ustawić parametr **enque/encni/set_so_keepalive** na **true**. Zobacz też [#2743751 notatki SAP](https://launchpad.support.sap.com/#/notes/2743751).  
        - Jeśli używasz konfiguracji klastra trybu failover systemu Windows, upewnij się, że czas reagowania na węzły niereagujące jest poprawnie ustawiony dla platformy Azure. Artykuł [Dostrajanie progów sieci klastra trybu failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) zawiera listę parametrów i ich wpływu na wrażliwość na tryb failover. Zakładając, że węzły klastra znajdują się w tej samej podsieci, należy zmienić następujące parametry:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHiliorylength = 30
    6. Ustawienia systemu operacyjnego lub poprawki
        - Aby uruchomić HANA na SAP, przeczytaj te uwagi i dokumentację:
            -   [Uwaga dotycząca pomocy technicznej SAP #2814271 — kopia zapasowa SAP HANA kończy się niepowodzeniem na platformie Azure z błędem sumy kontrolnej](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Uwaga dotycząca obsługi SAP #2753418 — potencjalna degradacja wydajności z powodu rezerwy czasomierza](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Uwaga dotycząca obsługi sap #2791572 — obniżenie wydajności z powodu braku obsługi funkcji VDSO dla funkcji Hyper-V na platformie Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Uwaga dotycząca obsługi SAP #2382421 — optymalizacja konfiguracji sieci na poziomie HANA i OS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Uwaga dotycząca obsługi SAP #2694118 — dodatek Red Hat Enterprise Linux HA na platformie Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Uwaga dotycząca obsługi sap #1984787 - SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Uwaga dotycząca obsługi sap #2002167 - Red Hat Enterprise Linux 7.x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Uwaga dotycząca obsługi sap #2292690 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Uwaga dotycząca obsługi SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalacja i konfiguracja](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Uwaga dotycząca obsługi sap #2777782 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Uwaga dotycząca obsługi sap #2578899 - SUSE Linux Enterprise Server 15: Uwaga dotycząca instalacji](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Uwaga dotycząca obsługi SAP #2455582 - Linux: Uruchamianie aplikacji SAP skompilowanych z GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Uwaga dotycząca pomocy technicznej SAP #2729475 — błąd HWCCT nie powiódł się z powodu błędu "Funkcja Hypervisor nie jest obsługiwana" na maszynach wirtualnych platformy Azure z certyfikatem SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Przetestuj swoje procedury wysokiej dostępności i odzyskiwania po awarii.
   1. Symuluj sytuacje pracy awaryjnej, wyłączając maszyny wirtualne (systemy operacyjne windows)) lub umieszczając systemy operacyjne w trybie paniki (systemy operacyjne dla gości Linuksa). Ten krok pomoże Ci ustalić, czy konfiguracje pracy awaryjnej działają zgodnie z projektem.
   1. Zmierz, jak długo trwa wykonanie pracy awaryjnej. Jeśli czasy są zbyt długie, należy wziąć pod uwagę:
        - W przypadku systemu SUSE Linux użyj urządzeń SBD zamiast agenta usługi Azure Fence, aby przyspieszyć przebłajanie awaryjne.
        - W przypadku systemu SAP HANA, jeśli ponowne załadowanie danych trwa zbyt długo, należy rozważyć inicjowanie obsługi administracyjnej większej przepustowości magazynu.
   3. Przetestuj sekwencję kopii zapasowych/przywracania i czas i w razie potrzeby wprowadzać poprawki. Upewnij się, że czas tworzenia kopii zapasowej jest wystarczający. Należy również przetestować działania przywracania i przywracania czasu. Upewnij się, że czasy przywracania znajdują się w ramach ujeżdnych łat.
   4. Przetestuj międzyregionową funkcjonalność i architekturę odzyskiwania po awarii.
1. Kontrole bezpieczeństwa.
   1. Przetestuj ważność architektury kontroli dostępu opartej na rolach platformy Azure (RBAC). Celem jest oddzielenie i ograniczenie dostępu i uprawnień różnych zespołów. Na przykład członkowie zespołu SAP Basis powinni mieć możliwość wdrażania maszyn wirtualnych i przypisywania dysków z usługi Azure Storage do danej sieci wirtualnej platformy Azure. Ale zespół SAP Basis nie powinien być w stanie utworzyć własnych sieci wirtualnych ani zmienić ustawień istniejących sieci wirtualnych. Członkowie zespołu sieciowego nie powinni mieć możliwości wdrażania maszyn wirtualnych w sieciach wirtualnych, w których są uruchomione aplikacje SAP i maszyny wirtualne dbms. Członkowie tego zespołu nie mogą również zmieniać atrybutów maszyn wirtualnych, a nawet usuwać maszyn wirtualnych lub dysków.  
   1.  Sprawdź, czy [sieciowa grupa zabezpieczeń i reguły ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) działają zgodnie z oczekiwaniami i chroni chronione zasoby.
   1.  Upewnij się, że wszystkie zasoby, które muszą być zaszyfrowane są szyfrowane. Definiowanie i implementowanie procesów w celu utworzenia kopii zapasowej certyfikatów, przechowywania i uzyskiwania dostępu do tych certyfikatów oraz przywracania zaszyfrowanych jednostek.
   1.  Użyj [szyfrowania dysków platformy Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) dla dysków systemu operacyjnego, jeśli to możliwe z punktu widzenia obsługi systemu operacyjnego.
   1.  Upewnij się, że nie używasz zbyt wielu warstw szyfrowania. W niektórych przypadkach warto używać szyfrowania dysków platformy Azure wraz z jedną z metod szyfrowania danych przezroczystych danych systemu DBMS w celu ochrony różnych dysków lub składników na tym samym serwerze.  Na przykład na serwerze SAP DBMS można włączyć szyfrowanie dysków azure (ADE) na dysku rozruchowym systemu operacyjnego (jeśli system operacyjny obsługuje ADE) i te dyski danych, które nie są używane przez pliki trwałości danych DBMS.  Przykładem jest użycie usługi ADE na dysku zawierającym klucze szyfrowania TDE DBMS.
1. Testowanie wydajności. W sap, na podstawie śledzenia SAP i pomiarów, dokonać tych porównań:
   - W stosownych przypadkach porównaj 10 najważniejszych raportów online z bieżącą implementacją.
   - W stosownych przypadkach porównaj 10 najważniejszych zadań wsadowych z bieżącą implementacją.
   - Porównaj transfery danych za pośrednictwem interfejsów do systemu SAP. Skoncentruj się na interfejsach, w których wiadomo, że transfer odbywa się między różnymi lokalizacjami, na przykład z lokalnego na platformę Azure.


## <a name="non-production-phase"></a>Faza nieprodukcowa 
W tej fazie zakładamy, że po pomyślnym pilotażu lub weryfikacji koncepcji (POC), rozpoczynasz wdrażanie nieprodukcyjnych systemów SAP na platformie Azure. Uwzględnij wszystko, czego nauczyłeś się i czego doświadczyłeś podczas poc do tego wdrożenia. Wszystkie kryteria i kroki wymienione dla poc stosuje się do tego wdrożenia, jak również.

W tej fazie zazwyczaj wdrażasz systemy programistyczne, systemy testowania jednostek i systemy testowania regresji biznesowej na platformie Azure. Zaleca się, aby co najmniej jeden system nieprodukcyjny w jednej linii aplikacji SAP miał pełną konfigurację wysokiej dostępności, którą będzie miał przyszły system produkcyjny. Oto kilka dodatkowych kroków, które należy wykonać w tej fazie:  

1.  Przed przeniesieniem systemów ze starej platformy na platformę Azure należy zbierać dane dotyczące zużycia zasobów, takie jak użycie procesora CPU, przepustowość magazynu i dane we/wy. Szczególnie zbierać te dane z jednostek warstwy DBMS, ale także zbierać je z jednostek warstwy aplikacji. Zmierz również opóźnienie sieci i magazynu.
2.  Rejestrowanie wzorców czasu użycia dostępności w systemach. Celem jest ustalenie, czy systemy nieprodukcyjne muszą być dostępne przez cały dzień, każdego dnia, czy też istnieją systemy nieprodukcyjne, które można wyłączyć w niektórych fazach tygodnia lub miesiąca.
3.  Przetestuj i określ, czy chcesz utworzyć własne obrazy systemu operacyjnego dla maszyn wirtualnych na platformie Azure, czy chcesz użyć obrazu z galerii obrazów udostępnionych platformy Azure. Jeśli używasz obrazu z Galerii obrazów udostępnionych, upewnij się, że używasz obrazu, który odzwierciedla umowę pomocy technicznej z dostawcą systemu operacyjnego. W przypadku niektórych dostawców systemu operacyjnego galeria obrazów udostępnionych umożliwia przenoszenie własnych obrazów licencji. W przypadku innych obrazów systemu operacyjnego pomoc techniczna jest wliczona w cenę podana przez platformę Azure. Jeśli zdecydujesz się utworzyć własne obrazy systemu operacyjnego, można znaleźć dokumentację w następujących artykułach:
    -   [Tworzenie uogólnionego obrazu maszyny Wirtualnej systemu Windows wdrożonej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Tworzenie uogólnionego obrazu maszyny wirtualnej z systemem Linux wdrożonej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Jeśli używasz obrazów SUSE i Red Hat Linux z Galerii obrazów udostępnionych, musisz użyć obrazów dla sap dostarczonych przez dostawców systemu Linux w Galerii obrazów udostępnionych.
4.  Upewnij się, że spełniasz wymagania pomocy technicznej SAP dla umów pomocy technicznej firmy Microsoft. Zobacz [#2015553 uwaga do obsługi SAP](https://launchpad.support.sap.com/#/notes/2015553). W przypadku wystąpień dużych hana zobacz [Wymagania dołączania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Upewnij się, że odpowiednie osoby otrzymują [powiadomienia o planowanej konserwacji,](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) dzięki czemu możesz wybrać najlepsze przestoje.
5.  Często sprawdzaj prezentacje platformy Azure w kanałach, takich jak [Kanał 9,](https://channel9.msdn.com/) aby uzyskać nowe funkcje, które mogą mieć zastosowanie do wdrożeń.
6.  Sprawdź notatki SAP związane z platformą Azure, takie jak [uwaga pomocy technicznej #1928533](https://launchpad.support.sap.com/#/notes/1928533), dla nowych jednostek SKU maszyn wirtualnych i nowo obsługiwanych wersji systemu operacyjnego i dbms. Porównaj ceny nowych typów maszyn wirtualnych z cenami starszych typów maszyn wirtualnych, dzięki czemu można wdrażać maszyny wirtualne z najlepszym stosunkiem ceny do wydajności.
7.  Ponownie sprawdzić notatki obsługi SAP, katalog sprzętu SAP HANA i SAP PAM. Upewnij się, że nie było żadnych zmian w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwane wersje systemu operacyjnego na tych maszynach wirtualnych i obsługiwane wersje SAP i DBMS.
8.  Sprawdź [w witrynie SAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) czy nie ma nowych jednostek SKU z certyfikatem HANA na platformie Azure. Porównaj ceny nowych jednostek SKU z tymi, które zostały zaplanowane do użycia. Ostatecznie, dokonać niezbędnych zmian, aby korzystać z tych, które mają najlepszy stosunek ceny do wydajności.
9.  Dostosuj skrypty wdrażania, aby używać nowych typów maszyn wirtualnych i włączaj nowe funkcje platformy Azure, których chcesz użyć.
10. Po wdrożeniu infrastruktury przetestuj i oceń opóźnienie sieci między maszynami wirtualnymi z warstwą aplikacji SAP a maszynami wirtualnymi dbms, zgodnie z uwagami obsługi SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki w wskazówki dotyczące opóźnienia sieci w [#1100926 w nocie pomocy technicznej SAP](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno znajdować się w umiarkowanym lub dobrym zakresie. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień HANA, zgodnie z opisem w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Upewnij się, że żadne z ograniczeń wymienionych w [rozważaniach dotyczących wdrażania DBMS maszyn wirtualnych platformy Azure dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) i konfiguracji infrastruktury SAP [HANA i infrastruktury SAP HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) nie mają zastosowania do wdrożenia.
11. Upewnij się, że maszyny wirtualne są wdrażane w odpowiedniej [grupie miejsc docelowych zbliżeniowych platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)zgodnie z opisem w [grupach miejsc docelowych zbliżeniowych platformy Azure, aby uzyskać optymalne opóźnienie sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
11. Przed zastosowaniem obciążenia należy wykonać wszystkie inne kontrole wymienione na etapie weryfikacji koncepcji.
12. W miarę stosowania obciążenia należy rejestrować zużycie zasobów systemów na platformie Azure. Porównaj to zużycie z rekordami ze starej platformy. Dostosuj rozmiary maszyn wirtualnych przyszłych wdrożeń, jeśli widzisz, że masz duże różnice. Należy pamiętać, że podczas zmniejszania rozmiaru, magazynu i przepustowości sieci maszyn wirtualnych zostanie zmniejszona, jak również.
    - [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Eksperymentuj z funkcjami i procesami kopiowania systemu. Celem jest ułatwienie kopiowania systemu dewelopera lub systemu testowego, aby zespoły projektowe mogły szybko uzyskać nowe systemy. Należy rozważyć użycie [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) dla tych zadań.
14. Optymalizuj i doskonalij dostęp, uprawnienia i procesy oparte na rolach zespołu na platformie Azure, aby upewnić się, że masz rozdzielenie obowiązków. Jednocześnie upewnij się, że wszystkie zespoły mogą wykonywać swoje zadania w infrastrukturze platformy Azure.
15. Ćwiczenia, testowanie i dokumentowanie procedur wysokiej dostępności i odzyskiwania po awarii, aby umożliwić pracownikom wykonywanie tych zadań. Identyfikowanie niedociągnięć i dostosowywanie nowych funkcji platformy Azure, które integrujesz z wdrożeniami.

 
## <a name="production-preparation-phase"></a>Faza przygotowania produkcji 
W tej fazie zebrać to, czego doświadczyłeś i dowiedziałeś się podczas wdrożeń nieprodukcyjnych i zastosuj je do przyszłych wdrożeń produkcyjnych. Należy również przygotować pracę transferu danych między bieżącą lokalizacją hostingu a platformą Azure.

1.  Przed przejściem na platformę Azure należy wykonać niezbędne uaktualnienia systemu SAP.
1.  Uzgodnij z właścicielami firm testy funkcjonalne i biznesowe, które muszą być przeprowadzone po migracji systemu produkcyjnego.
1.  Upewnij się, że te testy zostały zakończone z systemami źródłowymi w bieżącej lokalizacji hostingu. Unikaj przeprowadzania testów po raz pierwszy po przeniesieniu systemu na platformę Azure.
1.  Przetestuj proces migracji systemów produkcyjnych na platformę Azure. Jeśli nie przenosisz wszystkich systemów produkcyjnych na platformę Azure w tym samym przedziale czasu, stwórz grupy systemów produkcyjnych, które muszą znajdować się w tej samej lokalizacji hostingu. Przetestuj migrację danych. Oto kilka typowych metod:
    - Użyj metod DBMS, takich jak kopia zapasowa/przywracanie w połączeniu z programem SQL Server Always On, Replikacja systemu HANA lub Log wysyłka do materiału siewnego i synchronizacji zawartości bazy danych na platformie Azure.
    - Użyj kopii zapasowej/przywracania dla mniejszych baz danych.
    - Użyj Monitora migracji SAP, który jest zintegrowany z SAP SWPM, do wykonywania migracji heterogenicznych.
    - Użyj procesu [SAP DMO,](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) jeśli chcesz połączyć migrację z uaktualnieniem wersji SAP. Należy pamiętać, że nie wszystkie kombinacje źródłowego dbms i docelowego dbms są obsługiwane. Więcej informacji można znaleźć w konkretnych uwagach dotyczących obsługi SAP dla różnych wersji DMO. Na przykład [opcja migracji bazy danych (DMO) sumy 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Sprawdź, czy przepływność transferu danych jest lepsza za pośrednictwem Internetu lub za pośrednictwem usługi ExpressRoute, w przypadku konieczności przeniesienia kopii zapasowych lub plików eksportu SAP. Jeśli przenosisz dane przez Internet, może być konieczna zmiana niektórych reguł grupy zabezpieczeń sieciowych/grupy zabezpieczeń aplikacji, które będą potrzebne w przyszłych systemach produkcyjnych.
1.  Przed przeniesieniem systemów ze starej platformy na platformę Azure należy zebrać dane dotyczące zużycia zasobów. Przydatne dane obejmują użycie procesora CPU, przepływność magazynu i dane we/wy we/wy. Szczególnie zbierać te dane z jednostek warstwy DBMS, ale także zbierać je z jednostek warstwy aplikacji. Zmierz również opóźnienie sieci i magazynu.
1.  Ponownie sprawdzić notatki obsługi SAP i wymagane ustawienia systemu operacyjnego, katalog sprzętu SAP HANA i SAP PAM. Upewnij się, że nie było żadnych zmian w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwane wersje systemu operacyjnego w tych maszynach wirtualnych i obsługiwane wersje SAP i DBMS.
1.  Zaktualizuj skrypty wdrażania, aby uwzględnić najnowsze decyzje podjęte w typach maszyn wirtualnych i funkcjach platformy Azure.
1.  Po wdrożeniu infrastruktury i aplikacji sprawdź, czy:
    - Wdrożono odpowiednie typy maszyn wirtualnych z poprawnymi atrybutami i rozmiarami magazynu.
    - Maszyny wirtualne są na prawidłowe i pożądane wersje systemu operacyjnego i poprawki i są jednolite.
    - Maszyny wirtualne są hartowane zgodnie z wymaganiami i w jednolity sposób.
    - Zainstalowano i wdrożono poprawne wersje aplikacji i poprawki.
    - Maszyny wirtualne zostały wdrożone w zestawach dostępności platformy Azure zgodnie z planem.
    - Usługa Azure Premium Storage jest używana dla dysków wrażliwych na opóźnienia lub tam, gdzie wymagana jest [umowy SLA z jedną maszyną wirtualną 99,9%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - Akcelerator zapisu azure jest wdrożony poprawnie.
        - Upewnij się, że w obrębie maszyn wirtualnych, miejsca do magazynowania lub zestawy rozłożone zostały zbudowane poprawnie na dyskach, które wymagają akceleratora zapisu.
        - Sprawdź [konfigurację oprogramowania RAID na Linuksie](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Sprawdź [konfigurację LVM na maszynach wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)z systemem Linux na platformie Azure .
    - [Dyski zarządzane platformy Azure](https://azure.microsoft.com/services/managed-disks/) są używane wyłącznie.
    - Maszyny wirtualne zostały wdrożone w odpowiednich zestawach dostępności i strefach dostępności.
    - [Przyspieszona sieć platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i warstwie SAP DBMS.
    - Żadne [wirtualne urządzenia sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) nie znajdują się w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemów SAP opartych na SAP NetWeaver, Hybris lub S/4HANA.
    - Reguły grupy zabezpieczeń aplikacji i sieciowej grupy zabezpieczeń umożliwiają komunikację zgodnie z potrzebami i zaplanowaną oraz blokują komunikację w razie potrzeby.
    - Ustawienia limitu czasu są ustawione poprawnie, jak opisano wcześniej.
    - Maszyny wirtualne są wdrażane w odpowiedniej [grupie miejsc docelowych zbliżeniowych platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)zgodnie z opisem w [grupach umieszczania zbliżeniowego platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
    - Opóźnienie sieci między maszynami wirtualnymi z warstwą aplikacji SAP a maszynami wirtualnymi dbms jest testowane i sprawdzane zgodnie z opisem w uwagach do obsługi SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Oceń wyniki w wskazówki dotyczące opóźnienia sieci w [#1100926 w nocie pomocy technicznej SAP](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno znajdować się w umiarkowanym lub dobrym zakresie. Wyjątki mają zastosowanie do ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień HANA, zgodnie z opisem w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - Szyfrowanie zostało zaimplementowane w razie potrzeby i przy odpowiedniej metodzie szyfrowania.
    - Interfejsy i inne aplikacje mogą łączyć nowo wdrożoną infrastrukturę.
1.  Utwórz podręcznik do reagowania na planowane usługi konserwacji platformy Azure. Określ kolejność, w jakiej systemy i maszyny wirtualne powinny zostać ponownie uruchomione w celu planowanej konserwacji.
    

## <a name="go-live-phase"></a>Faza go-live
Podczas fazy go-live, należy śledzić podręczniki opracowane we wcześniejszych fazach. Wykonaj kroki, które zostały przetestowane i praktykowane. Nie akceptuj zmian w konfiguracjach i procesach w ostatniej chwili. Wykonaj również następujące kroki:

1. Sprawdź, czy monitorowanie witryny Azure portal i inne narzędzia do monitorowania działają. Zalecamy Monitor wydajności systemu Windows (perfmon) dla systemu Windows i SAR dla systemu Linux.
    - Liczniki procesora.
        - Średni czas procesora, suma (wszystkie procesory)
        - Średni czas procesora, każdy pojedynczy procesor (128 procesorów na maszynach wirtualnych M128)
        - Czas jądra procesora, każdy pojedynczy procesor
        - Czas użytkownika procesora, każdy pojedynczy procesor
    - Memory (pamięć).
        - Wolna pamięć
        - Strona pamięci w/sekundę
        - Wyjście strony pamięci/sekunda
    - Dysku.
        - Odczyt dysku w kbps, na pojedynczy dysk
        - Odczyty dysku na sekundę na pojedynczy dysk
        - Odczyt dysku w mikrosekundach/odczytie, na pojedynczy dysk
        - Zapis dysku w kbps, na pojedynczy dysk
        - Zapis dysku na sekundę na pojedynczy dysk
        - Zapis dysku w mikrosekundach/odczytie, na pojedynczy dysk
    - Sieci.
        - Pakiety sieciowe w/sekundę
        - Wyjścia pakietów sieciowych/na sekundę
        - Kb sieci w/sekundę
        - Wyjście bazy sieci/sekundę
1.  Po migracji danych wykonaj wszystkie testy sprawdzania poprawności uzgodnione z właścicielami firm. Zaakceptuj wyniki testów sprawdzania poprawności tylko wtedy, gdy masz wyniki dla oryginalnych systemów źródłowych.
1.  Sprawdź, czy interfejsy działają i czy inne aplikacje mogą komunikować się z nowo wdrożonymi systemami produkcyjnymi.
1.  Sprawdź system transportu i korekty za pośrednictwem transakcji SAP STMS.
1.  Wykonywanie kopii zapasowych bazy danych po wydaniu systemu do produkcji.
1.  Wykonywanie kopii zapasowych maszyn wirtualnych dla maszyn wirtualnych z warstwą aplikacji SAP po wydaniu systemu do środowiska produkcyjnego.
1.  W przypadku systemów SAP, które nie były częścią bieżącej fazy go-live, ale które komunikują się z systemami SAP, które zostały przeniesione na platformę Azure podczas tej fazy go-live, należy zresetować bufor nazw hosta w SM51. Spowoduje to usunięcie starych buforowanych adresów IP skojarzonych z nazwami wystąpień aplikacji przeniesionych na platformę Azure.  


## <a name="post-production"></a>Postprodukcja
Ta faza dotyczy monitorowania, obsługi i administrowania systemem. Z punktu widzenia SAP obowiązują zwykłe zadania, które były wymagane do wykonania w starej lokalizacji hostingu. Wykonaj również te zadania specyficzne dla platformy Azure:

1. Przejrzyj faktury platformy Azure dla systemów wysokiego obciążenia.
2. Optymalizacja wydajności cenowej i wydajności po stronie maszyny Wirtualnej i pamięci masowej.
3. Optymalizacja czasów, kiedy można wyłączyć systemy.  


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrożenie maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

