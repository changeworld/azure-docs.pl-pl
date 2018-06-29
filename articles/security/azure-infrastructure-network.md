---
title: Architektura sieci platformy Azure
description: Ten artykuł zawiera ogólny opis sieci infrastruktury Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102369"
---
# <a name="azure-network-architecture"></a>Architektura sieci platformy Azure
Architektura sieci platformy Azure wykonuje zmodyfikowanej wersji branży standardowe Core / / dostęp do dystrybucji modelu z warstwy sprzętu distinct. Warstwy obejmują:

- Core (routery centrum danych)
- Dystrybucja (routerów dostępu i agregacji L2) - warstwy dystrybucji oddziela L3 routingu przełącza L2
- Dostęp (przełączniki hosta L2)

Architektura sieci ma dwa poziomy z warstwy 2 przełączniki, agregowania ruchu z innych warstw i warstwy 2 pętle uwzględnienie nadmiarowości. Zapewnia korzyści, takich jak bardziej elastyczne wpływ sieci VLAN i zwiększa skalowanie portu. Architektura zachowuje L2 i L3 distinct, co umożliwia wykorzystanie sprzętu w każdym z różnych warstw w sieci i minimalizuje błędów w jednej warstwy z wpływu na inne warstwy. Szyby umożliwiają udostępnianie, takich jak łączność infrastruktury L3 zasobu.

## <a name="network-configuration"></a>Konfiguracja sieci
Architektura sieci klastra Microsoft Azure w ramach centrum danych obejmuje następujące urządzenia:

- Routery (Datacenter, routera dostępu i routery graniczne typu liść)
- Przełączniki (agregacji i początku przełączniki stojaku)
- Digi CMs
- PDU lub Nucleons

Na poniższej ilustracji zawiera omówienie architektury sieci platformy Azure w ramach klastra. Platforma Azure ma dwa oddzielne architektury. Niektóre istniejących klientów usługi Azure i usług udostępnionych znajdują się na komputerze o architekturze domyślna sieć LAN (DLA), natomiast nowych regionów i wirtualnych klienci będą mieli dostęp za pomocą architektury Quantum 10 (P10). Architektura DLA jest tradycyjnych drzewa projektu z routerami active pasywnym dostępu i zabezpieczeń listy ACL dotyczą routerów dostępu. Architektura 10 Quantum to projekt closa/siatki routerów, w którym listy kontroli dostępu nie są stosowane na routerach, ale poniżej routingu przy użyciu równoważenia obciążenia oprogramowania (Programowego) lub sieci VLAN definiowana przez oprogramowanie.

![Sieć platformy Azure][1]

### <a name="quantum-10-devices"></a>Urządzenia quantum 10
Projekt Quantum 10 prowadzi rozpowszechniania przełączania warstwy 3 na wielu urządzeniach w projekcie CLOSA/siatki. Korzyści wynikające z projektu P10 obejmują większe możliwości i większa możliwość skalowania istniejącej infrastruktury sieci. Projekt zostają routery graniczne typu liść, przełączniki kręgosłupa i górnej stojak routerów do przekazywania ruchu do klastrów przez wiele tras, co zapewnia odporność na uszkodzenia. Zabezpieczenia usług, takich jak translatora adresów sieciowych są obsługiwane za pośrednictwem oprogramowania równoważenia obciążenia, a nie na urządzeniach sprzętowych.

### <a name="access-routers"></a>Routery dostępu
Routery dystrybucji/access L3 (ARs) wykonać podstawowe funkcje routingu dla warstwy dostępu i dystrybucji. Te urządzenia są wdrażane jako pary i czy brama domyślna dla podsieci. Każda para AR może obsługiwać wiele par przełącznika L2 agregacji, w zależności od pojemności. Maksymalna liczba zależy od możliwości urządzenia, a także domenach awarii. Typowy numer na podstawie oczekiwanego pojemności będą trzy pary przełącznika L2 agregacji na AR pary.

### <a name="l2-aggregation-switches"></a>L2 Przełączniki agregacji  
Te urządzenia stanowią punkt agregacji ruchu L2. Są warstwy dystrybucji L2 sieci szkieletowej i może obsługiwać dużą ilość ruchu sieciowego. Ponieważ te urządzenia agregacji ruchu, 802.1Q jest wymagane i technologii wysokiej przepustowości, takich jak agregacji portów i 10GE są używane.

### <a name="l2-host-switches"></a>Pamięci podręcznej L2 Przełączniki hosta
Hosty bezpośrednie łączenie się z tych przełączników. Mogą być przełączniki stojaku lub wdrożenia podstawy. 802.1Q standardowe umożliwia oznaczenie jedną sieć VLAN jako macierzystą sieć VLAN traktowanie tej sieci VLAN jako normalne ramek Ethernet (nieoznakowanego). W normalnych okolicznościach ramek na macierzystą sieć VLAN są wysłanych i odebranych nieoznakowanego w standardzie 802.1Q magistrali port. Ta funkcja została zaprojektowana do migracji do standardu 802.1Q oraz zapewnia zgodność z innych niż-802.1Q urządzeń obsługujących. W ramach tej architektury w infrastrukturze sieci korzysta z macierzystą sieć VLAN.

Taka architektura Określa standard natywnego wybór sieci VLAN, zapewniający, jeśli to możliwe, że urządzenia AR mają unikatowe macierzystą sieć VLAN dla każdego magistrali i L2Aggregation do szyby L2Aggregation. L2Aggregation do przełącznika L2Host szyby ma macierzystą sieć VLAN innych niż domyślne.

### <a name="link-aggregation-8023ad"></a>Agregacja łączy (802.3ad)
Łącze agregacji (LAG) umożliwia wielu poszczególnych łączy się ze sobą i traktowane jako pojedyncze łącze logiczne. Służy do wyznaczania interfejsy kanału portu numer musi standaryzowane, aby operacyjne ułatwia debugowanie, pozostałej części sieci użyje tego samego numeru na obu końcach kanału portu. Wymaga to standard, aby określić, które koniec kanału portu można użyć do zdefiniowania najbliższy dostępny numer.

Liczby określone dla L2Agg do przełącznika L2Host są używane po stronie L2Agg numery portów kanału. Zakres numerów jest bardziej ograniczony po stronie L2Host, standardowy jest używana cyfry 1 i 2 po stronie L2Host do odwoływania się do kanałów portów będzie odpowiednio stronie "" i "b".

### <a name="vlans"></a>Sieci VLAN
Architektura sieci korzysta z sieci VLAN do grupy serwerów ze sobą do jednej domeny emisji. Liczby sieci VLAN odpowiada 802.1Q 1 – 4094 numerowane standardy, które obsługuje sieci VLAN.

### <a name="customer-vlans"></a>Klient sieci VLAN
Klienci mają różne opcje wdrożenia sieci VLAN można wdrażać za pośrednictwem portalu Azure na potrzeby rozdzielenie i architektura ich rozwiązania. Te rozwiązania są wdrażane za pomocą maszyn wirtualnych. Odwołanie klienta można znaleźć przykłady architektura [architektur referencyjnych Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura krawędzi
Centrach danych platformy Azure są wbudowane w system infrastruktur sieciowych wysokiej nadmiarowe i dobrze elastycznie. Sieci w centrach danych platformy Azure są wykonywane z "potrzeby plus 1" (N + 1) nadmiarowość architektur lub lepszej. Pełna trybu failover w ramach i między centrami danych funkcjom na zapewnienie dostępności sieci i usługi. Zewnętrznie centrów danych są obsługiwane przez obwodów dedykowanym, wysokiej przepustowości sieci, które nadmiarowo Uzyskuj dostęp do właściwości ponad 1200 usługodawcy internetowi globalnie w wielu punktach komunikacji równorzędnej, zapewniając ponad 2000 GB/s potencjalnych pojemności krawędzi w sieci.

Filtrowanie routerów w warstwie dostępu i krawędzi sieci Microsoft Azure zapewnia utrwalonego zabezpieczeń na poziomie pakietów, aby zapobiec nieautoryzowane próby połączenia z platformą Azure. Pomaga on rzeczywistej zawartości pakietów zawierają dane w oczekiwanym formacie i są zgodne z schemat komunikacji oczekiwanego klient/serwer. Azure implementuje to architektura warstwowe składające się z następujących podział sieci i składniki kontroli dostępu:

- Krawędzi routery — segregowanie środowiska aplikacji z Internetu. Routery brzegowe są przeznaczone do zapewnienia ochrony przed fałszywe i ograniczyć dostęp za pomocą listy kontroli dostępu (ACL).
- Routery dystrybucji (dostęp) - routery dostępu są przeznaczone do zezwalania Microsoft tylko zatwierdzone adresów IP, podaj ochrony przed fałszowaniem i nawiązać połączenia przy użyciu list kontroli dostępu.

### <a name="a10-ddos-mitigation-architecture"></a>A10 Architektura środki zaradcze DDOS
Ataki w dalszym ciągu stanowią zagrożenie rzeczywistych dla niezawodność usług online firmy Microsoft. Jak ataki staną się bardziej docelowej, bardziej złożone i jako staną się bardziej geograficznie różnych usług firmy Microsoft możliwość zapewnienia skutecznego mechanizmy do identyfikowania i zminimalizować skutków takiego ataku jest o wysokim priorytecie.

Poniżej wyjaśniono implementowania systemu środki zaradcze A10 DDOS z punktu widzenia architektury sieci.  
Microsoft Azure na DCR (Datacenter Router), które zapewniają automatyczne wykrywanie i zapobieganie za wykorzystano A10 urządzeń sieciowych. Rozwiązanie A10 korzysta z monitorowania sieci Azure przykładowe pakiety netto i określić, czy jest atak. Po wykryciu ataków A10 urządzeń są używane jako płuczki w osłabianiu ataków. Po środki zaradcze, dalsze czystą ruch jest dozwolony w centrum danych Azure bezpośrednio z DCR. Aby chronić infrastrukturę sieci platformy Azure jest używane rozwiązanie A10.

Zabezpieczenia przed atakami DDoS w rozwiązaniu A10 obejmują:

- UDP IPv4 i IPv6 wypełniania ochrony
- Protokół ICMP IPv4 i IPv6 wypełniania ochrony
- TCP IPv4 i IPv6 wypełniania ochrony
- TCP SYN ataku ochrony dla protokołów IPv4 i IPv6
- Atak fragmentacji

> [!NOTE]
> Ochrona przed atakami DDoS jest dostępna domyślnie dla wszystkich klientów platformy Azure.
>
>

## <a name="network-connection-rules"></a>Reguły połączenia sieciowego
Azure wdraża routerów krawędzi w swojej sieci, które zapewniają zabezpieczenia na poziomie pakietów, aby zapobiec nieautoryzowane próby połączenia z platformą Microsoft Azure. Usuń ich rzeczywistej zawartości pakietów danych w oczekiwanym formacie i odpowiadają schemat komunikacji oczekiwanego klient/serwer.

Routery brzegowe segregowanie środowiska aplikacji z Internetu. Routery brzegowe są przeznaczone do zapewnienia ochrony przed fałszywe i ograniczyć dostęp za pomocą listy kontroli dostępu (ACL). Te routery brzegowe są skonfigurowane przy użyciu warstwowego podejścia listy kontroli dostępu do limitu protokołów sieciowych, które są dozwolone przesyłania routery brzegowe i dostępu do routerów.

Urządzenia sieciowe znajdują się w lokalizacji dostępu i krawędzi i działać jako punkty granic, gdy transfer danych przychodzących i/lub wyjście filtry są stosowane.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
