---
title: Usługa Azure Migrate — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Migrate adresów
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: snehaa
ms.openlocfilehash: 2efa450b6b0cfa299370df3941224f4f64e91b4b
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230768"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Usługa Azure Migrate — często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Migrate. Jeśli masz dodatkowe pytania po przeczytaniu tego artykułu, opublikuj je na [forum usługi Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Usługa Azure Migrate obsługuje oceny tylko obciążeń oprogramowania VMware?

Tak, usługa Azure Migrate aktualnie obsługuje tylko oceny obciążeń oprogramowania VMware. W przyszłości zostanie włączona obsługa funkcji Hyper-V i serwerów fizycznych.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Czy usługa Azure Migrate potrzebuje programu vCenter Server, aby dowiedzieć się, w środowisku programu VMware?

Tak, usługa Azure Migrate wymaga programu vCenter Server, aby dowiedzieć się, w środowisku VMware. Nie obsługuje odnajdywanie hostów ESXi, które nie są zarządzane przez program vCenter Server.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Czym różni się usługa Azure Migrate z usługi Azure Site Recovery?

Usługa Azure Migrate jest usługą ocenę, która ułatwia odnajdywanie obciążeń lokalnych i Zaplanuj migrację na platformę Azure. [Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), oraz że rozwiązanie odzyskiwania po awarii, pomaga w migrowaniu obciążeń lokalnych do maszyn wirtualnych IaaS na platformie Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Jaka jest różnica między za pomocą usługi Azure Migrate dla ocen i narzędzi?

[Usługa Azure Migrate](migrate-overview.md) zapewnia ocenę migracji specjalnie, aby ułatwić przygotowanie do migracji i ocena obciążeń lokalnych na platformę Azure. [Microsoft Assessment i zestaw narzędzi do planowania (MAP)](https://www.microsoft.com/en-us/download/details.aspx?id=7826) ma inne funkcje. Na przykład migracja planowania dla nowszej wersji systemu Windows klienckich i serwerowych systemów operacyjnych, oprogramowania użycia śledzenia itp. W tych scenariuszach nadal używać narzędzi.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Czym różni się usługa Azure Migrate z Planisty wdrożenia usługi Azure Site Recovery?

Usługa Azure Migrate jest migracji za pomocą narzędzia do planowania i planista wdrażania usługi Azure Site Recovery jest Odzyskiwanie po awarii (DR), narzędzia do planowania.

**Migracja z programu VMware na platformę Azure**: Aby przeprowadzić migrację lokalnych obciążeń na platformę Azure, używać usługi Azure Migrate dotyczące planowania migracji. Usługa Azure Migrate ocenia obciążenia lokalne i zapewnia wskazówki, szczegółowe informacje i mechanizmy, aby pomóc w migracji do platformy Azure. Jeśli wszystko jest gotowe dla planu migracji, można użyć usług, takich jak Azure Site Recovery i Azure Database Migration Service, przeprowadzić migrację maszyn na platformę Azure.

**Migracja z funkcji Hyper-V na platformę Azure**: Usługa Azure Migrate aktualnie obsługuje tylko oceny maszyn wirtualnych VMware do migracji na platformę Azure. Obsługa funkcji Hyper-V znajduje się w planie dla usługi Azure Migrate. W międzyczasie można użyć Planisty wdrożenia usługi Site Recovery. Po włączeniu obsługi funkcji Hyper-V w usłudze Azure Migrate służy usługa Azure Migrate dotyczące planowania migracji obciążeń funkcji Hyper-V.

**Odzyskiwanie po awarii z programu VMware/funkcji Hyper-V na platformę Azure**: Jeśli planujesz wykonać odzyskiwanie po awarii (DR) na platformie Azure przy użyciu usługi Azure Site Recovery (Usługa Site Recovery), na użytek planista wdrażania usługi Site Recovery planowania odzyskiwania po awarii. Planista wdrażania usługi Site Recovery wykonuje głęboką, specyficzne dla usługi ASR oceny środowiska lokalnego. Zapewnia zaleceń, które są wymagane przez usługę Site Recovery dla pomyślnego operacji odzyskiwania po awarii, takich jak replikacja, failover maszyn wirtualnych.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Które lokalizacje geograficzne platformy Azure są obsługiwane przez usługę Azure Migrate?

Usługa Azure Migrate obsługuje obecnie Europa, USA i Azure dla instytucji rządowych jako lokalizacje geograficzne projektu. Mimo, że projekty migracji można tworzyć tylko w tych lokalizacjach geograficznych, można nadal oceniać gotowość maszyn w celu [wielu lokalizacjach docelowych](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Geograficzne projektu jest używana wyłącznie do przechowywania metadanych wykrytych.

**Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
--- | ---
Azure Government | Administracja USA — Wirginia
Azja | Azja Południowo-Wschodnia
Europa | Europa Północna lub Europa Zachodnia
Stany Zjednoczone | Wschodnie stany USA z Zachodniego środkowe stany USA

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Jak lokacją lokalną łączyć się z usługi Azure Migrate?

Połączenie może być za pośrednictwem Internetu lub korzystać z usługi ExpressRoute w publicznej komunikacji równorzędnej.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Czy można zabezpieczyć maszynę Wirtualną z szablonu OVA?

Tak długo, jak komunikacji i reguł zapory wymagane dla usługi Azure Migrate urządzenia do pracy pozostało jest dodatkowych składników (na przykład oprogramowanie antywirusowe) można dodać do szablonu OVA.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Utrwalanie urządzenia usługi Azure Migrate, jakie są zalecane wykluczenia programu antywirusowego (pliki audio i wideo)?

Należy wykluczyć poniższe foldery w urządzenia do skanowania antywirusowego:

- Folder, który zawiera pliki binarne usługi migracji platformy Azure. Wyklucz wszystkie podfoldery.
  %ProgramFiles%\ProfilerService  
- Usługa Azure Migrate aplikacji sieci Web. Wyklucz wszystkie podfoldery.
  %SystemDrive%\inetpub\wwwroot
- Lokalnej pamięci podręcznej dla plików dziennika i bazy danych. Usługa Azure migrate usługa wymaga RW dostęp do tego folderu.
  %SYSTEMDRIVE%\Profiler

## <a name="discovery"></a>Odnajdowanie

### <a name="what-data-is-collected-by-azure-migrate"></a>Jakie dane są zbierane przez usługę Azure Migrate?

Usługa Azure Migrate obsługuje dwa rodzaje odnajdywania: oparte na urządzeniach i oparte na agentach.
Odnajdywanie oparte na urządzeniu zbiera metadane dotyczące lokalnych maszyn wirtualnych, pełną listę metadane zbierane przez urządzenie znajduje się poniżej:

**Dane konfiguracji maszyny wirtualnej**
- Nazwa wyświetlana maszyny Wirtualnej (na klawiaturze vCenter)
- Ścieżka magazynu maszyny Wirtualnej (host/klaster/folder w programie vCenter)
- Adres IP
- Adres MAC
- System operacyjny
- Liczba rdzeni, dysków, kart sieciowych
- Rozmiar pamięci, rozmiary dysków

**Dane dotyczące wydajności maszyny wirtualnej**
- Użycie procesora CPU
- Użycie pamięci
- Dla każdego dysku, podłączonego do maszyny Wirtualnej:
  - Przepływność odczytu z dysku
  - Przepływność zapisu na dysku
  - Odczyt z dysku operacji na sekundę
  - Dysk zapisuje operacji na sekundę
- Dla każdej karty sieciowej jest dołączony do maszyny Wirtualnej:
  - Sieć — wejście
  - Sieć — wyjście

Opcja odnajdywania opartego na agentach uzupełnia odnajdywanie oparte na urządzeniach i pomaga klientom [wizualizować zależności](how-to-create-group-machine-dependencies.md) lokalnych maszyn wirtualnych. Agenci zależności zbierają informacje, takie jak nazwa FQDN, system operacyjny, adres IP, adres MAC, procesy uruchomione na maszynie wirtualnej oraz przychodzące/wychodzące połączenia TCP na maszynie wirtualnej. Odnajdywanie oparte na agentach jest opcjonalny, a użytkownik może nie zainstalować agentów, jeśli nie chcesz wizualizacja zależności maszyn wirtualnych.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Będzie mieć żadnego wpływu wydajności na przeanalizowany środowisko hosta ESXi?

W przypadku właściwości [jednej metody odnajdywania czasu](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods), aby zbierać dane dotyczące wydajności, poziomu statystyk na serwerze vCenter, musi być równa 3. Ustawienie tego poziomu będzie zbierać dużej ilości danych, które powinny być przechowywane w bazie danych serwera vCenter na rozwiązywanie problemów. Ten sposób może powodować pewne problemy z wydajnością na serwerze vCenter. Może być niewielki wpływ na hoście ESXi.

Firma Microsoft wprowadza ciągłe profilowania danych dotyczących wydajności (czyli w wersji zapoznawczej). Za pomocą ciągłego profilowania nie ma już potrzeby zmieniania poziomu statystyk serwera, aby uzyskać ocenę na podstawie wydajności programu vCenter. Urządzenie modułu zbierającego będzie teraz profilować maszyn lokalnych do mierzenia dane dotyczące wydajności maszyn wirtualnych. Będzie to miało bliski zeru negatywny wpływ na wydajność, na hostach ESXi, a także w programie vcenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie jest zebranych danych przechowywanych i na jak długo?

Dane zebrane przez urządzenie modułu zbierającego znajduje się w lokalizacji platformy Azure, które należy określić podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w ramach subskrypcji firmy Microsoft i zostanie usunięta, gdy użytkownik usuwa projekt usługi Azure Migrate.

Dla wizualizacji zależności po zainstalowaniu agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym usługi Log Analytics utworzonych w subskrypcji użytkownika. Usuwanie danych odbywa się podczas usuwania obszaru roboczego usługi Log Analytics w ramach subskrypcji. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i przesyłanych?

Tak, zebrane dane są szyfrowane zarówno magazynowanych i przesyłanych. Metadane zbierane przez urządzenie są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem sieci internet za pośrednictwem protokołu https. Zebrane metadane są przechowywane w [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) i [usługi Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft i jest szyfrowane, gdy.

Dane zbierane przez agentów zależności jest również zaszyfrowane na tranzyt (https bezpiecznej channel) i są przechowywane w obszarze roboczym usługi Log Analytics w subskrypcji użytkownika. Jest ona również są szyfrowane w stanie spoczynku.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak moduł zbierający komunikacji przy użyciu programu vCenter Server i usługi Azure Migrate?

Urządzenie modułu zbierającego łączy z programem vCenter Server (port 443) przy użyciu poświadczeń dostarczonych przez użytkownika na urządzeniu. Wykonuje kwerendę w programie vCenter Server przy użyciu interfejs PowerCLI programu VMware można zebrać metadanych informacje o maszynach wirtualnych zarządzanych przez program vCenter Server. Zbiera dane konfiguracji obu maszyn wirtualnych (rdzeni, pamięć, dyski, karta sieciowa itp.) oraz historii wydajności każdej maszyny wirtualnej dla ostatniego miesiąca z programu vCenter Server. Zebrane metadane są następnie wysyłane do usługi Azure Migrate (za pośrednictwem sieci internet za pośrednictwem protokołu https) dla oceny. [Dowiedz się więcej](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Czy można połączyć tego samego urządzenia modułu zbierającego, aby wiele serwerów vCenter?

Tak, urządzenie jeden moduł zbierający może służyć do wykrywania vCenter wielu serwerów, ale nie jednocześnie. Musisz uruchomić odnajdywania, jeden po drugim.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Czy szablon OVA używany przez usługę Site Recovery jest zintegrowana z usługą OVA używane przez usługę Azure Migrate?

Obecnie nie ma żadnych integracji. . Szablon OVA w usłudze Site Recovery jest używany do konfigurowania serwera konfiguracji Usługa Site Recovery w celu replikacji serwera fizycznego/VMware, maszyny Wirtualnej. . OVA używane przez usługę Azure Migrate służy do odnalezienia maszyn wirtualnych VMware, zarządzane przez serwer vCenter, na potrzeby oceny migracji.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Czy mogę zmienić mój rozmiar maszyny. Czy można ponownie uruchomić ocenę?

Jeśli zmienisz ustawienia na maszynie Wirtualnej, którą chcesz ocenić, wyzwalacz Dowiedz się ponownie przy użyciu urządzenia modułu zbierającego. W urządzeniu, należy użyć **ponownie uruchom zbieranie** opcję, aby to zrobić. Po zakończeniu zbierania wybierz opcję **Oblicz ponownie** dla oceny w portalu, aby pobrać zaktualizowane wyniki oceny.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak można odnaleźć środowiska z wieloma dzierżawami w usłudze Azure Migrate?

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywanie maszyn wirtualnych z jednej dzierżawy w innej dzierżawie subskrypcji, można użyć pola zakresu w urządzenia modułu zbierającego do określania zakresu odnajdywania. Jeśli dzierżawy współużytkują hostów, Utwórz poświadczenie, które ma dostęp tylko do odczytu do tylko maszyny wirtualne należące do określonej dzierżawy, a następnie korzystać z poświadczenia urządzenia modułu zbierającego i określ zakres jako hosta w celu odnajdywania. Alternatywnie można także utworzyć foldery, w programie vCenter Server (Załóżmy, że folder1 dla tenant1 folder2 dla tenant2), w obszarze udostępnionego hosta, Przenieś maszyny wirtualne dla tenant1 do folder1 i tenant2 do folder2 i zakres odnajdywania w module zbierającym w związku z tym określając odpowiedni folder.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Ile maszyn wirtualnych można znaleźć w projekcie migracji?

Może odnajdywać 1500 maszyn wirtualnych w projekcie migracji. Jeśli masz więcej maszyn w środowisku lokalnych [więcej](how-to-scale-assessment.md) o jak odkryjesz dużym środowisku, w usłudze Azure Migrate.


## <a name="assessment"></a>Ocena

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Usługa Azure Migrate wsparcia podmiotu trzeciego podstawie Enterprise Agreement (EA) kosztuje szacowania?

Usługa Azure Migrate nie obsługuje obecnie Szacowanie kosztów dla [oferty z umową Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Obejście polega na określić płatność za rzeczywiste użycie jako oferty i ręcznie określania procent rabatu (dotyczy subskrypcji) w polu "Zniżka" we właściwościach oceny.

  ![Rabat](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaka jest różnica między ustalania rozmiaru jako — w środowisku lokalnym i ustalania rozmiaru na podstawie wydajności?

Po określeniu kryterium ustalania rozmiaru jako jako on-premises zmiany rozmiaru, usługa Azure Migrate nie należy wziąć pod uwagę dane wydajności maszyn wirtualnych i rozmiarach maszyn wirtualnych na podstawie konfiguracji w środowisku lokalnym. Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, zmiany rozmiaru odbywa się na podstawie danych użycia. Na przykład, jeśli istnieje lokalna maszyna wirtualna z 4 rdzenie i 8 GB pamięci RAM z 50% wykorzystania Procesora i wykorzystania pamięci 50%. Jeśli kryterium ustalania rozmiaru jest rozmiaru jednostki SKU maszyny Wirtualnej platformy Azure z 4 rdzenie lokalne i 8GB pamięci RAM zaleca się, jednak jeśli kryterium ustalania rozmiaru na podstawie wydajności jako jednostki SKU maszyny Wirtualnej 2 rdzeni i 4 GB mogłoby być zaleca się jako procent wykorzystania jest uznawana za podczas rekomendowanie rozmiar. Podobnie dysków, rozmiaru dysku zależy od dwóch właściwości oceny — zmiany rozmiaru typu kryterium i magazynu. Jeśli kryterium ustalania rozmiaru jest oparte na wydajności i magazynu jest uruchomiana automatycznie, wartości operacje We/Wy i przepływność dysku są traktowane jako do identyfikowania typ dysku docelowego (standardowa / Premium). Jeśli kryterium ustalania rozmiaru jest oparte na wydajności i typ magazynu jest premium, zaleca się dysku w warstwie premium, dysku w warstwie premium, jednostki SKU na platformie Azure jest wybierane na podstawie rozmiaru dysku w środowisku lokalnym. Ta sama logika jest używany do dysku zmiany rozmiaru, jeśli kryterium ustalania rozmiaru jest lokalne ustalanie rozmiaru i typu magazynu to standardowa lub premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ wydajności historii i percentyl użycia ma na zaleceń dotyczących rozmiarów?

Te właściwości są tylko odpowiednie dla rozmiaru na podstawie wydajności. Usługa Azure Migrate umożliwia zbieranie informacji o historii wydajności maszyn lokalnych i używa ich do zaleca się typu dysk i rozmiar maszyny Wirtualnej na platformie Azure. Urządzenie modułu zbierającego stale profilów w środowisku lokalnym na potrzeby zbierania danych użycia w czasie rzeczywistym, co 20 sekund. Urządzenie zbiera przykłady 20 sekund i tworzy jeden punkt danych co 15 minut. Aby utworzyć jeden punkt danych, urządzenia szczytowa wartość wybierana jest opcja wszystkie przykłady 20 sekund i wysyła je do platformy Azure. Po utworzeniu oceny na platformie Azure, w oparciu o czas trwania wydajności i wartość percentylu historii wydajności, usługa Azure Migrate oblicza wartość efektywne wykorzystanie i używa go na potrzeby zmiany rozmiaru. Na przykład, jeśli zostały ustawione czas trwania wydajności za 1 dzień i percentyl wartości 95. percentyl, usługę Azure migrate przykładowe 15 min punktów wysyłane przez moduł zbierający dla ostatniego dnia, posortowane w kolejności rosnącej, a następnie wybiera wartość 95. percentyla jako skuteczne ut ilization. Wartość 95. percentyla gwarantuje, że ignorujesz jakiekolwiek elementy odstające, które mogą występować w przypadku wybrania 99. percentylu. Jeśli chcesz wybrać szczytowe użycie w okresie, a nie chcesz przeoczyć jakiekolwiek elementy odstające, należy wybrać 99. percentylu.

## <a name="dependency-visualization"></a>Wizualizacja zależności

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

### <a name="what-is-dependency-visualization"></a>Co to jest wizualizacji zależności?

Wizualizacja zależności umożliwia ocenę grupy maszyn wirtualnych do migracji z większą pewnością przez krzyżowe sprawdzanie zależności maszyny, przed uruchomieniem oceny. Wizualizacja zależności pomaga upewnić się, że nic nie jest pozostawione, unikanie nieoczekiwanych awarii podczas migracji na platformę Azure. Usługa Azure Migrate korzysta z rozwiązania mapy usługi w usłudze Log Analytics, aby umożliwić wizualizacji zależności.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Czy muszę płacić za używanie funkcji wizualizacji zależności?

Nie. Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Czy należy zainstalować żadnych wizualizacji zależności?

Aby użyć wizualizacji zależności, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który ma zostać oceniona.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi być zainstalowany na każdym komputerze.
- [Agenta zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musi być zainstalowany na każdym komputerze.
- Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować bramę usługi Log Analytics na nich.

Nie potrzebujesz tych agentów na komputerach, które chcesz ocenić, jeśli nie używasz wizualizacji zależności.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy można używać istniejącego obszaru roboczego w celu wizualizacji zależności?

Tak, usługa Azure Migrate umożliwia teraz dołączenie istniejącego obszaru roboczego do projektu migracji i wykorzystaj je do wizualizacji zależności. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie. Nie można wyeksportować wizualizacji zależności. Jednakże, ponieważ usługa Azure Migrate rozwiązania Service map dla wizualizacji zależności, możesz użyć [interfejsów API REST usługi Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) można pobrać zależności w formacie json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak można zautomatyzować instalację programu Microsoft Monitoring Agent (MMA) i agenta zależności

[W tym miejscu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) to skrypt, który służy do instalowania agenta zależności. Dla programu MMA [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) jest dostępna w witrynie TechNet, który można wykorzystać skrypt.

Oprócz skryptów, możesz także korzystać z narzędzia wdrażania, takich jak System Center Configuration Manager (SCCM), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) itp., aby wdrożyć agentów.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Co to są systemów operacyjnych obsługiwanych przez MMA?

Lista systemów operacyjnych Windows obsługiwanych przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Lista systemów operacyjnych Linux obsługiwane przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Co to są systemy operacyjne obsługiwane przez agenta zależności?

Lista systemów operacyjnych Windows obsługiwanych przez agenta zależności jest [tutaj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Lista systemów operacyjnych Linux obsługiwane przez agenta zależności jest [tutaj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Aby uzyskać więcej niż jedna godzina, czas trwania można zwizualizować zależności w usłudze Azure Migrate?
Nie, usługa Azure Migrate umożliwia wizualizowanie zależności maksymalnie jedną godzinę czasu trwania. Usługa Azure Migrate umożliwia wróć do określonej daty w historii dla maksymalnie ostatni miesiąc, ale maksymalny czas trwania, dla którego można wizualizować zależności jest maksymalnie 1 godzinę. Na przykład można użyć funkcji okres czasu z mapy zależności, aby wyświetlić zależności dla wczoraj, ale tylko wtedy można wyświetlić okna jedną godzinę.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Wizualizacji zależności jest obsługiwane dla grup za pomocą ponad 10 maszyn wirtualnych?
Możesz [wizualizacja zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ma się do 10 maszyn wirtualnych, jeśli istnieje grupa z ponad 10 maszyn wirtualnych, zalecamy wizualizowanie zależności i podzielić grupy w mniejszym grupom.


## <a name="next-steps"></a>Kolejne kroki

- Odczyt [usługi Azure Migrate — Przegląd](migrate-overview.md)
- Dowiedz się, jak [odnajdywanie i ocenianie](tutorial-assessment-vmware.md) środowisku programu VMware
