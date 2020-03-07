---
title: Często zadawane pytania — odnajdywanie, Ocena i analiza zależności w programie Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące odnajdywania, oceny i analizy zależności w programie Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362258"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Często zadawane pytania dotyczące odnajdywania, oceny i analizy zależności

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące odnajdywania, oceny i analizy zależności w programie Azure Migrate. Jeśli masz inne pytania, zapoznaj się z następującymi artykułami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate.
- [Pytania](common-questions-appliance.md) dotyczące urządzenia Azure Migrateowego.
- [Pytania](common-questions-server-migration.md) dotyczące migracji serwera.
- Publikuj pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych można odnajdywać za pomocą urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware, do 5 000 maszyn wirtualnych funkcji Hyper-V, a nawet do 250 serwery fizyczne przy użyciu jednego urządzenia. Jeśli masz więcej maszyn, zapoznaj się z artykułami dotyczącymi skalowania oceny [funkcji Hyper-V](scale-hyper-v-assessment.md), programu [VMware](scale-vmware-assessment.md)i [serwera fizycznego](scale-physical-assessment.md) .



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Zmieniono rozmiar maszyny wirtualnej. Czy mogę ponownie uruchomić ocenę?

Urządzenie Azure Migrate ciągle zbiera informacje o maszynach lokalnych, a Ocena to migawka do punktu w czasie. Jeśli zmienisz ustawienia na maszynie wirtualnej, która ma zostać oceniona, użyj opcji Oblicz ponownie, aby zaktualizować ocenę przy użyciu najnowszych zmian.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Jak mogę odnajdywania maszyn wirtualnych w środowisku wielodostępnym?

- **VMware**: Jeśli środowisko jest współużytkowane przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych dzierżawy w ramach subskrypcji innej dzierżawy, Utwórz vCenter Server poświadczenia, które mogą uzyskiwać dostęp tylko do maszyn wirtualnych, które chcesz odnajdywać. Następnie użyj tych poświadczeń po rozpoczęciu odnajdywania na urządzeniu Azure Migrate.
- **Funkcja Hyper-v**: odnajdywanie używa poświadczeń hosta funkcji Hyper-v. Jeśli maszyny wirtualne korzystają z tego samego hosta funkcji Hyper-V, nie ma możliwości oddzielenia odnajdywania.  


## <a name="do-i-need-vcenter-server"></a>Czy potrzebuję vCenter Server?

Tak, Azure Migrate wymaga vCenter Server do przeprowadzenia odnajdywania w środowisku programu VMware. Nie obsługuje odnajdywania hostów ESXi, które nie są zarządzane przez vCenter Server.


## <a name="whats-are-the-sizing-options"></a>Jakie są opcje ustalania wielkości?

W przypadku ustalania rozmiarów lokalnych Azure Migrate nie uwzględnia danych wydajności maszyny wirtualnej w celu oceny. Ocenia rozmiary maszyn wirtualnych na podstawie konfiguracji lokalnej. W przypadku ustalania rozmiarów na podstawie wydajności rozmiar jest oparty na danych użycia.

- Na przykład jeśli lokalna maszyna wirtualna ma 4 rdzenie i 8 GB pamięci na 50% użycia procesora CPU i 50% wykorzystania pamięci:
    - Ustalanie rozmiarów lokalnych będzie zalecane dla jednostki SKU maszyny wirtualnej platformy Azure, która ma cztery rdzenie i 8 GB pamięci.
    - Ustalanie rozmiarów na podstawie wydajności będzie zalecane dla jednostki SKU maszyny wirtualnej, która ma dwa rdzenie i 4 GB pamięci, ponieważ jest brana pod uwagę procent użycia.

- Podobnie rozmiary dysku są zależne od kryteriów ustalania rozmiarów i typu magazynu.
    - Jeśli kryteria ustalania wielkości są oparte na wydajności i typ magazynu jest automatyczny, Azure Migrate pobiera wartości IOPS i przepływności dysku do konta, gdy identyfikuje docelowy typ dysku (standardowa lub Premium).
    - Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, a typ magazynu to Premium, Azure Migrate zaleca SKU dysku w warstwie Premium na podstawie rozmiaru dysku lokalnego. Ta sama logika jest stosowana do wielkości dysku, gdy wielkość liter jest w środowisku lokalnym, a typ magazynu to standardowa lub Premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>Czy rozmiary zmian/wykorzystania mają wpływ na wydajność?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności.

- Azure Migrate zbiera historię wydajności maszyn lokalnych i używa jej do zaproponowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- Urządzenie nieustannie profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund.
- Urządzenie zbiera 20 sekund próbek i tworzy jeden punkt danych co 15 minut.
- Aby utworzyć punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych.
- Urządzenie wysyła ten punkt danych do platformy Azure.

Po utworzeniu oceny na platformie Azure na podstawie wartości percentylu czasu trwania wydajności i historii wydajności Azure Migrate oblicza wartość efektywnego wykorzystania i używa jej do ustalania rozmiarów.

- Na przykład jeśli ustawisz czas trwania wydajności na jeden dzień i wartość percentylu na 95 percentyl, Azure Migrate sortuje 15-minutowe punkty próbkowania wysyłane przez moduł zbierający dla ostatniego dnia w kolejności rosnącej i wybierają wartość percentylu używany 95. jako obowiązującą. zużycie.
- Użycie wartości percentylu używany 95. gwarantuje, że elementy odstające są ignorowane. Jeśli używasz 99 percentylu, można uwzględnić wartości odstających. Jeśli chcesz wybrać użycie szczytowe dla danego okresu, bez braku żadnych wartości odstających, wybierz 99 percentyl.

## <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Użyj wizualizacji zależności, aby ocenić grupy maszyn wirtualnych do migracji z większą pewnością. Wizualizacja między różnymi kontrolami zależności maszyn przed uruchomieniem oceny. Pozwala to zagwarantować, że nic nie zostanie pozostawione i w ten sposób można uniknąć nieoczekiwanego przestoju podczas migracji na platformę Azure. Azure Migrate używa rozwiązania Service Map w Azure Monitor, aby włączyć wizualizację zależności. [Dowiedz się więcej](concepts-dependency-visualization.md).

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Jaka jest różnica między opartymi na agentach i bez wykorzystania agentów?

Różnice między wizualizacją bez agenta i wizualizacją opartą na agentach zostały podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
Pomoc techniczna | Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-visualization) obsługiwane systemy operacyjne. | Ogólnie dostępna.
Agent | Nie trzeba instalować agentów na maszynach, które mają być sprawdzane krzyżowo. | Agenci do zainstalowania na każdej maszynie lokalnej, którą chcesz analizować: [program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)i [Agent zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Wymagania wstępne | [Zapoznaj](concepts-dependency-visualization.md#agentless-visualization) się z wymaganiami dotyczącymi wymagań wstępnych i wdrażania. | [Zapoznaj](concepts-dependency-visualization.md#agent-based-visualization) się z wymaganiami dotyczącymi wymagań wstępnych i wdrażania.
Log Analytics | Nie jest wymagane. | Azure Migrate używa rozwiązania [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) na potrzeby wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#agent-based-visualization).
Jak to działa | Przechwytuje dane połączenia TCP na maszynach z włączoną funkcją wizualizacji zależności. Po odnajdywaniu dane są zbierane w odstępach pięciu minut. | Service Map agenci zainstalowani na komputerze zbierają dane dotyczące procesów TCP oraz połączeń przychodzących/wychodzących dla każdego procesu.
Dane | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port. | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 
Wizualizacja | Mapę zależności pojedynczego serwera można wyświetlać w czasie trwania z przedziału od godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapę można wyświetlać tylko w ciągu godziny.<br/><br/> Mapa zależności grupy serwerów.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksport danych | Nie można obecnie pobrać w formacie tabelarycznym. | Dane można badać przy użyciu Log Analytics.




## <a name="do-i-pay-for-dependency-visualization"></a>Czy płacisz za wizualizację zależności?
Nie. [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Co należy zainstalować dla wizualizacji zależności opartej na agentach?

Aby można było używać wizualizacji zależności opartej na agentach, należy pobrać i zainstalować agentów na każdej maszynie lokalnej, którą chcesz oszacować.

Zainstaluj następujących agentów na każdej maszynie:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować bramę Log Analytics na nich.

Te agenci nie są potrzebne, chyba że jest używana Wizualizacja zależności oparta na agentach.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak, w przypadku wizualizacji zależności opartej na agentach można dołączyć istniejący obszar roboczy do projektu migracji i użyć go do wizualizacji zależności. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować raportu wizualizacji zależności w wizualizacji opartej na agentach. Jednak Azure Migrate używa Service Map i można użyć [interfejsu API REST Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) do pobrania zależności w formacie JSON.

## <a name="can-i-automate-agent-installation"></a>Czy można zautomatyzować instalację agenta?
W przypadku wizualizacji zależności opartej na agentach Automatyzowanie w następujący sposób:

- Ten [skrypt służy do instalowania agenta zależności](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- W przypadku MMA postępuj zgodnie z tymi [instrukcjami](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) , aby użyć wiersza polecenia lub automatyzacji, lub Użyj [tego skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oprócz skryptów można także użyć narzędzi wdrażania, takich jak Microsoft Endpoint Configuration Manager i [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) , aby wdrożyć agentów.


## <a name="what-operating-systems-does-mma-support"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- Wyświetl listę [systemów operacyjnych Windows obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Wyświetl listę [systemów operacyjnych Linux obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Czy mogę wizualizować zależności przez więcej niż godzinę?
W przypadku wizualizacji opartej na agentach można wizualizować zależności przez maksymalnie godzinę. Można wrócić do konkretnej daty w historii, o ile nie jest to miesiąc, ale maksymalny czas trwania wizualizacji to godzina. Można na przykład użyć czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można wyświetlić zależności tylko dla jednego okna. Można jednak użyć dzienników Azure Monitor do [wykonywania zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

W przypadku wizualizacji bez wykorzystania agentów można wyświetlić mapę zależności pojedynczego serwera od czasu trwania do 30 dni.


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Czy można wizualizować zależności dla grup o więcej niż 10 maszynach wirtualnych?
Możesz [wizualizować zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) dla grup zawierających maksymalnie 10 maszyn wirtualnych. Jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy poddzielenie grupy na mniejsze grupy, a następnie wizualizację zależności.




## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
