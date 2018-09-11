---
title: Usługa Azure Migrate — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Migrate adresów
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/03/2018
ms.author: snehaa
ms.openlocfilehash: f4ce2130b18b183f633c649f98fc1add30753a27
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296011"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Usługa Azure Migrate — często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Migrate. Jeśli masz dodatkowe pytania po przeczytaniu tego artykułu, opublikuj je na [forum usługi Azure Migrate](http://aka.ms/AzureMigrateForum).

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

**Odzyskiwanie po awarii z programu VMware/funkcji Hyper-V na platformę Azure**: Jeśli użytkownik zamierza wykonać odzyskiwanie po awarii (DR) na platformie Azure przy użyciu usługi Azure Site Recovery (Usługa Site Recovery), na użytek planista wdrażania usługi Site Recovery planowania odzyskiwania po awarii. Planista wdrażania usługi Site Recovery wykonuje głęboką, specyficzne dla usługi ASR oceny środowiska lokalnego. Zapewnia zaleceń, które są wymagane przez usługę Site Recovery dla pomyślnego operacji odzyskiwania po awarii, takich jak replikacja, failover maszyn wirtualnych.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Które regiony platformy Azure są obsługiwane przez usługę Azure Migrate?

Usługa Azure Migrate obsługuje obecnie wschodnie stany USA i zachodnie stany USA jako lokalizacje projektu migracji. Należy pamiętać, że mimo, że projekty migracji można tworzyć tylko w zachodnio-środkowe stany USA i wschodnie stany USA, można nadal oceniać gotowość maszyn w celu [wielu lokalizacjach docelowych](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Lokalizacja projektu jest używana wyłącznie do przechowywania odnalezione dane.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Jak lokacją lokalną łączyć się z usługi Azure Migrate?

Połączenie może być za pośrednictwem Internetu lub korzystać z usługi ExpressRoute w publicznej komunikacji równorzędnej.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Z maszyny Wirtualnej można zabezpieczyć. OVA szablonu?

Dodatkowe składniki (na przykład oprogramowanie antywirusowe) można dodać do. OVA tak długo, jak komunikacja i reguł zapory wymagane dla usługi Azure Migrate urządzenia do pracy pozostało jako jest szablon.   

## <a name="discovery-and-assessment"></a>Odnajdywanie i ocena

### <a name="what-data-is-collected-by-azure-migrate"></a>Jakie dane są zbierane przez usługę Azure Migrate?

Usługa Azure Migrate obsługuje dwa rodzaje odnajdywania, odnajdywanie na podstawie urządzenia i odnajdywanie na podstawie agenta.
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

Odnajdywanie oparte na agentach opcja jest dostępna na podstawie odnajdywania oparte na urządzeniu i pomaga klientom [wizualizacja zależności](how-to-create-group-machine-dependencies.md) lokalnych maszyn wirtualnych. Agenci zależności zbierać szczegółowe informacje, takie jak nazwy FQDN, system operacyjny, IP adresu, adres MAC, procesów uruchomionych wewnątrz maszyny Wirtualnej i połączenia wychodzące/przychodzące TCP z maszyny Wirtualnej. Odnajdywanie oparte na agentach jest opcjonalny, a użytkownik może nie zainstalować agentów, jeśli nie chcesz wizualizacja zależności maszyn wirtualnych.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Będzie mieć żadnego wpływu wydajności na przeanalizowany środowisko hosta ESXi?

Ponieważ firma Microsoft zbiera informacje za pośrednictwem serwera vCenter, nie jest bez wpływu na wydajność na hostach ESXi. Nawet w przypadku serwera vCenter jest bliski zeru negatywny wpływ na wydajność.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie jest zebranych danych przechowywanych i na jak długo?

Dane zebrane przez urządzenie modułu zbierającego znajduje się w lokalizacji platformy Azure, które należy określić podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w ramach subskrypcji firmy Microsoft i zostanie usunięta, gdy użytkownik usuwa projekt usługi Azure Migrate.

Dla wizualizacji zależności po zainstalowaniu agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym pakietu OMS utworzonego w subskrypcji użytkownika. Usuwanie danych odbywa się podczas usuwania obszaru roboczego pakietu OMS w ramach subskrypcji. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i przesyłanych?

Tak, zebrane dane są szyfrowane zarówno magazynowanych i przesyłanych. Metadane zbierane przez urządzenie są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem sieci internet za pośrednictwem protokołu https. Zebrane metadane są przechowywane w [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) i [usługi Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft i jest szyfrowane, gdy.

Dane zbierane przez agentów zależności jest również zaszyfrowane na tranzyt (https bezpiecznej channel) i są przechowywane w obszarze roboczym usługi Log Analytics w subskrypcji użytkownika. Jest ona również są szyfrowane w stanie spoczynku.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak moduł zbierający komunikacji przy użyciu programu vCenter Server i usługi Azure Migrate?

Urządzenie modułu zbierającego łączy z programem vCenter Server (port 443) przy użyciu poświadczeń dostarczonych przez użytkownika na urządzeniu. Wykonuje kwerendę w programie vCenter Server przy użyciu interfejs PowerCLI programu VMware można zebrać metadanych informacje o maszynach wirtualnych zarządzanych przez program vCenter Server. Zbiera dane konfiguracji obu maszyn wirtualnych (rdzeni, pamięć, dyski, karta sieciowa itp.) oraz historii wydajności każdej maszyny wirtualnej dla ostatniego miesiąca z programu vCenter Server. Zebrane metadane są następnie wysyłane do usługi Azure Migrate (za pośrednictwem sieci internet za pośrednictwem protokołu https) dla oceny. [Dowiedz się więcej](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Czy można połączyć tego samego urządzenia modułu zbierającego, aby wiele serwerów vCenter?

Tak, urządzenie jeden moduł zbierający może służyć do wykrywania vCenter wielu serwerów, ale nie jednocześnie. Musisz uruchomić odnajdywania, jeden po drugim.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Jest. Szablon OVA używane przez usługę Site Recovery jest zintegrowana z usługą. OVA używane przez usługę Azure Migrate?

Obecnie nie ma żadnych integracji. . Szablon OVA w usłudze Site Recovery jest używany do konfigurowania serwera konfiguracji Usługa Site Recovery w celu replikacji serwera fizycznego/VMware, maszyny Wirtualnej. . OVA używane przez usługę Azure Migrate służy do odnalezienia maszyn wirtualnych VMware, zarządzane przez serwer vCenter, na potrzeby oceny migracji.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Czy mogę zmienić mój rozmiar maszyny. Czy można ponownie uruchomić ocenę?

Jeśli zmienisz ustawienia na maszynie Wirtualnej, którą chcesz ocenić, wyzwalacz Dowiedz się ponownie przy użyciu urządzenia modułu zbierającego. W urządzeniu, należy użyć **ponownie uruchom zbieranie** opcję, aby to zrobić. Po zakończeniu zbierania wybierz opcję **Oblicz ponownie** dla oceny w portalu, aby pobrać zaktualizowane wyniki oceny.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak można odnaleźć środowiska z wieloma dzierżawami w usłudze Azure Migrate?

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywanie maszyn wirtualnych z jednej dzierżawy w innej dzierżawie subskrypcji, można użyć pola zakresu w urządzenia modułu zbierającego do określania zakresu odnajdywania. Jeśli dzierżawy współużytkują hostów, Utwórz poświadczenie, które ma dostęp tylko do odczytu do tylko maszyny wirtualne należące do określonej dzierżawy, a następnie korzystać z poświadczenia urządzenia modułu zbierającego i określ zakres jako hosta w celu odnajdywania. Alternatywnie można także utworzyć foldery, w programie vCenter Server (Załóżmy, że folder1 dla tenant1 folder2 dla tenant2), w obszarze udostępnionego hosta, Przenieś maszyny wirtualne dla tenant1 do folder1 i tenant2 do folder2 i zakres odnajdywania w module zbierającym w związku z tym określając odpowiedni folder.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Ile maszyn wirtualnych można znaleźć w projekcie migracji?

Może odnajdywać 1500 maszyn wirtualnych w projekcie migracji. Jeśli masz więcej maszyn w środowisku lokalnych [więcej](how-to-scale-assessment.md) o jak odkryjesz dużym środowisku, w usłudze Azure Migrate.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Usługa Azure Migrate wsparcia podmiotu trzeciego podstawie Enterprise Agreement (EA) kosztuje szacowania?

Usługa Azure Migrate nie obsługuje obecnie Szacowanie kosztów dla [oferty z umową Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Obejście polega na określić płatność za rzeczywiste użycie jako oferty i ręcznie określania procent rabatu (dotyczy subskrypcji) w polu "Zniżka" we właściwościach oceny.

  ![Rabat](./media/resources-faq/discount.png)

## <a name="dependency-visualization"></a>Wizualizacja zależności

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Czy muszę płacić za używanie funkcji wizualizacji zależności?

Usługa Azure Migrate jest dostępna bez dodatkowych opłat. Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Czy można używać istniejącego obszaru roboczego w celu wizualizacji zależności?

Usługa Azure Migrate nie obsługuje używania istniejącego obszaru roboczego dla wizualizacji zależności, jednak program Microsoft Monitoring Agent (MMA) obsługuje multihosting i umożliwia wysyłanie danych do wielu obszarów roboczych. Dlatego jeśli masz już agentów wdrożona i skonfigurowana do obszaru roboczego, możesz korzystać z obsługą wielu regionów w agenta MMA i skonfigurować go do obszaru roboczego usługi Azure Migrate (oprócz istniejącego obszaru roboczego) i upewnij się, że działa. [W tym miejscu](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) jest blogu w sposób umożliwiania multihosting agenta MMA.

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [usługi Azure Migrate — Przegląd](migrate-overview.md)
- Dowiedz się, jak [odnajdywanie i ocenianie](tutorial-assessment-vmware.md) środowisku programu VMware
