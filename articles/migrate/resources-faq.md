---
title: Azure Migrate — często zadawane pytania (FAQ) | Microsoft Docs
description: Adresy często zadawanych pytań dotyczących Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 03651ecb073d02a373c434b8cb55bdafec6d142a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142215"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate. Jeśli po przeczytaniu tego artykułu masz dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Które usługi Azure lokalizacje geograficzne Azure Migrate obsługiwać?

Zobacz [listę dla programu VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i [listę funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaka jest różnica między Azure Migrate i Azure Site Recovery?

Azure Migrate udostępnia scentralizowane centrum umożliwiające rozpoczęcie migracji, wykonanie i śledzenie odnajdywania i oceny maszyn oraz obciążeń oraz wykonywanie i śledzenie migracji maszyn i obciążeń na platformę Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) to rozwiązanie odzyskiwania po awarii. Migracja serwera Azure Migrate używa Azure Site Recovery w zapleczu, aby umożliwić migrację do migracji na maszynach lokalnych.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Urządzenie Azure Migrate (serwery VMware/fizyczne)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak urządzenie Azure Migrate nawiązuje połączenie z platformą Azure?

Połączenie może być nawiązywane za pośrednictwem Internetu lub można użyć usługi Azure ExpressRoute z usługą komunikacji równorzędnej (Public/Microsoft).

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Jakie są wymagania dotyczące łączności sieciowej Azure Migrate oceny i migracji serwera?

Aby uzyskać informacje o adresach URL i portach, które są konieczne do umożliwienia Azure Migrate komunikowania się z platformą Azure, zobacz macierze obsługi oprogramowania [VMware](migrate-support-matrix-vmware.md) i [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Czy mogę wzmocnić maszynę wirtualną urządzenia skonfigurowaną przy użyciu szablonu?

Do szablonu można dodawać składniki (na przykład program antywirusowy), o ile pozostawisz reguły dotyczące komunikacji i zapory wymagane przez urządzenie Azure Migrate.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Jakie dane są zbierane przez urządzenie Azure Migrate?

Szczegółowe informacje o danych zbieranych przez urządzenie Azure Migrate można znaleźć [w dokumentacji Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Czy istnieje jakikolwiek wpływ na wydajność analizowanego środowiska VMware lub funkcji Hyper-V?

Lokalne profile urządzeń Azure Migrate są ciągle mierzone w celu mierzenia danych wydajności maszyny wirtualnej. To Profilowanie nie ma prawie wpływu na hosty funkcji Hyper-V/ESXi ani na vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie są przechowywane zebrane dane i jak długo?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure wybranej podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w subskrypcji firmy Microsoft i usuwane po usunięciu projektu Azure Migrate.

W przypadku wizualizacji zależności, Jeśli instalujesz agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym Log Analytics utworzonym w ramach subskrypcji platformy Azure. Te dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Wizualizacja zależności](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Jaka ilość danych jest przekazywana przez urządzenie Azure Migrate podczas ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate różni się w zależności od kilku parametrów. Aby zapewnić zrozumienie woluminu: projekt Azure Migrate zawierający 10 maszyn (z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB dziennie. Ta wartość to przybliżenie. Zmieniają się w zależności od liczby punktów danych dla kart sieciowych i dysków. (Zwiększenie liczby wysyłanych danych jest nieliniowe, jeśli liczba maszyn, kart sieciowych lub dysków rośnie).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i podczas przesyłania?

Tak, oba. Metadane są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem Internetu za pośrednictwem protokołu HTTPS. Metadane są przechowywane w bazie danych [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) i w [usłudze Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w stanie spoczynku.

Dane zbierane przez agentów zależności są również szyfrowane w trakcie przesyłania (Secure HTTPS). Jest ona przechowywana w obszarze roboczym Log Analytics w ramach subskrypcji. Jest on także szyfrowany w spoczynku.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Jak urządzenie Azure Migrate komunikuje się z vCenter Server i usługą Azure Migrate?

Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia. Używa programu VMware PowerCLI do wykonywania zapytań vCenter Server, aby zbierać metadane dotyczące maszyn wirtualnych zarządzanych przez vCenter Server. Zbiera dane konfiguracyjne dotyczące maszyn wirtualnych (rdzeni, pamięci, dysków, kart sieciowych itp.), a także historię wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca. Zebrane metadane są następnie wysyłane do oceny serwera Azure Migrate (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Czy można połączyć to samo urządzenie z wieloma wystąpieniami vCenter Server?

Nie. Istnieje mapowanie jeden do jednego między urządzeniem i vCenter Server. Jeśli konieczne jest odnajdywanie maszyn wirtualnych w wielu wystąpieniach vCenter Server, należy wdrożyć wiele urządzeń.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Zmieniono rozmiar maszyny. Czy mogę ponownie uruchomić ocenę?

Urządzenie Azure Migrate ciągle zbiera informacje o środowisku lokalnym. Jednak Ocena to migawka lokalnych maszyn wirtualnych do określonego momentu. Jeśli zmienisz ustawienia na maszynie wirtualnej, która ma zostać oceniona, użyj opcji Oblicz ponownie, aby zaktualizować ocenę przy użyciu najnowszych zmian.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Jak przeprowadzić odnajdywanie w środowisku wielodostępnym w ramach oceny Azure Migrate Server?

W przypadku oprogramowania VMware, jeśli masz środowisko udostępnione w dzierżawach i nie chcesz odnajdywać maszyn wirtualnych jednej dzierżawy w ramach subskrypcji innej dzierżawy, Utwórz vCenter Server poświadczenia, które mogą uzyskiwać dostęp tylko do maszyn wirtualnych, które chcesz odnajdywać. Następnie użyj tych poświadczeń po rozpoczęciu odnajdywania na urządzeniu Azure Migrate.

W przypadku funkcji Hyper-V odnajdywanie używa poświadczeń hosta funkcji Hyper-V. Jeśli maszyny wirtualne korzystają z tego samego hosta funkcji Hyper-V, nie ma możliwości oddzielenia odnajdywania.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Ile maszyn wirtualnych można znaleźć w jednym urządzeniu migracji?

Możesz odkryć do 10 000 maszyn wirtualnych VMware oraz do 5 000 maszyn wirtualnych funkcji Hyper-V z jednym urządzeniem migracji. Jeśli masz więcej maszyn w środowisku lokalnym, Dowiedz się, jak skalować oceny [funkcji Hyper-V](scale-hyper-v-assessment.md) i programu [VMware](scale-vmware-assessment.md) .

## <a name="azure-migrate-server-assessment"></a>Azure Migrate oceny serwera

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Czy Ocena serwera Azure Migrate obsługuje ocenę serwerów fizycznych?

Nie, Azure Migrate obecnie nie obsługuje oceny serwerów fizycznych.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Czy Azure Migrate potrzebować vCenter Server do przeprowadzenia odnajdywania w środowisku programu VMware?

Tak, Azure Migrate wymaga vCenter Server do przeprowadzenia odnajdywania w środowisku programu VMware. Nie obsługuje odnajdywania hostów ESXi, które nie są zarządzane przez vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między oceną Azure Migrate serwera a zestawem narzędzi mapy?

Azure Migrate oceny serwera umożliwia ocenę migracji w celu ułatwienia migracji i oceny obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi firmy Microsoft do oceny i planowania (map)](https://www.microsoft.com/download/details.aspx?id=7826) pomaga z innymi zadaniami, takimi jak planowanie migracji nowszych wersji klienta systemu Windows i systemów operacyjnych serwera i śledzenia użycia oprogramowania. W tych scenariuszach Kontynuuj korzystanie z narzędzia MAP Toolkit.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Jaka jest różnica między oceną Azure Migrate serwera a Planista wdrażania usługi Azure Site Recovery?

Azure Migrate oceny serwera to narzędzie do planowania migracji. Planista wdrażania usługi Azure Site Recovery to narzędzie do planowania odzyskiwania po awarii.

**Migracja z oprogramowania VMware/Hyper-V do platformy Azure**: Jeśli planujesz migrację serwerów lokalnych na platformę Azure, użyj oceny serwera Azure Migrate na potrzeby planowania migracji. Ocenia obciążenia lokalne oraz udostępnia wskazówki, szczegółowe informacje i narzędzia ułatwiające Migrowanie serwerów do platformy Azure. Po przygotowaniu się do planu migracji możesz użyć narzędzi, takich jak Azure Migrate migracji serwera, aby przeprowadzić migrację maszyn na platformę Azure.

**Odzyskiwanie po awarii z oprogramowania VMware/Hyper-V do platformy Azure**: W przypadku odzyskiwania po awarii na platformie Azure za pośrednictwem Site Recovery Użyj Planista wdrażania Site Recovery w celu zaplanowania. Site Recovery Planista wdrażania to Szczegółowa, Site Recoveryna Ocena środowiska lokalnego. Zawiera zalecenia, które są niezbędne przez Site Recovery do pomyślnego wykonywania operacji awaryjnych, takich jak replikacja i tryb failover maszyn wirtualnych.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Czy Azure Migrateuje szacowanie kosztów dla programu Umowa Enterprise (EA)?

Azure Migrate obecnie nie obsługuje szacowania kosztów dla [programu Umowa Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/). Obejście polega na określeniu **oferty** z **opcją płatność zgodnie z rzeczywistym** użyciem w polu **rabatu** i ręcznym określeniu procentu rabatu (mającego zastosowanie do subskrypcji) we właściwości oceny:

  ![Właściwości oceny](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaka jest różnica między rozmiarem lokalnym a wymiarem opartym na wydajności?

W przypadku ustalania rozmiarów lokalnych Azure Migrate nie uwzględnia danych wydajności maszyny wirtualnej. Rozmiary maszyn wirtualnych są zmieniane na podstawie konfiguracji lokalnej. W przypadku ustalania rozmiarów na podstawie wydajności rozmiar jest oparty na danych użycia.

Rozważmy na przykład lokalną maszynę wirtualną, która ma 4 rdzenie i 8 GB pamięci na 50% użycia procesora CPU i 50% wykorzystania pamięci. Dla tej maszyny wirtualnej, w zależności od wielkości lokalnego, zaleca się użycie jednostki SKU maszyny wirtualnej platformy Azure, która ma cztery rdzenie i 8 GB pamięci. Ustalanie wielkości na podstawie wydajności zaleca użycie jednostki SKU maszyny wirtualnej, która ma dwa rdzenie i 4 GB pamięci, ponieważ jest brana pod uwagę procent użycia.

Podobnie rozmiary dysku są zależne od dwóch właściwości oceny: Określanie rozmiarów kryteriów i typów magazynów. Jeśli kryteria ustalania wielkości są oparte na wydajności, a typ magazynu to automatyczne, Azure Migrate uwzględnia wartości IOPS i przepływności dysku, gdy identyfikuje docelowy typ dysku (standardowa lub Premium).

Jeśli kryteria ustalania wielkości są oparte na wydajności, a typ magazynu to Premium, Azure Migrate zaleca dysk w warstwie Premium. Jednostka SKU dysku Premium jest wybierana na podstawie rozmiaru dysku lokalnego. Ta sama logika jest używana do określania rozmiarów dysków, gdy kryterium ustalania wielkości jest zgodne z rozmiarem lokalnym, a typ magazynu to standardowa lub Premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Jaki wpływ ma zaleceń dotyczących historii wydajności i użycia?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności.

Azure Migrate zbiera historię wydajności maszyn lokalnych i używa jej do zaproponowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.

Urządzenie nieustannie profiluje środowisko lokalne, aby gromadzić dane dotyczące użycia w czasie rzeczywistym co 20 sekund. Urządzenie zbiera 20 sekund próbek i tworzy jeden punkt danych na każde 15 minut. Aby utworzyć punkt danych, urządzenie wybiera wartość szczytową ze wszystkich próbek 20-sekundowych. Urządzenie wysyła ten punkt danych do platformy Azure.

Podczas tworzenia oceny na platformie Azure (na podstawie wartości percentylu wydajności i historii wydajności) Azure Migrate oblicza wartość efektywnego wykorzystania i używa jej do ustalania rozmiarów.

Załóżmy na przykład, że ustawisz czas trwania wydajności na jeden dzień, a wartość percentylu to 95 percentyl. Azure Migrate sortuje 15-minutowe punkty próbkowania wysyłane przez moduł zbierający dla ostatniego dnia w kolejności rosnącej i wybiera wartość percentylu używany 95. jako efektywne wykorzystanie.

Wartość percentylu używany 95. gwarantuje ignorowanie wszelkich wartości odstających. Jeśli używasz 99 percentylu, można uwzględnić wartości odstających. Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, wybierz 99 percentyl.

### <a name="what-is-dependency-visualization"></a>Co to jest Wizualizacja zależności?

Wizualizacja zależności umożliwia ocenę grup maszyn wirtualnych na potrzeby migracji z większą pewnością. Powoduje to sprawdzenie zależności maszyn przed uruchomieniem oceny. Wizualizacja zależności pomaga upewnić się, że nic nie zostanie pozostawione, aby uniknąć nieoczekiwanego przestoju podczas migracji na platformę Azure. Azure Migrate używa rozwiązania Service Map w dziennikach Azure Monitor w celu włączenia wizualizacji zależności.

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Czy muszę uiścić użycie wizualizacji zależności?

Nie. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Czy muszę zainstalować wszystko dla wizualizacji zależności?

Aby można było używać wizualizacji zależności, należy pobrać i zainstalować agentów na każdej maszynie lokalnej, którą chcesz oszacować.

Na każdej maszynie należy zainstalować następujących agentów:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować bramę Log Analytics na nich.

Te agenci nie są potrzebne, chyba że używasz wizualizacji zależności.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy mogę użyć istniejącego obszaru roboczego dla wizualizacji zależności?

Tak, możesz dołączyć istniejący obszar roboczy do projektu migracji i użyć go do wizualizacji zależności. Aby uzyskać więcej informacji, zobacz sekcję "jak to działa" w artykule dotyczącym [wizualizacji zależności](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>Czy mogę wyeksportować raport wizualizacji zależności?

Nie, nie można wyeksportować wizualizacji zależności. Ale ponieważ Azure Migrate używa Service Map do wizualizacji zależności, można użyć [interfejsu API REST Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) do uzyskania zależności w formacie JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Jak można zautomatyzować instalację Microsoft Monitoring Agent (MMA) i agenta zależności?

Ten [skrypt służy do instalowania agenta zależności](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Postępuj zgodnie [z tymi instrukcjami, aby zainstalować MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) za pomocą wiersza polecenia lub automatyzacji. W przypadku MMA Użyj [tego skryptu](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oprócz skryptów można także użyć narzędzi wdrażania, takich jak System Center Configuration Manager i [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) , aby wdrożyć agentów.

### <a name="what-operating-systems-are-supported-by-mma"></a>Jakie systemy operacyjne są obsługiwane przez MMA?

- Wyświetl listę [systemów operacyjnych Windows obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Wyświetl listę [systemów operacyjnych Linux obsługiwanych przez MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Jakie systemy operacyjne są obsługiwane przez agenta zależności?

Zapoznaj się z listą [systemów operacyjnych Windows i Linux obsługiwanych przez Azure monitor dla maszyn wirtualnych](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Czy mogę wizualizować zależności w Azure Migrate przez więcej niż godzinę?
Nie. Możesz wizualizować zależności przez maksymalnie godzinę. Można wrócić do konkretnej daty w historii, o ile nie jest to miesiąc, ale maksymalny czas trwania wizualizacji to godzina. Na przykład można użyć czasu trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednogodzinnego okna. Można jednak użyć dzienników Azure Monitor do [wykonywania zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Czy można używać wizualizacji zależności dla grup, które zawierają więcej niż 10 maszyn wirtualnych?
Możesz [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , które zawierają maksymalnie 10 maszyn wirtualnych. Jeśli masz grupę z więcej niż 10 maszyn wirtualnych, zalecamy poddzielenie grupy na mniejsze grupy, a następnie wizualizację zależności.

## <a name="azure-migrate-server-migration"></a>Migracja serwera Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Jaka jest różnica między migracją Azure Migrate serwera i Azure Site Recovery?

Migracja serwera Azure Migrate korzysta z aparatu replikacji Site Recovery do migracji maszyn wirtualnych VMware, migracji maszyn wirtualnych funkcji Hyper-V i migracji serwerów fizycznych na platformę Azure. Opcja bezagentowego migrowania maszyn wirtualnych VMware jest natywnie wbudowana w migrację serwera.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
