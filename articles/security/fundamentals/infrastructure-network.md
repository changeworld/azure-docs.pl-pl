---
title: Architektura sieci platformy Azure
description: Ten artykuł zawiera ogólny opis sieci infrastruktury platformy Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727170"
---
# <a name="azure-network-architecture"></a>Architektura sieci platformy Azure
Architektura sieci platformy Azure jest zgodna ze zmodyfikowaną wersją standardowego w branży modelu core/distribution/access z różnymi warstwami sprzętowymi. Warstwy obejmują:

- Core (routery centrów danych)
- Dystrybucja (routery dostępu i agregacja L2). Warstwa rozkładu oddziela routing L3 od przełączania L2.
- Dostęp (przełączniki hosta L2)

Architektura sieci ma dwa poziomy przełączników warstwy 2. Jedna warstwa agreguje ruch z drugiej warstwy. Druga warstwa pętli, aby uwzględnić nadmiarowość. Architektura zapewnia bardziej elastyczny rozmiar sieci VLAN i poprawia skalowanie portów. Architektura zachowuje różne L2 i L3, co umożliwia użycie sprzętu w każdej z różnych warstw w sieci i minimalizuje usterkę w jednej warstwie przed wpływem na drugą warstwę(-y). Korzystanie z pni umożliwia udostępnianie zasobów, takich jak łączność z infrastrukturą L3.

## <a name="network-configuration"></a>Konfiguracja sieci
Architektura sieci klastra platformy Azure w centrum danych składa się z następujących urządzeń:

- Routery (centrum danych, router dostępu i routery border leaf)
- Przełączniki (agregacja i przełączniki najwyższej klasy)
- Digi CMs
- Jednostki dystrybucji mocy

Platforma Azure ma dwie oddzielne architektury. Niektórzy istniejący klienci platformy Azure i usługi udostępnione znajdują się na domyślnej architekturze SIECI LAN (DLA), podczas gdy nowe regiony i klienci wirtualni znajdują się na architekturze Quantum 10 (Q10). Architektura DLA jest tradycyjnym projektem drzewa, z aktywnymi/pasywnymi routerami dostępu i listami kontroli dostępu do zabezpieczeń (ACL) zastosowanymi do routerów dostępu. Architektura Quantum 10 to projekt zamykania/siatki routerów, w którym listy ACL nie są stosowane na routerach. Zamiast tego listy ACL są stosowane poniżej routingu za pośrednictwem równoważenia obciążenia oprogramowania (SLB) lub zdefiniowanych programowo sieci VLAN.

Poniższy diagram zawiera omówienie wysokiego poziomu architektury sieci w klastrze platformy Azure:

![Diagram sieci platformy Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Urządzenia Quantum 10
Projekt Quantum 10 przeprowadza przełączanie warstwy 3 rozłożone na wielu urządzeniach w konstrukcji Clos/mesh. Zalety projektu Q10 obejmują większe możliwości i większą zdolność skalowania istniejącej infrastruktury sieciowej. Konstrukcja wykorzystuje routery graniczne, przełączniki kręgosłupa i routery najwyższej klasy, aby przekazywać ruch do klastrów na wielu trasach, co pozwala na odporność na uszkodzenia. Równoważenie obciążenia oprogramowania zamiast urządzeń sprzętowych obsługuje usługi zabezpieczeń, takie jak translacja adresów sieciowych.

### <a name="access-routers"></a>Dostęp do routerów
Routery L3 (ARs) dystrybucyjne/dostępowe (ARs) pełnią podstawową funkcję routingu dla warstw dystrybucji i dostępu. Te urządzenia są wdrażane jako para i są bramą domyślną dla podsieci. Każda para AR może obsługiwać wiele par przełączników agregacji L2, w zależności od pojemności. Maksymalna liczba zależy od pojemności urządzenia, a także domen awarii. Typowa liczba to trzy pary przełączników agregacji L2 na parę AR.

### <a name="l2-aggregation-switches"></a>Przełączniki agregacji L2  
Urządzenia te służą jako punkt agregacji dla ruchu L2. Są one warstwą dystrybucji dla sieci szkieletowej L2 i mogą obsługiwać duże ilości ruchu. Ponieważ te urządzenia agregują ruch, wymagają funkcji 802.1q i technologii o wysokiej przepustowości, takich jak agregacja portów i 10GE.

### <a name="l2-host-switches"></a>Przełączniki hosta L2
Hosty łączą się bezpośrednio z tymi przełącznikami. Mogą to być przełączniki montowane w stelażu lub wdrożenia obudowy. Standard 802.1q pozwala na oznaczenie jednej sieci VLAN jako natywnej sieci VLAN, traktując tę sieć VLAN jako normalne (nieoznakowane) kadrowanie Ethernet. W normalnych warunkach ramki na natywnej sieci VLAN są przesyłane i odbierane bez znaczników na porcie bagażnika 802.1q. Ta funkcja została zaprojektowana do migracji do 802.1q i zgodności z urządzeniami nieobjętymi 802.1q. W tej architekturze tylko infrastruktura sieciowa używa natywnej sieci VLAN.

Ta architektura określa standard dla natywnego zaznaczenia sieci VLAN. Norma zapewnia, w miarę możliwości, że urządzenia AR mają unikalną, natywną sieć VLAN dla każdego tułowia i pnie L2Aggregation do L2Aggregation. L2Aggregation do L2Host Przełącznik pnie mają nie-domyślne natywne VLAN.

### <a name="link-aggregation-8023ad"></a>Agregacja łączy (802.3ad)
Agregacja łączy umożliwia łączenie wielu pojedynczych łączy i traktowanie ich jako pojedynczego łącza logicznego. Aby ułatwić debugowanie operacyjne, należy znormalizować liczbę używaną do wyznaczania interfejsów kanału portów. Reszta sieci używa tego samego numeru na obu końcach kanału portowego.

Liczby określone dla przełącznika L2Agg do L2Host są numerami kanału portów używanymi po stronie L2Agg. Ponieważ zakres liczb jest bardziej ograniczony po stronie L2Host, standardem jest użycie liczb 1 i 2 po stronie L2Host. Odnoszą się one do kanału portowego przechodzącego odpowiednio do strony "a" i "b".

### <a name="vlans"></a>Sieci vlan
Architektura sieci używa sieci VLAN do grupowanie serwerów w jedną domenę emisji. Numery VLAN są zgodne ze standardem 802.1q, który obsługuje sieci VLAN o numerach 1–4094.

### <a name="customer-vlans"></a>Sieci VLAN dla klientów
Masz różne opcje implementacji sieci VLAN, które można wdrożyć za pośrednictwem witryny Azure portal, aby spełnić potrzeby separacji i architektury rozwiązania. Wdrażasz te rozwiązania za pośrednictwem maszyn wirtualnych. Aby zapoznać się z przykładami architektury referencyjnej dla klientów, zobacz [architektury referencyjne platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura krawędzi
Centra danych platformy Azure są oparte na wysoce nadmiarowej i dobrze aprowizowanym infrastrukturze sieciowej. Firma Microsoft implementuje sieci w centrach danych platformy Azure z architekturami nadmiarowości "need plus one" (N+1) lub lepszymi. Pełne funkcje pracy awaryjnej w centrach danych i między nimi pomagają zapewnić dostępność sieci i usług. Zewnętrznie centra danych są obsługiwane przez dedykowane obwody sieciowe o dużej przepustowości. Obwody te nadmiarowo łączą właściwości z ponad 1200 dostawcami usług internetowych na całym świecie w wielu punktach komunikacji równorzędnej. Zapewnia to ponad 2000 Gb/s potencjalnej przepustowości brzegowej w całej sieci.

Filtrowanie routerów w warstwie brzegowej i dostępu w sieci Platformy Azure zapewnia ugruntowane zabezpieczenia na poziomie pakietów i pomaga zapobiegać nieautoryzowanym próbom nawiązania połączenia z platformą Azure. Routery pomagają upewnić się, że rzeczywista zawartość pakietów zawiera dane w oczekiwanym formacie i jest zgodna z oczekiwanym schematem komunikacji klient/serwer. Platforma Azure implementuje architekturę warstwową, składającą się z następujących składników segregacji sieci i kontroli dostępu:

- **Routery brzegowe.** Oddzielają one środowisko aplikacji od Internetu. Routery brzegowe zostały zaprojektowane w celu zapewnienia ochrony przed fałszowaniem i ograniczenia dostępu za pomocą list ACL.
- **Routery dystrybucyjne (dostępowe).** Zezwalają one tylko na adresy IP zatwierdzone przez firmę Microsoft, zapewniają środki zapobiegające fałszowaniu i ustanawiają połączenia przy użyciu list ACL.

### <a name="ddos-mitigation"></a>Łagodzenie DDOS
Rozproszone ataki typu "odmowa usługi" (DDoS) nadal stanowią realne zagrożenie dla niezawodności usług online. W miarę jak ataki stają się bardziej ukierunkowane i wyrafinowane, a usługi świadczone przez firmę Microsoft stają się bardziej zróżnicowane geograficznie, identyfikacja i minimalizowanie wpływu tych ataków jest priorytetem.

[Standard ochrony przed atakami DDoS](../../virtual-network/ddos-protection-overview.md) firmy Azure zapewnia ochronę przed atakami DDoS. Aby dowiedzieć się więcej, zobacz [Ochrona przed atakami DDoS azure: Najważniejsze rozwiązania i architektury referencyjne.](ddos-best-practices.md)

> [!NOTE]
> Firma Microsoft domyślnie zapewnia ochronę przed atakami DDoS dla wszystkich klientów platformy Azure.
>
>

## <a name="network-connection-rules"></a>Reguły połączenia sieciowego
W swojej sieci platforma Azure wdraża routery brzegowe, które zapewniają bezpieczeństwo na poziomie pakietu, aby zapobiec nieautoryzowanym próbom nawiązania połączenia z platformą Azure. Routery brzegowe zapewniają, że rzeczywista zawartość pakietów zawiera dane w oczekiwanym formacie i jest zgodna z oczekiwanym schematem komunikacji klient/serwer.

Routery brzegowe oddzielają środowisko aplikacji od Internetu. Routery te zostały zaprojektowane w celu zapewnienia ochrony przed fałszowaniem i ograniczenia dostępu przy użyciu list ACL. Firma Microsoft konfiguruje routery brzegowe przy użyciu warstwowego podejścia ACL, aby ograniczyć protokoły sieciowe, które mogą przesyłać routery brzegowe i uzyskiwać dostęp do routerów.

Firma Microsoft umieszcza urządzenia sieciowe w lokalizacjach dostępu i krawędzi, aby działać jako punkty graniczne, w których są stosowane filtry ruchu przychodzącego lub wychodzącego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)


