---
title: Co to jest Azure Backup?
description: Zawiera omówienie usługi Azure Backup oraz sposobu wdrażania jej w ramach strategii ciągłości działania i odzyskiwania po awarii (BDCR).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7156042243b1ba28cea712dc3722600b9fc46c42
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360644"
---
# <a name="what-is-azure-backup"></a>Co to jest Azure Backup?

Usługa Azure Backup umożliwia wykonywanie kopii zapasowych danych w chmurze platformy Microsoft Azure. Możesz wykonywać kopie zapasowe lokalnych maszyn i obciążeń oraz maszyn wirtualnych platformy Azure.


## <a name="why-use-azure-backup"></a>Dlaczego warto używać usługi Azure Backup?

Usługa Azure Backup oferuje następujące kluczowe korzyści:

- **Odciążanie lokalnej kopii zapasowej**: Usługa Azure Backup oferuje proste rozwiązanie do wykonywania kopii zapasowych zasobów lokalnych w chmurze. Uzyskaj krótkoterminowe i długoterminowe kopie zapasowe bez konieczności wdrażania złożonych lokalnych rozwiązań do wykonywania kopii zapasowych. Wyeliminuj potrzebę używania taśm i kopii zapasowych poza siedzibą firmy.
- **Wykonywanie kopii zapasowych maszyn wirtualnych IaaS platformy Azure**: usługa Azure Backup umożliwia wykonywanie niezależnych i odizolowanych kopii zapasowych, co chroni przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi zarządzanymi punktami odzyskiwania. Konfiguracja i skalowalność są proste, kopie zapasowe są zoptymalizowane i można je łatwo przywrócić.
- **Łatwe skalowanie** — usługa Azure Backup używa dostępnej mocy i nieograniczonej skali chmury Azure do zapewniania wysokiej dostępności — bez narzutu na konserwację lub monitorowanie. Możesz skonfigurować alerty, aby udostępniać informacje o zdarzeniach, ale nie musisz obawiać się o wysoką dostępność danych w chmurze.
- **Nieograniczony transfer danych** — usługa Azure Backup nie ogranicza ilości przesyłanych danych przychodzących i wychodzących. W usłudze Azure Backup nie są również naliczane opłaty za przesyłane dane. Jeśli jednak używasz usługi Azure Import/Export do importowania dużych ilości danych, istnieje koszt związany z danymi przychodzącymi. Aby uzyskać więcej informacji o tym koszcie, zobacz [Offline-backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Przepływ pracy tworzenia kopii zapasowej offline w usłudze Azure Backup). Dane wychodzące to dane transferowane z magazynu usługi Recovery Services podczas operacji przywracania.
- **Bezpieczeństwo danych**: szyfrowanie danych umożliwia bezpieczną transmisję i przechowywania danych w chmurze publicznej. Hasło szyfrowania przechowujesz lokalnie i nigdy nie jest ono przesyłane ani przechowywane na platformie Azure. Jeśli jest konieczne przywrócenie jakichkolwiek danych, tylko Ty masz hasło lub klucz szyfrowania.
- **Kopie zapasowe spójne na poziomie aplikacji**: kopia zapasowa spójna na poziomie aplikacji oznacza, że punkt odzyskiwania ma wszystkie dane wymagane do przywrócenia kopii zapasowej. Usługa Azure Backup umożliwia wykonywanie kopii zapasowych spójnych na poziomie aplikacji, które zapewniają, że do przywrócenia danych nie są wymagane dodatkowe poprawki. Przywracanie danych spójnych na poziomie aplikacji skraca czas przywracania, co pozwala szybko powrócić do stanu roboczego.
- **Krótkoterminowe i długoterminowe przechowywanie**: **Przechowywanie długoterminowe** — magazynów usług Recovery Services możesz użyć do krótko- i długoterminowego przechowywania danych. Platforma Azure nie ogranicza czasu przechowywania danych w magazynie usługi Recovery Services. Dane możesz przechowywać w magazynie tak długo, jak chcesz. Usługa Azure Backup ma limit 9999 punktów odzyskiwania dla każdego chronionego wystąpienia. Zobacz sekcję [Tworzenie kopii zapasowej i przechowywanie](backup-introduction-to-azure-backup.md#backup-and-retention) w tym artykule, aby uzyskać informacje o tym, jaki wpływ ten limit może mieć na Twoje potrzeby związane z kopiami zapasowymi.
- **Automatyczne zarządzanie magazynem** — hybrydowe środowiska często wymagają heterogenicznego magazynu — znajdującego się w części lokalnie, a w części w chmurze. W usłudze Azure Backup nie płaci się za korzystanie z lokalnych urządzeń magazynujących. Usługa Azure Backup automatycznie przydziela pojemność i zarządza magazynem kopii zapasowych, przy czym użytkownik płaci tylko za faktyczne użycie. Płatność tylko za faktyczne użycie oznacza, że płacisz tylko za pamięć, której używasz. Aby uzyskać więcej informacji, zobacz [artykuł Cennik platformy Azure](https://azure.microsoft.com/pricing/details/backup).
- **Wiele opcji magazynowania** — aspektem wysokiej dostępności jest replikacja magazynu. Usługa Azure Backup oferuje dwa typy replikacji: [magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy-lrs.md) i [magazyn geograficznie nadmiarowy](../storage/common/storage-redundancy-grs.md). Wybierz opcję magazynu kopii zapasowych na podstawie potrzeb:
    - Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych istnieją w tym samym regionie. Magazyn LRS to ekonomiczna opcja ochrony danych przed awariami sprzętu lokalnego.
    - Magazyn geograficznie nadmiarowy (GRS) jest ustawieniem domyślnym i zalecaną opcją replikacji. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych). Magazyn GRS kosztuje więcej niż LRS, ale zapewnia wyższy poziom trwałości danych nawet w przypadku wystąpienia awarii regionalnej.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Jaka jest różnica między usługami Azure Backup i Azure Site Recovery?

Usługi Azure Backup i Azure Site Recovery są częścią strategii zachowywania ciągłości działania i odzyskiwania po awarii (BCDR) w firmie. Strategia BCDR obejmuje dwa duże cele:

- Zabezpieczanie danych biznesowych i umożliwianie odzyskiwania ich po awarii.
- Kontynuowanie działania aplikacji i obciążeń podczas planowanych lub nieplanowanych przestojów.

Obie usługi oferują różne funkcje, które się uzupełniają.

- **Azure Site Recovery**: Usługa Site Recovery zapewnia rozwiązanie do odzyskiwania po awarii dla maszyn lokalnych i maszyn wirtualnych platformy Azure. Możesz replikować maszyny z lokalizacji podstawowej do dodatkowej. W przypadku awarii maszyny są przełączane w tryb failover do lokalizacji dodatkowej i można uzyskiwać do nich dostęp z tego miejsca. Gdy wszystko będzie ponownie działać normalnie, przełącz maszyny z powrotem, aby odzyskać je w lokacji głównej.
- **Azure Backup**: Usługa Azure Backup wykonuje kopie zapasowe danych z maszyn lokalnych i maszyn wirtualnych platformy Azure. Kopie zapasowe i odzyskiwanie danych można wykonywać na szczegółowym poziomie, łącznie z kopiami zapasowymi plików, folderów, stanu systemu maszyny i kopiami zapasowymi danych z uwzględnieniem aplikacji. Usługa Azure Backup obsługuje dane na bardziej szczegółowym poziomie niż usługa Site Recovery. Jeśli na przykład prezentacja na laptopie zostanie uszkodzona, możesz ją przywrócić za pomocą usługi Azure Backup. Aby zapewnić bezpieczeństwo i dostępność konfiguracji oraz danych maszyny wirtualnej, możesz użyć usługi Site Recovery.  

Użyj punktów w tabeli, aby łatwiej ustalić wymagania w zakresie strategii BCDR. 

**Cel** | **Szczegóły** | **Porównanie**
--- | --- | --- | --- |
**Wykonywanie kopii zapasowej/przechowywanie danych** | Dane kopii zapasowej można przechowywać przez wiele dni, miesięcy lub nawet lat, jeśli jest to konieczne pod względem zgodności. | Rozwiązania do wykonywania kopii zapasowych, takie jak Azure Backup, umożliwiają precyzyjne wybieranie danych, dla których chcesz wykonać kopię zapasową, oraz precyzyjne dostosowywanie zasad wykonywania kopii zapasowych i przechowywania.<br/><br/> Usługa Site Recovery nie oferuje takiej precyzji konfiguracji.
**Cel punktu odzyskiwania** | Dopuszczalna ilość utraconych danych, jeśli wymagane jest odzyskiwanie. | Kopie zapasowe mają bardziej zmienny cel punktu odzyskiwania.<br/><br/> Kopie zapasowe maszyn wirtualnych mają zwykle cel punktu odzyskiwania na poziomie jednego dnia, natomiast kopie zapasowe baz danych mają cel punktu odzyskiwania o wartości 15 minut.<br/><br/> Usługa Site Recovery ma ustawiony niski cel punktu odzyskiwania, ponieważ replikacja jest przeprowadzana ciągle lub często, aby zachować małą różnicę między źródłem i kopią repliki.
**Cel czasu odzyskiwania** |Ilość czasu potrzebnego do ukończenia odzyskiwania lub przywracania. | Ilość danych, które musi przetworzyć rozwiązanie kopii zapasowych, jest zwykle znacznie wyższa (ze względu na większą wartość RPO), a to prowadzi do większych wartości RTO. Na przykład przywrócenie danych z taśmy może potrwać kilka dni, zależnie od czasu potrzebnego do przetransportowania taśmy z oddalonej lokalizacji. | Rozwiązania do odzyskiwania po awarii, takie jak usługa Site Recovery, mają niski cel punktu odzyskiwania, ponieważ ciągła/częsta replikacja zazwyczaj oznacza, że cel jest bardziej zsynchronizowany ze źródłem. |

## <a name="what-backup-scenarios-are-supported"></a>Jakie scenariusze wykonywania kopii zapasowej są obsługiwane?

Usługa Azure Backup umożliwia wykonywanie kopii zapasowych maszyn lokalnych i maszyn wirtualnych platformy Azure.

**Maszyna** | **Scenariusz wykonywania kopii zapasowej**
--- | ---
**Maszyny lokalne (fizyczne/wirtualne)** |  Możesz wykonywać kopie zapasowe pojedynczych maszyn lokalnych.<br/><br/>Możesz wykonywać kopie zapasowe maszyn lokalnych, które są chronione przez program System Center Data Protection Manager (DPM)<br/><br/> Możesz wykonywać kopie zapasowe maszyn lokalnych chronionych przez usługę Microsoft Azure Backup Server (MABS)...
**Maszyny wirtualne platformy Azure** | Możesz wykonywać kopie zapasowe pojedynczych maszyn wirtualnych platformy Azure.<br/><br/> Możesz wykonywać kopie zapasowe maszyn wirtualnych platformy Azure chronionych przez program DPM lub usługę MABS.

### <a name="back-up-servers"></a>Wykonywanie kopii zapasowych serwerów

Możesz wykonywać kopie zapasowe lokalnych serwerów i obciążeń lub maszyn wirtualnych platformy Azure i ich obciążeń na serwerze kopii zapasowych, a następnie w magazynie usługi Recovery Services. 

**Serwer kopii zapasowych** | **Szczegóły**
--- | ---
**System Center Data Protection Manager (DPM)** | Usługa Azure Backup umożliwia wykonywanie kopii zapasowych danych chronionych przez program DPM:<br/><br/> — Program DPM może działać w środowisku lokalnym (fizycznym lub wirtualnym) albo na platformie Azure.<br/><br/> — Możesz chronić różne typy danych na maszynach lokalnych i maszynach wirtualnych platformy Azure przez wykonywanie kopii zapasowych danych na serwerze programu DPM.<br/><br/> Z kolei kopię zapasową serwera programu DPM można wykonać w magazynie usługi Recovery Services za pomocą usługi Azure Backup.<br/><br/> Serwer programu DPM i chronione przez niego maszyny muszą być w tej samej sieci. Maszyny lokalne mogą być chronione tylko przez lokalny serwer programu DPM. Podobnie maszyny wirtualne platformy Azure mogą być chronione tylko przez program DPM działający na platformie Azure.
**Microsoft Azure Backup Server (MABS)** | Usługa Azure Backup umożliwia wykonywanie kopii zapasowych danych chronionych przez usługę MABS:<br/><br/> — Usługa MABS może działać w środowisku lokalnym (fizycznym lub wirtualnym) albo na platformie Azure.<br/><br/> — Możesz chronić różne typy danych na maszynach lokalnych i maszynach wirtualnych platformy Azure przez wykonywanie kopii zapasowych danych w usłudze MABS.<br/><br/> Z kolei kopię zapasową usługi MABS można wykonać w magazynie usługi Recovery Services za pomocą usługi Azure Backup.<br/><br/> — Usługa MABS oferuje funkcje podobne do funkcji programu DPM, ale nie umożliwia wykonywania kopii zapasowych na taśmie za pomocą usługi MABS. Usługa MABS nie wymaga licencji programu System Center.<br/><br/> Tak jak w przypadku programu DPM, maszyny lokalne mogą być chronione tylko przez lokalną usługę MABS. Maszyny wirtualne platformy Azure mogą być chronione tylko przez usługę MABS na platformie Azure.

Zalety wykonywania kopii zapasowej najpierw w programie DPM/usłudze MABS, a następnie w magazynie są następujące:

- Kopie zapasowe w programie DPM/usłudze MABS uwzględniają aplikacje i są zoptymalizowane pod kątem typowych aplikacji, takich jak SQL Server, Exchange i SharePoint, a także umożliwiają wykonywanie kopii zapasowych plików/folderów/woluminów i kopii zapasowych stanu maszyny (bez systemu operacyjnego, stan systemu).
- Nie trzeba instalować agenta usługi Azure Backup na każdej maszynie, dla której chcesz wykonać kopię zapasową. Na każdej maszynie jest uruchomiony agent ochrony programu DPM/usługi MABS, a agent usługi Microsoft Azure Recovery Services w usłudze Azure Backup działa tylko na serwerze programu DPM/w usłudze MABS.
- Zapewnia to większą elastyczność i szczegółowe opcje planowania wykonywania kopii zapasowych.
- Możesz zarządzać kopiami zapasowymi wielu maszyn zawartymi w grupach ochrony za pomocą jednej konsoli. Jest to szczególnie przydatne w sytuacji, gdy aplikacje są rozmieszczone warstwowo na wielu maszynach i chcesz wykonać ich kopię zapasową razem.

## <a name="what-can-be-backed-up"></a>Dla jakich elementów można wykonywać kopie zapasowe?

**Maszyna** | **Serwer kopii zapasowych** | **Wykonywanie kopii zapasowej**
--- | --- | ---
Lokalne maszyny wirtualne z systemem Windows | Bez kopii zapasowej w programie DPM lub usłudze MABS | Wykonywanie kopii zapasowej plików, folderów i stanu systemu.
Maszyny wirtualne platformy Azure (Windows i Linux) | Bez kopii zapasowej w programie DPM lub usłudze MABS | Wykonywanie kopii zapasowej plików, folderów i stanu systemu.<br/><br/> Kopie zapasowe uwzględniają aplikacje w przypadku maszyn z systemem Windows oraz uwzględniają pliki w przypadku maszyn z systemem Linux.
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure | Chronione przez program DPM | Wykonywanie kopii zapasowych wszystkich elementów chronionych przez program DPM, w tym plików/folderów/udziałów/woluminów i danych aplikacji. [Dowiedz się,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) dla jakich elementów można wykonywać kopie zapasowe w programie DPM.
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure | Chronione przez usługę MABS | Wykonywanie kopii zapasowych wszystkich elementów chronionych przez usługę MABS, w tym plików/folderów/udziałów/woluminów i danych aplikacji. [Dowiedz się,](backup-mabs-protection-matrix.md) dla jakich elementów można wykonywać kopie zapasowe w usłudze MABS.

## <a name="what-backup-agents-do-i-need"></a>Jakich agentów kopii zapasowych potrzebuję?
**Scenariusz** | **Agent** | **Szczegóły**
--- | --- | ---
Maszyny lokalne (bez serwera kopii zapasowych) | Agent usługi Microsoft Azure Recovery Services (MARS) działa na maszynie z systemem Windows. | Pobierz i zainstaluj agenta usługi MARS podczas wdrażania usługi Azure Backup.<br/><br/> Obsługa tylko maszyn z systemem Windows.
Maszyny wirtualne platformy Azure (bez serwera kopii zapasowych) | Nie jest wymagany agent jawny. Rozszerzenie maszyny wirtualnej platformy Azure do wykonywania kopii zapasowych działa na maszynie wirtualnej platformy Azure. | Rozszerzenie jest zainstalowane podczas uruchamiania pierwszej kopii zapasowej maszyny wirtualnej platformy Azure.<br/><br/> Obsługa systemów Windows i Linux.
Maszyny lokalne/maszyny wirtualne platformy Azure chronione przez program DPM. | Agent usługi MARS działa na serwerze programu DPM. | Nie musisz mieć agenta usługi MARS na poszczególnych maszynach.<br/><br/> Podczas wdrażania programu DPM agent ochrony programu DPM jest zainstalowany na każdej chronionej maszynie. 
Wykonywanie kopii zapasowych maszyn lokalnych i maszyn wirtualnych platformy Azure chronionych przez usługę MABS | Agent usługi MARS działa w usłudze MABS. | Nie musisz mieć agenta usługi MARS na poszczególnych maszynach.<br/><br/> Podczas wdrażania usługi MABS agent ochrony usługi MABS jest zainstalowany na każdej chronionej maszynie. 


## <a name="which-backup-agent-should-i-use"></a>Którego agenta kopii zapasowej należy używać?

**Tworzenie kopii zapasowych** | **Rozwiązanie** | **Ograniczenie**
--- | --- | ---
Chcę wykonać kopie zapasowe maszyn lokalnych z systemem Windows. Maszyny nie są chronione przez program DPM lub usługę MABS | Zainstaluj agenta usługi MARS na maszynie. | Możesz wykonywać kopie zapasowe plików, folderów i stanu systemu na platformie Azure. Kopie zapasowe nie uwzględniają aplikacji.
Chcę wykonać kopie zapasowe maszyn lokalnych z systemem Linux. Maszyny nie są chronione przez program DPM lub usługę MABS. | Musisz wdrożyć program DPM lub usługę MABS, aby móc wykonać kopię zapasową na platformie Azure.
Chcę wykonać kopie zapasowe aplikacji na maszynach lokalnych z systemem Windows | Aby można było wykonywać kopie zapasowe z uwzględnieniem aplikacji, maszyny z systemem Windows muszą być chronione przez program DPM lub usługę MABS.
Chcę wykonać kopie zapasowe maszyn wirtualnych platformy Azure | Wykonaj kopię zapasową za pomocą usługi Azure Backup. Rozszerzenie kopii zapasowej zostanie automatycznie skonfigurowane na maszynie wirtualnej platformy Azure z systemem Windows lub Linux. | Jest wykonywana kopia zapasowa dysków maszyny wirtualnej.<br/><br/> W przypadku maszyn wirtualnych z systemem Windows kopia zapasowa jest spójna na poziomie aplikacji. W przypadku systemu Linux kopia zapasowa jest spójna na poziomie plików. Aby zachować spójność na poziomie aplikacji, musisz skonfigurować kopię zapasową za pomocą skryptów niestandardowych.
Chcę wykonać kopie zapasowe maszyn wirtualnych platformy Azure z zachowaniem poziomu szczegółowości i elastyczności ustawień kopii zapasowych i odzyskiwania | Chroń maszyny wirtualne platformy Azure za pomocą programu DPM lub usługi MABS działające na platformie Azure, aby uzyskać dodatkową elastyczność w przypadku planowania wykonywania kopii zapasowych oraz pełną elastyczność podczas ochrony i przywracania plików, folderów, woluminów, aplikacji i stanu systemu.


## <a name="next-steps"></a>Następne kroki

- [Przejrzyj](backup-architecture.md) architekturę i składniki w różnych scenariuszach wykonywania kopii zapasowych.
- [Sprawdź](backup-support-matrix.md) obsługiwane funkcje i ustawienia kopii zapasowych.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

