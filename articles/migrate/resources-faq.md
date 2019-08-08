---
title: Azure Migrate — często zadawane pytania (FAQ) | Microsoft Docs
description: Adresy często zadawanych pytań dotyczących Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: bf591306220b2c8c1e6c8166686836d96432fc7d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856272"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate — często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące Azure Migrate. Jeśli po przeczytaniu tego artykułu masz jakieś dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Które usługi Azure lokalizacje geograficzne są obsługiwane przez Azure Migrate?

Listę dla programu VMware można znaleźć [tutaj](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i dla [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Czym różnią się Azure Migrate od Azure Site Recovery?

Azure Migrate udostępnia scentralizowany centrum do uruchamiania, wykonywania i śledzenia odnajdowania, oceny i migracji maszyn i obciążeń do platformy Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) to rozwiązanie odzyskiwania po awarii. Migracja serwera Azure Migrate wykorzystuje Azure Site Recovery w zapleczu, aby umożliwić migrację do migracji na maszynach lokalnych.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Urządzenie Azure Migrate (serwery VMware/fizyczne)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak urządzenie Azure Migrate nawiązuje połączenie z platformą Azure?

Połączenie może być nawiązywane za pośrednictwem Internetu lub można użyć usługi ExpressRoute z usługą komunikacji równorzędnej Public/Microsoft.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Jakie wymagania dotyczące łączności sieciowej są potrzebne do oceny i migracji serwera Azure Migrate

W przypadku adresów URL i portów wymaganych do Azure Migrate komunikowania się z platformą Azure należy zapoznać się z macierzami obsługi programu [VMware](migrate-support-matrix-vmware.md) i [funkcji Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Czy mogę wzmocnić konfigurację urządzenia skonfigurowanego za pomocą szablonu?

Dodatkowe składniki (na przykład program antywirusowy) można dodać do szablonu, o ile reguły komunikacji i zapory wymagane przez urządzenie Azure Migrate są pozostawione w stanie takim, w jakim się znajdują.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Jakie dane są zbierane przez Azure Migrate urządzenie?

Szczegółowe informacje o danych zbieranych przez urządzenie Azure Migrate można znaleźć [tutaj](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Czy istnieje jakikolwiek wpływ na wydajność analizowanego środowiska VMware lub funkcji Hyper-V?

W przypadku ciągłego profilowania danych wydajności, Azure Migrate są profile urządzeń lokalnych, aby mierzyć dane wydajności maszyny wirtualnej. Ma to niemal negatywny wpływ na hosty funkcji Hyper-V/ESXi, a także na vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie są przechowywane zebrane dane i jak długo?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure, która została określona podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w subskrypcji firmy Microsoft i usuwane po usunięciu projektu Azure Migrate.

W przypadku wizualizacji zależności, Jeśli instalujesz agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym Log Analytics utworzonym w ramach subskrypcji platformy Azure. Te dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji. [Dowiedz się więcej](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Jaka jest ilość danych przekazywanych przez urządzenie Azure Migrate podczas ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate zależy od kilku parametrów. Aby podać numer indykatywny, projekt Azure Migrate zawierający 10 maszyn (z jednym dyskiem i jedną kartą sieciową) wysyła do około 50 MB dziennie. Jest to przybliżona wartość, która zmienia się w zależności od liczby punktów danych dla kart sieciowych i dysków (dane wysyłane są nieliniowe, jeśli liczba maszyn, kart sieciowych lub dysków rośnie).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane w czasie spoczynku i w trakcie przesyłania?

Tak dla obu. Metadane są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem Internetu za pośrednictwem protokołu HTTPS. Metadane są przechowywane w [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)i w [usłudze Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft i szyfrowane w spoczynku.

Dane zbierane przez agentów zależności są również szyfrowane w trakcie przesyłania (Secure HTTPS) i są przechowywane w obszarze roboczym Log Analytics w ramach subskrypcji użytkownika. Jest on także szyfrowany w spoczynku.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak urządzenie Azure Migrate komunikuje się z vCenter Server i usługą Azure Migrate?

Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia. Wysyła zapytanie do vCenter Server przy użyciu programu VMware PowerCLI, aby zbierać metadane dotyczące maszyn wirtualnych zarządzanych przez vCenter Server. Zbiera dane konfiguracji dotyczące maszyn wirtualnych (rdzeni, pamięci, dysków, kart sieciowych itp.), a także historię wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca. Zebrane metadane są następnie wysyłane do oceny serwera Azure Migrate (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Czy można połączyć to samo urządzenie z wieloma serwerami vCenter?

Nie. Istnieje mapowanie jeden do jednego między urządzeniem i vCenter Server. Jeśli konieczne jest odnajdywanie maszyn wirtualnych na wielu serwerach vCenter, należy wdrożyć wiele urządzeń. 


### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Zmieniono rozmiar maszyny. Czy mogę ponownie uruchomić ocenę?

Urządzenie Azure Migrate ciągle zbiera informacje o środowisku lokalnym. Jednak Ocena to migawka lokalnych maszyn wirtualnych do określonego momentu. Jeśli zmienisz ustawienia na maszynie wirtualnej, którą chcesz ocenić, użyj opcji "Oblicz ponownie", aby zaktualizować ocenę przy użyciu najnowszych zmian.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Jak mogę odnaleźć środowisko wielodostępne w ocenie serwera Azure Migrate Server?

W przypadku oprogramowania VMware, jeśli masz środowisko udostępnione przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych jednej dzierżawy w ramach subskrypcji innej dzierżawy, Utwórz vCenter Server poświadczenia z dostępem tylko do tych maszyn wirtualnych, które chcesz odnajdywać. Następnie Użyj poświadczeń podczas uruchamiania odnajdywania na urządzeniu Azure Migrate.

W przypadku funkcji Hyper-V odnajdywanie używa poświadczeń hosta funkcji Hyper-V, jeśli maszyny wirtualne współużytkują ten sam host funkcji Hyper-V, ale nie ma możliwości oddzielenia odnajdywania.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Ile maszyn wirtualnych można odnaleźć przy użyciu jednego urządzenia migracji?

Możesz odkryć do 10 000 maszyn wirtualnych VMware oraz do 5 000 maszyn wirtualnych funkcji Hyper-V przy użyciu jednego urządzenia migracji.  Jeśli masz więcej maszyn w środowisku lokalnym, Dowiedz się, jak skalować oceny [funkcji Hyper-V](scale-hyper-v-assessment.md) i programu [VMware](scale-vmware-assessment.md) .

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Ocena serwera obsługuje ocenę serwerów fizycznych?

Nie, Azure Migrate obecnie nie obsługuje oceny serwerów fizycznych.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Czy Azure Migrate potrzebować vCenter Server odnajdywania środowiska VMware?

Tak, Azure Migrate musi vCenter Server odnajdywania środowiska VMware. Nie obsługuje odnajdywania hostów ESXi, które nie są zarządzane przez vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Różnica między używaniem Azure Migrate: Ocena serwera i zestaw narzędzi mapy?

Azure Migrate: Ocena serwera umożliwia ocenę migracji w celu ułatwienia migracji i oceny obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi firmy Microsoft do oceny i planowania (map)](https://www.microsoft.com/download/details.aspx?id=7826) zawiera inne funkcje, takie jak planowanie migracji nowszych wersji klienta systemu Windows i systemów operacyjnych serwera oraz śledzenie użycia oprogramowania. W tych scenariuszach Kontynuuj korzystanie z narzędzia MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Jak Azure Migrate: Ocena serwera różni się od Planista wdrażania usługi Azure Site Recovery?

Azure Migrate: Ocena serwera to narzędzie do planowania migracji. Planista wdrażania usługi Azure Site Recovery to narzędzie planowania odzyskiwania po awarii.

- **Migracja z oprogramowania VMware/Hyper-V do platformy Azure**: Jeśli zamierzasz przeprowadzić migrację serwerów lokalnych na platformę Azure, użyj Azure Migrate: Narzędzie do oceny serwera na potrzeby planowania migracji. Narzędzie ocenia obciążenia lokalne i zapewnia wskazówki, szczegółowe informacje i mechanizmy ułatwiające Migrowanie do platformy Azure. Po zakończeniu pracy z planem migracji możesz użyć narzędzi, takich jak Azure Migrate: Migracja serwera na platformę Azure.
- **Odzyskiwanie po awarii z oprogramowania VMware/Hyper-V do platformy Azure**: W przypadku odzyskiwania po awarii na platformie Azure przy użyciu Site Recovery należy użyć Site Recovery Planista wdrażania do planowania odzyskiwania po awarii. Site Recovery Planista wdrażania to Szczegółowa, Site Recoveryna Ocena środowiska lokalnego. Zawiera zalecenia, które są niezbędne przez Site Recovery do pomyślnego wykonywania operacji awaryjnych, takich jak replikacja, oraz przełączania do trybu failover maszyn wirtualnych.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Czy Azure Migrate obsługuje szacowanie kosztów oparte na Umowa Enterprise (EA)?

Azure Migrate obecnie nie obsługuje szacowania kosztów dla [Umowa Enterprise oferty](https://azure.microsoft.com/offers/enterprise-agreement-support/). Obejście polega na określeniu oferty płatność zgodnie z rzeczywistym użyciem, a ręcznie określić wartość procentową rabatu (mającą zastosowanie do subskrypcji) w polu "rabat" we właściwościach oceny.

  ![Rabat](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaka jest różnica między rozmiarem lokalnym a wymiarem opartym na wydajności?

- W przypadku ustalania rozmiarów lokalnych Azure Migrate nie uwzględnia danych wydajności maszyny wirtualnej. Rozmiary maszyn wirtualnych są zmieniane na podstawie konfiguracji lokalnej. — W przypadku ustalania rozmiarów na podstawie wydajności rozmiar jest oparty na danych użycia.
- Jeśli na przykład lokalna maszyna wirtualna ma 4 rdzenie i 8 GB pamięci z 50% użycia procesora CPU i 50% pamięci, jako że rozmiar lokalny zaleca użycie jednostki SKU maszyny wirtualnej platformy Azure z czterema rdzeniami i 8 GB pamięci. Ustalanie wielkości na podstawie wydajności, jednak zaleca użycie jednostki SKU maszyny wirtualnej dwóch rdzeni i 4 GB, ponieważ jest brana pod uwagę procent wykorzystania.
- Podobnie rozmiary dysku są zależne od dwóch właściwości oceny — kryterium ustalania wielkości i typu magazynu.
= Jeśli kryterium ustalania wielkości jest oparte na wydajności i typ magazynu to automatyczne, wartości IOPS i przepływność dysku są brane pod uwagę podczas identyfikowania docelowego typu dysku (Standard lub Premium).
- Jeśli kryterium ustalania wielkości jest oparte na wydajności, a typ magazynu to Premium, zalecany jest dysk w warstwie Premium. Jednostka SKU dysku Premium jest wybierana na podstawie rozmiaru dysku lokalnego. Ta sama logika jest używana do określania rozmiarów dysków, gdy kryterium ustalania wielkości jest w przypadku wielkości lokalnego, a typ magazynu to standardowa lub Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ ma historia wydajności i użycie percentylu na zaleceń dotyczących rozmiaru?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności.

- Azure Migrate zbiera historię wydajności maszyn lokalnych i używa jej do zaproponowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- Urządzenie nieustannie profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund. Urządzenie podsumowuje 20-sekundowe próbki i tworzy jeden punkt danych co 15 minut. Aby utworzyć jeden punkt danych, urządzenie wybiera szczytową wartość z wszystkich 20-sekundowych próbek i wysyła ją do platformy Azure.
- Podczas tworzenia oceny na platformie Azure (w oparciu o czas trwania wydajności i wartość percentylu historii wydajności) Azure Migrate oblicza wartość efektywnego wykorzystania i używa jej do ustalania rozmiarów.
- Na przykład, jeśli ustawisz czas trwania wydajności na jeden dzień, a wartość percentylu na 95 percentyl, Azure Migrate użyje 15 minut przykładowych punktów wysyłanych przez moduł zbierający w ciągu ostatniego dnia sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentyl jako efektywne wykorzystanie.
- Wartość percentylu używany 95. zapewnia ignorowanie wszelkich wartości odstających, co może wystąpić w przypadku użycia 99 percentylu. Jeśli chcesz wybrać szczytowe użycie w danym okresie i nie pomijać żadnych elementów odstających, wybierz 99. percentyl.

### <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Wizualizacja zależności umożliwia ocenę grup maszyn wirtualnych na potrzeby migracji z większą pewnością. Powoduje to sprawdzenie zależności maszyn przed uruchomieniem oceny. Wizualizacja zależności pomaga upewnić się, że nic nie zostało pozostawione, i uniknąć nieoczekiwanego przestoju podczas migracji na platformę Azure. Aby włączyć wizualizację zależności, Azure Migrate wykorzystuje Service Map rozwiązanie w dziennikach Azure Monitor.

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Czy muszę zapłacić za korzystanie z funkcji wizualizacji zależności?

Nie. [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Czy muszę zainstalować wszystko dla wizualizacji zależności?

Aby można było używać wizualizacji zależności, należy pobrać i zainstalować agentów na każdej maszynie lokalnej, którą chcesz oszacować.

- [Program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musi zostać zainstalowany na każdej maszynie.
- [Agent zależności](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musi być zainstalowany na każdej maszynie.
- Ponadto, jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować bramę Log Analytics na nich.

Te agenci nie są potrzebne, chyba że używasz wizualizacji zależności.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy mogę użyć istniejącego obszaru roboczego dla wizualizacji zależności?

Tak, możesz dołączyć istniejący obszar roboczy do projektu migracji i wykorzystać go do wizualizacji zależności. [Dowiedz się więcej](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować wizualizacji zależności. Jednak ponieważ Azure Migrate używa Service Map do wizualizacji zależności, można użyć [interfejsów API REST Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) , aby uzyskać zależności w formacie JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak można zautomatyzować instalację Microsoft Monitoring Agent (MMA) i agenta zależności?

[Użyj tego skryptu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) do instalacji agentów. [Postępuj zgodnie z tymi instrukcjami](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) , aby zainstalować MMA przy użyciu wiersza polecenia lub automatyzacji. W przypadku usługi MMA Skorzystaj z [tego skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oprócz skryptów można używać narzędzi wdrażania, takich jak System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) itp., aby wdrożyć agentów.

### <a name="what-operating-systems-are-supported-by-mma"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- [Zapoznaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) się z listą systemów operacyjnych Windows obsługiwanych przez MMA.
- [Zapoznaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) się z listą systemów operacyjnych Linux obsługiwanych przez MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Jakie systemy operacyjne są obsługiwane przez agenta zależności?

[Przejrzyj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) systemy operacyjne Windows obsługiwane przez agenta zależności.
[Zapoznaj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) się z listą systemów operacyjnych Linux obsługiwanych przez agenta zależności.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Czy mogę wizualizować zależności w Azure Migrate przez więcej niż godzinę?
Nie, można wizualizować zależności przez maksymalnie godzinę. Możesz wrócić do konkretnej daty w historii, do ostatniego miesiąca, ale maksymalny czas trwania wizualizacji to godzina. Na przykład można użyć czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednogodzinnego okna. Można jednak użyć dzienników Azure Monitor do [wykonywania zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Czy dla grup mających więcej niż 10 maszyn wirtualnych jest obsługiwana Wizualizacja zależności?
Możesz [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) z maksymalnie 10 maszynami wirtualnymi. Jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy podzielenie grupy w mniejszej liczbie, a następnie wizualizację zależności.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Jak Azure Migrate: Migracja serwera różni się od Azure Site Recovery?

Azure Migrate: Migracja serwera korzysta z aparatu replikacji Site Recovery na potrzeby migracji maszyn wirtualnych VMware, migracji maszyn wirtualnych funkcji Hyper-V i migracji serwerów fizycznych na platformę Azure. Opcja bezagentowego migrowania maszyn wirtualnych VMware jest natywnie wbudowana w ramach migracji serwera.

## <a name="next-steps"></a>Następne kroki
Przeczytaj [omówienie Azure Migrate](migrate-services-overview.md)
