---
title: Migrowanie Azure — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania na migrację Azure adresów
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: snehaa
ms.openlocfilehash: a18cab73a019039bf5e5829ad1faa4b8f1a70391
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209949"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Migrowanie Azure — często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure migracji. Jeśli masz dodatkowe pytania po przeczytaniu tego artykułu, opublikuj je na [forum migracji Azure](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Ogólne

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Jak migrować Azure różni się od usługi Azure Site Recovery?

Migracji Azure jest usługą ocenę, która pomaga wykrywać obciążeń lokalnych oraz planowanie migracji do usługi Azure. [Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), wraz z stanowi rozwiązanie odzyskiwania po awarii, ułatwia migrację obciążeń lokalnych do maszyn wirtualnych IaaS na platformie Azure. 

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Jak migrować Azure różni się od Azure lokacji odzyskiwania wdrożenia Planistę?

Azure migracji jest migracji za pomocą narzędzia do planowania i Azure lokacji odzyskiwania wdrożenia Planistę odzyskiwania awaryjnego (DR), narzędzia do planowania.

**Migracja z programu VMware do platformy Azure**: Jeśli planujesz migrację obciążeń lokalnych do platformy Azure, użyj migracji Azure dotyczące planowania migracji. Azure migracji ocenia obciążeń lokalnych i zawiera wskazówki, szczegółowych informacji i mechanizmów ułatwi migracji do usługi Azure. Po zakończeniu plan migracji, można użyć usług, takich jak usługi Azure Site Recovery i usługa migracji bazy danych Azure do migracji maszyn na platformie Azure.

**Migracja z funkcji Hyper-V w systemie Azure**: Migrowanie Azure aktualnie obsługuje tylko oceny maszyn wirtualnych VMware do migracji do usługi Azure. Obsługa funkcji Hyper-V znajduje się na plan dla migracji Azure. W międzyczasie można użyć automatycznego wdrożenia Planistę. Po włączeniu obsługi funkcji Hyper-V w migracji Azure można migrować Azure dotyczące planowania migracji obciążeń funkcji Hyper-V.

**Odzyskiwanie po awarii z VMware/funkcji Hyper-V do platformy Azure**: Jeśli planowane jest wykonanie odzyskiwania awaryjnego (DR) na platformie Azure za pomocą odzyskiwania lokacji Azure (ASR), na użytek automatycznego wdrożenia Planistę planowanie odzyskiwania po awarii. Planowanie wdrożenia usługi ASR ma bezpośredni, specyficzne dla usługi ASR oceny środowiska lokalnego. Zawiera zalecenia, które są wymagane przez ASR dla pomyślnej operacji odzyskiwania po awarii, takich jak replikacji, pracy awaryjnej maszyn wirtualnych.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Czy migracja Azure potrzebuje serwerowi odnajdywania w środowisku programu VMware vCenter?

Tak, migracja Azure wymaga serwera odnajdywania w środowisku programu VMware vCenter. Nie obsługuje odnajdowania hostach ESXi, które nie są zarządzane przez oprogramowanie vCenter Server.

## <a name="discovery"></a>Odnajdowanie

### <a name="what-data-is-collected-by-azure-migrate"></a>Jakie dane są zbierane przez migrację Azure?

Migracji Azure obsługuje dwa rodzaje odnajdywania, oparte na urządzeniu odnajdywania i odnajdowania opartego na agenta.
Odnajdywanie oparte na urządzeniu zbiera metadane dotyczące maszyn wirtualnych lokalnie, poniżej przedstawiono pełną listę metadanych zebrany przez urządzenia:

**Dane konfiguracji maszyny wirtualnej**
- Nazwa wyświetlana maszyny Wirtualnej (w programie vCenter)
- Ścieżka spisu maszyny Wirtualnej (host/klaster/folder w programie vCenter)
- Adres IP
- Adres MAC
- System operacyjny
- Liczba rdzeni, dysków, kart sieciowych
- Rozmiar pamięci, rozmiarów dysków

**Dane o wydajności maszyny wirtualnej**
- Użycie procesora CPU
- Użycie pamięci
- Dla każdego dysku dołączony do maszyny Wirtualnej:
  - Przepływność odczytu z dysku
  - Przepływność zapisu dysku
  - Operacje na sekundę odczytu dysku
  - Zapisu dysku na sekundę
- Dla każdej karty sieciowej podłączony do maszyny Wirtualnej:
  - Sieci w
  - Sieci limit

Odnajdywanie agenta na podstawie opcja jest dostępna na górze odnajdywania oparte na urządzeniu i ułatwia klientom [wizualizowanie zależności](how-to-create-group-machine-dependencies.md) lokalnych maszyn wirtualnych. Agentów zależności zbieranie szczegóły, takie jak nazwy FQDN, systemu operacyjnego, IP address, adres MAC, procesów uruchomionych wewnątrz maszyny Wirtualnej i połączeń TCP przychodzącego/wychodzącego z maszyny Wirtualnej. Odnajdywanie agenta na podstawie jest opcjonalna i użytkownik może zainstalować agentów nie, jeśli nie chcesz wizualizowanie zależności maszyn wirtualnych.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Gdzie jest zebrane dane przechowywane i jak długo?

Dane zebrane przez moduł zbierający urządzenie znajduje się w lokalizacji platformy Azure, które można określić podczas tworzenia projektu migracji. Dane są bezpiecznie przechowywane w ramach subskrypcji firmy Microsoft i są usuwane, gdy użytkownik usuwa Azure migracji projektu.

Dla wizualizacji zależności zainstalowanie agentów na maszynach wirtualnych, dane zbierane przez agentów zależności są przechowywane w Stanach Zjednoczonych, w obszarze roboczym pakietu OMS utworzona w subskrypcji użytkownika. Usuwanie danych odbywa się po obszar roboczy OMS w subskrypcji jej lub jego usunięcia przez użytkownika. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak moduł zbierający komunikacji z serwerem vCenter i usługę Azure migracji?

Moduł zbierający urządzenie łączy się programu vCenter Server (port 443) przy użyciu poświadczeń dostarczonych przez użytkownika w urządzeniu. Zapytanie programu vCenter Server za pomocą VMware PowerCLI do zbierania metadanych dotyczących maszyn wirtualnych zarządzanych przez serwer vCenter. Zbiera dane konfiguracji obu maszyn wirtualnych (rdzenie, pamięci, dysków, kart itp.) oraz wydajności historię każdej maszyny Wirtualnej przez ostatni miesiąc z vCenter Server. Zebranych metadanych są następnie wysyłane do usługi Azure migracji (za pośrednictwem Internetu za pośrednictwem protokołu https) do oceny. [Dowiedz się więcej](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i przesyłanych?

Tak, zebranych danych jest szyfrowany przy rest i podczas przesyłania. Metadane zebrany przez urządzenia jest bezpiecznie wysyłane do usługi Azure migracji za pośrednictwem Internetu za pośrednictwem protokołu https. Zebrane metadane są przechowywane w [DB rozwiązania Cosmos](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) i [magazynu obiektów blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w ramach subskrypcji firmy Microsoft i jest szyfrowane, gdy.

Dane zebrane przez agentów zależności jest również zaszyfrowane w przesyłania (https bezpiecznego kanału) i są przechowywane w obszarze roboczym analizy dzienników w subskrypcji użytkownika. Również są szyfrowane w stanie spoczynku.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak mogą odnajdować środowiska z wieloma dzierżawami w migracji Azure?

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych z jednego dzierżawcy w subskrypcji innej dzierżawy, można użyć pola zakresu w urządzeniu modułu zbierającego do określania zakresu odnajdywania. Dzierżawcy udostępniania hostów, Utwórz poświadczeniami, które mają dostęp tylko do odczytu, aby tylko maszyny wirtualne należące do określonej dzierżawy, a następnie użyć tych poświadczeń w urządzeniu moduł zbierający i określ zakres jako hosta w celu odnajdywania. Alternatywnie można także utworzyć foldery w programie vCenter Server (Załóżmy, że folder1 dla tenant1 folder2 dla tenant2), w obszarze udostępnionego hosta przenoszenia maszyn wirtualnych dla tenant1 do folder1 i tenant2 do folder2 i odpowiednio określić zakres odnajdywania w moduł zbierający określając odpowiedni folder.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Liczbę maszyn wirtualnych można odnaleźć w projekcie migracji?

Może odnajdywać 1500 maszyn wirtualnych w projekcie migracji. Jeśli masz więcej maszyn w środowisku lokalnym [więcej](how-to-scale-assessment.md) o jak odkrywasz duże środowisko w Azure migracji.

## <a name="dependency-visualization"></a>Wizualizacja zależności

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Należy zwrócić do użycia funkcji wizualizacji zależności?

Usługa Azure Migrate jest dostępna bez dodatkowych opłat. Więcej informacji o cenach usługi Azure Migrate można uzyskać [tutaj](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Dla wizualizacji zależności można używać istniejący obszar roboczy?

Azure migracji nie obsługuje korzystania z istniejącym obszarem roboczym dla wizualizacji zależności, jednak obsługuje wielu Microsoft Monitoring Agent (MMA) i umożliwia wysyłanie danych do wielu obszarów roboczych. Dlatego jeśli masz już agentów wdrożone i skonfigurowane do obszaru roboczego, można korzystać z wielu homing w MMA agent i skonfigurować go do obszaru roboczego migracji Azure (oprócz istniejący obszar roboczy) i własnych preferencji. [W tym miejscu](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) jest blog, w jaki można włączyć wielu homing w MMA agent.

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [migracji Azure — omówienie](migrate-overview.md)
- Dowiedz się, jak [odnajdywania i oceny](tutorial-assessment-vmware.md) środowisku programu VMware
