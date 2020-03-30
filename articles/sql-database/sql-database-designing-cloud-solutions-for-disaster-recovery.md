---
title: Projektowanie usług dostępnych na całym świecie
description: Dowiedz się więcej o projektowaniu aplikacji dla usług o wysokiej dostępności przy użyciu usługi Azure SQL Database.
keywords: odzyskiwanie po awarii w chmurze,rozwiązania do odzyskiwania po awarii,kopia zapasowa danych aplikacji,replikacja geograficzna,planowanie ciągłości biznesowej
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269070"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Projektowanie globalnie dostępnych usług przy użyciu bazy danych SQL azure

Podczas tworzenia i wdrażania usług w chmurze za pomocą usługi Azure SQL Database, należy użyć [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [grup auto-failover,](sql-database-auto-failover-group.md) aby zapewnić odporność na awarie regionalne i katastrofalne awarie. Ta sama funkcja umożliwia tworzenie globalnie rozproszonych aplikacji zoptymalizowanych pod kątem lokalnego dostępu do danych. W tym artykule omówiono typowe wzorce aplikacji, w tym korzyści i kompromisy każdej opcji.

> [!NOTE]
> Jeśli używasz baz danych Premium lub Business Critical i pul elastycznych, możesz je odporne na awarie regionalne, konwertując je na konfigurację wdrożenia nadmiarowego strefy. Zobacz [bazy danych nadmiarowe strefowe](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenariusz 1: Używanie dwóch regionów platformy Azure do ciągłości biznesowej przy minimalnym przestoju

W tym scenariuszu aplikacje mają następujące cechy:

* Aplikacja jest aktywna w jednym regionie platformy Azure
* Wszystkie sesje bazy danych wymagają dostępu do odczytu i zapisu (RW) do danych
* Warstwa sieci Web i warstwa danych muszą być kolokowane w celu zmniejszenia opóźnień i kosztów ruchu
* Zasadniczo przestoje są większym ryzykiem biznesowym dla tych aplikacji niż utrata danych

W takim przypadku topologia wdrażania aplikacji jest zoptymalizowana pod kątem obsługi awarii regionalnych, gdy wszystkie składniki aplikacji muszą razem przełączyć się w tryb failover. Poniższy diagram przedstawia tę topologię. W przypadku nadmiarowości geograficznej zasoby aplikacji są wdrażane w regionach A i B. Jednak zasoby w regionie B nie są wykorzystywane, dopóki region A nie powiedzie się. Grupa trybu failover jest skonfigurowana między dwoma regionami do zarządzania łącznością bazy danych, replikacją i funkcją failover. Usługa sieci web w obu regionach jest skonfigurowana do uzyskiwania dostępu do bazy danych za pośrednictwem odbiornika odczytu i zapisu ** &lt;w pełni w błąd nazwa&gt;grupy .database.windows.net** (1). Menedżer ruchu jest skonfigurowany do [używania metody routingu priorytetowego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Usługa Azure traffic manager](../traffic-manager/traffic-manager-overview.md) jest używany w tym artykule tylko do celów ilustracyjnych. Można użyć dowolnego rozwiązania równoważenia obciążenia, które obsługuje metodę routingu priorytetu.

Na poniższym diagramie przedstawiono tę konfigurację przed awarią:

![Scenariusz 1. Konfiguracja przed awarią.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po awarii w regionie podstawowym usługa bazy danych SQL wykrywa, że podstawowa baza danych nie jest dostępna i wyzwala przełączenie w tryb failover do regionu pomocniczego na podstawie parametrów zasad automatycznego trybu failover (1). W zależności od umowy SLA aplikacji można skonfigurować okres prolongaty, który steruje czasem między wykryciem awarii a samą przełączeniem w tryb failover. Jest możliwe, że menedżer ruchu inicjuje pracy awaryjnej punktu końcowego, zanim grupa trybu failover wyzwala pracy awaryjnej bazy danych. W takim przypadku aplikacja sieci web nie może natychmiast ponownie połączyć się z bazą danych. Ale ponowne połączenia automatycznie powiedzie się, jak tylko zakończy się przełączenie w stan failover bazy danych. Gdy region nie powiodło się jest przywracany i z powrotem w trybie online, stary podstawowy automatycznie łączy się ponownie jako nowy pomocniczy. Poniższy diagram ilustruje konfigurację po pracy awaryjnej.

> [!NOTE]
> Wszystkie transakcje zatwierdzone po przełączeniu w stan failover zostaną utracone podczas ponownego połączenia. Po zakończeniu pracy awaryjnej aplikacja w regionie B może ponownie połączyć się i ponownie uruchomić przetwarzanie żądań użytkowników. Zarówno aplikacja sieci web, jak i podstawowa baza danych znajdują się teraz w regionie B i pozostają współlokacji.

![Scenariusz 1. Konfiguracja po przemijazie awaryjnym](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Jeśli wystąpi awaria w regionie B, proces replikacji między podstawową i pomocniczą bazą danych zostanie zawieszony, ale łącze między nimi pozostaje nienaruszone (1). Zarządzanie ruchem wykrywa, że łączność z regionem B jest uszkodzony i oznacza aplikacji sieci web punktu końcowego 2 jako zdegradowane (2). Wydajność aplikacji nie ma wpływu w tym przypadku, ale baza danych staje się narażona i w związku z tym na większe ryzyko utraty danych w przypadku regionu A nie powiedzie się po kolei.

> [!NOTE]
> W przypadku odzyskiwania po awarii zaleca się konfigurację z wdrożeniem aplikacji ograniczoną do dwóch regionów. Dzieje się tak, ponieważ większość obszarów geograficznych platformy Azure ma tylko dwa regiony. Ta konfiguracja nie chroni aplikacji przed jednoczesną katastrofalną awarią obu regionów. W mało prawdopodobnym przypadku takiego błędu można odzyskać bazy danych w trzecim regionie za pomocą [operacji przywracania geograficznego](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Po usunięciu awarii pomocnicza baza danych automatycznie ponownie synchronizuje się z podstawową. Podczas synchronizacji może mieć wpływ na wydajność podstawowego. Konkretny wpływ zależy od ilości danych, które nowy podstawowy nabyte od pracy awaryjnej. Na poniższym diagramie przedstawiono awarię w regionie pomocniczym:

![Scenariusz 1. Konfiguracja po awarii w regionie pomocniczym.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Główne **zalety** tego wzoru projektowego to:

* Ta sama aplikacja sieci web jest wdrażana w obu regionach bez konfiguracji specyficznej dla regionu i nie wymaga dodatkowej logiki do zarządzania trybem failover.
* Nie ma wpływu na wydajność aplikacji w trybie failover, ponieważ aplikacja sieci web i baza danych są zawsze zlokalizowane.

Głównym **kompromisem** jest to, że zasoby aplikacji w regionie B są niedostatecznie wyniszczane przez większość czasu.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenariusz 2: Regiony platformy Azure dla ciągłości biznesowej z maksymalnym zachowaniem danych

Ta opcja najlepiej nadaje się do zastosowań o następujących cechach:

* Każda utrata danych wiąże się z wysokim ryzykiem biznesowym. Przełączenie awaryjne bazy danych może służyć tylko w ostateczności, jeśli awaria jest spowodowana katastrofalnym błędem.
* Aplikacja obsługuje tryby operacji tylko do odczytu i odczytu i zapisu i może działać w "trybie tylko do odczytu" przez pewien czas.

W tym wzorzec aplikacja przełącza się do trybu tylko do odczytu, gdy połączenia odczytu i zapisu rozpocząć uzyskiwanie błędów przesunięcia czasu. Aplikacja sieci Web jest wdrażana w obu regionach i obejmują połączenie z punktem końcowym odbiornika odczytu i zapisu i inne połączenie z punktem końcowym odbiornika tylko do odczytu (1). Profil Usługi Traffic Manager powinien używać [routingu priorytetowego](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorowanie punktu końcowego](../traffic-manager/traffic-manager-monitoring.md) powinno być włączone dla punktu końcowego aplikacji w każdym regionie (2).

Na poniższym diagramie przedstawiono tę konfigurację przed awarią:

![Scenariusz 2. Konfiguracja przed awarią.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Gdy menedżer ruchu wykryje awarię łączności z regionem A, automatycznie przełącza ruch użytkownika do wystąpienia aplikacji w regionie B. Za pomocą tego wzorca ważne jest, aby ustawić okres prolongaty z utratą danych na wystarczająco wysoką wartość, na przykład 24 godziny. Zapewnia, że utrata danych jest zapobiegana, jeśli awaria zostanie złagodzona w tym czasie. Po włączeniu aplikacji sieci Web w regionie B operacje odczytu i zapisu zaczynają się niepowodzeniem. W tym momencie należy przełączyć się do trybu tylko do odczytu (1). W tym trybie żądania są automatycznie kierowane do pomocniczej bazy danych. Jeśli awaria jest spowodowana katastrofalną awarią, najprawdopodobniej nie można jej złagodzić w okresie prolongaty. Po wygaśnięciu grupy trybu failover wyzwala pracy awaryjnej. Następnie odbiornik odczytu i zapisu staje się dostępny, a połączenia z nim przestają się kończyć niepowodzeniem (2). Na poniższym diagramie przedstawiono dwa etapy procesu odzyskiwania.

> [!NOTE]
> Jeśli awaria w regionie podstawowym zostanie złagodzona w okresie prolongaty, menedżer ruchu wykrywa przywrócenie łączności w regionie podstawowym i przełącza ruch użytkownika z powrotem do wystąpienia aplikacji w regionie A. To wystąpienie aplikacji wznawia i działa w trybie odczytu i zapisu przy użyciu podstawowej bazy danych w regionie A, jak pokazano na poprzednim diagramie.

![Scenariusz 2. Etapy odzyskiwania po awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Jeśli awaria występuje w regionie B, menedżer ruchu wykrywa awarię punktu końcowego web-app-2 w regionie B i oznacza go zdegradowany (1). W międzyczasie grupa trybu failover przełącza odbiornik tylko do odczytu do regionu A (2). Ta awaria nie ma wpływu na środowisko użytkownika końcowego, ale podstawowa baza danych jest narażona podczas awarii. Na poniższym diagramie przedstawiono błąd w regionie pomocniczym:

![Scenariusz 2. Awaria regionu pomocniczego.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Po usunięciu awarii pomocnicza baza danych jest natychmiast synchronizowana z podstawowego i odbiornik tylko do odczytu jest przełączany z powrotem do pomocniczej bazy danych w regionie B. Podczas synchronizacji wydajność podstawowego może mieć niewielki wpływ w zależności od ilości danych, które muszą być synchronizowane.

Ten wzór ma kilka **zalet:**

* Pozwala uniknąć utraty danych podczas tymczasowych awarii.
* Przestoje zależą tylko od tego, jak szybko menedżer ruchu wykrywa awarię łączności, która jest konfigurowalna.

**Kompromisem** jest to, że aplikacja musi być w stanie działać w trybie tylko do odczytu.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenariusz 3: Przeniesienie aplikacji do innej lokalizacji geograficznej bez utraty danych i prawie zerowych przestojów

W tym scenariuszu aplikacja ma następujące cechy:

* Użytkownicy końcowi uzyskują dostęp do aplikacji z różnych regionów geograficznych
* Aplikacja zawiera obciążenia tylko do odczytu, które nie zależą od pełnej synchronizacji z najnowszymi aktualizacjami
* Dostęp do zapisu danych powinien być obsługiwany w tej samej lokalizacji geograficznej dla większości użytkowników
* Opóźnienie odczytu ma kluczowe znaczenie dla środowiska użytkownika końcowego

Aby spełnić te wymagania, należy zagwarantować, że urządzenie użytkownika **zawsze** łączy się z aplikacją wdrożoną w tej samej lokalizacji geograficznej dla operacji tylko do odczytu, takich jak przeglądanie danych, analizy itp. Podczas gdy operacje OLTP są przetwarzane w tej samej geografii **przez większość czasu**. Na przykład w ciągu dnia operacje OLTP są przetwarzane w tej samej lokalizacji geograficznej, ale w godzinach wolnych mogą być przetwarzane w innej lokalizacji geograficznej. Jeśli działanie użytkownika końcowego występuje najczęściej w godzinach pracy, można zagwarantować optymalną wydajność dla większości użytkowników przez większość czasu. Na poniższym diagramie przedstawiono tę topologię.

Zasoby aplikacji powinny być wdrażane w każdej lokalizacji geograficznej, gdzie masz znaczne zapotrzebowanie na użycie. Na przykład jeśli aplikacja jest aktywnie używana w Stanach Zjednoczonych, Unii Europejskiej i Azji Południowo-Wschodniej, aplikacja powinna zostać wdrożona we wszystkich tych regionach geograficznych. Podstawowa baza danych powinna być dynamicznie przełączana z jednej lokalizacji geograficznej do następnej na końcu godzin pracy. Metoda ta nazywana jest "podążać za słońcem". Obciążenie OLTP zawsze łączy się z bazą danych za pośrednictwem odbiornika odczytu i zapisu ** &lt;w błąd nazwa grupy&gt;.database.windows.net** (1). Obciążenie tylko do odczytu łączy się z lokalną bazą danych bezpośrednio przy użyciu ** &lt;nazwy&gt;serwera** punktu końcowego baz danych .database.windows.net (2). Menedżer ruchu jest skonfigurowany przy [metodzie routingu wydajności](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zapewnia, że urządzenie użytkownika końcowego jest podłączone do usługi sieci web w najbliższym regionie. Zarządca ruchu powinien być skonfigurowany z włączonym monitorowaniem punktów końcowych dla każdego punktu końcowego usługi sieci web (3).

> [!NOTE]
> Konfiguracja grupy trybu failover określa, który region jest używany do pracy awaryjnej. Ponieważ nowa podstawowa znajduje się w innej lokalizacji geograficznej, praca awaryjna powoduje dłuższe opóźnienie dla obciążeń OLTP i tylko do odczytu, dopóki region, który ma wpływ, powróci do trybu online.

![Scenariusz 3. Konfiguracja z podstawową w wschodnich stanach USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na koniec dnia, na przykład o godzinie 23:00 czasu lokalnego, aktywne bazy danych powinny zostać przeniesione do następnego regionu (Europa Północna). To zadanie można w pełni zautomatyzować przy użyciu [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md). Zadanie obejmuje następujące kroki:

* Przełączanie serwera podstawowego w grupie trybu failover do Europy Północnej przy użyciu przyjaznego trybu failover (1)
* Usuwanie grupy trybu failover między wschodnimi stanami USA a Europą Północną
* Utwórz nową grupę trybu failover o tej samej nazwie, ale między Europą Północną a Azją Wschodnią (2).
* Dodaj podstawową w Europie Północnej i drugorzędną w Azji Wschodniej do tej grupy trybu failover (3).

Na poniższym diagramie przedstawiono nową konfigurację po planowanym przemijeniu awaryjnym:

![Scenariusz 3. Przejście podstawowej do Europy Północnej.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Jeśli na przykład awaria występuje w Europie Północnej, automatyczna praca awaryjna bazy danych jest inicjowana przez grupę trybu failover, co skutecznie powoduje przeniesienie aplikacji do następnego regionu przed terminem (1).  W takim przypadku wschód USA jest jedynym regionem drugorzędnym, dopóki Europa Północna nie wróci do trybu online. Pozostałe dwa regiony obsługują klientów we wszystkich trzech regionach geograficznych, przełączając role. Usługa Azure Logic Apps musi zostać odpowiednio dostosowana. Ponieważ pozostałe regiony uzyskać dodatkowy ruch użytkownika z Europy, wydajność aplikacji ma wpływ nie tylko przez dodatkowe opóźnienia, ale także przez zwiększoną liczbę połączeń użytkowników końcowych. Po usunięciu przerwy w dostawie prądu w Europie Północnej pomocnicza baza danych jest natychmiast synchronizowana z bieżącą podstawową. Poniższy diagram ilustruje awarię w Europie Północnej:

![Scenariusz 3. Awaria w Europie Północnej.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Można skrócić czas, gdy środowisko użytkownika końcowego w Europie jest zdegradowany przez długie opóźnienie. Aby to zrobić, należy proaktywnie wdrożyć kopię aplikacji i utworzyć pomocnicze bazy danych w innym regionie lokalnym (Europa Zachodnia) jako zastąpienie wystąpienia aplikacji w trybie offline w Europie Północnej. Gdy ta ostatnia jest z powrotem w trybie online, możesz zdecydować, czy nadal korzystać z Europy Zachodniej, czy usunąć tam kopię aplikacji i wrócić do korzystania z Europy Północnej.

Główne **zalety** tego projektu to:

* Obciążenie aplikacji tylko do odczytu uzyskuje dostęp do danych w regionie szaf przez cały czas.
* Obciążenie aplikacji odczytu i zapisu uzyskuje dostęp do danych w najbliższym regionie w okresie najwyższej aktywności w każdej lokalizacji geograficznej
* Ponieważ aplikacja jest wdrażana w wielu regionach, może przetrwać utratę jednego z regionów bez żadnych znaczących przestojów.

Ale są pewne **kompromisy:**

* Regionalna awaria powoduje, że geografia ma wpływ na dłuższe opóźnienie. Zarówno do odczytu i zapisu tylko do odczytu obciążeń jest obsługiwany przez aplikację w innej lokalizacji geograficznej.
* Obciążenia tylko do odczytu musi połączyć się z innym punktem końcowym w każdym regionie.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planowanie ciągłości biznesowej: wybierz projekt aplikacji do odzyskiwania po awarii w chmurze

Określona strategia odzyskiwania po awarii w chmurze może łączyć lub rozszerzać te wzorce projektowania, aby najlepiej odpowiadać potrzebom aplikacji.  Jak wspomniano wcześniej, wybrana strategia jest oparta na umowy SLA, którą chcesz zaoferować klientom, oraz na topologii wdrażania aplikacji. Aby ułatwić prowadzenie decyzji, w poniższej tabeli porównano wybory oparte na celu punktu odzyskiwania (RPO) i szacowanym czasie odzyskiwania (ERT).

| Wzorce | CEL PUNKTU ODZYSKIWANIA | Ert |
|:--- |:--- |:--- |
| Aktywne-pasywne wdrożenie do odzyskiwania po awarii z dostępem do bazy danych |Dostęp do odczytu i zapisu < 5 sek. |Czas wykrywania awarii + czas wygaśnięcia DNS |
| Aktywne-aktywne wdrażanie do równoważenia obciążenia aplikacji |Dostęp do odczytu i zapisu < 5 sek. |Czas wykrywania awarii + czas wygaśnięcia DNS |
| Aktywne-pasywne wdrożenie w celu zachowania danych |Dostęp tylko do odczytu < 5 sek. | Dostęp tylko do odczytu = 0 |
||Dostęp do odczytu i zapisu = zero | Dostęp do odczytu i zapisu = Czas wykrywania awarii + okres prolongaty z utratą danych |
|||

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md)
* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatycznego trybu failover, zobacz [Grupy auto-trybu failover](sql-database-auto-failover-group.md).
* Aby uzyskać informacje na temat aktywnej replikacji geograficznej z pulami elastycznymi, zobacz [Strategie odzyskiwania po awarii puli elastycznej](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
