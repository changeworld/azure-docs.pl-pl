---
title: Funkcje zabezpieczeń usługi Azure SQL Database
description: Ten artykuł zawiera ogólny opis sposobu ochrony danych klienta na platformie Azure przez usługę Azure Database.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942955"
---
# <a name="azure-sql-database-security-features"></a>Funkcje zabezpieczeń usługi Azure SQL Database    
Usługa Azure SQL Database zapewnia relacyjne usługi bazy danych na platformie Azure. Aby chronić dane klientów i zapewnić silne funkcje zabezpieczeń, których klienci oczekują od relacyjnej usługi bazy danych, baza danych SQL ma własne zestawy funkcji zabezpieczeń. Te możliwości są chyliłmi, które są dziedziczone z platformy Azure.

## <a name="security-capabilities"></a>Możliwości zabezpieczeń

### <a name="usage-of-the-tds-protocol"></a>Korzystanie z protokołu TDS
Usługa Azure SQL Database obsługuje tylko protokół tds (tabular data stream), który wymaga, aby baza danych była dostępna tylko za pośrednictwem domyślnego portu TCP/1433.

### <a name="azure-sql-database-firewall"></a>Zapora bazy danych SQL usługi Azure
Aby pomóc w ochronie danych klienta, usługa Azure SQL Database zawiera funkcję zapory, która domyślnie uniemożliwia dostęp do serwera bazy danych SQL, jak pokazano poniżej.

![Zapora bazy danych SQL usługi Azure](./media/infrastructure-sql/sql-database-firewall.png)

Zapora bramy może ograniczać adresy, co umożliwia klientom szczegółowe sterowanie określania zakresów dopuszczalnych adresów IP. Zapora udziela dostępu na podstawie źródłowego adresu IP każdego żądania.

Klienci mogą osiągnąć konfigurację zapory przy użyciu portalu zarządzania lub programowo przy użyciu interfejsu API REST usługi Azure SQL Database Management. Zapora bramy bazy danych SQL Azure domyślnie uniemożliwia wszystkim klientom dostęp TDS do wystąpień bazy danych SQL platformy Azure. Klienci muszą skonfigurować dostęp przy użyciu list kontroli dostępu (ACL), aby zezwolić na połączenia usługi Azure SQL Database według źródłowych i docelowych adresów internetowych, protokołów i numerów portów.

### <a name="dosguard"></a>DoSGuard (DoSGuard)
Ataki typu "odmowa usługi" (DoS) są redukowane przez usługę bramy bazy danych SQL o nazwie DoSGuard. DoSGuard aktywnie śledzi nieudane logowania z adresów IP. Jeśli istnieje wiele nieudanych logowania z określonego adresu IP w określonym czasie, adres IP jest zablokowany dostęp do żadnych zasobów w usłudze przez wstępnie zdefiniowany okres czasu.

Ponadto brama bazy danych SQL azure wykonuje:

- Bezpieczne negocjacje w sprawie możliwości kanału w celu wdrożenia sprawdzonych połączeń szyfrowanych TDS FIPS 140-2 podczas łączenia się z serwerami bazy danych.
- Stanowa inspekcja pakietów TDS, gdy akceptuje połączenia od klientów. Brama sprawdza poprawność informacji o połączeniu i przekazuje pakiety TDS do odpowiedniego serwera fizycznego na podstawie nazwy bazy danych określonej w ciągu połączenia.

Nadrzędną zasadą zabezpieczeń sieciowych usługi Azure SQL Database oferuje jest dopuszczenie tylko połączenia i komunikacji, które są niezbędne do umożliwienia działania usługi. Wszystkie inne porty, protokoły i połączenia są domyślnie blokowane. Wirtualne sieci lokalne (VLAN) i listy ACL są używane do ograniczania komunikacji sieciowej przez sieci źródłowe i docelowe, protokoły i numery portów.

Mechanizmy zatwierdzone do implementowania sieciowych list ACL obejmują listy ACL na routerach i modułach równoważenia obciążenia. Mechanizmy te są zarządzane przez sieć platformy Azure, zaporę maszyn wirtualnych gości i reguły zapory bramy usługi Azure SQL Database, które są konfigurowane przez klienta.

## <a name="data-segregation-and-customer-isolation"></a>Segregacja danych i izolacja klientów
Sieć produkcyjna platformy Azure jest skonstruowana w taki sposób, że publicznie dostępne składniki systemu są oddzielone od zasobów wewnętrznych. Granice fizyczne i logiczne istnieją między serwerami sieci web, które zapewniają dostęp do publicznej witryny Azure portal i podstawowej infrastruktury wirtualnej platformy Azure, gdzie znajdują się wystąpienia aplikacji klienta i danych klienta.

Wszystkie publicznie dostępne informacje są zarządzane w ramach sieci produkcyjnej platformy Azure. Sieć produkcyjna podlega mechanizmom uwierzytelniania dwuskładnikowego i ochrony granic, używa zestawu funkcji zapory i zabezpieczeń opisanych w poprzedniej sekcji i używa funkcji izolacji danych, jak wspomniano w następnych sekcjach.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nieautoryzowane systemy i izolacja FC
Ponieważ kontroler sieci szkieletowej (FC) jest centralnym koordynatorem sieci szkieletowej platformy Azure, istnieją znaczące formanty w celu ograniczenia zagrożeń dla niego, szczególnie z potencjalnie naruszonych fa w aplikacjach klienta. FC nie rozpoznaje żadnego sprzętu, którego informacje o urządzeniu (na przykład adres MAC) nie są wstępnie załadowane w fc. Serwery DHCP w fc skonfigurowały listy adresów MAC węzłów, które chcą uruchomić. Nawet jeśli nieautoryzowane systemy są połączone, nie są one włączone do magazynu sieci szkieletu, a zatem nie są połączone lub autoryzowane do komunikowania się z żadnym systemem w magazynie sieci szkieletowej. Zmniejsza to ryzyko komunikacji nieautoryzowanych systemów z FC i uzyskania dostępu do sieci VLAN i platformy Azure.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
Sieć produkcyjna platformy Azure jest logicznie podzielona na trzy podstawowe sieci VLAN:

- Główna sieć VLAN: łączy niezaufane węzły klienta.
- FC VLAN: Zawiera zaufane kontrolery domeny i systemy pomocnicze.
- Sieć VLAN urządzenia: zawiera zaufaną sieć i inne urządzenia infrastruktury.

### <a name="packet-filtering"></a>Filtrowanie pakietów
IpFilter i zapory oprogramowania, które są implementowane w głównym os i gościnnym systemu operacyjnego węzłów wymuszają ograniczenia łączności i zapobiec nieautoryzowanemu ruchowi między maszynami wirtualnymi.

### <a name="hypervisor-root-os-and-guest-vms"></a>Funkcja hypervisor, główny system operacyjny i maszyny wirtualne gościa
Izolacja głównego systemu operacyjnego z maszyn wirtualnych gościa i maszyn wirtualnych gościa od siebie jest zarządzana przez hipernadzorcę i główny system operacyjny.

### <a name="types-of-rules-on-firewalls"></a>Typy reguł na zapory
Reguła jest zdefiniowana jako:

{Src IP, port Src, docelowy adres IP, port docelowy, protokół docelowy, wyjęty/ wyjęty, stanowy/bezstanowy, stateful flow timeout}.

Synchroniczne pakiety znaków bezczynności (SYN) są dozwolone tylko wtedy, gdy pozwala na to jedna z reguł. W przypadku protokołu TCP platforma Azure używa reguł bezstanowych, w których zasada jest taka, że zezwala tylko na wszystkie pakiety inne niż SYN do lub z maszyny Wirtualnej. Założenie zabezpieczeń jest to, że każdy stos hosta jest odporny na ignorowanie non-SYN, jeśli nie widział pakietu SYN wcześniej. Sam protokół TCP jest stanowy i w połączeniu z regułą opartą na syn bezstanowym osiąga ogólne zachowanie implementacji stanowej.

W przypadku protokołu UDP (User Datagram Protocol) platforma Azure używa reguły stanowej. Za każdym razem, gdy pakiet UDP pasuje do reguły, przepływ odwrotny jest tworzony w innym kierunku. Ten przepływ ma wbudowany limit czasu.

Klienci są odpowiedzialni za konfigurowanie własnych zapór na podstawie tego, co zapewnia platforma Azure. W tym miejscu klienci mogą definiować reguły ruchu przychodzącego i wychodzącego.

### <a name="production-configuration-management"></a>Zarządzanie konfiguracją produkcji
Standardowe bezpieczne konfiguracje są obsługiwane przez odpowiednie zespoły operacyjne w usłudze Azure i usłudze Azure SQL Database. Wszystkie zmiany konfiguracji systemów produkcyjnych są dokumentowane i śledzone za pomocą centralnego systemu śledzenia. Zmiany w oprogramowaniu i sprzęcie są śledzone za pośrednictwem centralnego systemu śledzenia. Zmiany sieciowe związane z listy ACL są śledzone za pomocą usługi zarządzania listy ACL.

Wszystkie zmiany konfiguracji na platformie Azure są opracowywane i testowane w środowisku przejściowym, a następnie są wdrażane w środowisku produkcyjnym. Kompilacje oprogramowania są sprawdzane w ramach testowania. Kontrole bezpieczeństwa i prywatności są sprawdzane w ramach kryteriów listy kontrolnej wejścia. Zmiany są wdrażane w zaplanowanych odstępach czasu przez odpowiedni zespół wdrożeniowy. Wersje są przeglądane i podpisywane przez odpowiedni personel zespołu wdrożeniowego, zanim zostaną wdrożone w produkcji.

Zmiany są monitorowane pod kątem powodzenia. W przypadku awarii zmiana jest przywracana do poprzedniego stanu lub poprawka jest wdrażana w celu rozwiązania błędu z zatwierdzeniem wyznaczonego personelu. Źródło Depot, Git, TFS, Master Data Services (MDS), runners, monitorowania zabezpieczeń platformy Azure, FC i platformy WinFabric są używane do centralnego zarządzania, stosowania i weryfikacji ustawień konfiguracji w środowisku wirtualnym platformy Azure.

Podobnie zmiany sprzętu i sieci ustanowiły kroki sprawdzania poprawności, aby ocenić ich przestrzeganie wymagań kompilacji. Wersje są przeglądane i autoryzowane za pośrednictwem skoordynowanej rady doradczej zmian (CAB) odpowiednich grup na stosie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
