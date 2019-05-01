---
title: Co to jest Azure Backup?
description: Omówienie usługi Azure Backup i jak przyczynia się do Twojej ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR) strategii odzyskiwania.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bd90d315fd5590a8bd862a1a3397cf8c254fccc8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714283"
---
# <a name="what-is-azure-backup"></a>Co to jest Azure Backup?

Usługa Azure Backup umożliwia wykonywanie kopii zapasowych danych w chmurze platformy Microsoft Azure. Możesz wykonywać kopie zapasowe lokalnych maszyn i obciążeń oraz maszyn wirtualnych platformy Azure.


## <a name="why-use-azure-backup"></a>Dlaczego warto używać usługi Azure Backup?

Usługa Azure Backup oferuje następujące kluczowe korzyści:

- **Odciążanie lokalnej kopii zapasowej**: Usługa Azure Backup oferuje proste rozwiązanie do wykonywania kopii zapasowych zasobów lokalnych w chmurze. Uzyskaj krótkoterminowe i długoterminowe kopie zapasowe bez konieczności wdrażania złożonych lokalnych rozwiązań do wykonywania kopii zapasowych.
- **Wykonywanie kopii zapasowych maszyn wirtualnych IaaS platformy Azure**: usługa Azure Backup umożliwia wykonywanie niezależnych i odizolowanych kopii zapasowych, co chroni przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi zarządzanymi punktami odzyskiwania. Konfiguracji i skalowalność to proste, kopie zapasowe są zoptymalizowane i można je łatwo przywrócić, zgodnie z potrzebami.
- **Łatwe skalowanie** — usługa Azure Backup używa dostępnej mocy i nieograniczonej skali chmury Azure do zapewniania wysokiej dostępności, bez narzutu na konserwację lub monitorowanie.
- **Uzyskać nieograniczony transfer danych**: Usługa Azure Backup nie ogranicza ilość danych przychodzących lub wychodzących przetransferowana lub opłaty za przesyłane dane.
    - Dane wychodzące to dane transferowane z magazynu usługi Recovery Services podczas operacji przywracania.
    - Jeśli tworzysz wstępną kopię zapasową w trybie offline za pomocą usługi Azure Import/Export w celu importowania dużych ilości danych, istnieje koszt związany z danymi przychodzącymi.  [Dowiedz się więcej](backup-azure-backup-import-export.md).
- **Bezpieczeństwo danych**: Usługa Azure Backup oferuje rozwiązania do zabezpieczania danych przesyłanych i magazynowanych.
- **Kopie zapasowe spójne na poziomie aplikacji**: kopia zapasowa spójna na poziomie aplikacji oznacza, że punkt odzyskiwania ma wszystkie dane wymagane do przywrócenia kopii zapasowej. Usługa Azure Backup umożliwia wykonywanie kopii zapasowych spójnych na poziomie aplikacji, które zapewniają, że do przywrócenia danych nie są wymagane dodatkowe poprawki. Przywracanie danych spójnych na poziomie aplikacji skraca czas przywracania, co pozwala szybko powrócić do stanu roboczego.
- **Krótkoterminowe i długoterminowe przechowywanie danych**: Magazynów usług Recovery Services możesz użyć do krótko- i długoterminowego przechowywania danych. Platforma Azure nie ogranicza czasu przechowywania danych w magazynie usługi Recovery Services. Możesz je przechowywać przez dowolny czas. Usługa Azure Backup ma limit 9999 punktów odzyskiwania dla każdego chronionego wystąpienia. [Dowiedz się więcej](backup-introduction-to-azure-backup.md#backup-and-retention) o wpływie tego limitu na wymagania dotyczące tworzenia kopii zapasowych.
- **Automatyczne zarządzanie magazynem** — hybrydowe środowiska często wymagają heterogenicznego magazynu — znajdującego się w części lokalnie, a w części w chmurze. W usłudze Azure Backup nie płaci się za korzystanie z lokalnych urządzeń magazynujących. Usługa Azure Backup automatycznie przydziela magazyn kopii zapasowych i zarządza nim oraz używa modelu płatności zgodnie z rzeczywistym użyciem, dzięki czemu płacisz tylko za używane miejsce w magazynie. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup) o cenach.
- **Wiele opcji magazynowania** — usługa Azure Backup oferuje dwa typy replikacji, aby zapewnić wysoką dostępność magazynu/danych.
    - [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych istnieją w tym samym regionie. Magazyn LRS to ekonomiczna opcja ochrony danych przed awariami sprzętu lokalnego.
    - [Magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md) jest ustawieniem domyślnym i zalecaną opcją replikacji. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych). Magazyn GRS kosztuje więcej niż LRS, ale zapewnia wyższy poziom trwałości danych nawet w przypadku wystąpienia awarii regionalnej.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Jaka jest różnica między usługami Azure Backup i Azure Site Recovery?

Usługi Azure Backup i Azure Site Recovery są częścią strategii zachowywania ciągłości działania i odzyskiwania po awarii (BCDR) w firmie. Strategia BCDR obejmuje dwa duże cele:

- Zabezpieczanie danych biznesowych i umożliwianie odzyskiwania ich po awarii.
- Kontynuowanie działania aplikacji i obciążeń podczas planowanych lub nieplanowanych przestojów.

Obie usługi oferują różne funkcje, które się uzupełniają.

- **Azure Site Recovery**: Usługa Site Recovery zapewnia rozwiązanie do odzyskiwania po awarii dla maszyn lokalnych i maszyn wirtualnych platformy Azure. Możesz replikować maszyny z lokalizacji podstawowej do dodatkowej. W przypadku awarii przełączasz maszyny w tryb failover do lokalizacji dodatkowej i możesz uzyskiwać do nich dostęp z tego miejsca. Gdy wszystko będzie ponownie działać normalnie, przełączasz maszyny z powrotem, aby odzyskać je w lokacji głównej.
- **Azure Backup**: Usługa Azure Backup wykonuje kopie zapasowe danych z maszyn lokalnych i maszyn wirtualnych platformy Azure. Kopie zapasowe i odzyskiwanie danych można wykonywać na szczegółowym poziomie, łącznie z kopiami zapasowymi plików, folderów, stanu systemu maszyny i kopiami zapasowymi danych z uwzględnieniem aplikacji. Usługa Azure Backup obsługuje dane na bardziej szczegółowym poziomie niż usługa Site Recovery. Jeśli na przykład prezentacja na laptopie zostanie uszkodzona, możesz ją przywrócić za pomocą usługi Azure Backup. Aby zapewnić bezpieczeństwo i dostępność konfiguracji oraz danych maszyny wirtualnej, możesz użyć usługi Site Recovery.  

Użyj punktów w tabeli, aby łatwiej ustalić wymagania w zakresie strategii BCDR.

**Cel** | **Szczegóły** | **Porównanie**
--- | --- | ---
**Wykonywanie kopii zapasowej/przechowywanie danych** | Dane kopii zapasowej można przechowywać przez wiele dni, miesięcy lub nawet lat, jeśli jest to konieczne pod względem zgodności. | Rozwiązania do wykonywania kopii zapasowych, takie jak Azure Backup, umożliwiają precyzyjne wybieranie danych, dla których chcesz wykonać kopię zapasową, oraz precyzyjne dostosowywanie zasad wykonywania kopii zapasowych i przechowywania.<br/><br/> Usługa Site Recovery nie zezwala na tym samym dostrajanie.
**Cel punktu odzyskiwania** | Dopuszczalna ilość utraconych danych, jeśli wymagane jest odzyskiwanie. | Kopie zapasowe mają bardziej zmienny cel punktu odzyskiwania.<br/><br/> Kopie zapasowe maszyn wirtualnych mają zwykle cel punktu odzyskiwania na poziomie jednego dnia, natomiast kopie zapasowe baz danych mają cel punktu odzyskiwania o wartości 15 minut.<br/><br/> Usługa Site Recovery ma ustawiony niski cel punktu odzyskiwania, ponieważ replikacja jest przeprowadzana ciągle lub często, aby zachować małą różnicę między źródłem i kopią repliki.
**Cel czasu odzyskiwania** |Ilość czasu potrzebnego do ukończenia odzyskiwania lub przywracania. | Ilość danych, które musi przetworzyć rozwiązanie kopii zapasowych, jest zwykle znacznie wyższa (ze względu na większą wartość RPO), a to prowadzi do większych wartości RTO. Na przykład przywrócenie danych z taśmy może potrwać kilka dni, zależnie od czasu potrzebnego do przetransportowania taśmy z oddalonej lokalizacji.

## <a name="what-backup-scenarios-are-supported"></a>Jakie scenariusze wykonywania kopii zapasowej są obsługiwane?

Usługa Azure Backup umożliwia wykonywanie kopii zapasowych maszyn lokalnych i maszyn wirtualnych platformy Azure.

**Maszyna** | **Scenariusz wykonywania kopii zapasowej**
--- | ---
**Lokalna kopia zapasowa** |  (1) Uruchom agenta usługi Microsoft Azure Recovery Services (MARS) w usłudze Azure Backup na lokalnych maszynach z systemem Windows, aby tworzyć kopię zapasową poszczególnych plików i stanu systemu. <br/><br/>(2) tworzyć kopie zapasowe maszyn lokalnych na serwer zapasowy (System Center Data Protection Manager (DPM) lub Microsoft Azure Backup serwera (MABS)), a następnie skonfiguruj serwer kopii zapasowych, aby utworzyć kopię zapasową w magazynie usługi Azure Backup Recovery Services na platformie Azure.
**Maszyny wirtualne platformy Azure** | (1) Włącz tworzenie kopii zapasowych dla poszczególnych maszyn wirtualnych platformy Azure. Przy włączaniu tworzenia kopii zapasowych usługa Azure Backup instaluje rozszerzenie agenta maszyny wirtualnej platformy Azure, który działa na maszynie wirtualnej. Agent tworzy kopię zapasową całej maszyny wirtualnej.<br/><br/> (2) Uruchom agenta MARS na maszynie wirtualnej platformy Azure. Jest to przydatne, jeśli chcesz tworzyć kopie zapasowe poszczególnych plików i folderów na maszynie wirtualnej.<br/><br/> (3) Utwórz kopię zapasową maszyny wirtualnej platformy Azure na serwerze programu DPM lub usługi MABS na platformie Azure. Następnie utwórz kopię zapasową serwera programu DPM lub usługi MABS w magazynie przy użyciu usługi Azure Backup.


## <a name="why-use-a-backup-server"></a>Dlaczego warto używać serwera kopii zapasowych?
Zalety kopie zapasowe maszyn i aplikacji do magazynu serwera usługi Mab/programu DPM, a następnie tworzenia kopii zapasowych magazynu programu DPM/serwera usługi Mab magazynu są następujące:

- Kopie zapasowe w usłudze MABS lub programie DPM uwzględniają aplikacje i są zoptymalizowane pod kątem typowych aplikacji, takich jak SQL Server, Exchange i SharePoint, a także umożliwiają tworzenie kopii zapasowych plików/folderów/woluminów i kopii zapasowych stanu maszyny (bez systemu operacyjnego, stan systemu).
- W przypadku maszyn lokalnych nie trzeba instalować agenta MARS na każdej maszynie, której kopię zapasową chcesz utworzyć. Każda maszyna działa agent ochrony programu DPM/serwera usługi Mab i agenta usług MARS jest uruchamiany na serwera usługi Mab/DPM tylko.
- Zapewnia to większą elastyczność i szczegółowe opcje planowania wykonywania kopii zapasowych.
- Możesz zarządzać kopiami zapasowymi wielu maszyn zawartymi w grupach ochrony za pomocą jednej konsoli. Jest to szczególnie przydatne w sytuacji, gdy aplikacje są rozmieszczone warstwowo na wielu maszynach i chcesz wykonać ich kopię zapasową razem.

Dowiedz się więcej o [sposobie działania kopii zapasowych](backup-architecture.md#architecture-back-up-to-dpmmabs) podczas korzystania z serwera kopii zapasowych oraz [wymaganiach dotyczących obsługi](backup-support-matrix-mabs-dpm.md) dla serwerów kopii zapasowych.

## <a name="what-can-i-back-up"></a>Dla jakich danych mogę tworzyć kopię zapasową?

**Maszyna** | **Metoda tworzenia kopii zapasowych** | **Wykonywanie kopii zapasowej**
--- | --- | ---
**Lokalne maszyny wirtualne z systemem Windows** | Uruchamianie agenta MARS | Wykonywanie kopii zapasowej plików, folderów i stanu systemu.<br/><br/> Maszyny z systemem Linux nie są obsługiwane.
**Maszyny lokalne** | Tworzenie kopii zapasowych w programie DPM lub usłudze MABS | Można tworzyć kopie zapasowe wszystkich elementów chronionych przez program [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) lub usługę [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), w tym plików/folderów/udziałów/woluminów i danych aplikacji.
**Maszyny wirtualne platformy Azure** | Uruchamianie rozszerzenia kopii zapasowej dla agenta maszyny wirtualnej platformy Azure | Tworzenie kopii zapasowych całej maszyny wirtualnej
**Maszyny wirtualne platformy Azure** | Uruchamianie agenta MARS | Wykonywanie kopii zapasowej plików, folderów i stanu systemu.<br/><br/> Maszyny z systemem Linux nie są obsługiwane.
**Maszyny wirtualne platformy Azure** | Tworzenie kopii zapasowych w usłudze MABS lub programie DPM na platformie Azure | Można tworzyć kopie zapasowe wszystkich elementów chronionych przez usługę [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) lub program [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807), w tym plików/folderów/udziałów/woluminów i danych aplikacji.

## <a name="what-backup-agents-do-i-need"></a>Jakich agentów kopii zapasowych potrzebuję?

**Scenariusz** | **Agent**
--- | ---
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure** | Agent nie jest wymagany. Rozszerzenie maszyny wirtualnej platformy Azure do tworzenia kopii zapasowych jest instalowane na maszynie wirtualnej platformy Azure przy pierwszym uruchomieniu tworzenia kopii zapasowej tej maszyny.<br/><br/> Obsługa systemów Windows i Linux.
**Tworzenie kopii zapasowych maszyn z systemem Windows w środowisku lokalnym** | Pobierz, zainstaluj i uruchom agenta MARS bezpośrednio na maszynie.
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure za pomocą agenta usług MARS** | Pobierz, zainstaluj i uruchom agenta MARS bezpośrednio na maszynie. Agent MARS może działać równocześnie z rozszerzeniem kopii zapasowej.
**Tworzenie kopii zapasowych maszyn lokalnych i maszyn wirtualnych platformy Azure chronionych przez program DPM lub usługę MABS** | Agent ochrony programu DPM lub usługi MABS jest uruchomiony na maszynach, które mają być chronione. Agent MARS jest uruchomiony na serwerze programu DPM lub w usłudze MABS w celu tworzenia kopii zapasowych na platformie Azure.

## <a name="which-backup-agent-should-i-use"></a>Którego agenta kopii zapasowej należy używać?

**Tworzenie kopii zapasowych** | **Rozwiązanie** | **Ograniczenie**
--- | --- | ---
**Chcę tworzyć kopie zapasowe całej maszyny wirtualnej platformy Azure** | Włącz tworzenie kopii zapasowych dla maszyny wirtualnej. Rozszerzenie kopii zapasowej zostanie automatycznie skonfigurowane na maszynie wirtualnej platformy Azure z systemem Windows lub Linux. | Tworzona jest kopia zapasowa całej maszyny wirtualnej <br/><br/> W przypadku maszyn wirtualnych z systemem Windows kopia zapasowa jest spójna na poziomie aplikacji. W przypadku systemu Linux kopia zapasowa jest spójna na poziomie plików. Jeśli potrzebujesz programu app-aware dla maszyn wirtualnych systemu Linux, należy skonfigurować to za pomocą skryptów niestandardowych.
**Chcę tworzyć kopie zapasowe określonych plików/folderów na maszynie wirtualnej platformy Azure** | Wdróż agenta MARS na maszynie wirtualnej.
**Chcę bezpośrednio tworzyć kopie zapasowe maszyn lokalnych z systemem Windows** | Zainstaluj agenta usługi MARS na maszynie. | Możesz wykonywać kopie zapasowe plików, folderów i stanu systemu na platformie Azure. Kopie zapasowe nie uwzględniają aplikacji.
**Chcę bezpośrednio tworzyć kopie zapasowe maszyn lokalnych z systemem Linux** | Musisz wdrożyć program DPM lub usługę MABS, aby móc wykonać kopię zapasową na platformie Azure. | Kopia zapasowa hosta systemu Linux nie jest obsługiwana, tylko kopię zapasową można wykonywać maszyny gościa z systemem Linux hostowanych w funkcji Hyper-V lub VMWare.
**Chcę tworzyć kopie zapasowe aplikacji działających lokalnie** | Aby można było tworzyć kopie zapasowe z uwzględnieniem aplikacji, maszyny muszą być chronione przez program DPM lub usługę MABS.
**Potrzebuję szczegółowych i elastycznych ustawień kopii zapasowych i odzyskiwania dla maszyn wirtualnych platformy Azure** | Chroń maszyny wirtualne platformy Azure za pomocą usługi MABS lub programu DPM na platformie Azure, aby uzyskać dodatkową elastyczność w przypadku planowania tworzenia kopii zapasowych oraz pełną elastyczność podczas ochrony i przywracania plików, folderów, woluminów, aplikacji i stanu systemu.

## <a name="how-does-azure-backup-work-with-encryption"></a>Jak działa usługa Azure Backup przy użyciu szyfrowania

**Szyfrowanie** | **Tworzenie kopii zapasowych w środowisku lokalnym** | **Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure** | **Tworzenie kopii zapasowej bazy danych SQL na maszynach wirtualnych platformy Azure**
--- | --- | --- | ---
Szyfrowanie w spoczynku<br/> (Szyfrowanie danych, gdzie jest utrwalony/są przechowywane) | Hasło określony przez klienta jest używany do szyfrowania danych | Azure [szyfrowanie usługi Storage (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) jest używany do szyfrowania danych przechowywanych w magazynie.<br/><br/> Kopia zapasowa automatycznie szyfruje dane przed przekazaniem jej. Usługa Azure Storage odszyfrowuje dane przed ich pobraniem. Korzystanie z kluczy zarządzanych przez klienta dla SSE nie jest obecnie obsługiwane.<br/><br/> Można utworzyć kopię zapasową maszyn wirtualnych, które używają [usługa Azure disk encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) do szyfrowania dysków systemu operacyjnego i danych. Usługa Azure Backup obsługuje maszyny wirtualne szyfrowane przy użyciu klucza szyfrowania bloków — tylko i przy użyciu zarówno klucz szyfrowania bloków i [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Przegląd [ograniczenia](backup-azure-vms-encryption.md#encryption-support). | Usługa Azure Backup obsługuje kopie zapasowe baz danych programu SQL Server lub serwera z [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) włączone. Backup obsługuje funkcję TDE, za pomocą kluczy zarządzanych przez platformę Azure lub za pomocą kluczy zarządzanych przez klienta (BYOK).<br/><br/> Kopia zapasowa nie wykonuje żadnych szyfrowanie SQL jako część procesu tworzenia kopii zapasowej.
Szyfrowanie podczas transferu<br/> (Szyfrowanie danych wysyłanych z jednej lokalizacji do innej) | Dane są szyfrowane przy użyciu AES256 i wysyłane do magazynu na platformie Azure przy użyciu protokołu HTTPS | W ramach platformy Azure danych między magazynu platformy Azure i magazyn jest chroniony przez protokół HTTPS. Te dane pozostają w sieci szkieletowej platformy Azure.<br/><br/> Podczas odzyskiwania plików iSCSI zabezpiecza dane przesyłane między magazynu i maszyn wirtualnych platformy Azure. Bezpiecznego tunelowania chroni kanału iSCSI. | W ramach platformy Azure danych między magazynu platformy Azure i magazyn jest chroniony przez protokół HTTPS.<br/><br/> Odzyskiwanie plików nie są istotne dla programu SQL.

## <a name="next-steps"></a>Kolejne kroki

- [Przejrzyj](backup-architecture.md) architekturę i składniki w różnych scenariuszach wykonywania kopii zapasowych.
- [Sprawdź](backup-support-matrix.md) obsługiwane funkcje i ustawienia kopii zapasowych.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
