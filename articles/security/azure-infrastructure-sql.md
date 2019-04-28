---
title: Funkcje zabezpieczeń w usłudze Azure SQL Database
description: Ten artykuł zawiera opis ogólny, jak usługi Azure SQL Database chroni dane klientów na platformie Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121660"
---
# <a name="azure-sql-database-security-features"></a>Funkcje zabezpieczeń w usłudze Azure SQL Database    
Usługa Azure SQL Database udostępnia usługę relacyjnej bazy danych na platformie Azure. Aby chronić dane klientów i zapewnia silne zabezpieczenia funkcji, których klienci oczekują od usługa relacyjnej bazy danych, SQL Database ma swoje własne zestawy funkcji zabezpieczeń. Te możliwości bazują na kontrolki, które są dziedziczone z platformy Azure.

## <a name="security-capabilities"></a>Możliwości zabezpieczeń

### <a name="usage-of-the-tds-protocol"></a>Użycie protokołu TDS
Usługa Azure SQL Database obsługuje tylko dane tabelaryczne usługi stream (TDS) protokołu, który wymaga bazy danych był dostępny za pośrednictwem tylko domyślny port dla protokołu TCP/1433.

### <a name="azure-sql-database-firewall"></a>Zapora usługi SQL Database platformy Azure
Aby lepiej chronić dane klientów, Azure SQL Database obejmuje funkcje zapory, która domyślnie uniemożliwia dostęp do serwera SQL Database, jak pokazano poniżej.

![Zapora usługi SQL Database platformy Azure][1]

Zapory bramy można ograniczyć adresy, które umożliwia klientom szczegółową kontrolę określić zakresy dopuszczalnych adresów IP. Zapora udziela dostępu na podstawie źródłowego adresu IP każdego żądania.

Klienci mogą osiągnąć konfiguracji zapory za pomocą portalu zarządzania lub programowo przy użyciu interfejsu API REST zarządzania bazy danych SQL Azure. Zapora bramy usługi Azure SQL Database, domyślnie zapobiega wszystkich klientów TDS dostępu do wystąpienia bazy danych Azure SQL. Klienci, należy skonfigurować dostęp przy użyciu list kontroli dostępu (ACL), aby zezwolić na połączenia z bazą danych SQL Azure za źródłowych i docelowych adresów internetowych, protokołów oraz numery portów.

### <a name="dosguard"></a>DoSGuard
Atakom typu odmowa usługi (DoS) zostały zredukowane przez usługi bramy bazy danych SQL o nazwie DoSGuard. DoSGuard aktywnie śledzenia nieudanych prób logowania z adresów IP. W przypadku wielu nieudanych prób logowania z określonego adresu IP w danym okresie czasu, adres IP zablokowania dostępu do żadnych zasobów w usłudze w okresie wstępnie zdefiniowane.

Ponadto bramy usługi Azure SQL Database są wykonywane:

- Negocjacje możliwości bezpiecznego kanału do zaimplementowania TDS FIPS 140-2 zweryfikować szyfrowanych połączeń podczas łączenia z serwerów baz danych.
- Stanowe TDS inspekcję pakietów w czasie, gdy będzie akceptować połączenia od klientów. Brama sprawdza informacje o połączeniu i przekazuje pakiety TDS do odpowiedniego serwera fizycznego na podstawie nazwy bazy danych, który jest określony w parametrach połączenia.

Nadrzędna zasada zabezpieczeń sieciowych oferty usługi Azure SQL Database jest umożliwienie tylko połączenia i komunikacji, który jest niezbędne w celu umożliwienia do działania usługi. Domyślnie są blokowane innych portów, protokołów i połączeń. Wirtualne sieci lokalne (VLAN) i listy kontroli dostępu są używane do ograniczenia komunikacji sieciowej sieci źródłowym i docelowym, protokołów i numery portów.

Mechanizmy, które zostały zatwierdzone do zaimplementowania list ACL opartych na sieci zawierają listy ACL na routerach i moduły równoważenia obciążenia. Mechanizmy te są zarządzane przez sieci platformy Azure, zapory maszyny Wirtualnej gościa i reguł zapory bramy usługi Azure SQL Database, które zostały skonfigurowane przez klienta.

## <a name="data-segregation-and-customer-isolation"></a>Izolacja danych podziału i klientów
Sieć platformy Azure środowiska produkcyjnego są skonstruowane w taki sposób, że składniki systemu publicznie dostępne są odizolowani od zasobów wewnętrznych. Istnieje fizycznie i logicznie granice między serwerami sieci web, zapewniające dostęp do witryny Azure portal w publicznych i bazowej infrastruktury platformy Azure wirtualnego, gdzie znajdują się wystąpienia aplikacji klienta i dane klienta.

Wszystkie informacje publicznie odbywa się w obrębie sieci platformy Azure środowiska produkcyjnego. Sieci produkcyjnej podlega uwierzytelniania dwuskładnikowego i granic mechanizmy ochrony, używa zestawu funkcji zapory i zabezpieczeń, opisanego w poprzedniej sekcji, która używa funkcji izolacji danych, jak wspomniano w kolejnych sekcjach.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nieautoryzowany systemów i izolacji FC
Ponieważ kontroler sieci szkieletowej (FC) jest centralnego koordynatora w usłudze Azure Service Fabric, znaczące kontrolki znajdują się w miejscu, aby uniknąć zagrożenia, zwłaszcza z FAs mogą mieć złamane zabezpieczenia w aplikacji klienta. FC nie rozpoznaje żadnego sprzętu, którego informacje o urządzeniach (na przykład adres MAC) nie jest wstępnie załadowane w FC. Serwery DHCP w FC skonfigurowano listy adresów MAC węzłów, które chcą rozruchu. Nawet jeśli nieautoryzowany systemy są połączone, ich są nie włączone do magazynu sieci szkieletowej i w związku z tym nie połączone lub autoryzacji do komunikacji z dowolnego systemu w sieci szkieletowej magazynu. Zmniejsza to ryzyko nieautoryzowanych systemów podczas komunikowania się z FC, i uzyskania dostępu do sieci VLAN i na platformie Azure.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
Sieć platformy Azure środowiska produkcyjnego logicznie można podzielić na trzy podstawowe sieci VLAN:

- Główny sieci VLAN: Dla połączeń klientów niezaufanych węzłów.
- Sieć VLAN FC: Zawiera zaufanych FCs i obsługi systemów.
- Urządzenie sieci VLAN: Zawiera zaufanych sieci i innych urządzeniach infrastruktury.

### <a name="packet-filtering"></a>Filtrowanie pakietów
IPFilter zapory oprogramowania, które są implementowane w główny system operacyjny i systemu operacyjnego gościa węzłów wymuszać ograniczenia łączności i uniemożliwić nieautoryzowany ruch między maszynami wirtualnymi.

### <a name="hypervisor-root-os-and-guest-vms"></a>Funkcja hypervisor, główny system operacyjny i maszyn wirtualnych gościa
Izolacja główny system operacyjny z maszyn wirtualnych gościa oraz maszyny wirtualne gościa od siebie nawzajem jest zarządzana przez funkcji hypervisor i katalog główny systemu operacyjnego.

### <a name="types-of-rules-on-firewalls"></a>Typy reguł zapory
Reguła jest zdefiniowany jako:

{Security Response Center (Src), Src Port, docelowy adres IP, Port docelowy docelowy protokołu IP, we/wy, limit czasu stanowych/bezstanowych i stanowej usługi Flow}.

Pakiety synchroniczne bezczynne znak (SYN) są dozwolone wewnątrz lub na zewnątrz, tylko wtedy, gdy jeden z reguły to umożliwia. Dla protokołu TCP platforma Azure używa bezstanowych reguł, w której zasada jest możliwość tylko pakiety bez SYN do lub z maszyny Wirtualnej. Lokalne zabezpieczenia jest odporność zignorowanie bez SYN, jeśli go nie otrzymała pakietu SYN wcześniej dowolnym stosie hosta. Samego protokołu TCP jest stanowa, a w połączeniu z bezstanowych reguł opartych na SYN osiąga ogólnym zachowaniom stanowych implementacji.

Dla protokołu UDP (User Datagram), platforma Azure używa reguła stanowa. Za każdym razem, gdy pakiet UDP jest zgodny z regułą, odwrotnej przepływ zostanie utworzony w innym kierunku. Ten przepływ ma wbudowane limitu czasu.

Klienci są zobowiązani do konfigurowania ich własnymi zaporami na system Azure oferuje. W tym miejscu klienci będą mogli Definiowanie reguł dla ruchu przychodzącego i wychodzącego.

### <a name="production-configuration-management"></a>Zarządzanie konfiguracją w środowisku produkcyjnym
Standardowa bezpieczne konfiguracje są obsługiwane przez zespoły odpowiednie działania na platformie Azure i usługi Azure SQL Database. Wszystkie zmiany konfiguracji na systemy produkcyjne są udokumentowane i śledzone za pośrednictwem centralnej śledzenia systemu. Zmiany oprogramowania i sprzętu są śledzone za pośrednictwem systemu śledzenia centralnej. Zmiany dotyczące sieci, które odnoszą się do listy ACL są śledzone za pomocą usługi zarządzania listy ACL.

Wszystkie zmiany konfiguracji na platformie Azure opracowany i przetestowany w środowisku przejściowym, a następnie są one wdrażane w środowisku produkcyjnym. Kompilacje oprogramowania są przeglądane w ramach testowania. Kontrole zabezpieczeń i prywatności są przeglądane w ramach wpisu listy kontrolnej kryteriów. Zmiany są wdrażane w zaplanowanych odstępach czasu przez zespół odpowiednich wdrożenia. Wersje są przeglądane i wyrejestrowane przez pracowników zespołu odpowiednich wdrażania przed ich wdrożeniem w środowisku produkcyjnym.

Zmiany są monitorowane w celu osiągnięcia sukcesu. W scenariuszu awarii zmiana zostanie wycofana do jego poprzedniego stanu, lub poprawka jest wdrażana w rozwiązać błąd z zatwierdzeniem wyznaczonym personelu. Magazyn źródłowy, Git, TFS, Master Data Services (MDS), drugie miejsca, monitorowania zabezpieczeń platformy Azure, FC i platformy WinFabric służą centralne zarządzanie nimi, stosowania i sprawdzić ustawienia konfiguracji w środowisku wirtualnym platformy Azure.

Podobnie sieci i zmian ustalonymi kroki weryfikacji, aby ocenić ich przestrzeganie wymagania dotyczące kompilacji. Nowości są przeglądane i autoryzacji za pośrednictwem skoordynowanej zmiany tablicy doradztwa technicznego dotyczącego (CAB) odpowiednich grup w stosie.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
