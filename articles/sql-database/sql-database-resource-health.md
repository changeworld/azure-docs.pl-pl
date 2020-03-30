---
title: Monitorowanie kondycji bazy danych za pomocą usługi Azure Resource Health
description: Usługa Azure Resource Health służy do monitorowania kondycji bazy danych SQL, pomaga diagnozować i utrzymywać pomoc techniczną, gdy problem z platformą Azure wpływa na zasoby SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208873"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Use Resource Health to troubleshoot connectivity for Azure SQL Database (Rozwiązywanie problemów z łącznością z usługą Azure SQL Database za pomocą usługi Resource Health)

## <a name="overview"></a>Omówienie

[Kondycja zasobów](../service-health/resource-health-overview.md#get-started) dla bazy danych SQL pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure wpływa na zasoby SQL. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

![Omówienie](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontrole kondycji

Kondycja zasobów określa kondycję zasobu SQL, badając powodzenie i niepowodzenie logowania do zasobu. Obecnie kondycja zasobów dla zasobu bazy danych SQL tylko sprawdza błędy logowania z powodu błędu systemu, a nie błąd użytkownika. Stan kondycji zasobu jest aktualizowany co 1-2 minuty.

## <a name="health-states"></a>Stany zdrowia

### <a name="available"></a>Dostępne

Stan **Dostępne** oznacza, że kondycja zasobów nie wykryła błędów logowania z powodu błędów systemowych w zasobie SQL.

![Dostępne](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Obniżona wydajność

Stan **Obniżona sprawność** oznacza, że usługa Resource Health zarejestrowała w większości zakończone sukcesem próby zalogowania się, ale wystąpiły pewne błędy. Są to najprawdopodobniej przejściowe błędy logowania. Aby zmniejszyć wpływ problemów z połączeniem spowodowanych przez przejściowe błędy logowania, należy zaimplementować [logikę ponawiania w](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) kodzie.

![Obniżona wydajność](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niedostępny

Stan **Niedostępne** oznacza, że kondycja zasobów wykryła spójne błędy logowania do zasobu SQL. Jeśli zasób pozostaje w tym stanie przez dłuższy czas, skontaktuj się z pomocą techniczną.

![Niedostępny](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Nieznane

Stan kondycji **Unknown** wskazuje, że kondycja zasobu nie otrzymała informacji o tym zasobie przez więcej niż 10 minut. Mimo że ten stan nie jest ostatecznym wskazaniem stanu zasobu, jest ważnym punktem danych w procesie rozwiązywania problemów. Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na Dostępne po kilku minutach. Jeśli występują problemy z zasobem, nieznany stan kondycji może sugerować, że zdarzenie na platformie ma wpływ na zasób.

![Nieznane](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informacje historyczne

Dostęp do maksymalnie 14 dni historii zdrowia można uzyskać w sekcji Historia kondycji kondycji zasobów. Sekcja będzie również zawierać przyczynę przestojów (jeśli są dostępne) dla przestojów zgłaszanych przez kondycję zasobów. Obecnie platforma Azure pokazuje przestój zasobu usługi SQL Database z dokładnością do dwóch minut. W rzeczywistości przestój trwa prawdopodobnie krócej niż minutę — średni czas to 8 s.

### <a name="downtime-reasons"></a>Przyczyny przestojów

Gdy baza danych SQL doświadcza przestojów, analiza jest wykonywana w celu ustalenia przyczyny. Jeśli jest to możliwe, przyczyna przestoju jest raportowana w sekcji Historia kondycji kondycji zasobów. Przyczyny przestojów są zazwyczaj publikowane w ciągu 30 minut po wystąpieniu zdarzenia.

#### <a name="planned-maintenance"></a>Planowana konserwacja

Infrastruktura platformy Azure okresowo wykonuje planowaną konserwację — uaktualnianie składników sprzętu lub oprogramowania w centrum danych. Podczas gdy baza danych przechodzi konserwację, SQL może zakończyć niektóre istniejące połączenia i odmówić nowych. Błędy logowania wystąpiły podczas planowanej konserwacji są zazwyczaj przejściowe i [logiki ponawiania pomaga](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) zmniejszyć wpływ. Jeśli nadal występują błędy logowania, skontaktuj się z pomocą techniczną.

#### <a name="reconfiguration"></a>Ponowne konfigurowanie

Ponowne konfiguracje są uważane za warunki przejściowe i są oczekiwane od czasu do czasu. Zdarzenia te mogą być wyzwalane przez równoważenie obciążenia lub awarie oprogramowania/sprzętu. Każda aplikacja produkcyjna klienta, która łączy się z bazą danych w chmurze należy zaimplementować [logikę ponawiania ponawiania](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)połączenia, ponieważ pomogłoby to złagodzić te sytuacje i powinno ogólnie sprawić, że błędy będą przezroczyste dla użytkownika końcowego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ponawianiu błędów przejściowych w przypadku błędów przejściowych](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Rozwiązywanie problemów, diagnozowanie i zapobieganie błędom połączeń SQL)
- Dowiedz się więcej o [konfigurowaniu alertów kondycji zasobów](../service-health/resource-health-alert-arm-template-guide.md)
- Omówienie [kondycji zasobów](../service-health/resource-health-overview.md)
- [Często zadawane pytania dotyczące usługi Resource Health](../service-health/resource-health-faq.md)
