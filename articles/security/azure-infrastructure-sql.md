---
title: Funkcje zabezpieczeń bazy danych SQL Azure
description: Ten artykuł zawiera ogólny opis bazy danych SQL Azure chroni dane klientów na platformie Azure.
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
ms.openlocfilehash: cca8febb004029b13b0df09a047da701c4528e8e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102580"
---
# <a name="microsoft-azure-sql-database-security-features"></a>Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft    
Baza danych SQL Azure Microsoft udostępnia usługę relacyjnej bazy danych na platformie Azure. Do ochrony danych klienta i udostępnia funkcje silne zabezpieczenie, które klienci oczekują od usługa relacyjnej bazy danych, baza danych SQL ma własne zestawy funkcji zabezpieczeń. Te możliwości zależą od formantów, które są dziedziczone z platformy Azure.

## <a name="security-capabilities"></a>Funkcje zabezpieczeń

### <a name="usage-of-tabular-data-stream-tds-protocol"></a>Użycie protokołu strumienia danych tabelarycznych (TDS)
Baza danych programu Microsoft Azure SQL obsługuje tylko protokołu TDS, który wymaga bazy danych, aby umożliwić dostęp tylko za pośrednictwem domyślnego portu z TCP/1433.

### <a name="microsoft-azure-sql-database-firewall"></a>Zapory bazy danych SQL Microsoft Azure
Aby ułatwić ochronę danych klientów, baza danych SQL Azure Microsoft obejmuje funkcje zapory, która domyślnie uniemożliwia dostęp do serwera bazy danych SQL, jak pokazano poniżej.

![Zapory bazy danych SQL Azure][1]

Zapora bramy umożliwia ograniczenie adresów, co pozwala na kontrolę klientów do określ zakresy adresów IP dopuszczalne. Zapora udziela dostępu na podstawie źródłowego adresu IP dla każdego żądania.

Konfiguracja zapory można osiągnąć za pomocą portalu zarządzania lub programowo przy użyciu programu Microsoft Azure SQL bazy danych zarządzania interfejsu API REST. Zapory bramy usługi Microsoft Azure SQL bazy danych domyślnie uniemożliwia klienta TDS dostęp do baz danych Microsoft Azure. Musi być skonfigurowany dostęp przy użyciu listy kontroli dostępu, aby umożliwić połączenia bazy danych SQL Azure Microsoft źródła i docelowe adresy internetowe, protokołów i numery portów.

### <a name="dosguard"></a>DoSGuard
Typu "odmowa usługi" (DoS) ataków zostały zredukowane przez usługę bramy bazy danych SQL o nazwie DoSGuard. DoSGuard aktywnie śledzi nieudanych logowań z adresów IP. Jeśli istnieje wiele nieudanych logowań z określonego adresu IP w przedziale czasu, adres IP jest zablokowany dostęp do wszystkich zasobów w usłudze wstępnie zdefiniowanym okresie.

Oprócz powyższego również wykonuje bramy Microsoft Azure SQL Database:

- Negocjacji możliwości bezpiecznego kanału do zaimplementowania TDS FIPS 140-2 zweryfikować szyfrowanych połączeń podczas łączenia się z serwerami bazy danych.
- Stanowe inspekcję pakietów TDS podczas akceptowania połączeń z klientami. Brama weryfikuje informacje o połączeniu i przekazuje pakiety TDS do odpowiedniego serwera fizycznego na podstawie nazwy bazy danych określona w parametrach połączenia.

Nadrzędna zasadę zabezpieczeń sieci oferty Microsoft Azure SQL Database jest Zezwalaj tylko na połączenia i komunikacji, które są niezbędne umożliwić działanie usługi. Domyślnie są blokowane innych portów, protokołów i połączeń. Sieci VLAN i listy kontroli dostępu są używane w celu ograniczenia komunikacji sieciowej źródła i sieci docelowych, protokołów i numery portów.

Zatwierdzone do wdrożenia listy ACL oparte na sieci obejmują: listy ACL na routerach i moduły równoważenia obciążenia. Są one zarządzane przez sieci Azure, maszyny Wirtualnej gościa zapory i reguł zapory bramy bazy danych SQL Azure firmy Microsoft, które są konfigurowane przez klienta.

## <a name="data-segregation-and-customer-isolation"></a>Izolacja podział i odbiorcy danych
Sieci produkcyjnej Azure jest struktury w taki sposób, że składniki systemowe publicznie są rozdzielone od zasobów wewnętrznych. Granice fizycznych i logicznych istnieje między serwerami sieci web, zapewniając dostęp do portalu Azure publicznych i podstawowej platformy Azure infrastruktury wirtualnego, gdzie znajdują się wystąpień aplikacji klienta i dane klienta.

Wszystkie informacje publicznie odbywa się w sieci produkcyjnej Azure. Sieci produkcyjnej podlega uwierzytelniania dwuskładnikowego i mechanizmy ochrony granic, korzysta z zestawu opisane w poprzedniej sekcji funkcji zapory i zabezpieczeń i korzysta z funkcji izolacji danych przypadków wymienionych poniżej.

### <a name="unauthorized-systems-and-isolation-of-fc"></a>Nieautoryzowane systemów i izolacji FC
Ponieważ FC jest centralną programu orchestrator z sieci szkieletowej programu Microsoft Azure, znaczących są stosowane środki ograniczających zagrożenia, szczególnie ze potencjalnie złamany FAs w aplikacji klienta. FC nie rozpoznaje żadnego sprzętu, którego informacje o urządzeniu (na przykład adres MAC) nie jest wstępnie załadowane w ramach FC. Serwery DHCP w FC skonfigurowano listy adresów MAC węzłów, które chcą rozruchu. Nawet jeśli nieautoryzowany systemy są połączone, ich są nie włączona do sieci szkieletowej magazynu i w związku z tym nie połączona lub autoryzacji do komunikacji z dowolnego systemu w sieci szkieletowej magazynu. Zmniejsza ryzyko nieautoryzowanego systemów komunikacji z FC i uzyskanie dostępu do sieci VLAN i Azure.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
Sieci Azure środowiska produkcyjnego jest logicznie podzielić na trzy podstawowe sieci VLAN:

- Główne sieci VLAN — dla połączeń klienta niezaufanych węzłów
- Sieci VLAN FC — zawiera zaufanych FCs i systemów pomocniczych
- Urządzenie sieci VLAN — zawiera zaufanych sieci i innych urządzeniach infrastruktury

### <a name="packet-filtering"></a>Filtrowanie pakietów
IPFilter zapór programowych zaimplementowany w głównym systemu operacyjnego i systemu operacyjnego gościa z węzłów wymuszać ograniczenia łączności i uniemożliwić nieautoryzowany ruch między maszynami wirtualnymi.

### <a name="hypervisor-root-os-and-guest-vms"></a>Funkcja hypervisor, głównego systemu operacyjnego i maszyny wirtualne gości
Izolacja głównego systemu operacyjnego z maszyn wirtualnych gościa, a Gość maszyn wirtualnych od siebie nawzajem jest zarządzana przez funkcji Hypervisor i systemu operacyjnego głównego.

### <a name="types-of-rules-on-firewalls"></a>Typy reguł dotyczących zapory
Reguła jest zdefiniowany jako:

{Security Response Center (Src), Src Port docelowy IP i Port docelowy docelowego protokołu IP, we/wy przepływu Stateful/bezstanowych, stanowe limitu czasu}.

SYN pakiety są dozwolone przychodzący lub wychodzący tylko wtedy, gdy jeden z reguły zezwala na. Dla protokołu TCP Microsoft Azure używa reguł bezstanowych, w której zasada jest tylko możliwość wszystkie pakiety z systemem innym niż SYN do lub z maszyny Wirtualnej. Lokalne zabezpieczeń jest odporność zignorowanie z systemem innym niż SYN, jeśli go nie otrzymała pakietu SYN wcześniej stosu dowolnego hosta. Samego protokołu TCP jest obiektem stanowym i w połączeniu z bezstanowej SYNbased reguły osiąga ogólną zachowanie stanowego wdrożenia.

Dla protokołu UDP (User Datagram), Microsoft Azure korzysta z regułą stanową. Za każdym razem, gdy pakiet UDP zgodny z regułą, odwrotnej przepływ zostanie utworzony w innym kierunku. Ten przepływ ma wbudowane limitu czasu.

Klienci są zobowiązani do definiowania własnych zapór na górze zapewnia Microsoft Azure. Klienci są w tym miejscu można zdefiniować reguły dla ruchu przychodzącego i wychodzącego.

### <a name="production-configuration-management"></a>Zarządzanie konfiguracją produkcji
Standardowa bezpieczne konfiguracje są obsługiwane przez zespoły odpowiednie działania w Azure i Microsoft Azure SQL Database. Wszystkie zmiany konfiguracji do systemów produkcyjnych są Udokumentowanie i śledzenie za pośrednictwem systemu śledzenia centralnej. Programowe i sprzętowe zmiany są śledzone za pośrednictwem centralnej śledzenia systemu. Zmiany dotyczące sieci odnoszących się do listy ACL są śledzone za pomocą list kontroli dostępu usługi zarządzania (AMS).

Wszystkie zmiany konfiguracji do systemu Microsoft Azure opracowany i przetestowany w środowisku przemieszczania; a następnie wdrożony w środowisku produkcyjnym. Kompilacje oprogramowania zostaną zweryfikowane jako część testowania. Zabezpieczenia i prywatność kontroli zostaną zweryfikowane jako część kryterium wpisu listy kontrolnej. Zmiany są wdrażane w zaplanowanych odstępach czasu przez zespół odpowiednich wdrożenia. Zwalnia sprawdzone, a wyrejestrowane przez pracowników zespołu wdrażania odpowiednich przed ich wdrożeniem w środowisku produkcyjnym.

Zmiany są monitorowane w przypadku powodzenia. W przypadku awarii zmiana jest wycofywany wstecz do poprzedniego stanu lub poprawka jest wdrażana na adres błędu z zatwierdzaniem wyznaczonych personelu. Centralnie zarządzać zastosowania i sprawdzić ustawienia konfiguracji w środowisku wirtualnym platformy Azure są używane źródłowego magazynu, Git, TFS, MDS, uczestników, monitorowania zabezpieczeń Azure (ASM), FC i platformy WinFabric.

Podobnie zmian sieci i opracowaniu kroki weryfikacji, aby ocenić ich zgodność z wymogami kompilacji. Wydanie są sprawdzone i autoryzację za pośrednictwem skoordynowany sposób zmiany Advisory tablicy (CAB) odpowiednich grup na stosie.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
