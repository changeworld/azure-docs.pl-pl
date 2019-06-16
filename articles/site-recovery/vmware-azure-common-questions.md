---
title: Często zadawane pytania dotyczące programu VMware do odzyskiwania po awarii platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417773"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Często zadawane pytania dotyczące programu VMware do platformy Azure replikacji

Ten artykuł zawiera odpowiedzi na często zadawane pytania, które mogą się podczas wdrażania odzyskiwania po awarii maszyn wirtualnych VMware w środowisku lokalnym (VM) na platformie Azure.

## <a name="general"></a>Ogólne

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co jest potrzebne do odzyskiwania po awarii maszyn wirtualnych programu VMware?

[Dowiedz się więcej o składniki zaangażowane](vmware-azure-architecture.md) odzyskiwanie po awarii maszyn wirtualnych programu VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery można użyć do migrowania maszyn wirtualnych VMware na platformę Azure?

Tak. Oprócz używania Site Recovery, aby skonfigurować odzyskiwanie po awarii w pełnej dla maszyn wirtualnych VMware, umożliwia także Site Recovery do migrowania lokalnych maszyn wirtualnych z programu VMware na platformie Azure. W tym scenariuszu można replikować maszyny wirtualne VMware w środowisku lokalnym, do usługi Azure Storage. Następnie możesz Failover ze środowiska lokalnego do platformy Azure. Po przejściu w tryb failover swoje aplikacje i obciążenia są dostępne i działają na maszynach wirtualnych platformy Azure. Proces przypomina konfigurowanie odzyskiwania po awarii w pełnej, z tą różnicą, że w przypadku migracji nie powrotu po awarii z platformy Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy Moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?

Jeśli jesteś administratorem subskrypcji, masz uprawnienia do replikacji, które są potrzebne. Jeśli nie jesteś administratorem, musisz mieć uprawnienia do wykonania tych działań:

- Utwórz Maszynę wirtualną platformy Azure w zasobie grupy i wirtualnych sieci, można określić podczas konfigurowania Site Recovery.
- Zapis na wybranym koncie magazynu lub dysk zarządzany na podstawie konfiguracji.

[Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) o wymaganych uprawnień.

### <a name="what-applications-can-i-replicate"></a>Jakie aplikacje można replikować?

Można replikować dowolną aplikację lub obciążenia uruchomione na maszynie Wirtualnej VMware, która spełnia [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md#replicated-machines).

- Usługa Site Recovery obsługuje replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą być przełączone w tryb failover i do inteligentnego stanu.
- Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory. Jego współpracuje również blisko z czołowymi producentami, w tym Oracle, SAP, IBM i Red Hat.

[Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Czy można używać licencję serwera systemu operacyjnego gościa, na platformie Azure?

Tak, Microsoft Software Assurance klienci mogą używać [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oszczędności na licencjonowanie kosztów dla maszyny z systemem Windows Server, które są migrowane do usługi Azure lub korzystanie z systemu Azure w celu odzyskiwania po awarii.

## <a name="security"></a>Bezpieczeństwo

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jakiego dostępu do serwerów VMware czy potrzebna jest usługa Site Recovery?

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Skonfiguruj maszynę Wirtualną VMware z systemem serwera konfiguracji usługi Site Recovery.
- Automatyczne odnajdywanie maszyn wirtualnych do replikacji.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Których praw dostępu do maszyn wirtualnych VMware czy potrzebna jest usługa Site Recovery?

- Aby zreplikować, maszyny Wirtualnej programu VMware musi mieć usługi Site Recovery Mobility zainstalowany i uruchomiony. Narzędzie można wdrożyć ręcznie lub można określić, czy usługa Site Recovery wykonywania instalacji wypychanej usługi po włączeniu replikacji dla maszyny Wirtualnej.
- Podczas replikacji maszyn wirtualnych komunikują się z usługą Site Recovery w następujący sposób:
    - Maszyny wirtualne komunikują się z serwerem konfiguracji na porcie HTTPS 443 na potrzeby zarządzania replikacją.
    - Maszyny wirtualne wysyłać dane replikacji do serwera przetwarzania na porcie HTTPS 9443. (To ustawienie można zmodyfikować.)
    - Jeśli włączono spójność wielu maszyn wirtualnych, maszyny wirtualne komunikują się ze sobą za pośrednictwem portu 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Dane replikacji są wysyłane do usługi Site Recovery?

Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Dane replikacji są wymieniane między funkcjami hypervisor programu VMware i Azure Storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest certyfikat ISO 27001: 2013 i 27018, HIPAA i DPA. Trwa SOC2 i FedRAMP JAB.

## <a name="pricing"></a>Cennik

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Jak obliczyć przybliżoną opłaty za odzyskiwanie po awarii programu VMware?

Użyj [Kalkulator cen](https://aka.ms/asr_pricing_calculator) do szacowania kosztów przy użyciu usługi Site Recovery.

Aby uzyskać szczegółową prognozę kosztów, uruchom narzędzie planista wdrażania na potrzeby [VMware](https://aka.ms/siterecovery_deployment_planner) i użyj [raport szacowania kosztów](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Czy istnieją różnice w koszcie między replikowanych do magazynu lub bezpośrednio do usługi managed disks?

Dyski zarządzane są naliczane nieco inaczej na kontach magazynu. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/managed-disks/) o cenach dysku zarządzanego.

## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie mogę znaleźć pliki instalacyjne usługi mobilności

Pliki instalacyjne znajdują się w folderze %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak zainstalować usługę mobilności?

Na każdej maszynie Wirtualnej, które mają być replikowane należy zainstalować usługę za pomocą jednej z kilku metod:

- [Instalacja wypychana](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalacja ręczna](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z interfejsu użytkownika lub programu PowerShell
- Wdrożenie przy użyciu narzędzia wdrażania, takie jak [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Dyski zarządzane

### <a name="where-does-site-recovery-replicate-data-to"></a>Gdy usługa Site Recovery może replikować dane?

Usługa Site Recovery replikuje lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych do usługi managed disks na platformie Azure.

- Serwer przetwarzania Site Recovery zapisuje dzienników replikacji na konto magazynu pamięci podręcznej w regionie docelowym.
- Dzienniki te są używane do tworzenia punktów odzyskiwania na usługi Azure managed disks, które mają prefiks **asrseeddisk**.
- Po przejściu do trybu failover, wybrany punkt odzyskiwania jest używany do tworzenia nowego dysku zarządzanego docelowego. Ten dysk zarządzany jest dołączony do maszyny Wirtualnej na platformie Azure.
- Nie ma wpływu na maszyny wirtualne, które zostały wcześniej zreplikowane na koncie magazynu (przed marca 2019 r).

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Nowe maszyny można replikować do kont magazynu?

Nie. Począwszy od marca 2019 r, w witrynie Azure portal można replikować tylko do usługi Azure managed disks.

Replikacja nowych maszyn wirtualnych na konto magazynu jest dostępne tylko przy użyciu programu PowerShell lub interfejsu API REST (w wersji 2018-01-10 lub 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jakie są zalety replikowania do usługi managed disks?

[Dowiedz się, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery upraszcza odzyskiwanie po awarii z dyskami zarządzanymi.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Typ dysku zarządzanego można zmienić po włączeniu ochrony maszyny?

Tak, możesz z łatwością [zmienić typ dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) dla trwającej replikacji. Przed zmianą typu, upewnij się, że nie sygnatury dostępu współdzielonego, adres URL jest generowany na dysku zarządzanego:

1. Przejdź do **dysku zarządzanego** zasobów w witrynie Azure portal i sprawdź, czy masz transparent adresu URL sygnatury dostępu współdzielonego na **Przegląd** bloku.
1. Jeśli występuje banerze wybierz go, można anulować eksportu ciągłego.
1. Zmień typ dysku w ciągu najbliższych kilku minut. Jeśli zmienisz typ dysku zarządzanego, poczekaj, aż punkty odzyskiwania świeże generowanej przez usługę Azure Site Recovery.
1. Użyj nowe punkty odzyskiwania dla każdego testu trybu failover lub pracy awaryjnej w przyszłości.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Czy mogę przełączać replikacji z dysków zarządzanych do dysków niezarządzanych

Nie. Przełączenie z zarządzane do niezarządzanego nie jest obsługiwane.

## <a name="replication"></a>Replikacja

### <a name="what-are-the-replicated-vm-requirements"></a>Co to są replikowane wymagań dotyczących maszyn wirtualnych?

[Dowiedz się więcej](vmware-physical-azure-support-matrix.md#replicated-machines) o wymaganiach pomocy technicznej dla maszyn wirtualnych VMware i serwerów fizycznych.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?

Replikacja jest ciągłe podczas replikowania maszyn wirtualnych VMware na platformę Azure.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację

Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Można zrobić początkowej replikacji offline?

Replikacji w trybie offline nie jest obsługiwane. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co to jest asrseeddisk?

Dla każdego dysku źródłowego dane są replikowane na dysk zarządzany na platformie Azure. Ten dysk ma prefiks **asrseeddisk**. Przechowuje kopię dysku źródłowego i wszystkie migawki punktu odzyskiwania.

### <a name="can-i-exclude-disks-from-replication"></a>Czy można wykluczyć dyski z replikacji?

Tak, można wykluczyć dysków.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Czy można replikować maszyny wirtualne, które mają dynamiczne dyski?

Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Czy w przypadku używania grup replikacji w celu zachowania spójności wielu maszyn wirtualnych, można dodać nowej maszyny Wirtualnej do istniejącej grupy replikacji?

Tak, można dodać nowe maszyny wirtualne do istniejącej grupy replikacji po włączeniu replikacji dla nich. Jednak:

- Po rozpoczęciu replikacji, nie można dodać do istniejącej grupy replikacji maszyny Wirtualnej.
- Nie można utworzyć grupy replikacji dla istniejących maszyn wirtualnych.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Można zmodyfikować maszyny wirtualne, które jest replikowana, dodając lub zmienianie rozmiaru dysków?

Potrzeby replikacji oprogramowania VMware na platformę Azure można zmodyfikować rozmiaru dysku. Jeśli chcesz dodać nowe dyski, należy dodać dysk i ponownie włączyć ochronę maszyny Wirtualnej.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Można przeprowadzić migrację maszyn lokalnych nowy serwer vCenter bez wywierania wpływu na trwającą replikację?

Nie. Zmiana VMware Vcenter lub migracji będzie miało wpływ na trwającą replikację. Konfigurowanie Site Recovery przy użyciu nowego serwera vCenter, a następnie włącz ponownie replikację dla maszyn.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Można replikować do pamięci podręcznej lub elementu docelowego konta magazynu, która ma sieci wirtualnej (z zapory platformy Azure) skonfigurowane na nim?

Nie, Usługa Site Recovery nie obsługuje replikację do usługi Azure Storage w sieciach wirtualnych.

## <a name="component-upgrade"></a>Składnik uaktualniania

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moja wersja serwera agenta lub Konfiguracja usługi mobilności jest stara, a Mój uaktualnienie nie powiodło się. Co mam zrobić?

Usługa Site Recovery następuje N-4 model pomocy technicznej. [Dowiedz się więcej](https://aka.ms/asr_support_statement) o sposobie uaktualniania z bardzo starsze wersje.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Gdzie można znaleźć informacje o wersji i pakiety zbiorcze aktualizacji dla usługi Azure Site Recovery?

[Dowiedz się więcej o nowych aktualizacjach](site-recovery-whats-new.md), i [uzyskać informacje dotyczące pakietu zbiorczego](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Gdzie mogę znaleźć informacje o uaktualnieniu do odzyskiwania po awarii na platformie Azure

[Dowiedz się więcej o uaktualnianiu](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Czy muszę ponowny rozruch maszyn źródłowych dla każdego uaktualnienia?

Ponowne uruchomienie jest zalecane, ale nie jest to konieczne w przypadku każdego uaktualnienia. [Dowiedz się więcej](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego służy serwer konfiguracji?

Serwer konfiguracji działa lokalnie składniki usługi Site Recovery, w tym:

- Sam serwer konfiguracji. Serwer służy do koordynowania komunikacji między składnikami w środowisku lokalnym i platformą Azure i zarządzania replikacją danych.
- Serwer przetwarzania działa jako brama replikacji. Ten serwer:
    1. Odbiera dane replikacji.
    2. Optymalizuje dane z pamięci podręcznej, kompresji i szyfrowania.
    3. Wysyła dane do usługi Azure Storage.
  Serwer przetwarzania również nie instalację wypychaną usługi Mobility na maszynach wirtualnych i przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- Główny serwer docelowy, który służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

[Dowiedz się więcej](vmware-azure-architecture.md) dotyczące składników serwera konfiguracji i procesów.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdzie skonfigurować serwer konfiguracji?

Potrzebujesz jednego, wysoką dostępność, lokalnych zasobów programu VMware maszyny Wirtualnej serwera konfiguracji. Do odzyskiwania po awarii serwerów fizycznych Zainstaluj serwer konfiguracji na komputerze fizycznym.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co jest potrzebne dla serwera konfiguracji?

Przegląd [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Można ręcznie skonfigurować serwer konfiguracji, a nie przy użyciu szablonu?

Zaleca się, że możesz [tworzenie maszyny Wirtualnej serwera konfiguracji](vmware-azure-deploy-configuration-server.md) przy użyciu najnowszej wersji szablonu maszyny wirtualnej Format OVF (Open). Jeśli nie możesz użyć szablonu (na przykład, jeśli nie masz dostępu do serwera VMware), [Pobierz](physical-azure-set-up-source.md) plik Instalatora z portalu i konfigurowanie serwera konfiguracji.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Serwer konfiguracji można replikować do więcej niż jednym regionie?

Nie. Aby replikować do więcej niż jednym regionie, należy serwera konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy można hostować serwer konfiguracji na platformie Azure?

Mimo że jest to możliwe, maszyna wirtualna Azure, które działają na serwerze konfiguracji musi komunikować się z lokalną infrastrukturą programu VMware i maszyn wirtualnych. Ta komunikacja dodaje opóźnienie i ma wpływ na trwającą replikację.

### <a name="how-do-i-update-the-configuration-server"></a>Jak zaktualizować serwer konfiguracji?

[Dowiedz się,](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) sposób aktualizacji na serwerze konfiguracji.

- Najnowsze informacje o aktualizacji można znaleźć na [aktualizacje platformy Azure, strona](https://azure.microsoft.com/updates/?product=site-recovery).
- Możesz pobrać najnowszą wersję z portalu. Lub możesz pobrać najnowszą wersję serwera konfiguracji bezpośrednio z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Jeśli więcej niż cztery w wersji wcześniejszej niż bieżąca wersja jest w wersji, zobacz [obsługuje instrukcji](https://aka.ms/asr_support_statement) wskazówki dotyczące uaktualnienia.

### <a name="should-i-back-up-the-configuration-server"></a>Należy wykonywać kopie zapasowe serwera konfiguracji?

Zaleca się wykonywanie regularnych zaplanowanych kopii zapasowych serwera konfiguracji.

- Pomyślne powrotu po awarii powrót po awarii maszyny Wirtualnej musi istnieć w bazie danych serwera konfiguracji.
- Serwer konfiguracji musi być uruchomiona i w stanie połączonym.
- [Dowiedz się więcej](vmware-azure-manage-configuration-server.md) o typowych zadań zarządzania serwerem konfiguracji.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Gdy jestem konfigurowania serwera konfiguracji, można pobrać i ręczne instalowanie programu MySQL?

Tak. Pobierz MySQL i umieść go w folderze C:\Temp\ASRSetup. Następnie należy zainstalować go ręcznie. Podczas konfigurowania serwera konfiguracji maszyny Wirtualnej i zaakceptuj warunki, MySQL będzie wyświetlana jako **zainstalowane** w **Pobierz i zainstaluj**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Można I uniknąć pobierania MySQL, ale pozwól Site Recovery zainstaluj go?

Tak. Pobierz Instalator MySQL i umieść go w folderze C:\Temp\ASRSetup. Po skonfigurowaniu serwera konfiguracji maszyny Wirtualnej, zaakceptuj warunki i wybierz **Pobierz i zainstaluj**. Portal użyje Instalatora, który został dodany do Instalowanie programu MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Serwera konfiguracji maszyny Wirtualnej można używać do innych?

Nie. Tylko dla serwera konfiguracji, należy użyć maszyny Wirtualnej.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Można sklonować serwer konfiguracji i używać go w ramach aranżacji?

Nie. Konfigurowanie serwera nowej konfiguracji, aby uniknąć problemów z rejestracji.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Czy można zmienić magazynu, w którym zarejestrowano serwer konfiguracji?

Nie. Po magazynu jest skojarzony z serwerem konfiguracji, nie można zmienić. [Dowiedz się,](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) dotyczące rejestrowania serwera konfiguracji za pomocą innego magazynu.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych można użyć tego samego serwera konfiguracji?

Tak, ale należy pamiętać, można ten komputer fizyczny kopii tylko do maszyny Wirtualnej VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Gdzie można pobrać hasła dla serwera konfiguracji?

[Dowiedz się,](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) jak pobrać hasło.

### <a name="where-can-i-download-vault-registration-keys"></a>Gdzie można pobrać klucze rejestracyjne magazynu?

W magazynie usługi Recovery Services, wybierz **serwery konfiguracji** w **infrastruktura usługi Site Recovery** > **Zarządzaj**. Następnie w **serwerów**, wybierz opcję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.

## <a name="process-server"></a>Serwer przetwarzania

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Dlaczego nie mogę wybrać serwera przetwarzania po włączeniu replikacji?

Aktualizacje w wersji 9,24 i nowszej są teraz wyświetlane [kondycję serwera przetwarzania po włączeniu replikacji](vmware-azure-enable-replication.md#enable-replication). Ta funkcja pomaga uniknąć ograniczania serwer przetwarzania oraz zminimalizować użycie serwerów przetwarzania w złej kondycji.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Jak zaktualizować serwer przetwarzania do wersji 9.24 lub nowszym w przypadku informacji o kondycji dokładne?

Począwszy od [wersji 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), więcej alertów zostały dodane do wskazania kondycję serwera przetwarzania. [Aktualizacją składników usługi Site Recovery do wersji 9.24 lub nowszej] (service-updates-how-to.md#links-to-currently-supported-update-rollups) tak, aby wszystkie alerty są generowane.

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Do powrotu po awarii może używać serwera przetwarzania w środowisku lokalnym?

Zdecydowanie zaleca się utworzenie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii, aby uniknąć opóźnienia transferu danych. Ponadto w przypadku, gdy za pomocą sieci platformy Azure umożliwiający dostęp do Internetu na serwerze konfiguracji jest oddzielone ze źródłową siecią maszyn wirtualnych, jest niezbędne, aby użyć serwera przetwarzania na platformie Azure utworzone do powrotu po awarii.

### <a name="can-i-keep-the-ip-address-on-failover"></a>W trybie failover można zapewnić adres IP?

Tak, można zachować adres IP w trybie failover. Upewnij się, że podajesz docelowy adres IP w **obliczenia i sieć** ustawienia dla maszyny Wirtualnej przed włączeniem trybu failover. Ponadto Zamknij maszyny w momencie przejścia w tryb failover w celu uniknięcia konfliktów adresów IP podczas powrotu po awarii.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Można zmienić rozmiar docelowej maszyny Wirtualnej lub typ maszyny Wirtualnej przed włączeniem trybu failover?

Tak, można zmienić typu i rozmiaru maszyny Wirtualnej, w dowolnym momencie przed włączeniem trybu failover. W portalu, użyj **obliczenia i sieć** ustawienia dla zreplikowanej maszyny Wirtualnej.

### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?

Dla oprogramowania VMware na platformę Azure starsze punkty odzyskiwania, których można użyć to 72 godziny.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?

Po przejściu w tryb failover maszyn wirtualnych platformy Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN lokacja lokacja lub za pośrednictwem usługi Azure ExpressRoute. Aby nawiązać połączenie, należy przygotować kilka rzeczy. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Dane w trybie failed-over są odporne?

Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z wymaganiami platformy Azure umowy poziomu usług (SLA). Po przejściu do trybu failover, możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Uruchamianie trybu failover, wprowadzając pojedynczy wybór w portalu lub można użyć [PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia przejścia w tryb failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Może nie do innej lokalizacji?

Tak. W przypadku niepowodzenia za pośrednictwem platformy Azure można powrotu po awarii do innej lokalizacji Jeśli oryginalny jest niedostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Dlaczego muszę sieci VPN lub usługi ExpressRoute za pomocą prywatnej komunikacji równorzędnej do powrotu po awarii?

Podczas powrotu po awarii z platformy Azure, są kopiowane dane z platformy Azure, powrót do lokalnej maszyny Wirtualnej, a dostęp prywatny jest wymagany.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Czy mogę zmienić rozmiar maszyny Wirtualnej platformy Azure po włączeniu trybu failover?

Nie, nie można zmienić rozmiaru lub typu docelową maszynę Wirtualną po przejściu w tryb failover.

## <a name="automation-and-scripting"></a>Automatyzacji i opracowywaniu skryptów

### <a name="can-i-set-up-replication-with-scripting"></a>Można skonfigurować replikację za pomocą skryptów?

Tak. Za pomocą interfejsu API Rest, programu PowerShell lub zestawu SDK usługi Azure, możesz zautomatyzować przepływy pracy Site Recovery. [Dowiedz się więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność

### <a name="can-i-throttle-replication-bandwidth"></a>Czy mogę ograniczyć przepustowość replikacji

Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd](vmware-physical-azure-support-matrix.md) wymagań.
- [Konfigurowanie](vmware-azure-tutorial.md) programu VMware do platformy Azure replikacji.
