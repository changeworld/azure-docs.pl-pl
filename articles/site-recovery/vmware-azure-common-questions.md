---
title: Typowe pytania dotyczące odzyskiwania po awarii usługi VMware za pomocą usługi Azure Site Recovery
description: Uzyskaj odpowiedzi na typowe pytania dotyczące odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: ae16138ae44262f53a8f9948d6287f0acf621244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240020"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Typowe pytania dotyczące replikacji z programu VMware do platformy Azure

W tym artykule odniewany jest odpowiedzi na typowe pytania, które mogą pojawić się podczas wdrażania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware (VMware) na platformie Azure.

## <a name="general"></a>Ogólne

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Czego potrzebuję do odzyskiwania po awarii VMware VM?

[Dowiedz się więcej o składnikach zaangażowanych](vmware-azure-architecture.md) w odzyskiwanie po awarii maszyn wirtualnych VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Czy mogę używać usługi Site Recovery do migracji maszyn wirtualnych VMware na platformę Azure?

Tak. Oprócz używania usługi Site Recovery do konfigurowania pełnego odzyskiwania po awarii dla maszyn wirtualnych VMware, można również użyć usługi Site Recovery do migracji lokalnych maszyn wirtualnych VMware na platformę Azure. W tym scenariuszu można replikować lokalnych maszyn wirtualnych VMware do usługi Azure Storage. Następnie można przejść awaryjnie z lokalnego do platformy Azure. Po przemijaniu awaryjnego aplikacje i obciążenia są dostępne i uruchomione na maszynach wirtualnych platformy Azure. Proces jest jak konfigurowanie pełnego odzyskiwania po awarii, z tą różnicą, że w migracji nie można po awarii z powrotem z platformy Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?

Jeśli jesteś administratorem subskrypcji, masz uprawnienia replikacji, których potrzebujesz. Jeśli nie jesteś administratorem, potrzebujesz uprawnień do podjęcia następujących działań:

- Utwórz maszynę wirtualną platformy Azure w grupie zasobów i sieci wirtualnej, które można określić podczas konfigurowania usługi Site Recovery.
- Zapis na wybranym koncie magazynu lub dysku zarządzanym na podstawie konfiguracji.

[Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) o wymaganych uprawnieniach.

### <a name="what-applications-can-i-replicate"></a>Jakie aplikacje można replikować?

Można replikować dowolną aplikację lub obciążenie uruchomione na maszynie wirtualnej VMware, która spełnia [wymagania replikacji.](vmware-physical-azure-support-matrix.md#replicated-machines)

- Usługa Site Recovery obsługuje replikację obsługującą aplikację, dzięki czemu aplikacje mogą zostać przejęte awaryjnie i po awarii z powrotem do stanu inteligentnego.
- Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Ściśle współpracuje również z wiodącymi dostawcami, w tym Oracle, SAP, IBM i Red Hat.

[Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Czy mogę używać licencji serwera systemu operacyjnego gościa na platformie Azure?

Tak, klienci korzystający z pakietu Microsoft Software Assurance mogą korzystać z [korzyści hybrydowych platformy Azure,](https://azure.microsoft.com/pricing/hybrid-benefit/) aby zaoszczędzić na kosztach licencjonowania maszyn z systemem Windows Server, które są migrowane na platformę Azure, lub użyć platformy Azure do odzyskiwania po awarii.

## <a name="security"></a>Zabezpieczenia

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jaki dostęp do serwerów VMware wymaga od odzyskiwania witryny?

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Skonfiguruj maszynę wirtualną VMware z serwerem konfiguracji odzyskiwania lokacji.
- Automatyczne odnajdowanie maszyn wirtualnych do replikacji.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Jaki dostęp do maszyn wirtualnych VMware wymaga od odzyskiwania witryny?

- Aby replikować, maszyna wirtualna VMware musi mieć zainstalowaną i uruchominą usługę mobilności odzyskiwania lokacji. Narzędzie można wdrożyć ręcznie lub określić, że usługa Site Recovery umożliwia instalację wypychaną usługi po włączeniu replikacji maszyny Wirtualnej.
- Podczas replikacji maszyny wirtualne komunikują się z odzyskiem lokacji w następujący sposób:
    - Maszyny wirtualne komunikują się z serwerem konfiguracji na porcie HTTPS 443 w celu zarządzania replikacją.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443. (To ustawienie można modyfikować).
    - Jeśli włączysz spójność wielu maszyn wirtualnych, maszyny wirtualne komunikują się ze sobą za pomocą portu 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?

Nie, usługa Site Recovery nie przechwytuje replikowanych danych i nie ma żadnych informacji o tym, co jest uruchomione na maszynach wirtualnych. Dane replikacji są wymieniane między hipernadzorcami VMware i usługą Azure Storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.

Site Recovery posiada certyfikaty ISO 27001:2013 i 27018, HIPAA i DPA. Jest w trakcie ocen SOC2 i FedRAMP JAB.

## <a name="pricing"></a>Cennik

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Jak obliczyć przybliżone opłaty za odzyskiwanie po awarii VMware?

Kalkulator [cen](https://aka.ms/asr_pricing_calculator) służy do szacowania kosztów podczas korzystania z usługi Site Recovery.

Aby uzyskać szczegółowe oszacowanie kosztów, uruchom narzędzie do planowania wdrażania dla [VMware](https://aka.ms/siterecovery_deployment_planner) i użyj [raportu szacowania kosztów](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Czy istnieje jakaś różnica w kosztach między replikowaniem do magazynu lub bezpośrednio do dysków zarządzanych?

Dyski zarządzane są naliczane nieco inaczej niż konta magazynu. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/managed-disks/) o cenach dysków zarządzanych.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Czy istnieje jakaś różnica w kosztach podczas replikowania do ogólnego przeznaczenia konta magazynu w wersji 2?

Zazwyczaj zobaczysz wzrost kosztów transakcji poniesionych na kontach magazynu GPv2, ponieważ usługa Azure Site Recovery jest transakcjami o dużym obciążeniu. [Przeczytaj więcej,](../storage/common/storage-account-upgrade.md#pricing-and-billing) aby oszacować zmianę.

## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie mogę znaleźć instalatorów usługi Mobilności?

Instalatory znajdują się w folderze %ProgramData%\ASR\home\svsystems\pushinstallsvc\repozytorium na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak zainstalować usługę mobilności?

Na każdej maszynie wirtualnej, którą chcesz replikować, zainstaluj usługę za pomocą jednej z kilku metod:

- [Instalacja wypychania](vmware-physical-mobility-service-overview.md#push-installation)
- [Ręczna instalacja](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z interfejsu użytkownika lub programu PowerShell
- Wdrażanie przy użyciu narzędzia wdrażania, takiego jak [Menedżer konfiguracji](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Dyski zarządzane

### <a name="where-does-site-recovery-replicate-data-to"></a>Gdzie usługa Site Recovery replikuje dane?

Usługa Site Recovery replikuje lokalne maszyny wirtualne VMware i serwery fizyczne do dysków zarządzanych na platformie Azure.

- Serwer procesów odzyskiwania lokacji zapisuje dzienniki replikacji na koncie magazynu pamięci podręcznej w regionie docelowym.
- Te dzienniki są używane do tworzenia punktów odzyskiwania na dyskach zarządzanych przez platformę Azure, które mają prefiks **asrseeddisk**.
- W przypadku pracy awaryjnej wybrany punkt odzyskiwania jest używany do tworzenia nowego docelowego dysku zarządzanego. Ten dysk zarządzany jest dołączony do maszyny Wirtualnej na platformie Azure.
- Maszyny wirtualne, które zostały wcześniej zreplikowane na konto magazynu (przed marcem 2019) nie są zagrożone.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Czy można replikować nowe maszyny na konta magazynu?

Nie. Począwszy od marca 2019 r. w witrynie Azure portal można replikować tylko na dyskach zarządzanych platformy Azure.

Replikacja nowych maszyn wirtualnych na konto magazynu jest dostępna tylko przy użyciu programu PowerShell lub interfejsu API REST (wersja 2018-01-10 lub 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jakie są korzyści z replikowania do dysków zarządzanych?

[Dowiedz się, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Usługa Site Recovery upraszcza odzyskiwanie po awarii za pomocą dysków zarządzanych.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Czy można zmienić typ dysku zarządzanego po zabezpieczonych komputera?

Tak, można łatwo [zmienić typ dysku zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) dla trwających replikacji. Przed zmianą typu upewnij się, że na dysku zarządzanym nie jest generowany żaden adres URL podpisu dostępu współdzielonego:

1. Przejdź do zasobu **Dysku zarządzanego** w witrynie Azure portal i sprawdź, czy masz baner URL sygnatury dostępu udostępnionego w bloku **Przegląd.**
1. Jeśli baner jest obecny, wybierz go, aby anulować trwający eksport.
1. Zmień typ dysku w ciągu najbliższych kilku minut. Jeśli zmienisz typ dysku zarządzanego, poczekaj na nowe punkty odzyskiwania, które mają być generowane przez usługę Azure Site Recovery.
1. Użyj nowych punktów odzyskiwania dla każdego testu pracy awaryjnej lub pracy awaryjnej w przyszłości.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Czy można przełączyć replikację z dysków zarządzanych na dyski niezarządzane?

Nie. Przełączanie z zarządzanego na niezarządzane nie jest obsługiwane.

## <a name="replication"></a>Replikacja

### <a name="what-are-the-replicated-vm-requirements"></a>Jakie są wymagania replikowanej maszyny Wirtualnej?

[Dowiedz się więcej](vmware-physical-azure-support-matrix.md#replicated-machines) o wymaganiach dotyczących pomocy technicznej dla maszyn wirtualnych VMware i serwerów fizycznych.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformie Azure?

Replikacja jest ciągła podczas replikowania maszyn wirtualnych na platformie Azure.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację?

Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Czy mogę wykonać replikację początkową w trybie offline?

Replikacja w trybie offline nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co to jest asrseeddisk?

Dla każdego dysku źródłowego dane są replikowane na dysku zarządzanym na platformie Azure. Ten dysk ma prefiks **asrseeddisk**. Przechowuje kopię dysku źródłowego i wszystkie migawki punktu odzyskiwania.

### <a name="can-i-exclude-disks-from-replication"></a>Czy można wykluczyć dyski z replikacji?

Tak, można wykluczyć dyski.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?

Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Jeśli używam grup replikacji dla spójności wielu maszyn wirtualnych, czy mogę dodać nową maszynę wirtualną do istniejącej grupy replikacji?

Tak, można dodać nowe maszyny wirtualne do istniejącej grupy replikacji po włączeniu replikacji dla nich. Jednak:

- Nie można dodać maszyny Wirtualnej do istniejącej grupy replikacji po rozpoczęciu replikacji.
- Nie można utworzyć grupy replikacji dla istniejących maszyn wirtualnych.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Czy można modyfikować maszyny wirtualne replikowane przez dodawanie lub zmienianie rozmiaru dysków?

W przypadku replikacji VMware na platformę Azure można zmodyfikować rozmiar dysku źródłowych maszyn wirtualnych. Jeśli chcesz dodać nowe dyski, należy dodać dysk i ponownie konfigurować ochronę maszyny Wirtualnej.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Czy można migrować komputery lokalne do nowego serwera vCenter bez wpływu na trwającą replikację?

Nie. Zmiana VMware Vcenter lub migracji wpłynie na trwającą replikację. Skonfiguruj usługę Site Recovery za pomocą nowego serwera vCenter Server i ponownie włącz replikację maszyn.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Czy można replikować do pamięci podręcznej lub konta magazynu docelowego, który ma sieć wirtualną (z zapory platformy Azure) skonfigurowane na nim?

Nie, usługa Site Recovery nie obsługuje replikacji do usługi Azure Storage w sieciach wirtualnych.

## <a name="component-upgrade"></a>Uaktualnienie komponentu

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moja wersja agenta usług mobilności lub serwera konfiguracji jest stara, a moje uaktualnienie nie powiodło się. Co mam zrobić?

Odzyskiwanie witryny jest zgodne z modelem obsługi N-4. [Dowiedz się więcej](https://aka.ms/asr_support_statement) o tym, jak uaktualnić z bardzo starych wersji.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Gdzie można znaleźć informacje o wersji i pakiety zbiorcze aktualizacji dla usługi Azure Site Recovery?

[Dowiedz się więcej o nowych aktualizacjach](site-recovery-whats-new.md)i [uzyskaj informacje o zestawieniu](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Gdzie można znaleźć informacje o uaktualnieniu do odzyskiwania po awarii na platformie Azure?

[Dowiedz się więcej o uaktualnianiu](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Czy muszę ponownie uruchomić maszyny źródłowe dla każdego uaktualnienia?

Ponowne uruchomienie komputera jest zalecane, ale nie jest obowiązkowe dla każdego uaktualnienia. [Dowiedz się więcej](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego działa serwer konfiguracji?

Serwer konfiguracji uruchamia lokalne składniki odzyskiwania lokacji, w tym:

- Sam serwer konfiguracji. Serwer koordynuje komunikację między składnikami lokalnymi a platformą Azure i zarządza replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Ten serwer:
    1. odbiera dane replikacji,
    2. Optymalizuje dane za pomocą buforowania, kompresji i szyfrowania.
    3. Wysyła dane do usługi Azure Storage.
  Serwer procesów wykonuje również instalację wypychaną usługi mobilności na maszynach wirtualnych i wykonuje automatyczne odnajdowanie lokalnych maszyn wirtualnych VMware.
- Główny serwer docelowy, który obsługuje dane replikacji podczas powrotu po awarii z platformy Azure.

[Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdzie skonfigurować serwer konfiguracyjny?

Potrzebujesz jednej, wysoce dostępnej lokalnej maszyny Wirtualnej VMware dla serwera konfiguracji. W przypadku odzyskiwania po awarii serwera fizycznego należy zainstalować serwer konfiguracji na komputerze fizycznym.

### <a name="what-do-i-need-for-the-configuration-server"></a>Czego potrzebuję dla serwera konfiguracji?

Przejrzyj [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Czy można ręcznie skonfigurować serwer konfiguracji zamiast szablonu?

Zaleca się [utworzenie maszyny Wirtualnej serwera konfiguracji](vmware-azure-deploy-configuration-server.md) przy użyciu najnowszej wersji szablonu OVF (Open Virtualization Format). Jeśli nie możesz użyć szablonu (na przykład, jeśli nie masz dostępu do serwera VMware), [pobierz](physical-azure-set-up-source.md) plik instalacyjny z portalu i skonfiguruj serwer konfiguracji.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Czy serwer konfiguracji może być replikowany do więcej niż jednego regionu?

Nie. Aby replikować do więcej niż jednego regionu, potrzebny jest serwer konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy mogę hostować serwer konfiguracji na platformie Azure?

Mimo że jest to możliwe, maszyna wirtualna platformy Azure z uruchomionym serwerem konfiguracji będzie musiała komunikować się z lokalną infrastrukturą VMware i maszynami wirtualnymi. Ta komunikacja dodaje opóźnienia i wpływa na trwającą replikację.

### <a name="how-do-i-update-the-configuration-server"></a>Jak zaktualizować serwer konfiguracyjny?

[Dowiedz się,](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) jak zaktualizować serwer konfiguracji.

- Najnowsze informacje o aktualizacji można znaleźć na [stronie aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Najnowszą wersję można pobrać z portalu. Możesz też pobrać najnowszą wersję serwera konfiguracyjnego bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).
- Jeśli wersja jest więcej niż cztery wersje starsze niż bieżąca wersja, zobacz [instrukcje pomocy technicznej](https://aka.ms/asr_support_statement) dla aktualizacji.

### <a name="should-i-back-up-the-configuration-server"></a>Czy należy ponownie wyw.

Zaleca się wykonywanie regularnych zaplanowanych kopii zapasowych serwera konfiguracji.

- W przypadku pomyślnego powrotu po awarii maszyna wirtualna, która nie powiodła się, musi istnieć w bazie danych serwera konfiguracji.
- Serwer konfiguracji musi być uruchomiony i jest podłączony.
- [Dowiedz się więcej](vmware-azure-manage-configuration-server.md) o typowych zadaniach zarządzania serwerem konfiguracji.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Czy podczas konfigurowania serwera konfiguracji można ręcznie pobrać i zainstalować mysql?

Tak. Pobierz aplikację MySQL i umieść ją w folderze C:\Temp\ASRSetup. Następnie zainstaluj go ręcznie. Po skonfigurowaniu maszyny Wirtualnej serwera konfiguracji i zaakceptowaniu warunków mysql zostanie wyświetlony jako **Już zainstalowany** w **pobierz i zainstaluje**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Czy mogę uniknąć pobierania MySQL, ale niech site recovery go zainstalować?

Tak. Pobierz instalator MySQL i umieść go w folderze C:\Temp\ASRSetup. Podczas konfigurowania maszyny Wirtualnej serwera konfiguracji zaakceptuj warunki i wybierz pozycję **Pobierz i zainstaluj**. Portal użyje instalatora, który został dodany do zainstalowania MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Czy mogę używać maszyny Wirtualnej serwera konfiguracji dla czegokolwiek innego?

Nie. Maszyny Wirtualnej należy używać tylko dla serwera konfiguracji.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Czy mogę sklonować serwer konfiguracji i używać go do aranżacji?

Nie. Skonfiguruj nowy serwer konfiguracji, aby uniknąć problemów z rejestracją.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Czy mogę zmienić przechowalnię, w której jest zarejestrowany serwer konfiguracji?

Nie. Po skojarzeniu przechowalni z serwerem konfiguracji nie można go zmienić. [Dowiedz się więcej](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) o rejestrowaniu serwera konfiguracji w innym magazynie.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Czy mogę używać tego samego serwera konfiguracji do odzyskiwania po awarii zarówno maszyn wirtualnych VMware, jak i serwerów fizycznych?

Tak, ale należy pamiętać, że komputer fizyczny można powiodu tylko do maszyny wirtualnej VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Gdzie mogę pobrać hasło serwera konfiguracji?

[Dowiedz się,](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) jak pobrać hasło.

### <a name="where-can-i-download-vault-registration-keys"></a>Gdzie mogę pobrać klucze rejestracyjne skarbca?

W przechowalni usług odzyskiwania wybierz pozycję **Serwery konfiguracji** w obszarze**Zarządzanie infrastrukturą** **odzyskiwania lokacji** > . Następnie w **obszarze Serwery**wybierz pozycję **Pobierz klucz rejestracji,** aby pobrać plik poświadczeń przechowalni.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Czy serwer konfiguracji można używać do ochrony wielu wystąpień vCenter?

Tak, pojedynczy serwer konfiguracji może chronić maszyny wirtualne w wielu centrach wirtualnych.  Nie ma ograniczeń co do liczby wystąpień vCenter można dodać do serwera konfiguracji, jednak ograniczenia dotyczące liczby maszyn wirtualnych, które serwer konfiguracji może chronić, mają zastosowanie.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Czy serwer konfiguracji może chronić wiele klastrów w centrum vCenter?

Tak, usługa Azure Site Recovery może chronić maszyny wirtualne w różnych klastrach.

## <a name="process-server"></a>Serwer przetwarzania

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Dlaczego nie mogę wybrać serwera przetwarzania po włączeniu replikacji?

Aktualizacje w wersji 9.24 i nowszych są teraz wyświetlane kondycji [serwera przetwarzania po włączeniu replikacji](vmware-azure-enable-replication.md#enable-replication). Ta funkcja pomaga uniknąć ograniczania przepustowości serwera procesów i zminimalizować użycie serwerów procesów w złej kondycji.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Jak zaktualizować serwer przetwarzania do wersji 9.24 lub nowszej, aby uzyskać dokładne informacje o kondycji?

Począwszy od [wersji 9.24,](service-updates-how-to.md#links-to-currently-supported-update-rollups)dodano więcej alertów wskazujących kondycję serwera przetwarzania. [Zaktualizuj składniki odzyskiwania witryny do wersji 9.24 lub nowszej,](service-updates-how-to.md#links-to-currently-supported-update-rollups) aby wszystkie alerty były generowane.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Jak mogę zapewnić wysoką dostępność serwera przetwarzania?

Konfigurując więcej niż jeden serwer przetwarzania, projekt zapewnia elastyczność przenoszenia chronionych komputerów z serwera procesów w złej kondycji do działającego serwera przetwarzania. Przemieszczanie komputera z jednego serwera przetwarzania do innego musi być zainicjowane jawnie/ręcznie za pomocą określonych kroków w tym miejscu: [przenoszenie maszyn wirtualnych między serwerami przetwarzania](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Czy mogę używać lokalnego serwera przetwarzania do powrotu po awarii?

Zdecydowanie zaleca się utworzenie serwera przetwarzania na platformie Azure w celach powrotu po awarii, aby uniknąć opóźnień transferu danych. Ponadto w przypadku oddzielenia źródłowej sieci maszyn wirtualnych z siecią opartą na platformie Azure na serwerze konfiguracji, konieczne jest użycie serwera przetwarzania utworzonego na platformie Azure w celu powrotu po awarii.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Czy mogę zachować adres IP w pracy awaryjnej?

Tak, adres IP można zachować w pracy awaryjnej. Upewnij się, że określisz docelowy adres IP w **ustawieniach obliczeniowych i sieciowych** dla maszyny Wirtualnej przed przełączeniem awaryjnym. Ponadto zamknij maszyny w czasie pracy awaryjnej, aby uniknąć konfliktów adresów IP podczas powrotu po awarii.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Czy mogę zmienić docelowy rozmiar maszyny Wirtualnej lub typ maszyny Wirtualnej przed przełączeniem w prawo pracy awaryjnej?

Tak, można zmienić typ lub rozmiar maszyny Wirtualnej w dowolnym momencie przed przejściem awaryjnym. W portalu użyj ustawień **obliczeniowych i sieciowych** dla replikowanej maszyny Wirtualnej.

### <a name="how-far-back-can-i-recover"></a>Jak daleko wstecz można odzyskać?

W przypadku platformy VMware na platformie Azure najstarszy punkt odzyskiwania, którego można użyć, to 72 godziny.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak uzyskać dostęp do maszyn wirtualnych platformy Azure po przemijaniu awaryjnym?

Po przełączeniu w błąd można uzyskać dostęp do maszyn wirtualnych platformy Azure za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN typu lokacja-lokacja lub za pośrednictwem usługi Azure ExpressRoute. Aby się połączyć, musisz przygotować kilka rzeczy. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Czy dane awaryjne są odporne?

Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery została zaprojektowana pod kątem pracy awaryjnej w pomocniczym centrum danych platformy Azure, zgodnie z wymogami umowy dotyczącej poziomu usług platformy Azure (SLA). W przypadku pracy awaryjnej upewnij się, że metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

[Praca awaryjna](site-recovery-failover.md) nie jest automatyczna. Pracę awaryjną można uruchomić, zaznaczając pojedynczy wybór w portalu lub można użyć [programu PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia pracy awaryjnej.

### <a name="can-i-fail-back-to-a-different-location"></a>Czy mogę wrócić do innej lokalizacji?

Tak. Jeśli po awarii na platformie Azure, można powiększyć po awarii do innej lokalizacji, jeśli oryginalna nie jest dostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Dlaczego potrzebuję sieci VPN lub usługi ExpressRoute z prywatną komunikacją równorzędną, aby wrócić awaryjnie?

Po powrocie z platformy Azure dane z platformy Azure są kopiowane z powrotem do lokalnej maszyny Wirtualnej i wymagany jest dostęp prywatny.


## <a name="automation-and-scripting"></a>Automatyzacja i skrypty

### <a name="can-i-set-up-replication-with-scripting"></a>Czy można skonfigurować replikację za pomocą skryptów?

Tak. Przepływy pracy odzyskiwania lokacji można zautomatyzować przy użyciu interfejsu API rest, programu PowerShell lub sdk platformy Azure. [Dowiedz się więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność

### <a name="can-i-throttle-replication-bandwidth"></a>Czy można ograniczyć przepustowość replikacji?

Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj](vmware-physical-azure-support-matrix.md) wymagania dotyczące pomocy technicznej.
- [Konfiguracja](vmware-azure-tutorial.md) Replikacja VMware do platformy Azure.
