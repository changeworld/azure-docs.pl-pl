---
title: Planowanie obciążeń SAP i Lista kontrolna wdrożenia | Microsoft Docs
description: Lista kontrolna planowania i wdrożeń obciążeń SAP na platformie Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d6170dead4e8577cea6883ffea25b90ebe39b88
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079007"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Obciążenie SAP na liście kontrolnej planowania i wdrażania platformy Azure 

Ta lista kontrolna jest przeznaczona dla klientów, którzy przenosili aplikacje SAP NetWeaver, S/4HANA i Hybris do infrastruktury platformy Azure jako usługi.  Ta lista kontrolna powinna zostać sprawdzona przez partnera klienta i/lub SAP w czasie trwania projektu. Należy pamiętać, że wiele testów jest przeprowadzanych na początku projektu i w fazie planowania. Po zakończeniu wdrożenia podstawowe zmiany dotyczące wdrożonej infrastruktury platformy Azure lub wydań oprogramowania SAP mogą stać się złożone. Przejrzyj tę listę kontrolną w najważniejszych punktach kontrolnych w całym projekcie.  Małe problemy mogą zostać wykryte, zanim staną się duże problemy i że istnieje wystarczająco dużo czasu, aby ponownie opracowywać i testować wszelkie niezbędne zmiany. Lista kontrolna bez względu na to, jakie oświadczenia mają być kompletne. Zależnie od konkretnej sytuacji może istnieć wiele więcej kontroli, które należy wykonać. 

Zestawienie z listą kontrolną nie obejmuje zadań, które są niezależne od platformy Azure.  Przykład: Interfejsy aplikacji SAP zmieniają się podczas przenoszenia na chmurę publiczną platformy Azure lub do dostawcy hostingu.    

Ta lista kontrolna może być również używana dla już wdrożonych systemów. Od czasu wdrożenia mogły zostać dodane nowe funkcje, takie jak akcelerator zapisu, Strefy dostępności i nowe typy maszyn wirtualnych.  Dlatego warto okresowo przeglądać listę kontrolną, aby upewnić się, że znasz nowe funkcje platformy Azure. 

## <a name="project-preparation-and-planning-phase"></a>Faza przygotowywania i planowania projektu
W tej fazie zaplanowano migrację obciążenia SAP do chmury publicznej platformy Azure. Minimalny zestaw jednostek i elementów omówionych i zdefiniowanych list, takich jak:

1. Dokument projektu wysokiego poziomu — ten dokument powinien zawierać:
    1. Bieżący spis składników i aplikacji SAP oraz docelowy spis aplikacji na platformie Azure
    2. Twórz i pracuj za pomocą macierzy przypisywania odpowiedzialności (RACI), która określa obowiązki i przypisania różnych osób, których dotyczy. Rozpocznij na wysokim poziomie i pracuj z bardziej szczegółowymi poziomami przepływności planowanie i pierwsze wdrożenia
    2. Architektura rozwiązania wysokiego poziomu
    3. Decyzja dotycząca regionów świadczenia usługi Azure do wdrożenia. Aby zapoznać się z listą regionów świadczenia usługi Azure, sprawdź [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Aby uzyskać dostęp do usług w poszczególnych regionach świadczenia usługi Azure, zapoznaj się z artykułem [dostępnym](https://azure.microsoft.com/global-infrastructure/services/) dla poszczególnych regionów
    4. Architektura sieci do łączenia się z lokalnego programu z platformą Azure. Zacznij od siebie znać [Plan wirtualnego centrum danych dla platformy Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Zasady zabezpieczeń dotyczące uruchamiania wysokich danych o wpływie na platformę Azure. Aby czytać materiały, Rozpocznij od [dokumentacji zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/)
2.  Dokument techniczny projektu — zawiera:
    1.  Diagram blokowy rozwiązania 
    2.  Ustalanie rozmiarów składników obliczeniowych, magazynu i sieci na platformie Azure. Aby zmienić rozmiar SAP maszyn wirtualnych platformy Azure, zapoznaj się z uwagami dotyczącymi pomocy technicznej SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Ciągłość działania i architektura odzyskiwania po awarii
    4.  Szczegółowe wersje pakietu wsparcia dla systemów operacyjnych, baz danych, jądra i SAP. Nie wynika to z tego, że żadna wersja systemu operacyjnego obsługiwana przez oprogramowanie SAP NetWeaver lub S/4HANA jest obsługiwana na maszynach wirtualnych platformy Azure. To samo jest prawdziwe w przypadku wersji systemu DBMS. Należy zaewidencjonować następujące źródła, aby wyrównać i w razie potrzeby uaktualnić wydania SAP, wersje systemu DBMS lub wersje systemów operacyjnych, aby można było skorzystać z okna obsługiwanego w oprogramowaniu SAP i platformie Azure. Aby zapewnić pełną pomoc techniczną dla oprogramowania SAP i Microsoft, należy się zastanowić, że są używane kombinacje wydań obsługiwane w oprogramowaniu SAP i platformie Azure. W razie potrzeby należy zaplanować uaktualnienie niektórych składników oprogramowania. Więcej informacji o obsługiwanych oprogramowaniu SAP, OS i DBMS opisano w następujących lokalizacjach:
        1.  Uwaga dotycząca pomocy technicznej SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Ta Uwaga definiuje minimalne wersje systemu operacyjnego obsługiwane na maszynach wirtualnych platformy Azure. Definiuje również minimalne wersje bazy danych wymagane w przypadku większości baz danych innych niż HANA. Zawarto również informacje dotyczące ustalania rozmiarów SAP dla różnych typów maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP.
        2.  Uwaga dotycząca pomocy technicznej SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619). Uwaga definiuje Macierz obsługi Oracle na platformie Azure. Należy pamiętać, że program Oracle obsługuje tylko systemy Windows i Oracle Linux jako system operacyjny gościa na platformie Azure dla obciążeń SAP. Ta instrukcja obsługi dotyczy również warstwy aplikacji SAP, w której działają wystąpienia SAP. Jednak firma Oracle nie obsługuje wysokiej dostępności usług SAP Central w Oracle Linux za pomocą Pacemaker. Jeśli potrzebujesz wysokiej dostępności dla ASCS na Oracle Linux, musisz użyć pakietu ochrony oprogramowanie SIOS dla systemu Linux. Aby uzyskać szczegółowe informacje o certyfikatach SAP, sprawdź pomoc techniczną SAP [#1662610 — szczegóły pomocy technicznej dla programu oprogramowanie SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610). W przypadku systemu Windows rozwiązanie SAP obsługujące klaster trybu failover systemu Windows dla usług SAP Central jest obsługiwane w połączeniu z bazą danych Oracle jako warstwą DBMS. 
        3.  Uwaga dotycząca pomocy technicznej SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) uzyskać macierz pomocy technicznej dla SAP HANA w różnych wersjach systemu operacyjnego
        4.  W [tym miejscu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) znajdują się SAP HANA obsługiwane maszyny wirtualne platformy Azure i [duże wystąpienia Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
        5.  [Macierz dostępności produktu SAP](https://support.sap.com/en/)
        6.  Inne uwagi dotyczące oprogramowania SAP dla innych określonych produktów SAP  
    5.  Zalecamy stosowanie rygorystycznych projektów 3-warstwowych dla systemów produkcyjnych SAP. Łączenie serwerów ASCS i aplikacji na tej samej maszynie wirtualnej nie jest zalecane.  Korzystanie z konfiguracji klastra z obsługą wiele identyfikatorów SID dla usług SAP Central jest obsługiwane w systemie Windows jako system operacyjny gościa na platformie Azure. Konfiguracje klastra z usługą SAP Central Services nie są obsługiwane w systemach operacyjnych Linux na platformie Azure. Dokumentacja dotycząca przypadku systemu operacyjnego gościa systemu Windows znajduje się w temacie:
        1.  [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem usługi Windows Server Failover Clustering i dysku udostępnionego na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem klastra trybu failover systemu Windows Server i udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Architektura wysokiej dostępności i odzyskiwania po awarii
        1.  Zdefiniuj w oparciu o RTO i cel punktu odzyskiwania, co ma mieć wygląd
        2.  Aby zapewnić wysoką dostępność w ramach tej samej strefy, należy sprawdzić, co wymaga system DBMS w systemie Azure. Większość systemów DBMS oferuje synchroniczną metodę synchronicznej rezerwy gorącą, którą zalecamy w systemach produkcyjnych. Zapoznaj się również z dokumentacją dotyczącą oprogramowania SAP dla różnych baz danych, rozpoczynając od [uwagi dotyczącej wdrażania platformy Azure Virtual Machines DBMS dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) i powiązanych dokumentów
            1.  Korzystanie z usługi klastra trybu failover systemu Windows z konfiguracją udostępnionego dysku dla warstwy DBMS, na przykład opisanej [tutaj](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) SQL Server **nie** jest obsługiwane. Zamiast tego rozwiązania takie jak:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Ochrona danych firmy Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replikacja systemu HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  W przypadku odzyskiwania po awarii w różnych regionach świadczenia usługi Azure Sprawdź, jakie możliwości są oferowane przez różnych dostawców systemu DBMS. Większość z nich obsługuje replikację asynchroniczną lub wysyłanie dziennika
        4.  W przypadku warstwy aplikacji SAP należy określić, czy należy uruchamiać systemy testowe regresji biznesowej, które najlepiej sprawdzają się w przypadku replik wdrożeń produkcyjnych w tym samym regionie świadczenia usługi Azure lub w regionie odzyskiwania po awarii. W tym drugim przypadku można wskazać, że system regresji biznesowej jako element docelowy odzyskiwania po awarii dla środowiska produkcyjnego
        5.  Jeśli zdecydujesz się nie umieszczać systemów nieprodukcyjnych w witrynie odzyskiwania po awarii, zapoznaj się z Azure Site Recovery jako dożywotną metodę replikowania warstwy aplikacji SAP do regionu Azure DR. Zobacz również [Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrażania aplikacji SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  W przypadku podjęcia decyzji o użyciu połączonej konfiguracji HA/DR, wykorzystując [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) należy zapoznać się z regionami świadczenia usługi Azure, w których strefy dostępności są dostępne, oraz z ograniczeniami, które mogą być wprowadzane przez zwiększone opóźnienia sieci między dwoma Strefy dostępności  
3.  Klient/Partner powinien utworzyć spis wszystkich interfejsów SAP (SAP i non-SAP). 
4.  Projekt usług Foundation Services — ten projekt obejmuje elementy takie jak
    1.  Projektowanie Active Directory i DNS
    2.  Topologia sieci na platformie Azure i przypisywanie różnych systemów SAP
    3.  Struktura [dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) dla różnych zespołów zarządzających infrastrukturą i aplikacjami SAP na platformie Azure
    3.  Topologia grupy zasobów 
    4.  [Strategia tagowania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Konwencja nazewnictwa dla maszyn wirtualnych i innych składników infrastruktury i/lub nazw logicznych
5.  Kontrakt pomoc techniczna Premier firmy Microsoft — identyfikowanie programu MS Technical Account Manager (konsultant). Aby uzyskać wymagania dotyczące pomocy technicznej zgodnie z przepisami SAP Read support SAP, [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Zdefiniuj liczbę subskrypcji platformy Azure i limit przydziału rdzeni dla różnych subskrypcji. [Otwórz żądania obsługi, aby zwiększyć przydziały subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w razie potrzeby 
7.  Planowanie zmniejszenia i migracji danych na potrzeby migrowania danych SAP na platformę Azure. W przypadku systemów SAP NetWeaver w systemie SAP zawarto wskazówki dotyczące zachowywania ilości dużej ilości danych. SAP opublikował [ten głęboki Przewodnik](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) dotyczący zarządzania danymi w systemach SAP ERP. Jednak niektóre treści są ogólnie stosowane do systemów NetWeaver i S/4HANA.
8.  Definiowanie i decydowanie o zautomatyzowanym podejściu wdrażania. Celem automatyzacji za wdrażaniem infrastruktury na platformie Azure jest wdrożenie w sposób deterministyczny i uzyskanie deterministycznych wyników. Wielu klientów używa powłoki PowerShell lub skryptów opartych na interfejsie wiersza polecenia. Istnieją jednak różne technologie "open source", które mogą służyć do wdrażania infrastruktury platformy Azure dla oprogramowania SAP, a nawet do instalowania programów SAP. Przykłady można znaleźć w witrynie GitHub:
    1.  [Automatyczne wdrożenia SAP w chmurze platformy Azure](https://github.com/Azure/sap-hana)
    2.  [Instalacja SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definiowanie regularnego przeglądu projektu i wdrożenia erze między klientem, integratorem systemów, firmą Microsoft i innymi podmiotami.

 
## <a name="pilot-phase-strongly-recommended"></a>Faza pilotażowa (zdecydowanie zalecane)
 
Pilotaż może być uruchamiany przed lub równolegle do planowania i przygotowywania projektu. Fazy można także użyć do testowania podejścia i projektowania w fazie planowania i przygotowywania. Fazę pilotażową można rozciągnąć do prawdziwej weryfikacji koncepcji. Zalecane jest skonfigurowanie i zweryfikowanie pełnego rozwiązania HA/DR oraz projektu zabezpieczeń podczas wdrażania pilotażowego. W przypadku niektórych klientów można także przeprowadzić testy skalowalne w tej fazie. Inni klienci używają wdrożenia systemów piaskownicy SAP jako fazy pilotażowej. Załóżmy, że określono system do migracji na platformę Azure w celu uruchomienia pilotażu.

1. Zoptymalizuj transfer danych na platformę Azure. Wysoce zależnie od tego, czy transfer przypadków za pośrednictwem [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ze sklepu lokalnego był szybszy, jeśli obwód Express miał wystarczającą przepustowość. W przypadku innych klientów przechodzenie przez Internet w szybszy sposób
2. W przypadku migracji na platformę programu SAP heterogeniczną, która obejmuje eksport i import danych bazy danych, testowanie i optymalizowanie faz eksportu i importu. W przypadku dużych migracji obejmujących SQL Server jako platformę docelową zalecenia można znaleźć [tutaj](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Możesz skorzystać z metody Monitor migracji/SWPM na wypadek, gdyby podczas łączenia migracji z aktualizacją wydania SAP nie jest potrzebny proces uaktualnienia wydania lub rozwiązania [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) udokumentowane, na przykład w przypadku [opcji Migrowanie bazy danych (DMO) z sumy 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Eksportuj do źródła, Eksportuj przekazywanie plików na platformę Azure i zaimportuj wydajność.  Maksymalizuj nakładanie się między eksportem a importem
   2.  Oceń wielkość bazy danych między platformą docelową a docelową w celu odzwierciedlenia wielkości infrastruktury    
   3.  Sprawdzanie poprawności i optymalizowanie chronometrażu 
3. Weryfikacja techniczna 
   1. Typy maszyn wirtualnych
      1.  Sprawdź poprawność zasobów na temat pomocy technicznej SAP, SAP HANA katalogu sprzętu i usługi Azure PAM ponownie, aby upewnić się, że nie zostały wprowadzone żadne zmiany w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwane wersje systemu operacyjnego dla tych typów maszyn wirtualnych oraz obsługiwane wersje oprogramowania SAP i DBMS
      2.  Sprawdź ponownie rozmiar aplikacji oraz infrastrukturę wdrażaną na platformie Azure. W przypadku przeniesienia istniejących aplikacji często można uzyskać niezbędne protokoły SAP z infrastruktury, z której korzystasz, oraz [stronę sieci Web testu porównawczego SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) i porównać ją z numerami punktów SAP wymienionymi w uwagach pomocy technicznej SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Należy również pamiętać o [tym artykule](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
      3.  Oceń i przetestuj rozmiary maszyn wirtualnych platformy Azure w celu uzyskania maksymalnej przepływności magazynu i przepływności sieci dla różnych typów maszyn wirtualnych wybranych w fazie planowania. Dane można znaleźć w:
          1.  [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Ważne jest, aby uwzględnić **maksymalną przepływność dysku** niebuforowanego dla zmiany rozmiaru
          2.  [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Ważne jest, aby uwzględnić **maksymalną przepływność dysku** niebuforowanego dla zmiany rozmiaru
   2. Magazyn
      1.  Korzystanie z [usługi Azure SSD w warstwie Standardowa Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) jako minimum dla maszyn wirtualnych reprezentujących warstwy aplikacji SAP oraz wdrożenia nieuwzględniającego wydajności poufnego systemu DBMS
      2.  Zalecamy, aby nie używać [dysków usługi Azure HDD w warstwie Standardowa](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) w ogóle
      2.  Użyj [Premium Storage platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) dla wszystkich maszyn wirtualnych z systemem DBMS, które są zdalnie wrażliwe na wydajność
      2.  Korzystanie z [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Użyj usługi Azure akcelerator zapisu dla dysków dziennika DBMS z serii M. Należy pamiętać o limitach i użyciu akceleratorów zapisu zgodnie z opisem w [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  W przypadku różnych typów systemów DBMS należy zapoznać się z [ogólną dokumentacją dla systemu DBMS dotyczącą oprogramowania SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) oraz dokumentami specyficznymi dla systemu DBMS.
      5.  Aby uzyskać SAP HANA, więcej informacji znajduje się w temacie [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Nie należy instalować dysków danych platformy Azure na maszynę wirtualną z systemem Linux platformy Azure przy użyciu identyfikatora urządzenia. Zamiast tego należy użyć uniwersalnego identyfikatora unikatowego (UUID). Należy zachować ostrożność podczas instalowania dysków danych platformy Azure przy użyciu narzędzi graficznych. Podwójne sprawdzenie wpisów w/etc/fstab, aby upewnić się, że dyski są zainstalowane przy użyciu identyfikatora UUID
          1.  Więcej szczegółów można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Networking
      1.  Przetestuj i Oceń infrastrukturę sieci wirtualnej oraz dystrybucję aplikacji SAP w różnych sieciach wirtualnych platformy Azure lub w ich obrębie
          1.  Oceń podejście do architektury sieci wirtualnej typu Hub i szprych lub mikrosegmentacji w ramach jednej sieci wirtualnej platformy Azure opartej na
              1.  Koszty związane z wymianą danych między [usługą Azure sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Aby sprawdzić koszty [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Zalety szybkiego rozłączenia komunikacji równorzędnej między sieciami wirtualnymi platformy Azure w porównaniu z sieciowej grupy ZABEZPIECZEŃem w celu odizolowania podsieci w sieci wirtualnej, w przypadku których aplikacje lub maszyny wirtualne hostowane w podsieci sieci wirtualnej stały się zagrożeniem bezpieczeństwa
              3.  Centralne rejestrowanie i inspekcja ruchu sieciowego między lokalnym, zewnętrznym i wirtualnym centrum danych, które zostało utworzone na platformie Azure
          2.  Oceń i Testuj ścieżkę danych między warstwą aplikacji SAP i warstwą SAP DBMS. 
              1.  Wszystkie rozmieszczenie [urządzeń wirtualnych sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a WARSTWą DBMS w systemach SAP NetWeaver, Hybris lub S/4HANA opartych na systemie SAP nie jest w ogóle obsługiwane
              2.  Umieszczenie warstwy aplikacji SAP i SAP DBMS w różnych sieciach wirtualnych platformy Azure, które nie są połączone za pomocą komunikacji równorzędnej, nie jest obsługiwane
              3.  [Reguły usługi Azure ASG i sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) obsługują Definiowanie tras między warstwą aplikacji SAP i WARSTWĄ SAP DBMS
          3.  Upewnij się, że [usługa Azure przyspieszone sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i w warstwie systemu SAP DBMS. Należy pamiętać, że różne poziomy systemu operacyjnego są konieczne do obsługi przyspieszonej sieci na platformie Azure:
              1.  Windows Server 2012 R2 lub nowsze wersje
              2.  SUSE Linux 12 z dodatkiem SP3 lub nowszym
              3.  RHEL 7,4 lub nowsze wersje
              4.  Oracle Linux 7,5. W przypadku używania jądra RHCKL wersja musi być 3.10.0-862.13.1. el7. Wymagana jest wersja 5 jądra programu Oracle UEK
          4.   Przetestuj i Oceń opóźnienia sieci między MASZYNami wirtualnymi aplikacji SAP i maszyną wirtualną DBMS zgodnie z uwagą pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)uwagi dotyczące pomocy technicznej SAP. Oceń wyniki na wskazówki dotyczące opóźnień sieci w przypadku pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w średnim i odpowiednim zakresie. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Upewnij się, że wdrożenia ILB są skonfigurowane do używania bezpośredniego powrotu do serwera. To ustawienie spowoduje zredukowanie opóźnień w przypadkach, w których usługa Azure ILB jest używana do konfiguracji wysokiej dostępności na warstwie DBMS
          6.   Jeśli używasz Azure Load Balancer w połączeniu z systemami operacyjnymi gościa z systemem Linux, sprawdź, czy parametr sieci systemu Linux **net. IPv4. TCP _timestamps** jest ustawiony na **wartość 0**. Zaleceń w starszych wersjach programu SAP Uwaga [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Należy pamiętać, że w celu odzwierciedlenia faktu, że parametr musi być ustawiony na wartość 0, aby współdziałać z modułami równoważenia obciążenia platformy Azure.
          7.   Rozważ użycie [grupy umieszczania usługi Azure zbliżeniowe](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , zgodnie z opisem w temacie [grupy umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md) , aby uzyskać optymalne opóźnienia sieci.
   4. Wdrożenia wysokiej dostępności i odzyskiwania po awarii. 
      1. W przypadku wdrażania warstwy aplikacji SAP bez definiowania określonej strefy dostępności platformy Azure upewnij się, że wszystkie maszyny wirtualne z uruchomionym wystąpieniem okna dialogowego SAP lub oprogramowaniem pośredniczącym pojedynczego systemu SAP są wdrożone w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Jeśli usługa SAP Central i system DBMS nie wymagają wysokiej dostępności, te maszyny wirtualne można wdrożyć w tym samym zestawie dostępności co warstwa aplikacji SAP.
      2. W przypadku ochrony usług SAP Central i warstwy DBMS w celu zapewnienia wysokiej dostępności przy użyciu replik pasywnych należy mieć dwa węzły dla usług SAP Central w jednym osobnym zestawie dostępności i dwóch węzłach DBMS w innym zestawie dostępności.
      3. W przypadku wdrażania w Strefy dostępności platformy Azure nie można korzystać z zestawów dostępności. Należy jednak upewnić się, że węzły Active i pasywny Central Services są wdrażane w dwóch różnych Strefy dostępnościach, które pokazują najmniejsze opóźnienia między strefami.
         1.   Należy pamiętać, że w przypadku ustanowienia klastrów trybu failover systemu Windows lub Pacemaker dla warstwy systemów DBMS i SAP centralnych między Strefy dostępności należy używać [Usługa Load Balancer w warstwie Standardowa platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) . [Podstawowego Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) nie można używać w przypadku wdrożeń strefowych 
   5. Ustawienia limitu czasu
      1. Sprawdź ślady oprogramowania SAP NetWeaver Developer w różnych wystąpieniach oprogramowania SAP i upewnij się, że nie są wymienione żadne przerwy połączenia między serwerem z kolejki a procesami roboczymi SAP. Te przerwy połączenia można uniknąć, ustawiając te dwa parametry rejestru:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 — Zobacz również [ten artykuł](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 — Zobacz również [ten artykuł](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Aby uniknąć czasu graficznego interfejsu użytkownika między wdrożonymi w jednym miejscu interfejsem GUI interfejsu użytkownika oprogramowania SAP i warstwami aplikacji SAP wdrożonymi na platformie Azure, sprawdź, czy następujące parametry są ustawione w domyślnym. pfl lub profilu wystąpienia:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/utrzymywanie aktywności = 20
      3. Aby zapobiec zakłóceniom ustanowionych połączeń między procesem kolejkowania SAP a programem SAP workprocesse, parametr umieścić/encni/set_so_keepalive musi być ustawiony na wartość "true". Zobacz też [#2743751 uwagi dotyczące oprogramowania SAP](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Jeśli używasz konfiguracji klastra trybu failover systemu Windows, upewnij się, że czas reakcji na węzły, które nie odpowiada, jest ustawiony poprawnie dla platformy Azure. [Progi sieci klastra trybu failover dostrajania](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) artykułu firmy Microsoft zawierają parametry i ich wpływ na sensitivities trybu failover. Przy założeniu, że węzły klastra znajdują się w tej samej podsieci, należy zmienić następujące parametry:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Testowanie wysokiej dostępności i procedur odzyskiwania po awarii
   1. Symuluj sytuacje trybu failover, zamykając maszyny wirtualne (system operacyjny gościa systemu Windows) lub umieszczając systemy operacyjne w trybie awaryjnego (system operacyjny gościa Linux), aby ustalić, czy konfiguracje trybu failover działają zgodnie z założeniami. 
   2. Zmierz czasy wykonywania przejścia w tryb failover. Jeśli czas trwa zbyt długo, Rozważ następujące kwestie:
      1.   W przypadku systemu SUSE Linux Użyj urządzeń SBD zamiast Agenta ogrodzenia platformy Azure w celu przyspieszenia pracy w trybie failover
      2.   W przypadku SAP HANA, jeśli ponowne załadowanie danych trwa zbyt długo, należy rozważyć zwiększenie przepustowości magazynu
   3. Przetestuj wykonywanie kopii zapasowej/Przywróć sekwencję i czas i dostosuj je w razie potrzeby. Upewnij się, że nie tylko czasy wykonywania kopii zapasowej są wystarczające. Przetestuj również przywracanie i podejmij czas działania przywracania. Upewnij się, że czasy przywracania znajdują się w RTO umowy SLA, gdzie RTO zależy od bazy danych lub procesu przywracania maszyny wirtualnej
   4. Testowanie funkcji i architektury DR w regionie
5. Sprawdzanie zabezpieczeń
   1.  Przetestuj ważność wdrożonej architektury dostępu opartej na rolach (RBAC) na platformie Azure. Celem jest oddzielenie i ograniczenie dostępu oraz uprawnień różnych zespołów. Przykładowo członkowie zespołu SAP powinni mieć możliwość wdrażania maszyn wirtualnych i przypisywania dysków z usługi Azure Storage do danej sieci wirtualnej platformy Azure. Jednak zespół podstawy SAP nie powinien mieć możliwości tworzenia własnych sieci wirtualnych ani zmieniać ustawień istniejących sieci wirtualnych. Po drugiej stronie członkowie zespołu sieciowego nie mogą wdrażać maszyn wirtualnych w sieciach wirtualnych, w których są uruchomione maszyny wirtualne aplikacji SAP i DBMS. Ani członkowie zespołu sieci mogą zmieniać atrybuty maszyn wirtualnych, a nawet usuwać maszyny wirtualne lub dyski.  
   2.  Sprawdź, czy reguły [sieciowej grupy zabezpieczeń i ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) działają zgodnie z oczekiwaniami, i Włącz ochronę chronionych zasobów
   3.  Upewnij się, że wszystkie zasoby, które muszą być szyfrowane, są szyfrowane. Definiowanie i wykonywanie procesów w celu tworzenia kopii zapasowych certyfikatów, magazynu i uzyskiwania dostępu do tych certyfikatów oraz przywracania zaszyfrowanych jednostek. 
   4.  Użyj [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) i/lub dla dysków systemu operacyjnego, jeśli to możliwe, z punktu widzenia obsługi systemu operacyjnego
   5.  Sprawdź, czy użyto zbyt wielu warstw szyfrowania. Jest to ograniczone, aby korzystać z usługi Azure Disk Encryption, a następnie na jednej z tych metod szyfrowania niejawnych baz danych systemu DBMS
6. Testowanie wydajnościowe
   1.  W oprogramowaniu SAP w oparciu o śledzenie i pomiary SAP Porównaj 10 pierwszych raportów online z bieżącą implementacją, o ile ma to zastosowanie 
   2.  W oprogramowaniu SAP w oparciu o śledzenie i pomiary oprogramowania SAP Porównaj 10 najważniejszych zadań wsadowych z bieżącą implementacją, o ile ma to zastosowanie 
   3.  W oprogramowaniu SAP w oparciu o śledzenie i pomiary SAP Porównaj transfery danych za pośrednictwem interfejsów z systemem SAP. Skup się na interfejsach, w których wiesz, że transfer odbywa się teraz między różnymi lokalizacjami, takimi jak przechodzenie do platformy lokalnej na platformę Azure 


## <a name="non-production-phase"></a>Faza nieprodukcyjna 
W tej fazie przyjęto założenie, że po pomyślnym uruchomieniu pilotażu lub koncepcji ZK zaczniesz wdrożyć nieprodukcyjne systemy SAP na platformie Azure. Wszystkie informacje i doświadczenia z weryfikacji koncepcji należy dostosować do takiego wdrożenia. Wszystkie kryteria i kroki wymienione w koncepcji ZK są stosowane również w przypadku tego typu wdrożeń. W tej fazie zwykle wdrażane są systemy deweloperskie, systemy testów jednostkowych i systemy testów regresji biznesowej na platformie Azure. Zaleca się, aby co najmniej jeden system nieprodukcyjny w jednym wierszu aplikacji SAP miał pełną konfigurację wysokiej dostępności, która będzie miała przyszły system produkcyjny. Dodatkowe kroki, które należy wziąć pod uwagę podczas tej fazy, to:  

1.  Przed przeniesieniem systemów ze starej platformy do platformy Azure zbiera dane dotyczące zużycia zasobów, takie jak użycie procesora CPU, przepływność magazynu i dane IOPS. Szczególnie w przypadku jednostek warstwy DBMS, ale również od jednostek warstwy aplikacji. Należy również mierzyć opóźnienia sieci i magazynu.
2.  Zapisz wzorce czasu korzystania z dostępności dla systemów. Celem jest ustalenie, czy systemy nieprodukcyjne muszą być 7x24 dostępne lub czy istnieją systemy nieprodukcyjne, które można zamknąć w niektórych fazach tygodnia lub miesiąca.
3.  Przetestuj i zdefiniuj, czy chcesz utworzyć własne obrazy systemu operacyjnego dla maszyn wirtualnych na platformie Azure, czy chcesz użyć obrazu z galerii obrazów platformy Azure. Jeśli używasz obrazu z galerii platformy Azure, upewnij się, że podano prawidłowy obraz, który odzwierciedla umowę pomocy technicznej z dostawcą systemu operacyjnego. W przypadku niektórych dostawców systemów operacyjnych Galerie platformy Azure oferują własne obrazy licencji. W przypadku innych obrazów systemu operacyjnego pomoc techniczna jest uwzględniana w cenie oferowanej przez platformę Azure. Jeśli zdecydujesz się utworzyć własne obrazy systemu operacyjnego, możesz znaleźć dokumentację w następujących artykułach:
    1.  Można skompilować uogólniony obraz maszyny wirtualnej z systemem Windows wdrożonej na platformie Azure na podstawie [tej dokumentacji](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Można utworzyć uogólniony obraz maszyny wirtualnej z systemem Linux wdrożonej na platformie Azure na podstawie [tej dokumentacji](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Jeśli używasz obrazów SUSE i Red Hat Linux z galerii maszyn wirtualnych platformy Azure, musisz użyć obrazów dla SAP dostarczonych przez dostawców systemu Linux w galerii maszyn wirtualnych platformy Azure.
4.  Upewnij się, że spełniasz wymagania dotyczące pomocy technicznej dotyczące umów pomocy technicznej firmy Microsoft. Informacje znajdują się w temacie uwagi dotyczące pomocy technicznej SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Duże wystąpienia HANA zapoznaj się z [wymaganiami dotyczącymi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements) dołączania do dokumentów
4.  Upewnij się, że odpowiednie osoby otrzymują [powiadomienia o planowanej konserwacji](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), więc możesz wybrać przestoje i ponowne uruchomienie maszyn wirtualnych w czasie
5.  Stale sprawdzaj dokumentację platformy Azure dla prezentacji firmy Microsoft na takich kanałach jak [channel9](https://channel9.msdn.com/) , aby uzyskać nowe funkcje, które mogą być stosowane do wdrożeń
6.  Sprawdź uwagi SAP powiązane z platformą Azure, takie jak [#1928533](https://launchpad.support.sap.com/#/notes/1928533) uwagi na temat pomocy technicznej dla nowych jednostek SKU maszyn wirtualnych lub nowo obsługiwane wersje systemów operacyjnych i DBMS. Porównaj nowe typy maszyn wirtualnych ze starszymi typami maszyn wirtualnych w cenniku, dzięki czemu możesz wdrażać maszyny wirtualne z najlepszym wskaźnikiem cen/wydajności
7.  Sprawdź poprawność zasobów na temat pomocy technicznej SAP, SAP HANA katalogu sprzętu i usługi Azure PAM ponownie, aby upewnić się, że nie zostały wprowadzone żadne zmiany w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwane wersje systemu operacyjnego w tych maszynach wirtualnych oraz obsługiwane wersje oprogramowania SAP i DBMS
8.  Zapoznaj się z nowymi jednostkami SKU certyfikowanych platformy HANA na platformie Azure i Porównaj ceny z zaplanowanymi planami i ostatecznie Zmień, aby uzyskać dostęp do jednostek z lepszymi dawkami dotyczącymi wydajności [](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 
9.  Dostosuj skrypty wdrażania, aby wykorzystać nowe typy maszyn wirtualnych i uwzględnić nowe funkcje platformy Azure, których chcesz użyć
10. Po wdrożeniu infrastruktury Przetestuj i Oceń opóźnienia sieci między MASZYNami wirtualnymi aplikacji SAP i maszyną wirtualną DBMS, zgodnie z uwagami dotyczącymi pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)uwagi dotyczące pomocy technicznej SAP. Oceń wyniki na wskazówki dotyczące opóźnień sieci w przypadku pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w średnim i odpowiednim zakresie. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA, jak opisano [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Upewnij się, że żadne ograniczenia wymienione w temacie [uwagi dotyczące wdrażania platformy azure Virtual Machines DBMS dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) i [SAP HANA konfiguracje infrastruktury i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) mają zastosowanie do Twojego wdrożenia
11. Upewnij się, że maszyny wirtualne zostały wdrożone przy użyciu poprawnej [grupy umieszczania w pobliżu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , zgodnie z opisem w temacie [grupy umieszczania zbliżeniowe platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP](sap-proximity-placement-scenarios.md)
11. Wykonaj wszystkie inne kontrole wymienione w fazie sprawdzania koncepcji przed zastosowaniem obciążenia
12. Ponieważ obowiązuje obciążenie, należy zarejestrować użycie zasobów przez te systemy na platformie Azure i porównać z rekordami uzyskanymi ze starej platformy. Dostosuj rozmiary maszyn wirtualnych w przyszłych wdrożeniach, Jeśli zobaczysz, że masz większe różnice. Należy pamiętać, że w przypadku, gdy downsizing, magazyn i przepustowość sieci maszyny wirtualnej zostaną zmniejszone również:
    1.  [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Pracuj nad funkcją i procesami kopiowania systemu. Celem jest ułatwienie kopiowania systemu deweloperskiego lub systemu testowego, dzięki czemu zespoły projektu mogą szybko uzyskać dostęp do nowych systemów. Należy rozważyć rozwiązanie [SAP Lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) jako narzędzie do wykonywania takich zadań.
14. Zoptymalizuj i stworzyć właściwy dostęp oparty na rolach, uprawnienia i procesy platformy Azure dla zespołu, aby upewnić się, że masz rozdzielenie cła z jednej strony. Z drugiej strony, chcesz, aby wszystkie zespoły mogły wykonywać swoje zadania w infrastrukturze platformy Azure.
15. Ćwiczenia, testowanie i udokumentowanie procedur wysokiej dostępności i odzyskiwania po awarii, aby umożliwić pracownikom wykonywanie takich zadań. Zidentyfikuj niedoskonałości i Dostosuj nowe funkcje platformy Azure, które są integrowane ze wdrożeniami

 
## <a name="production-preparation-phase"></a>Faza przygotowania produkcyjnego 
W tej fazie warto zebrać wszystkie doświadczenia i informacje o wdrożeniach nieprodukcyjnych i zastosować je w przyszłych wdrożeniach produkcyjnych. Oprócz tego należy również przygotować zadania transferu danych między bieżącą lokalizacją hostingu i platformą Azure. 

1.  Przed przejściem do platformy Azure Pracuj z niezbędnymi uaktualnieniami wydania SAP w systemach produkcyjnych
2.  Wyrażanie zgody właścicielom biznesowym w testach funkcjonalnych i gospodarczych, które należy wykonać po migracji systemu produkcyjnego
    1.  Upewnij się, że wszystkie te testy są wykonywane z systemami źródłowymi w bieżącej lokalizacji hostingu. Chcesz uniknąć przeprowadzania testów po raz pierwszy po przeniesieniu systemu na platformę Azure
2.  Testowanie procesu migracji produkcyjnej na platformę Azure. W przypadku nie przenoszenia wszystkich systemów produkcyjnych na platformę Azure w tym samym przedziale czasowym grupy kompilacji systemów produkcyjnych, które muszą znajdować się w tej samej lokalizacji hostingu. Ćwiczenie i testowanie migracji danych. Lista wspólnych metod, taka jak:
    1.  Korzystanie z metod systemu DBMS, takich jak tworzenie kopii zapasowej/przywracanie w połączeniu z funkcją SQL Server AlwaysOn, replikacja systemu HANA lub wysyłanie dziennika do inicjatora i synchronizowanie zawartości bazy danych na platformie Azure
    2.  Korzystanie z kopii zapasowej/przywracania dla mniejszych baz danych
    3.  Używanie monitora migracji SAP zaimplementowanego w narzędziu SAP SWPM do wykonywania migracji heterogenicznych
    4.  Użyj procesu [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) , jeśli chcesz połączyć się z uaktualnieniem SAP Release. Należy pamiętać, że nie wszystkie kombinacje między źródłem i docelowym systemem DBMS są obsługiwane. Więcej informacji można znaleźć w informacjach o pomocy technicznej SAP dla różnych wersji DMO. Na przykład [opcja migracji bazy danych (DMO) z sumy 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Testowanie, czy transfer danych za poorednictwem Internetu, czy przez ExpressRoute, jest lepszym rozwiązaniem przepływności na wypadek, gdyby trzeba było przenieść kopie zapasowe lub pliki eksportu SAP. W przypadku przeniesienia danych za pomocą Internetu może być konieczna zmiana niektórych reguł zabezpieczeń sieciowej grupy zabezpieczeń/ASG, które należy wprowadzić w przyszłych systemach produkcyjnych
3.  Przed przeniesieniem systemów ze starej platformy do platformy Azure zbiera dane dotyczące zużycia zasobów, takie jak użycie procesora CPU, przepływność magazynu i dane IOPS. Szczególnie w przypadku jednostek warstwy DBMS, ale również od jednostek warstwy aplikacji. Należy również mierzyć opóźnienia sieci i magazynu.
4.  Sprawdź poprawność zasobów na temat pomocy technicznej SAP, SAP HANA katalogu sprzętu i usługi Azure PAM ponownie, aby upewnić się, że nie zostały wprowadzone żadne zmiany w obsługiwanych maszynach wirtualnych dla platformy Azure, obsługiwane wersje systemu operacyjnego w tych maszynach wirtualnych oraz obsługiwane wersje oprogramowania SAP i DBMS 
4.  Dostosowuj skrypty wdrażania do najnowszych zmian wprowadzonych w typach maszyn wirtualnych i funkcji platformy Azure
5.  Po wdrożeniu infrastruktury i aplikacji przejdź przez serię kontroli w celu sprawdzenia:
    1.  Prawidłowe typy maszyn wirtualnych zostały wdrożone z prawidłowymi atrybutami i rozmiarami magazynu.
    2.  Sprawdź, czy maszyny wirtualne znajdują się w poprawnych i odpowiednich wersjach systemu operacyjnego oraz poprawek i są jednorodne
    3.  Sprawdź, czy ograniczanie funkcjonalności maszyn wirtualnych jest wymagane i ujednolicone
    4.  Sprawdź, czy poprawne wersje i poprawki aplikacji zostały zainstalowane i wdrożone
    5.  Maszyny wirtualne zostały wdrożone w zestawach dostępności platformy Azure jako planowane
    6.  Usługa Azure Premium Storage została użyta w przypadku dysków z wrażliwym opóźnieniem lub jeśli wymagana jest [Umowa SLA dla jednej maszyny wirtualnej o 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    7.  Sprawdź poprawność wdrożenia usługi Azure akcelerator zapisu
        1.  Upewnij się, że w ramach maszyny wirtualnej, funkcji miejsca do magazynowania lub zestawów rozłożonych zostały prawidłowo skompilowane na dyskach wymagających pomocy technicznej platformy Azure akcelerator zapisu
            1.  Sprawdź [konfigurację oprogramowania RAID w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Sprawdź [konfigurację LVM na maszynie wirtualnej z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Usługa Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) została użyta wyłącznie
    9.  Maszyny wirtualne zostały wdrożone w poprawnych zestawach dostępności i Strefy dostępności
    10. Upewnij się, że [usługa Azure przyspieszone sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na maszynach wirtualnych używanych w warstwie aplikacji SAP i w warstwie systemu SAP DBMS
    11. Brak rozmieszczenia [urządzeń wirtualnych sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemu SAP NetWeaver, Hybris lub S/4HANA opartych na systemie SAP
    12. Reguły ASG i sieciowej grupy zabezpieczeń umożliwiają komunikację zgodnie z potrzebami oraz zaplanowano i blokuje komunikację, gdy jest to wymagane.
    13. Ustawienia limitu czasu zgodnie z wcześniejszym opisem zostały ustawione prawidłowo
    14. Upewnij się, że maszyny wirtualne zostały wdrożone przy użyciu poprawnej [grupy umieszczania w pobliżu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , zgodnie z opisem w temacie [grupy umieszczania zbliżeniowe platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP](sap-proximity-placement-scenarios.md)
    15. Przetestuj i Oceń opóźnienia sieci między MASZYNami wirtualnymi aplikacji SAP i maszyną wirtualną DBMS zgodnie z uwagą pomocy technicznej SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)uwagi dotyczące pomocy technicznej SAP. Oceń wyniki na wskazówki dotyczące opóźnień sieci w przypadku pomocy technicznej SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Opóźnienie sieci powinno być w średnim i odpowiednim zakresie. Wyjątki dotyczą ruchu między maszynami wirtualnymi i jednostkami dużych wystąpień platformy HANA zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Sprawdź, czy szyfrowanie zostało wdrożone, jeśli jest to konieczne, i z niezbędną metodą szyfrowania
    16. Sprawdź, czy interfejsy i inne aplikacje mogą łączyć nowo wdrożoną infrastrukturę
6.  Utwórz element PlayBook do oddziałania w ramach zaplanowanej konserwacji platformy Azure. Zdefiniuj kolejność systemów i maszyn wirtualnych do ponownego uruchomienia w przypadku planowanej konserwacji
    

## <a name="go-live-phase"></a>Przejdź do fazy Live
W przypadku fazy "przejdź do" należy upewnić się, że elementy PlayBook został opracowany we wcześniejszych fazach. Wykonaj kroki, które zostały przetestowane i przeszkolone. Nie Akceptuję ostatnich zmian w konfiguracjach i procesie. Oprócz tego stosowane są następujące miary:

1. Sprawdź, czy Azure Portal monitorowanie i inne narzędzia do monitorowania działają.  Zalecane narzędzia to perfmon (Windows) lub SAR (Linux): 
    1.  Liczniki procesora CPU 
        1.  Średni czas procesora CPU — łącznie (wszystkie procesory CPU)
        2.  Średni czas procesora CPU — każdy procesor (na 128 procesorów na maszynie wirtualnej M128)
        3.  Jądro czasu procesora CPU — każdy pojedynczy procesor
        4.  Użytkownik czasu procesora — każdy pojedynczy procesor
    5.  Memory (Pamięć) 
        1.  Wolna pamięć
        2.  Liczba stron pamięci na sekundę
        3.  Stronicowana pamięć/s
    4.  Dysk 
        1.  Liczba odczytanych dysków KB/s — na poszczególnych dyskach 
        2.  Odczyty dysku/s — na poszczególnych dyskach
        3.  Odczyt dysku MS/Read — na pojedynczy dysk
        4.  Bajty zapisu dysku (KB/s) na poszczególnych dyskach 
        5.  Zapis na dysku/s — na poszczególnych dyskach
        6.  Zapis na dysku MS/Read — na poszczególnych dyskach
    5.  Sieć 
        1.  Pakiety sieciowe na sekundę
        2.  Wychodzące pakiety sieciowe/s
        3.  Sieć KB/s
        4.  Sieć KB/s 
2.  Po przeprowadzeniu migracji danych wykonaj wszystkie testy weryfikacyjne, które zostały uzgodnione z właścicielami firmy. Akceptuj tylko wyniki testów sprawdzania poprawności, w których masz wyniki dla oryginalnych systemów źródłowych
3.  Sprawdź, czy interfejsy działają i czy inne aplikacje mogą komunikować się z nowo wdrożonymi systemami produkcyjnymi
4.  Sprawdzanie systemu transportowego i korekcji przy użyciu protokołu SAP Transaction STMS
5.  Wykonaj kopie zapasowe bazy danych po udostępnieniu systemu dla środowiska produkcyjnego
6.  Wykonaj kopie zapasowe maszyn wirtualnych dla warstwy aplikacji SAP po wydaniu systemu dla środowiska produkcyjnego
7.  W przypadku systemów SAP, które nie były częścią bieżącej fazy go-Live, ale komunikują się z systemami SAP, które zostały przeniesione na platformę Azure w tej fazie go-Live, należy zresetować bufor nazw hostów w SM51. Ten krok spowoduje wyczyszczenie starych buforowanych adresów IP skojarzonych z nazwami wystąpień aplikacji przenoszonych na platformę Azure  


## <a name="post-production"></a>Publikuj produkcję
W tej fazie wszystkie informacje na temat monitorowania, działania i administrowania systemem. Z punktu widzenia oprogramowania SAP typowe zadania, które były wymagane do wykonania w starej lokalizacji hostingu, mają zastosowanie. Zadania specyficzne dla platformy Azure, które chcesz wykonać:

1. Analizuj faktury platformy Azure pod kątem systemów o wysokim obciążeniu
2. Optymalizowanie wydajności cen/wydajności po stronie maszyny wirtualnej i po stronie magazynu
3. Optymalizowanie systemów czasu można wyłączyć  


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

