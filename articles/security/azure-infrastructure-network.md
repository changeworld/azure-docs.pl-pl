---
title: Architektura sieci platformy Azure
description: Ten artykuł zawiera ogólny opis sieci infrastruktury Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 48a7e52d4284e5c2db1d77d24d91fd4701aad8d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587161"
---
# <a name="azure-network-architecture"></a>Architektura sieci platformy Azure
Architektura sieci platformy Azure jest zgodna zmodyfikowaną wersję branży core / / dostęp do dystrybucji modelu standardowego, z warstwy sprzętowej distinct. Warstwy obejmują:

- Podstawowe (routery centrum danych)
- Dystrybucja (routerów dostępu i agregacji L2). Warstwa dystrybucji oddziela L3 routingu z pamięci podręcznej L2 zmiany.
- Dostęp (L2 hosta przełączniki)

Architektura sieci ma dwa poziomy przełączniki warstwy 2. Jedna warstwa agregacji ruchu z innej warstwy. Włączenie nadmiarowości w pętli drugą warstwę. Architektura zapewnia większą elastyczność zużycie sieci VLAN i poprawia skalowanie portu. Architektura przechowuje P2 i P3 distinct, co pozwala na wykorzystanie sprzętu w każdym z różnych warstw w sieci i minimalizuje błędów w jednej warstwie z wpływu na pozostałe warstwy. Użycie szyby umożliwia udostępnianie, takich jak łączność z infrastruktury L3 zasobów.

## <a name="network-configuration"></a>Konfiguracja sieci
Architektura sieci w klastrze platformy Azure w centrum danych składa się z następującymi urządzeniami:

- Routery (centrum danych, router dostępu i routery liścia obramowanie)
- Przełączniki (przełączników top of rack i agregacji)
- System CMs Digi
- Jednostki dystrybucji zasilania

Platforma Azure ma dwa oddzielne architektury. Niektórych istniejących klientów platformy Azure i usług udostępnionych znajdują się na architekturę sieci LAN domyślny (DLA), natomiast nowych regionów i klientów wirtualnych znajdują się na architekturze Quantum 10 (P10). Architektura DLA to projekt tradycyjnych drzewa, za pomocą routerów aktywny/pasywny dostępu i zabezpieczeń listy kontroli dostępu (ACL) stosowane do routerów dostępu. Architektura Quantum 10 to projekt Zamknij/siatki routerów, gdzie list ACL nie są stosowane w routery. Zamiast tego listy ACL są stosowane poniższe routingu, za pomocą oprogramowania Load Balancing (SLB) lub oprogramowania zdefiniowane sieci VLAN.

Na poniższym diagramie przedstawiono ogólne omówienie architektury sieci w klastrze platformy Azure:

![Diagram sieci platformy Azure][1]

### <a name="quantum-10-devices"></a>Urządzenia quantum 10
Projekt Quantum 10 prowadzi warstwy 3 przełączania rozprzestrzeniania się za pośrednictwem wielu urządzeń w projekcie Closa/siatki. Korzyści wynikające z projektu P10 obejmują większych możliwości i większe możliwości skalowania w istniejącej infrastrukturze sieci. Projekt zatrudnia routery liścia graniczne, grzbietu, routery i przełączniki top-of-rack do przekazywania ruchu do klastrów przez wiele tras, co zapewnia odporność na uszkodzenia. Oprogramowanie Równoważenie obciążenia, a nie urządzeń sprzętowych, obsługi usług zabezpieczeń, takich jak translatora adresów sieciowych.

### <a name="access-routers"></a>Routery dostępu
Routery dystrybucji/dostęp L3 (ARs), wykonuje podstawowe funkcje routingu warstwy dystrybucji i dostępem. Urządzenia te są wdrażane jako parę i są bramy domyślnej dla podsieci. Każda para AR może obsługiwać wiele par przełącznika agregacji L2, w zależności od pojemności. Maksymalna liczba zależy od możliwości urządzenia, a także domen błędów. Typowa liczba jest trzy pary przełącznika za parę AR L2 agregacji.

### <a name="l2-aggregation-switches"></a>Przełączniki agregacji pamięci podręcznej L2  
Te urządzenia służyć jako punkt agregacji dla ruchu w pamięci podręcznej L2. Ich warstwy dystrybucji dla sieci szkieletowej L2 i może obsługiwać dużą ilością ruchu. Ponieważ te urządzenia agregacji ruchu, wymagają one 802.1Q funkcji i technologii wysokiej przepustowości, takich jak 10GE i port agregacji.

### <a name="l2-host-switches"></a>Przełączniki hosta pamięci podręcznej L2
Hosty łączyć się bezpośrednio z tych przełączników. Mogą być przełączniki montowane w stojaku lub obudowy wdrożeń. 802\.1Q standardowa umożliwia oznaczenie jednej sieci VLAN jako macierzystą sieć VLAN, traktując tej sieci VLAN jako normalny (nieoznakowany) ramki Ethernet. W normalnych warunkach ramek na macierzystą sieć VLAN są wysłanych i odebranych nieoznakowany na 802.1Q portem magistrali. Ta funkcja została zaprojektowana pod kątem migracji do 802.1Q przeznaczenia i zgodności z innych niż-802.1Q urządzeń obsługujących. W ramach tej architektury w infrastrukturze sieci korzysta z macierzystą sieć VLAN.

Ta architektura określa standardowe rozwiązanie dla natywnych wybór sieci VLAN. Standardowa zapewnia, gdzie to możliwe, urządzenia AR unikatowy, natywne sieci VLAN dla każdego magistrali i L2Aggregation do szyby L2Aggregation. L2Aggregation do przełącznika L2Host szyby mają macierzystą sieć VLAN innych niż domyślne.

### <a name="link-aggregation-8023ad"></a>Agregacja łączy (802.3ad)
Agregacja łączy pozwala wielu pojedynczych łączy ze sobą powiązane i traktowane jako pojedyncze łącze logiczne. W celu ułatwienia debugowania operacyjnej, powinien być znormalizowany numer, który służy do wyznaczania interfejsów portów kanału. Ten sam numer jest używany w pozostałej części sieci na obu końcach kanału portu.

Numery określona dla L2Agg przełącznikiem L2Host są używane po stronie L2Agg numery portów kanału. Ponieważ zakres liczb jest bardziej ograniczone po stronie L2Host, standardowy jest Użyj numery 1 i 2 po stronie L2Host. Te można znaleźć kanału portu zamierzasz po stronie "" i po stronie "b", odpowiednio.

### <a name="vlans"></a>Sieci VLAN
Architektura sieci korzysta z sieci VLAN do grupy serwerów razem do jednej domeny emisji. Numery sieci VLAN odpowiada 802.1Q standardowa, który obsługuje numerowane 1 – 4094 sieci VLAN.

### <a name="customer-vlans"></a>Klient sieci VLAN
Dostępne są różne opcje wdrożenia sieci VLAN można wdrożyć w witrynie Azure portal, aby zaspokoić potrzeby rozdzielenie i architektury rozwiązania. Możesz wdrożyć te rozwiązania przy użyciu maszyn wirtualnych. Przykłady architektury odwołanie do klienta, zobacz [architektury referencyjne platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura usługi Edge
Centra danych platformy Azure są wbudowane w system infrastruktur sieciowych wysoko nadmiarowy i dobrze elastycznie. Firma Microsoft implementuje sieci w obrębie centrów danych platformy Azure za pomocą "potrzebę plus 1" (N + 1) architektury nadmiarowości lub lepszej. Pełne trybu failover na funkcje, wewnątrz i między centrami danych ułatwiają zapewnienie dostępności sieci i usługi. Zewnętrznie centra danych są obsługiwane przez obwodów dedykowanej sieci o wysokiej przepustowości. Te obwodów nadmiarowo skupionej właściwości powyżej 1200 usług dwóch usługodawców internetowych globalnie w wielu punktach komunikacji równorzędnej. Dzięki temu przekraczające 2000 GB/s, potencjalne pojemności edge za pośrednictwem sieci.

Filtrowanie routerów w warstwie usługi edge i dostępu do sieci platformy Azure zapewnia sprawdzone zabezpieczenia na poziomie pakietów i uniemożliwia nieautoryzowane próby połączenia z platformą Azure. Routery pomóc zapewnić rzeczywistej zawartości pakiety zawierają dane w oczekiwanym formacie i być zgodna z schemat komunikacji oczekiwanego klient/serwer. Azure implementuje architektury warstwowej, składające się z następujących podziału sieci i składniki kontroli dostępu:

- **Routery graniczne.** To oddzielenie czynności związanych z środowisko aplikacji z Internetu. Routery graniczne są przeznaczone do zapewnienia ochrony przed fałszywe i ograniczanie dostępu przy użyciu list kontroli dostępu.
- **Routery dystrybucji (dostęp).** Te zezwala na tylko firma Microsoft zatwierdzone adresów IP, zapewnienia ochrony przed fałszowaniem i nawiązywać połączenia przy użyciu list kontroli dostępu.

### <a name="ddos-mitigation"></a>Ataki DDOS
Rozproszone atakom typu odmowa usługi (DDoS) w dalszym ciągu stanowią prawdziwym zagrożeniem niezawodności usług online. Ataki stało się docelowych i zaawansowane, a usługi firma Microsoft oferuje więcej różnorodnych geograficznie, identyfikowanie i minimalizując wpływ te ataki jest wysoki priorytet.

[Standard ochrony przed atakami DDoS Azure](../virtual-network/ddos-protection-overview.md) zapewnia ochronę przed atakami DDoS. Zobacz [usługi Azure DDoS Protection: Najlepsze rozwiązania i architektury referencyjne](azure-ddos-best-practices.md) Aby dowiedzieć się więcej.

> [!NOTE]
> Firma Microsoft udostępnia usługę DDoS protection domyślnie dla wszystkich klientów platformy Azure.
>
>

## <a name="network-connection-rules"></a>Reguły połączeń sieciowych
Dla tej sieci platforma Azure wdroży routery graniczne, które zapewniają zabezpieczenia na poziomie pakietów, aby uniemożliwić nieautoryzowane próby połączenia z platformą Azure. Routery graniczne zagwarantować, że rzeczywistej zawartości pakietów zawierają dane w oczekiwanym formacie i być zgodna z schemat komunikacji oczekiwanego klient/serwer.

Routery graniczne segregowanie środowisko aplikacji z Internetu. Te routery mają na celu zapewnia ochronę przed fałszywe i ograniczyć dostęp za pomocą listy kontroli dostępu. Microsoft konfiguruje routerami granicznymi przy użyciu warstwowego podejścia listy ACL, limit protokołów sieciowych, które są dozwolone do przesyłania routerów granicznych i dostępu do routerów.

Microsoft umieszcza urządzeń sieciowych w lokalizacjach dostępu i urządzeniami brzegowymi, jako granic punkty, których są stosowane filtry transferu danych przychodzących lub wychodzących.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, co firma Microsoft używa do zabezpieczania infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
