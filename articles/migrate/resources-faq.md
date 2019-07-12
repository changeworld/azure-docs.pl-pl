---
title: Usługa Azure Migrate — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Migrate adresów
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807324"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Usługa Azure Migrate — często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Migrate. Jeśli masz dodatkowe pytania po przeczytaniu tego artykułu, opublikuj je na [forum usługi Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Które lokalizacje geograficzne platformy Azure są obsługiwane przez usługę Azure Migrate?
Usługa Azure Migrate obsługuje obecnie szereg różnych obszarach geograficznych, w których można utworzyć projekt usługi Azure Migrate. Mimo że projekty można tworzyć tylko w tych lokalizacjach geograficznych, możesz ocenić swoje maszyn do innych lokalizacji docelowej. Geograficzne projektu jest używana wyłącznie do przechowywania metadanych wykrytych.

**Lokalizacja geograficzna** | **lokalizacji magazynu metadanych** Azure dla instytucji rządowych | US Gov Wirginia Azja | Azja południowo-wschodnia i Azja Wschodnia, Europa | Południowo-Europa lub Europa Zachodnia Zjednoczonego Królestwa | Południowe Zjednoczone Królestwo lub Zjednoczone Królestwo zachodnie Stany Zjednoczone | Środkowe stany USA i zachodnie stany USA 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Czym różni się usługa Azure Migrate z usługi Azure Site Recovery?

Usługa Azure Migrate udostępnia narzędzia, które ułatwiają odnajdowanie, ocenianie i Migrowanie maszyn i obciążeń na platformę Azure. [Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) jest rozwiązanie odzyskiwania po awarii. Obie te usługi udostępniania niektórych składników.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Urządzenie Azure Migrate (VMware/serwerów fizycznych)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak usługa Azure Migrate urządzenia łączenie z platformą Azure?

Połączenie może być za pośrednictwem Internetu lub za pomocą usługi ExpressRoute publicznej komunikacji równorzędnej.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Jakie wymagania łączności sieci są wymagane na potrzeby oceny Server migracji platformy Azure i migracji

Zapoznaj się z adresów URL i portów wymaganych przez usługę Azure Migrate do komunikowania się z platformą Azure, [VMWare](migrate-support-matrix-vmware.md) i [funkcji Hyper-V](migrate-support-matrix-hyper-v.md) obsługuje macierzy.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>Czy można zabezpieczyć urządzenie maszyny Wirtualnej VMware, które można skonfigurować za pomocą szablonu OVA?

Tak długo, jak komunikacja i reguł zapory wymagane dla urządzenia usługi Azure Migrate ponownie po lewej stronie, co jest dodatkowe składniki (na przykład oprogramowanie antywirusowe) można dodać do szablonu OVA.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Utrwalanie urządzenia usługi Azure Migrate, jakie są zalecane wykluczenia programu antywirusowego (pliki audio i wideo)?

Należy wyłączyć następujące foldery ze skanowania na urządzeniu:

- Folder zawierający pliki binarne dla usługi Azure Migrate. Wyklucz wszystkie podfoldery.
- %ProgramFiles%\ProfilerService  
- Usługa Azure Migrate aplikacji sieci Web. Wyklucz wszystkie podfoldery.
- %SystemDrive%\inetpub\wwwroot
- Lokalnej pamięci podręcznej dla plików dziennika i bazy danych. Usługa Azure Migrate potrzebuje odczyt/zapis dostęp do tego folderu.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Jakie dane są zbierane przez usługę Azure Migrate?

Urządzenie Azure Migrate umożliwia zbieranie informacji o metadanych dla lokalnych maszyn wirtualnych, w tym:

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

Dodanie tn, jeśli wdrożysz mapowanie zależności agenci mapowania zależności zbierać informacje, które zawiera nazwę FQDN komputera, systemu operacyjnego, adres IP, adres MAC procesów uruchomionych wewnątrz maszyny Wirtualnej i wychodzących/przychodzących połączeń TCP dla maszyny Wirtualnej. W wyniku tego odnajdywania jest opcjonalna używana tylko po włączeniu mapowania zależności do odnajdywania.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>W środowisku przeanalizowany hosta ESXi jest wpływ na wydajność?

Za pomocą ciągłego profilowania dane dotyczące wydajności, profile urządzenia w usłudze Azure Migrate maszyn lokalnych do pomiaru danych wydajności maszyny Wirtualnej. Ma prawie zero negatywny wpływ na wydajność, na hostach ESXi, a także w programie vcenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie jest zebranych danych przechowywanych i na jak długo?

Dane zebrane przez usługę Azure Migrate urządzenie znajduje się w lokalizacji platformy Azure, która została określona podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w ramach subskrypcji firmy Microsoft i zostanie usunięta po usunięciu projektu Azure Migrate.

Dla wizualizacji zależności po zainstalowaniu agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym usługi Log Analytics utworzonych w subskrypcji platformy Azure. Usuwanie danych odbywa się podczas usuwania obszaru roboczego usługi Log Analytics w ramach subskrypcji. [Dowiedz się więcej](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>Co to jest ilość danych przekazanych przez usługę Azure Migrate podczas profilowania ciągły?

Ilość danych wysyłanych do usługi Azure Migrate zależy od kilku parametrów. Aby dać liczbą orientacyjny, projekt usługi Azure Migrate z 10 maszynami, (każdy z jednego dysku i jedną kartą Sieciową), wysyła około 50 MB dziennie. Jest to wartość przybliżona, które zmiany na podstawie liczby punktów danych dla kart sieciowych i dyskach (dane wysyłane jest inny niż liniowy Jeśli zwiększyć liczbę maszyn, kart sieciowych lub dysków).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Jest w stanie spoczynku i przesyłane dane zaszyfrowane?

Tak, dla obu. Metadane są bezpiecznie wysyłane do usługi Azure Migrate w Internecie za pośrednictwem protokołu https. Metadane są przechowywane w [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest), a następnie w [usługi Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft i szyfrowania magazynowanych.

Dane zbierane przez agentów zależności również są zaszyfrowane na tranzyt (bezpieczne HTTPS) i są przechowywane w obszarze roboczym usługi Log Analytics w ramach subskrypcji użytkownika. Jest to również szyfrowania magazynowanych.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak urządzenia usługi Azure Migrate komunikacji przy użyciu programu vCenter Server i usługi Azure Migrate?

Urządzenie łączy z programem vCenter Server (port 443) przy użyciu poświadczeń dostarczonych podczas konfigurowania urządzenia. Wykonuje kwerendę w programie vCenter Server przy użyciu interfejs PowerCLI programu VMware, można zebrać metadanych informacje o maszynach wirtualnych zarządzanych przez program vCenter Server. Zbiera informacje o maszynach wirtualnych (rdzeni, pamięć, dyski, karta sieciowa itp.), zarówno dla danych konfiguracji oraz historii wydajności każdej maszyny wirtualnej w ostatnim miesiącu. Zebrane metadane są następnie wysyłane do migracji serwera oceny Azure (za pośrednictwem sieci internet za pośrednictwem protokołu HTTPS) dla oceny. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Czy można połączyć tego samego urządzenia, aby wiele serwerów vCenter?

Tak, z jednego urządzenia usługi Azure Migrate może służyć do odnajdywania vCenter wielu serwerów, ale nie jednocześnie. Musisz uruchomić odnajdywanie, jeden po drugim.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Czy szablon OVA używany przez usługę Site Recovery jest zintegrowana z usługą OVA używane przez usługę Azure Migrate?

Obecnie nie ma żadnych integracji. . Szablon OVA w usłudze Site Recovery jest używany do konfigurowania serwera konfiguracji Usługa Site Recovery w celu replikacji serwera fizycznego/VMware, maszyny Wirtualnej. . OVA używane przez usługę Azure Migrate jest używana do wykrywania zarządzane przez serwer vCenter do celów oceny i migracji maszyn wirtualnych VMware.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Czy mogę zmienić mój rozmiar maszyny. Czy można ponownie uruchomić ocenę?
Urządzenie Azure Migrate stale zbiera informacje o środowisku lokalnym. Ocena jest jednak migawkę punktu w czasie lokalnych maszyn wirtualnych. Jeśli zmienisz ustawienia na maszynie Wirtualnej, którą chcesz ocenić, użyj opcji "Oblicz ponownie", można zaktualizować oceny z najnowszymi zmianami.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak można odnaleźć środowiska z wieloma dzierżawami w usłudze Azure Migrate?

Dla oprogramowania VMware Jeśli masz środowisko, który jest współużytkowany przez dzierżawców, a nie chcesz do odnalezienia maszyn wirtualnych z jednej dzierżawy w innej dzierżawie subskrypcji, Utwórz vCenter poświadczeniami serwera z dostępem tylko do tych maszyn wirtualnych, które chcesz odnajdywać. Następnie użyj poświadczeń podczas uruchamiania odnajdywania w programie urządzenia usługi Azure Migrate.

Dla funkcji Hyper-V, odnajdywanie przy użyciu poświadczeń hosta funkcji Hyper-V, jeśli maszyny wirtualne mają tego samego hosta funkcji Hyper-V, obecnie nie ma możliwości do oddzielania odnajdywania.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Ile maszyn wirtualnych mogą być wykrywane przez urządzenie jedną migrację?

Można odnajdywać maksymalnie 10 000 maszyn wirtualnych VMware i maksymalnie 5000 maszyn wirtualnych funkcji Hyper-V, przy użyciu urządzenia jedną migrację.  Jeśli masz więcej maszyn w środowisku w środowisku lokalnym, Dowiedz się, jak skalować [funkcji Hyper-V](scale-hyper-v-assessment.md) i [VMware](scale-vmware-assessment.md) oceny.


## <a name="azure-migrate-server-assessment"></a>Usługa Azure Migrate: Ocena serwera

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Usługa Azure Migrate: Ocena Server obsługuje oceny serwerów fizycznych?

Nie, usługa Azure Migrate aktualnie nie obsługuje oceny serwerów fizycznych. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Czy usługa Azure Migrate potrzebuje programu vCenter Server, aby dowiedzieć się, w środowisku programu VMware?

Tak, Azure Migrate potrzebuje programu vCenter Server, aby dowiedzieć się, w środowisku VMware. Odnajdywanie hostów ESXi, które nie są zarządzane przez program vCenter Server nie jest obsługiwana.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między za pomocą usługi Azure Migrate: Ocena serwera i narzędzi?

Usługa Azure Migrate: Ocena serwera zawiera oceny migracji, aby ułatwić przygotowanie do migracji i ocena obciążeń pod kątem migracji na platformę Azure. [Microsoft Assessment i zestaw narzędzi do planowania (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) ma inne funkcje, takie jak migracja planowanie nowsze wersje systemów operacyjnych klienta i serwera Windows i śledzenie użycia oprogramowania. W tych scenariuszach nadal używać narzędzi.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Jak jest usługa Azure Migrate: Ocena serwera różni się od planista wdrażania usługi Azure Site Recovery?

Usługa Azure Migrate: Ocena serwera jest migracji za pomocą narzędzia do planowania. Planista wdrażania usługi Azure Site Recovery jest narzędzia do planowania odzyskiwania po awarii.

- **Migracja z programu VMware/funkcji Hyper-V na platformę Azure**: Aby przeprowadzić migrację usługi na serwerach lokalnych na platformę Azure, należy użyć usługi Azure Migrate: Narzędzie oceny serwera dotyczące planowania migracji. Narzędzie ocenia obciążenia lokalne i zapewnia wskazówki, szczegółowe informacje i mechanizmy, aby pomóc w migracji do platformy Azure. Jeśli wszystko jest gotowe dla planu migracji, można użyć narzędzi, takich jak usługa Azure Migrate: Migracja serwera przeprowadzić migrację maszyn na platformę Azure.
- **Odzyskiwanie po awarii z programu VMware/funkcji Hyper-V na platformę Azure**: Do odzyskiwania po awarii na platformie Azure przy użyciu funkcji odzyskiwania lokacji na użytek planista wdrażania usługi Site Recovery planowania odzyskiwania po awarii. Planista wdrażania usługi Site Recovery wykonuje szczegółowej, oceny określonego odzyskiwania lokacji środowiska lokalnego. Zapewnia zaleceń zapewniających pomyślne odzyskiwanie po operacji, takich jak replikacji i trybu failover maszyn wirtualnych przez usługę Site Recovery. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Usługa Azure Migrate obsługuje szacowania kosztów na podstawie umowy Enterprise Agreement EA?

Usługa Azure Migrate nie obsługuje obecnie Szacowanie kosztów dla [oferty z umową Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Obejście polega na określić płatność za rzeczywiste użycie jako oferty i ręcznie określić procent rabatu (dotyczy subskrypcji) w polu "Rabat" we właściwościach oceny.

  ![Rabat](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaka jest różnica między ustalania rozmiaru jako — w środowisku lokalnym i ustalania rozmiaru na podstawie wydajności?

- W lokalne zmiany rozmiaru, usługa Azure Migrate nie bierze pod uwagę dane wydajności maszyn wirtualnych. Jego rozmiary maszyn wirtualnych na podstawie konfiguracji w środowisku lokalnym. -W ustalania rozmiaru na podstawie wydajności, zmiany rozmiaru jest oparta na danych użycia.
- Na przykład jeśli lokalna maszyna wirtualna ma 4 rdzenie i 8 GB pamięci RAM z 50% użycia Procesora CPU i użycie pamięci przez program 50%, jako lokalne ustalanie rozmiaru zaleca jednostki SKU maszyny Wirtualnej platformy Azure z 4 rdzenie i 8GB pamięci RAM. Ustalanie rozmiaru na podstawie wydajności, jednak zaleca, jednostki SKU maszyny Wirtualnej 2 rdzeni i 4 GB, ponieważ przyjęto, że wartość procentowa użycia.
- Podobnie zmiany rozmiaru dysku zależy od dwóch właściwości oceny — zmiany rozmiaru typu kryterium i magazynu.
= Jeśli kryterium ustalania rozmiaru jest oparte na wydajności i magazynu jest uruchomiana automatycznie, wartości operacje We/Wy i przepływność dysku są uwzględniane przy identyfikowaniu typ dysku docelowego (standardowa / Premium).
- Jeśli kryterium ustalania rozmiaru jest oparte na wydajności i typ magazynu jest premium, zaleca się dysku w warstwie premium. Dysku w warstwie premium, jednostka SKU jest wybierane na podstawie rozmiaru dysku w środowisku lokalnym. Ta sama logika jest używany do dysku zmiany rozmiaru, jeśli kryterium ustalania rozmiaru to lokalne zmiany rozmiaru, a typ magazynu to standardowa lub premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ wydajności historii i percentyl użycia ma na zaleceń dotyczących rozmiarów?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności.

- Usługa Azure Migrate umożliwia zbieranie informacji o historii wydajności maszyn lokalnych i używa go w celu zaleca się typu dysk i rozmiar maszyny Wirtualnej na platformie Azure.
- Urządzenie ciągle profile w lokalnym środowisku na potrzeby zbierania danych użycia w czasie rzeczywistym, co 20 sekund. Urządzenie podsumowuje 20-sekundowe próbki i tworzy jeden punkt danych co 15 minut. Aby utworzyć jeden punkt danych, urządzenie wybiera szczytową wartość z wszystkich 20-sekundowych próbek i wysyła ją do platformy Azure.
- Po utworzeniu oceny na platformie Azure (oparte na czas trwania wydajności i wartość percentylu historii wydajności) usługi Azure Migrate oblicza wartość efektywne wykorzystanie i używa go na potrzeby zmiany rozmiaru.
- Na przykład, jeśli ustawić czas trwania wydajności za jeden dzień, a wartość do 95. percentyl, usługa Azure Migrate używa 15 punktów minuty przykładowe wysyłanych przez moduł zbierający w ciągu ostatniego dnia, posortowane w kolejności rosnącej, a wybiera wartość 95. percentyla jako efektywne wykorzystanie.
- Wartość 95. percentyla gwarantuje, że ignorujesz jakiekolwiek elementy odstające, które mogą wystąpić, jeśli używasz 99. percentylu. Jeśli chcesz wybrać szczytowe użycie w danym okresie i nie pomijać żadnych elementów odstających, wybierz 99. percentyl.

### <a name="what-is-dependency-visualization"></a>Co to jest wizualizacji zależności?

Wizualizacja zależności umożliwia ocenę grupy maszyn wirtualnych do migracji z większą pewnością. Jego kontroli zależności maszyny przed uruchomieniem oceny. Wizualizacja zależności pomaga upewnić się, że nic nie jest pozostawione i uniknąć awarii podczas migracji na platformę Azure. Usługa Azure Migrate korzysta z rozwiązania Service Map w dziennikach usługi Azure Monitor, aby umożliwić wizualizacji zależności.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Czy muszę płacić za używanie funkcji wizualizacji zależności?

Nie. [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Czy należy zainstalować żadnych wizualizacji zależności?

Aby użyć wizualizacji zależności, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który ma zostać oceniona.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi być zainstalowany na każdym komputerze.
- [Agenta zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musi być zainstalowany na każdym komputerze.
- Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować bramę usługi Log Analytics na nich.

Jeśli nie używasz wizualizacji zależności, nie potrzebujesz tych agentów.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy można używać istniejącego obszaru roboczego w celu wizualizacji zależności?

Tak, można dołączyć istniejącego obszaru roboczego do projektu migracji i korzystanie z jej na potrzeby wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować wizualizacji zależności. Jednakże, ponieważ usługa Azure Migrate rozwiązania Service map dla wizualizacji zależności, możesz użyć [interfejsów API REST usługi Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) można pobrać zależności w formacie json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak można zautomatyzować instalację programu Microsoft Monitoring Agent (MMA) i agenta zależności

[Użyj tego skryptu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) instalacji agentów. [Wykonaj te instrukcje](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) do zainstalowania przy użyciu wiersza polecenia lub automatyzacji programu MMA. Agent MMA, można wykorzystać [ten skrypt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oprócz skryptów, można użyć narzędzia wdrażania, takich jak System Center Configuration Manager [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) itp., aby wdrożyć agentów.

### <a name="what-operating-systems-are-supported-by-mma"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- [Przegląd](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) listę systemów operacyjnych Windows obsługiwanych przez MMA.
- [Przeglądu] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) listę systemów operacyjnych Linux obsługiwane przez MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Co to są systemy operacyjne obsługiwane przez agenta zależności?

[Przegląd](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) systemy operacyjne Windows, które są obsługiwane przez agenta zależności.
[Przegląd](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) listę systemów operacyjnych Linux obsługiwane przez agenta zależności.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Czy mogę zwizualizować zależności w usłudze Azure Migrate na więcej niż godzinę?
Nie można wizualizować zależności nawet przez godzinę. Możesz przejść z powrotem do określonej daty w historii, maksymalnie ostatni miesiąc, ale maksymalny czas trwania dla wizualizacji jest godzina. Na przykład można użyć czas trwania w mapowaniu zależności, aby wyświetlić zależności dla wczoraj, ale tylko wtedy można wyświetlić okna jedną godzinę. Jednak można użyć usługi Azure Monitor dzienniki, aby [wykonywanie zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Wizualizacji zależności jest obsługiwane dla grup za pomocą ponad dziesięć maszyn wirtualnych?
Możesz [wizualizacja zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) maksymalnie 10 maszyn wirtualnych. Jeśli istnieje grupa z więcej niż dziesięć maszyn wirtualnych, firma Microsoft zaleca, aby podzielić grupy w mniejszych grup, a następnie wizualizować zależności.

## <a name="azure-migrate-server-migration"></a>Usługa Azure Migrate: Migracja serwera

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Jak jest usługa Azure Migrate: Migracja serwera różni się od usługi Azure Site Recovery?

Usługa Azure Migrate: Migracja serwera korzysta z aparatu replikacji usługa Site Recovery do migracji serwerów do platformy Azure opartej o agentów.
## <a name="next-steps"></a>Kolejne kroki
Odczyt [usługi Azure Migrate — Przegląd](migrate-services-overview.md)
