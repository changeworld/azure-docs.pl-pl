---
title: Często zadawane pytania dotyczące Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: snehaa
ms.openlocfilehash: a622a5793e817be2445e60d925354ab37bb8e331
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185774"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate. Jeśli po przeczytaniu tego artykułu masz dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="which-azure-geographies-are-supported"></a>Które usługi Azure lokalizacje geograficzne są obsługiwane?

Przejrzyj Azure Migrate obsługiwane lokalizacje geograficzne dla [maszyn wirtualnych VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i [maszyn wirtualnych funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Jaka jest różnica między Azure Migrate i Site Recovery?

Azure Migrate udostępnia scentralizowany centrum do zarządzania odnajdywaniem, oceną i migracją maszyn lokalnych oraz obciążeń do platformy Azure oraz śledzenia ich. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) to rozwiązanie odzyskiwania po awarii. Azure Migrate: Narzędzie do migracji serwera używa niektórych funkcji Site Recovery zaplecza na potrzeby migracji podnoszenia i przesunięcia niektórych maszyn lokalnych.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Jak mogę usunąć projekt Azure Migrate?

Postępuj zgodnie z [tymi instrukcjami](how-to-delete-project.md) , aby usunąć projekt. [Przejrzyj zasoby](how-to-delete-project.md#created-resources) , które są tworzone podczas odnajdywania, oceniania i migrowania maszyn i obciążeń w projekcie Azure Migrate.


## <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

### <a name="how-does-the-appliance-connect-to-azure"></a>Jak urządzenie nawiązuje połączenie z platformą Azure?

Połączenie może być nawiązywane za pośrednictwem Internetu lub używać usługi Azure ExpressRoute z usługą komunikacji równorzędnej Public/Microsoft.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Jaka łączność sieciowa jest wymagana do oceny i migracji Azure Migrate serwera?

Aby uzyskać informacje o adresach URL i portach wymaganych Azure Migrate do komunikowania się z platformą Azure, należy zapoznać się z macierzami obsługi programu [VMware](migrate-support-matrix-vmware.md) i [funkcji Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Czy mogę zabezpieczyć maszynę wirtualną urządzenia utworzoną za pomocą szablonu?

Do szablonu można dodawać składniki (na przykład program antywirusowy), o ile pozostawisz reguły dotyczące komunikacji i zapory wymagane przez urządzenie Azure Migrate.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Jakie dane są zbierane przez urządzenie Azure Migrate?

Przejrzyj dane zbierane przez urządzenie w następujący sposób:
- [Dane wydajności](migrate-appliance.md#collected-performance-data-vmware) i [metadane](migrate-appliance.md#collected-metadata-vmware) dla maszyn wirtualnych VMware.
- [Dane wydajności](migrate-appliance.md#collected-performance-data-hyper-v) i [metadane](migrate-appliance.md#collected-metadata-hyper-v) dla maszyn wirtualnych funkcji Hyper-V.


### <a name="does-appliance-analysis-impact-performance"></a>Czy analiza urządzenia ma wpływ na wydajność?

Lokalne profile urządzeń Azure Migrate są ciągle mierzone w celu mierzenia danych wydajności maszyny wirtualnej. To Profilowanie nie ma prawie wpływu na hosty funkcji Hyper-V/ESXi ani na vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Gdzie i jak długo są przechowywane dane?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure wybranej podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w subskrypcji firmy Microsoft i usuwane po usunięciu projektu Azure Migrate.

W przypadku wizualizacji zależności zebrane dane są przechowywane w Stany Zjednoczone w obszarze roboczym Log Analytics utworzonym w ramach subskrypcji platformy Azure. Te dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji. [Dowiedz się więcej](concepts-dependency-visualization.md) o wizualizacji zależności.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Jaka ilość danych jest przekazywana podczas ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate różni się w zależności od kilku parametrów. Aby zapewnić zrozumienie woluminu, projekt Azure Migrate zawierający 10 maszyn (z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB dziennie. Ta wartość jest przybliżona i zmienia się w zależności od liczby punktów danych dla kart sieciowych i dysków. Zwiększenie liczby wysyłanych danych jest nieliniowe, jeśli liczba maszyn, kart sieciowych lub dysków rośnie.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane w czasie spoczynku i w trakcie przesyłania?

Tak, oba.
- Metadane są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem Internetu za pośrednictwem protokołu HTTPS.
- Metadane są przechowywane w bazie danych [usługi Azure Cosmos Database](../cosmos-db/database-encryption-at-rest.md) i w [usłudze Azure Blob Storage](../storage/common/storage-service-encryption.md) w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w stanie spoczynku.
- Dane dotyczące analizy zależności są również szyfrowane w trakcie przesyłania (Secure HTTPS). Jest ona przechowywana w obszarze roboczym Log Analytics w ramach subskrypcji. Jest on także szyfrowany w spoczynku.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Jak urządzenie komunikuje się z vCenter Server i Azure Migrate?

1. Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia.
2. Urządzenie używa programu VMware PowerCLI do wysyłania zapytań do vCenter Server, aby zbierać metadane dotyczące maszyn wirtualnych zarządzanych przez vCenter Server. Zbiera on dane konfiguracyjne dotyczące maszyn wirtualnych (rdzeni, pamięci, dysków, kart sieciowych) i historię wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca.
3. Zebrane metadane są następnie wysyłane do Azure Migrate: Ocena serwera (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Czy można połączyć to samo urządzenie z wieloma wystąpieniami vCenter Server?

Nie. Istnieje mapowanie jeden do jednego między urządzeniem i vCenter Server. Aby odnajdywać maszyny wirtualne w wielu wystąpieniach vCenter Server, należy wdrożyć wiele urządzeń.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Zmieniono rozmiar maszyny. Czy mogę ponownie uruchomić ocenę?

Urządzenie Azure Migrate ciągle zbiera informacje o środowisku lokalnym. Jednak Ocena to migawka lokalnych maszyn wirtualnych do określonego momentu. Jeśli zmienisz ustawienia na maszynie wirtualnej, która ma zostać oceniona, użyj opcji Oblicz ponownie, aby zaktualizować ocenę przy użyciu najnowszych zmian.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Jak przeprowadzić odnajdywanie w środowisku wielodostępnym?

- W przypadku oprogramowania VMware, jeśli środowisko jest udostępniane między dzierżawcami i nie chcesz odnajdywać maszyn wirtualnych jednej dzierżawy w ramach subskrypcji innej dzierżawy, Utwórz poświadczenia vCenter Server, które mogą uzyskiwać dostęp tylko do maszyn wirtualnych, które chcesz odnajdywać. Następnie użyj tych poświadczeń po rozpoczęciu odnajdywania na urządzeniu Azure Migrate.
- W przypadku funkcji Hyper-V odnajdywanie używa poświadczeń hosta funkcji Hyper-V. Jeśli maszyny wirtualne korzystają z tego samego hosta funkcji Hyper-V, nie ma możliwości oddzielenia odnajdywania.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Ile maszyn wirtualnych można znaleźć za pomocą jednego urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware oraz do 5 000 maszyn wirtualnych funkcji Hyper-V z jednym urządzeniem migracji. Jeśli masz więcej maszyn w środowisku lokalnym, Dowiedz się, jak skalować oceny [funkcji Hyper-V](scale-hyper-v-assessment.md) i programu [VMware](scale-vmware-assessment.md) .

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Czy mogę usunąć urządzenie Azure Migrate z projektu?

Obecnie Usuwanie urządzenia z projektu nie jest obsługiwane.

- Jedynym sposobem usunięcia urządzenia jest usunięcie grupy zasobów zawierającej projekt Azure Migrate skojarzony z urządzeniem.
- Jednak usunięcie grupy zasobów spowoduje również usunięcie innych zarejestrowanych urządzeń, wykrytego spisu, ocen i wszystkich innych składników platformy Azure skojarzonych z projektem w grupie zasobów.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate oceny serwera


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Czy Azure Migrate potrzebować vCenter Server do odnajdywania maszyn wirtualnych VMWare?

Tak, Azure Migrate wymaga vCenter Server do przeprowadzenia odnajdywania w środowisku programu VMware. Nie obsługuje odnajdywania hostów ESXi, które nie są zarządzane przez vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między oceną Azure Migrate serwera a zestawem narzędzi mapy?

Ocena serwera zapewnia ocenę, aby pomóc w zagotowości do migracji oraz ocenę obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi firmy Microsoft do oceny i planowania (map)](https://www.microsoft.com/download/details.aspx?id=7826) ułatwia wykonywanie innych zadań, w tym planowanie migracji nowszych wersji klienta/serwera operacyjnego Windows i śledzenia użycia oprogramowania. W tych scenariuszach Kontynuuj korzystanie z narzędzia MAP Toolkit.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaka jest różnica między oceną serwera a Site Recovery Planista wdrażania?

Ocena serwera to narzędzie do planowania migracji. Planista wdrażania Site Recovery jest narzędziem do planowania odzyskiwania po awarii. 

- **Planowanie migracji lokalnej na platformę Azure**: Jeśli planujesz migrację serwerów lokalnych do platformy Azure, użyj oceny serwera na potrzeby planowania migracji. Ocenia obciążenia lokalne i udostępnia wskazówki i narzędzia ułatwiające Migrowanie. Po zakończeniu planowania migracji można użyć narzędzi, w tym migracji serwera Azure Migrate, aby przeprowadzić migrację maszyn na platformę Azure.
- **Planowanie odzyskiwania po awarii na platformie Azure**: Jeśli planujesz skonfigurować odzyskiwanie po awarii na platformie Azure przy użyciu Site Recovery, użyj Site Recovery planista wdrażania. Planista wdrażania zapewnia głębokiej, Site Recoveryowej oceny środowiska lokalnego na potrzeby odzyskiwania po awarii. Zawiera zalecenia dotyczące odzyskiwania po awarii, takie jak replikacja i tryb failover.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>Czy Azure Migrate oszacować koszty programu Umowa Enterprise (EA)?

Ocena serwera Azure Migrate obecnie nie obsługuje szacowania kosztów dla [programu Umowa Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/). Aby obejść ten krok, podczas tworzenia oceny możesz określić **płatność zgodnie z rzeczywistym** użyciem jako **ofertę**i ręcznie dodać stawkę procentową rabatu (mającą zastosowanie do subskrypcji) jako **Rabat** we właściwościach oceny:

  ![Właściwości oceny](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Jaka jest różnica między rozmiarem lokalnym i opartym na wydajności?

W przypadku ustalania rozmiarów lokalnych Azure Migrate nie uwzględnia danych wydajności maszyny wirtualnej w celu oceny. Ocenia rozmiary maszyn wirtualnych na podstawie konfiguracji lokalnej. W przypadku ustalania rozmiarów na podstawie wydajności rozmiar jest oparty na danych użycia.

- Jeśli na przykład lokalna maszyna wirtualna ma 4 rdzenie i 8 GB pamięci na 50% użycia procesora CPU i 50% wykorzystania pamięci, następujące wystąpienia zostaną wykonane:
    - Ustalanie rozmiarów lokalnych będzie zalecane dla jednostki SKU maszyny wirtualnej platformy Azure, która ma cztery rdzenie i 8 GB pamięci.
    - Ustalanie rozmiarów na podstawie wydajności będzie zalecane dla jednostki SKU maszyny wirtualnej, która ma dwa rdzenie i 4 GB pamięci, ponieważ jest brana pod uwagę procent użycia.

- Podobnie rozmiary dysku są zależne od dwóch właściwości oceny: Określanie rozmiarów kryteriów i typów magazynów.
    - Jeśli kryteria ustalania wielkości są oparte na wydajności i typ magazynu jest automatyczny, Azure Migrate pobiera wartości IOPS i przepływności dysku do konta, gdy identyfikuje docelowy typ dysku (standardowa lub Premium).
    - Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, a typ magazynu to Premium, Azure Migrate zaleca SKU dysku w warstwie Premium na podstawie rozmiaru dysku lokalnego. Ta sama logika jest stosowana do wielkości dysku, gdy wielkość liter jest w środowisku lokalnym, a typ magazynu to standardowa lub Premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>Czy zaleceń dotyczących rozmiaru i stopnia wpływu na wydajność?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności.

- Azure Migrate zbiera historię wydajności maszyn lokalnych i używa jej do zaproponowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- Urządzenie nieustannie profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund.
- Urządzenie zbiera 20 sekund próbek i tworzy jeden punkt danych co 15 minut.
- Aby utworzyć punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych.
- Urządzenie wysyła ten punkt danych do platformy Azure.

Po utworzeniu oceny na platformie Azure na podstawie wartości percentylu czasu trwania wydajności i historii wydajności Azure Migrate oblicza wartość efektywnego wykorzystania i używa jej do ustalania rozmiarów.

- Na przykład jeśli ustawisz czas trwania wydajności na jeden dzień i wartość percentylu na 95 percentyl, Azure Migrate sortuje 15-minutowe punkty próbkowania wysyłane przez moduł zbierający dla ostatniego dnia w kolejności rosnącej i wybierają wartość percentylu używany 95. jako obowiązującą. zużycie.
- Użycie wartości percentylu używany 95. gwarantuje, że elementy odstające są ignorowane. Jeśli używasz 99 percentylu, można uwzględnić wartości odstających. Jeśli chcesz wybrać użycie szczytowe dla danego okresu, bez braku żadnych wartości odstających, wybierz 99 percentyl.

## <a name="server-assessment---dependency-visualization"></a>Ocena serwera — Wizualizacja zależności


### <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Użyj wizualizacji zależności, aby ocenić grupy maszyn wirtualnych do migracji z większą pewnością. Wizualizacja między różnymi kontrolami zależności maszyn przed uruchomieniem oceny. Pozwala to zagwarantować, że nic nie zostanie pozostawione i w ten sposób można uniknąć nieoczekiwanego przestoju podczas migracji na platformę Azure. Azure Migrate używa rozwiązania Service Map w Azure Monitor, aby włączyć wizualizację zależności. [Dowiedz się więcej](concepts-dependency-visualization.md).

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Czy muszę uiścić użycie wizualizacji zależności?
Nie. [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Czy muszę zainstalować wszystko dla wizualizacji zależności?

Aby można było używać wizualizacji zależności, należy pobrać i zainstalować agentów na każdej maszynie lokalnej, którą chcesz oszacować.

Na każdej maszynie należy zainstalować następujących agentów:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować bramę Log Analytics na nich.

Te agenci nie są potrzebne, chyba że używasz wizualizacji zależności.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy mogę użyć istniejącego obszaru roboczego dla wizualizacji zależności?

Tak, możesz dołączyć istniejący obszar roboczy do projektu migracji i użyć go do wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować wizualizacji zależności. Jednak Azure Migrate używa Service Map i można użyć [interfejsu API REST Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) do pobrania zależności w formacie JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Jak można zautomatyzować instalację Microsoft Monitoring Agent (MMA) i agenta zależności?

Ten [skrypt służy do instalowania agenta zależności](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Postępuj zgodnie [z tymi instrukcjami, aby zainstalować MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) za pomocą wiersza polecenia lub automatyzacji. W przypadku MMA Użyj [tego skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oprócz skryptów można także użyć narzędzi wdrażania, takich jak System Center Configuration Manager i [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) , aby wdrożyć agentów.


### <a name="what-operating-systems-are-supported-by-mma"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- Wyświetl listę [systemów operacyjnych Windows obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Wyświetl listę [systemów operacyjnych Linux obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Czy mogę wizualizować zależności przez więcej niż godzinę?
Nie. Możesz wizualizować zależności przez maksymalnie godzinę. Można wrócić do konkretnej daty w historii, o ile nie jest to miesiąc, ale maksymalny czas trwania wizualizacji to godzina. Można na przykład użyć czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można wyświetlić zależności tylko dla jednego okna. Można jednak użyć dzienników Azure Monitor do [wykonywania zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Czy mogę używać wizualizacji zależności dla grup o więcej niż 10 maszynach wirtualnych?
Możesz [wizualizować zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) dla grup zawierających maksymalnie 10 maszyn wirtualnych. Jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy poddzielenie grupy na mniejsze grupy, a następnie wizualizację zależności.

## <a name="azure-migrate-server-migration"></a>Migracja serwera Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Jaka jest różnica między migracją Azure Migrate serwera i Site Recovery?

- Migracja maszyn wirtualnych programu VMware bez wykorzystania agentów jest natywna w ramach migracji Azure Migrate serwera.
- Do migracji maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych i maszyn wirtualnych VMware opartych na agentach migracja Azure Migrate Server korzysta z aparatu replikacji Azure Site Recovery.


## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
