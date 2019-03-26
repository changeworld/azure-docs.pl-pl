---
title: Często zadawane pytania — program VMware do odzyskiwania po awarii platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podsumowanie często zadawane pytania, podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: cdb8fe5deb71c014f7e0af01d070e5004d8c9994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418797"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Często zadawane pytania — program VMware do platformy Azure replikacji

Ten artykuł zawiera odpowiedzi na typowe pytania, które widać w przypadku wdrażania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware do platformy Azure. Jeśli masz pytania dotyczące po przeczytaniu tego artykułu, opublikuj je na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Ogólne
### <a name="how-is-site-recovery-priced"></a>Jak jest rozliczana Usługa Site Recovery?
Przegląd [cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) szczegółowe informacje.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zapłacić za maszyny wirtualne platformy Azure?
Podczas replikacji dane są replikowane do usługi Azure storage, a nie płacić za wszelkie zmiany maszyny Wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure. Po tym są naliczane za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Co można zrobić z oprogramowania VMware do platformy Azure replikacji?
- **Odzyskiwanie po awarii**: Możesz skonfigurować odzyskiwanie po awarii w pełnej. W tym scenariuszu można replikować maszyny wirtualne VMware w środowisku lokalnym, do usługi Azure storage. Następnie, jeśli z infrastruktury lokalnej jest niedostępna, może zostać przeniesiony, do platformy Azure. Po przełączeniu w tryb failover maszyn wirtualnych platformy Azure są tworzone przy użyciu replikowanych danych. Dostępne aplikacje i obciążenia na maszynach wirtualnych platformy Azure, dopóki w lokalnym centrum danych stanie się ponownie dostępny. Następnie można powrotu po awarii z platformy Azure do lokacji lokalnej.
- **Migracja**: Usługa Site Recovery umożliwia migrowanie lokalnych maszyn wirtualnych z programu VMware na platformę Azure. W tym scenariuszu można replikować maszyny wirtualne VMware w środowisku lokalnym, do usługi Azure storage. Następnie możesz Failover ze środowiska lokalnego do platformy Azure. Po przejściu w tryb failover swoje aplikacje i obciążenia są dostępne i działają na maszynach wirtualnych platformy Azure.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Czego potrzebujesz na platformie Azure?
Potrzebujesz subskrypcji platformy Azure, magazyn usługi Recovery Services, konto magazynu pamięci podręcznej, następująca liczba dysków zarządzanych i sieci wirtualnej. Dyski zarządzane magazynu, konto magazynu pamięci podręcznej, a sieć musi znajdować się w tym samym regionie.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy Moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz uprawnienia do replikacji, które są potrzebne. Jeśli nie masz, musisz mieć uprawnienia do utworzenia maszyny Wirtualnej platformy Azure w grupie zasobów i sieci wirtualnej można określić podczas konfigurowania Site Recovery i uprawnienia do zapisu do wybranego konta magazynu lub zarządzanych dysków na podstawie konfiguracji. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="can-i-use-guest-os-server-license-on-azure"></a>Czy można używać licencji serwera systemu operacyjnego gościa, na platformie Azure?
Tak, Microsoft Software Assurance klienci mogą używać [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oszczędności na koszty licencjonowania **maszyn z systemem Windows Server** , migracji na platformę Azure lub korzystanie z systemu Azure w celu odzyskiwania po awarii.

## <a name="pricing"></a>Cennik

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Jak opłaty licencyjne są obsługiwane podczas replikacji po włączeniu trybu failover

Zob. często zadawanych Pytaniach dotyczących licencji [tutaj](https://aka.ms/asr_pricing_FAQ) Aby uzyskać więcej informacji.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak obliczyć przybliżoną opłaty za podczas stosowania Site Recovery

Możesz użyć [Kalkulator cen](https://aka.ms/asr_pricing_calculator) do szacowania kosztów podczas korzystania z usługi Azure Site Recovery. Aby uzyskać szczegółową prognozę kosztów, uruchom narzędzie planista wdrażania (https://aka.ms/siterecovery_deployment_planner) i analizowanie [raport szacowania kosztów](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>Czy istnieją różnice w koszcie podczas replikacji bezpośrednio do dysków zarządzanych?

Dyski zarządzane są naliczane trochę różnić się od konta magazynu. Zobacz przykład poniżej dysk źródłowy o rozmiarze 100 GiB. Przykład dotyczy różnicowej koszty związane z magazynowaniem. Ten koszt nie obejmuje kosztów dla migawki, Magazyn pamięci podręcznej i transakcji.

* Konto magazynu w warstwie standardowa programu Vs. Standardowy dysk twardy, dysk zarządzany

    - **Dysk magazynu aprowizowanego przez usługę Azure Site Recovery**: S10
    - **Konto magazynu w warstwie standardowa opłata na używane woluminu**: 5 USD miesięcznie
    - **Standardowy dysk zarządzany na aprowizowanego woluminu**: $5.89 na miesiąc

* Konto magazynu Premium storage programu Vs. Dysk zarządzany w warstwie Premium SSD 
    - **Dysk magazynu aprowizowanego przez usługę Azure Site Recovery**: P10
    - **Konto magazynu Premium storage jest naliczana na aprowizowanego woluminu**: $17.92 na miesiąc
    - **Rozliczane w systemie aprowizowanego woluminu dysku zarządzanego w warstwie Premium**: $17.92 na miesiąc

Dowiedz się więcej o [szczegółowe informacje o cenach z dyskami zarządzanymi](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>Są naliczane dodatkowe opłaty dla konta magazynu pamięci podręcznej w przypadku dysków zarządzanych?

Nie, nie zostaną naliczone dodatkowe opłaty za pamięci podręcznej. Pamięć podręczna jest zawsze częścią oprogramowania VMware do architektury platformy Azure. W przypadku replikacji konta magazynu w warstwie standardowa to pamięć podręczna jest częścią tego samego konta magazynu docelowego.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jestem użytkownikiem usługi Azure Site Recovery od ponad miesiąca. Czy nadal uzyskam pierwsze 31 bezpłatnych dni dla każdego chronionego wystąpienia?

Tak, nie ma znaczenia, jak długo używasz usługi Azure Site Recovery. Przez pierwsze 31 dni opłaty usługi Azure Site Recovery dla każdego chronionego wystąpienia nie są naliczane. Jeśli na przykład 10 wystąpień podlegało ochronie przez ostatnie 6 miesięcy, a 11 wystąpienie zostało połączone z usługą Azure Site Recovery, opłaty usługi Azure Site Recovery za to wystąpienie nie będą naliczane przez pierwsze 31 dni. Opłaty usługi Azure Site Recovery za pierwsze 10 wystąpień będą nadal naliczane, ponieważ były one chronione przez dłużej niż 31 dni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy podczas pierwszych 31 dni będą naliczane inne opłaty usługi Azure?

Tak. Nawet jeśli usługa Azure Site Recovery będzie bezpłatna przez pierwsze 31 dni dla chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynowe i transfer danych. Opłaty za zasoby obliczeniowe platformy Azure mogą zostać również naliczone dla odzyskanej maszyny wirtualnej.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Jakie opłaty są naliczane podczas korzystania z usługi Azure Site Recovery?

Odnoszą się do naszych [często zadawane pytania dotyczące kosztów poniesionych](https://aka.ms/asr_pricing_FAQ) Aby uzyskać szczegółowe informacje.

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>Wiążą się do testowania odzyskiwania po awarii i testowania w tryb failover?

Istnieje nie oddzielnych koszt testowania odzyskiwania po awarii. Nastąpi opłaty za zasoby obliczeniowe, po utworzeniu wpisu testowy tryb failover maszyny wirtualnej.

## <a name="azure-site-recovery-components-upgrade"></a>Uaktualnij składniki usługi Azure Site Recovery

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>Moje mobilności/konfiguracja agenta serwera/wersja serwera przetwarzania jest bardzo starych i Moje uaktualnienie nie powiodło się. Jak należy uaktualnić do najnowszej wersji?

Usługa Azure Site Recovery następuje N-4 model pomocy technicznej. Odnoszą się do naszych [obsługuje instrukcji](https://aka.ms/asr_support_statement) Aby poznać szczegółowe informacje na temat uaktualniania z bardzo starsze wersje.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Gdzie można znaleźć pakiety zbiorcze aktualizacji/na informacje o wersji usługi Azure Site Recovery?

Zapoznaj się [dokumentu](https://aka.ms/asr_update_rollups) informacji o wersji. Linkach instalacyjnych odpowiednich składników można znaleźć w każdym zbiorczy aktualizacji.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Jak uaktualnić składniki usługi Site Recovery dla lokalnych zasobów programu VMware lub fizyczne witryny na platformie Azure?

Zobacz nasze wskazówki podane [tutaj](https://aka.ms/asr_vmware_upgrades) uaktualniania składników usługi.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>Ponowne uruchomienie maszyny źródłowej jest obowiązkowy w przypadku każdego uaktualnienia?

Chociaż jest to zalecane, nie jest to konieczne w przypadku każdego uaktualnienia. Zapoznaj się [tutaj](https://aka.ms/asr_vmware_upgrades) przypadku wyczyść wytycznych.

## <a name="on-premises"></a>Lokalnie

### <a name="what-do-i-need-on-premises"></a>Co zrobić, należy w środowisku lokalnym?

W lokalnym potrzebne są:
- Site Recovery zainstalowanie tych składników, pojedynczej maszyny Wirtualnej VMware.
- Infrastruktura VMware przy użyciu co najmniej jeden ESXi host, a firma Microsoft zaleca, aby serwer vCenter.
- Co najmniej jeden maszyn wirtualnych programu VMware do replikowania.

[Dowiedz się więcej](vmware-azure-architecture.md) o architektura VMware – Azure.

Lokalny serwer konfiguracji można wdrożyć w następujący sposób:

- Zalecamy wdrażanie serwera konfiguracji jako Maszynę wirtualną VMware przy użyciu szablonu usługi OVA z wstępnie zainstalowanym serwerem konfiguracji.
- Jeśli z jakiegoś powodu nie można użyć szablonu, należy ręcznie skonfigurować serwer konfiguracji. [Dowiedz się więcej](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie są lokalne maszyny wirtualne replikowane do
Dane są replikowane do usługi Azure storage. Po uruchomieniu trybu failover Usługa Site Recovery automatycznie tworzy maszyny wirtualne platformy Azure z konta magazynu lub zgodnie z konfiguracją dysku zarządzanego.

## <a name="replication"></a>Replikacja

### <a name="what-applications-can-i-replicate"></a>Jakie aplikacje można replikować?
Można replikować dowolną aplikację lub obciążenia uruchomione na maszynie Wirtualnej VMware, który jest zgodny z [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md##replicated-machines). Usługa Site Recovery obsługuje replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą przełączone w tryb failover i do inteligentnego stanu. Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>Czy mogę chronić maszyny wirtualnej, która ma konfigurację dysku platformy Docker

Nie, jest to nieobsługiwany scenariusz.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Można replikować na platformę Azure za pomocą połączenia VPN lokacja lokacja?
Usługa Site Recovery replikuje dane ze środowiska lokalnego do usługi Azure storage za pośrednictwem publicznego punktu końcowego lub przy użyciu usługi ExpressRoute publicznej komunikacji równorzędnej. Replikacja za pośrednictwem sieci VPN typu lokacja lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Można replikować na platformę Azure przy użyciu usługi ExpressRoute?
Tak, usługa ExpressRoute może służyć do replikowania maszyn wirtualnych na platformie Azure. Usługa Site Recovery replikuje dane do usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) za pomocą usługi ExpressRoute dla replikacji usługi Site Recovery. Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji. Upewnij się, że [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) spełnione są również do replikacji. Po maszyny wirtualne nie za pośrednictwem sieci wirtualnej platformy Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Jak mogę zmienić konta magazynu, po włączeniu ochrony maszyny?

Należy wyłączyć i włączyć replikację uaktualnić lub obniżyć typ konta magazynu.

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>Można replikować do kont magazynu do nowego komputera?

Nie, począwszy od marca "19, można replikować do usługi managed disks na platformie Azure z poziomu portalu. Replikacja do kont magazynu do nowego komputera jest dostępna tylko za pośrednictwem interfejsu API REST i Powershell. Replikacja do konta magazynu, należy użyć interfejsu API w wersji 2016-08-10 lub 2018-01-10.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Jakie są korzyści w replikacji do usługi managed disks?

Przeczytaj artykuł na temat [usługi Azure Site Recovery upraszcza odzyskiwanie po awarii z dyskami zarządzanymi](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Jak można zmienić typ dysku zarządzanego, po włączeniu ochrony maszyny?

Tak, można łatwo zmienić typ dysku zarządzanego. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Jednak jeśli zmienisz typ dysku zarządzanego, upewnij się, poczekaj punktów odzyskiwania świeże wygenerowany, jeśli potrzebujesz do testowania trybu failover lub pracy awaryjnej Opublikuj to działanie.

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>Czy mogę przełączać replikacji z dysków zarządzanych do dysków niezarządzanych

Nie, przełączanie z zarządzane do niezarządzanego jest nieobsługiwane.

### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

Podczas replikacji do platformy Azure, ruch związany z replikacją osiągnie publicznych punktów końcowych usługi Azure Storage, dlatego tylko można replikować za pośrednictwem publicznej sieci internet przy użyciu usługi ExpressRoute (publicznej komunikacji równorzędnej) i sieci VPN nie działa.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Czy można używać Riverbed SteelHeads w przypadku replikacji?

Nasz partner Riverbed, zawiera szczegółowe wskazówki na temat pracy z usługą Azure Site Recovery. Zapoznaj się ich [Przewodnik po rozwiązaniu](https://community.riverbed.com/s/article/DOC-4627).

### <a name="what-are-the-replicated-vm-requirements"></a>Co to są replikowane wymagań dotyczących maszyn wirtualnych?

W przypadku replikacji maszyny Wirtualnej programu VMware musi działać obsługiwany system operacyjny. Ponadto maszyna wirtualna musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](vmware-physical-azure-support-matrix.md##replicated-machines) w macierzy obsługi.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?
Replikacja jest ciągłe podczas replikowania maszyn wirtualnych VMware na platformę Azure.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Czy można zachować adres IP w trybie failover?
Tak, można zachować adres IP w trybie failover. Upewnij się, wspomnieć o docelowy adres IP w bloku "Obliczenia i sieć" przed włączeniem trybu failover. Upewnij się również, aby zamknąć maszyny w momencie przejścia w tryb failover w celu uniknięcia konfliktów adresów IP w momencie powrotu po awarii.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Można zrobić początkowej replikacji offline?
Ta funkcja nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy można wykluczyć dysków?
Tak, można wykluczyć dyski z replikacji.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Można zmienić rozmiar docelowej maszyny Wirtualnej lub typ maszyny Wirtualnej przed włączeniem trybu failover?
Tak, można zmienić typu i rozmiaru maszyny Wirtualnej kiedykolwiek przed włączeniem trybu failover, przechodząc do ustawień elementu replikację z poziomu portalu obliczenia i sieć.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Maszyny wirtualne można replikować z dyskami dynamicznymi?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Czy w przypadku używania grup replikacji w celu zachowania spójności wielu maszyn wirtualnych, można dodać nowej maszyny Wirtualnej do istniejącej grupy replikacji?
Tak, można dodać nowe maszyny wirtualne do istniejącej grupy replikacji po włączeniu replikacji dla nich. Po zainicjowaniu replikacji i nie można utworzyć grupy replikacji dla istniejących maszyn wirtualnych, nie można dodać do istniejącej grupy replikacji maszyny Wirtualnej.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Można zmodyfikować maszyny wirtualne, które jest replikowana, dodając lub zmienianie rozmiaru dysków?

Potrzeby replikacji oprogramowania VMware do platformy Azure można zmodyfikować rozmiaru dysku. Jeśli chcesz dodać nowe dyski, czego potrzebujesz, aby dodać dysk, a następnie ponownie włączyć ochronę maszyny Wirtualnej.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>Czy mogę zmigrować na komputerach lokalnych, aby nowe Vcenter bez wywierania wpływu na trwającą replikację?
Nie, zmiany Vcenter lub migracji będzie miało wpływ na trwającą replikację. Należy skonfigurować usługę Azure Site Recovery z programem Vcenter nowe i włączyć replikację dla maszyn.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Można replikować do pamięci podręcznej/docelowego konta magazynu, która ma sieci wirtualnej (przy użyciu zapór usługi Azure storage) skonfigurowane na nim?
Nie, usługa Azure Site Recovery nie obsługuje replikację do magazynu w sieci wirtualnej.

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego służy serwer konfiguracji?
Serwer konfiguracji działa lokalnie składniki usługi Site Recovery, w tym:
- Serwer konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła go do usługi Azure storage., serwer przetwarzania instaluje także usługę Mobility na maszynach wirtualnych, o których mają być replikowane i przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- Serwer główny serwer docelowy, który służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

[Dowiedz się więcej](vmware-azure-architecture.md) dotyczące składników serwera konfiguracji i procesów.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdzie skonfigurować serwer konfiguracji?
Potrzebujesz jednego o wysokiej dostępności lokalnych zasobów programu VMware maszyny Wirtualnej serwera konfiguracji.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Jakie są wymagania dotyczące serwera konfiguracji?

Przegląd [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Można ręcznie skonfigurować serwer konfiguracji, a nie przy użyciu szablonu?
Zaleca się, że używasz najnowszej wersji szablonu pakietu OVF do [tworzenie maszyny Wirtualnej serwera konfiguracji](vmware-azure-deploy-configuration-server.md). Jeśli z jakiegoś powodu nie można na przykład nie masz dostępu do serwera VMware, możesz to zrobić [Pobierz plik instalacyjny ujednoliconego](physical-azure-set-up-source.md) z portalu, i uruchom go na maszynie Wirtualnej.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Serwer konfiguracji można replikować do więcej niż jednym regionie?
Nie. Aby to zrobić, należy skonfigurować serwer konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy można hostować serwer konfiguracji na platformie Azure?
Ile jest to możliwe maszyna wirtualna Azure, które działają na serwerze konfiguracji musi komunikować się z lokalną infrastrukturą programu VMware i maszyn wirtualnych. To jest dodawanie opóźnienia i mieć wpływ na trwającą replikację.

### <a name="how-do-i-update-the-configuration-server"></a>Jak zaktualizować serwer konfiguracji?
[Dowiedz się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) aktualizację serwera konfiguracji. Można znaleźć informacje o najnowszych aktualizacji w [aktualizacje platformy Azure, strona](https://azure.microsoft.com/updates/?product=site-recovery). Można również bezpośrednio pobrać najnowszą wersję serwera konfiguracji z [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Jeśli używana wersja jest starsza niż 4 w wersjach od bieżącej wersji, zajrzyj do naszej [obsługuje instrukcji](https://aka.ms/asr_support_statement) wskazówki dotyczące uaktualnienia.

### <a name="should-i-backup-the-deployed-configuration-server"></a>Należy utworzyć kopię zapasową serwera wdrożonej konfiguracji?
Zaleca się wykonywanie regularnych zaplanowanych kopii zapasowych serwera konfiguracji. Pomyślne powrotu po awarii powrót po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji, a serwer konfiguracji musi być uruchomiona w stanie połączonym. Dowiedz się więcej na temat typowych zadań zarządzania serwerem konfiguracji [tutaj](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Gdy jestem konfigurowania serwera konfiguracji, można pobrać i ręczne instalowanie programu MySQL?

Tak. Pobierz MySQL i umieść go w **C:\Temp\ASRSetup** folderu. Następnie zainstalować go ręcznie. Podczas konfigurowania serwera konfiguracji maszyny Wirtualnej i zaakceptuj warunki, MySQL będzie wyświetlana jako **zainstalowane** w **Pobierz i zainstaluj**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Można I uniknąć pobierania MySQL, ale pozwól Site Recovery zainstaluj go?

Tak. Pobierz Instalator MySQL i umieść go w **C:\Temp\ASRSetup** folderu.  Po skonfigurowaniu serwera konfiguracji maszyny Wirtualnej, należy zaakceptować warunki, a następnie kliknij **Pobierz i zainstaluj**, portal użyje Instalator dodaje zainstalować oprogramowanie MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Serwera konfiguracji maszyny Wirtualnej można używać do innych?
Nie, możesz należy używać tylko maszyny Wirtualnej serwera konfiguracji. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Można sklonować serwer konfiguracji i używać go w ramach aranżacji?
Nie, należy skonfigurować serwer nowej konfiguracji, aby uniknąć problemów z rejestracji.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Czy można zmienić magazynu zarejestrowany na serwerze konfiguracji?
Nie. Po zarejestrowaniu magazynu z serwera konfiguracji nie można zmienić. Przegląd [w tym artykule](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) kroków ponownej rejestracji.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych można użyć tego samego serwera konfiguracji
Tak, ale należy pamiętać, ten komputer fizyczny można tylko można ponownie do maszyny Wirtualnej VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Gdzie można pobrać hasła dla serwera konfiguracji?
[Zapoznaj się z tym artykułem](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) Aby dowiedzieć się więcej o pobieraniu hasło.

### <a name="where-can-i-download-vault-registration-keys"></a>Gdzie można pobrać klucze rejestracyjne magazynu?

W **magazyn usług Recovery Services**, **zarządzanie** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**. W **serwerów**, wybierz opcję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.



## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie mogę znaleźć pliki instalacyjne usługi mobilności
Pliki instalacyjne są przechowywane w **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** folderu na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak zainstalować usługę mobilności?
Zainstaluj na każdej maszynie Wirtualnej, którą chcesz replikować, za pomocą [instalacja wypychana](vmware-physical-mobility-service-overview.md#push-installation), lub [Instalacja ręczna](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z interfejsu użytkownika lub środowiska Powershell. Alternatywnie, można wdrożyć przy użyciu narzędzia wdrażania, takich jak [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Bezpieczeństwo

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Jakiego dostępu czy usługa Site Recovery należy serwery VMware?
Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Skonfiguruj Maszynę wirtualną VMware z serwera konfiguracji i inne składniki usługi Site Recovery w środowisku lokalnym. [Dowiedz się więcej](vmware-azure-deploy-configuration-server.md)
- Automatyczne odnajdywanie maszyn wirtualnych do replikacji. Więcej informacji na temat przygotowywanie konta do automatycznego odnajdowania. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Jakiego dostępu czy usługa Site Recovery należy maszyn wirtualnych programu VMware?

- Aby replikować, maszyn wirtualnych programu VMware musi mieć usługi Site Recovery Mobility zainstalowany i uruchomiony. Ręczne wdrażanie narzędzia lub określ, czy usługa Site Recovery należy wykonać instalacji wypychanej usługi po włączeniu replikacji dla maszyny Wirtualnej. Dotyczące instalacji wypychanej Site Recovery wymaga konta, które można użyć do instalacji składnika usług. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Serwer przetwarzania (domyślnie uruchamiane na serwerze konfiguracji) wykonuje tę instalację. [Dowiedz się więcej](vmware-azure-install-mobility-service.md) o instalacja usługi mobilności.
- Podczas replikacji maszyn wirtualnych komunikują się z usługą Site Recovery w następujący sposób:
    - Maszyny wirtualne komunikują się z serwerem konfiguracji na porcie HTTPS 443 na potrzeby zarządzania replikacją.
    - Maszyny wirtualne wysyłać dane replikacji do serwera przetwarzania na porcie HTTPS 9443 (można modyfikować).
    - Jeśli włączono spójność wielu maszyn wirtualnych, maszyny wirtualne komunikują się ze sobą za pośrednictwem portu 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Dane replikacji są wymieniane między funkcjami hypervisor programu VMware i Azure storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Może nam obowiązek przechowywania metadanych w środowisku lokalnym w ramach regionów geograficznych?
Tak. Po utworzeniu magazynu w regionie, Upewniamy się, że wszystkie metadane używane przez pozostaje Site Recovery w ramach tego regionu geograficznego.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie przesyłanych w i [szyfrowania na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.


## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Czy za pomocą serwera przetwarzania w lokalnych uzyskać powrotu po awarii
Zdecydowanie zaleca się tworzenie serwera przetwarzania na platformie Azure w celu powrotu po awarii, aby uniknąć opóźnienia transferu danych. Ponadto w przypadku, gdy za pomocą sieci platformy Azure umożliwiający dostęp do Internetu na serwerze konfiguracji jest oddzielone ze źródłową siecią maszyn wirtualnych, jest niezbędne, aby użyć serwera przetwarzania na platformie Azure utworzone do powrotu po awarii.

### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?
Dla replikacji VMware – Azure najstarszy punkt odzyskiwania, których można użyć to 72 godziny.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?
Po przejściu w tryb failover maszyn wirtualnych platformy Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN lokacja lokacja lub za pośrednictwem usługi Azure ExpressRoute. Należy przygotować kilka rzeczy w celu nawiązania połączenia. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Jest w trybie Failover odporność danych?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z umową SLA platformy Azure. Po przejściu do trybu failover, możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [PowerShell](/powershell/module/azurerm.siterecovery) do wyzwolenia przejścia w tryb failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Może nie do innej lokalizacji?
Tak, jeśli Failover na platformie Azure można powrotu po awarii do innej lokalizacji Jeśli oryginalny jest niedostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Dlaczego muszę sieci VPN lub usługi ExpressRoute do powrotu po awarii?
Podczas powrotu po awarii z platformy Azure, powrót do maszyny Wirtualnej w środowisku lokalnym są kopiowane dane z platformy Azure i dostęp prywatny jest wymagany.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Czy mogę zmienić rozmiar maszyny Wirtualnej platformy Azure po włączeniu trybu failover?
Nie, nie można zmienić rozmiaru lub typu docelową maszynę Wirtualną po przejściu w tryb failover.


## <a name="automation-and-scripting"></a>Automatyzacji i opracowywaniu skryptów

### <a name="can-i-set-up-replication-with-scripting"></a>Można skonfigurować replikację za pomocą skryptów?
Tak. Możesz zautomatyzować przepływy pracy Site Recovery przy użyciu interfejsu API Rest, programu PowerShell lub zestawu SDK usługi Azure. [Więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność
### <a name="can-i-throttle-replication-bandwidth"></a>Czy mogę ograniczyć przepustowość replikacji
Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd](vmware-physical-azure-support-matrix.md) wymagań.
* [Konfigurowanie](vmware-azure-tutorial.md) programu VMware do platformy Azure replikacji.
