---
title: Używanie Azure Resource Health do monitorowania kondycji bazy danych
description: Użyj usługi Azure Resource Health, aby monitorować kondycję bazy danych SQL, pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure wpłynie na Twoje zasoby programu SQL.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208873"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Umożliwia Rozwiązywanie problemów z łącznością dla usługi Azure SQL Database Resource Health

## <a name="overview"></a>Omówienie

[Resource Health](../service-health/resource-health-overview.md#get-started) dla SQL Database pomaga zdiagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure ma wpływ na zasoby SQL. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

![Omówienie](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontrole kondycji

Kondycja zasobu określa kondycję zasobu bazy danych SQL, sprawdzając sukcesów i niepowodzeń logowania się do zasobu. Obecnie usługa kondycja zasobu dla zasobu bazy danych SQL sprawdza niepowodzenia logowania z powodu błędu systemu, a nie błąd użytkownika. Stan kondycji zasobu jest aktualizowany co 1 – 2 minutach.

## <a name="health-states"></a>Stany kondycji

### <a name="available"></a>Dostępne

Stan **dostępne** oznacza, że Resource Health nie wykrył niepowodzeń logowania z powodu błędów systemowych w Twoim zasobie SQL.

![Dostępne](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Obniżona wydajność

Stan **Obniżona sprawność** oznacza, że usługa Resource Health zarejestrowała w większości zakończone sukcesem próby zalogowania się, ale wystąpiły pewne błędy. Są to najbardziej prawdopodobne błędy przejściowe logowania. Aby zmniejszyć wpływ problemów z połączeniami spowodowanych przez przejściowe błędy logowania, należy wdrożyć [logikę ponowień](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) w kodzie.

![Obniżona wydajność](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niedostępny

Stan **niedostępne** oznacza, że Resource Health wykryła spójne błędy logowania do zasobu SQL. Jeśli zasób pozostaje w tym stanie przez dłuższy czas, skontaktuj się z pomocy technicznej.

![Niedostępny](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Nieznane

Stan kondycji **nieznany** wskazuje, że Resource Health nie otrzymał informacji o tym zasobie przez więcej niż 10 minut. Mimo że ten stan nie jest ostateczną wskazanie stan zasobu, to ważny punkt danych w proces rozwiązywania problemów. Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na dostępne po kilku minutach. Jeśli występują problemy z zasobem, Nieznana kondycja może sugerować, że zdarzenie na platformie ma wpływ na zasób.

![Nieznane](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informacje historyczne

Możesz uzyskać dostęp do 14 dni historii kondycji w historii kondycji części Resource Health. Sekcji zawiera również z powodu przestojów (jeśli jest dostępna) dla przestoje zgłoszone przez Resource Health. Obecnie platforma Azure pokazuje przestój zasobu usługi SQL Database z dokładnością do dwóch minut. W rzeczywistości przestój trwa prawdopodobnie krócej niż minutę — średni czas to 8 s.

### <a name="downtime-reasons"></a>Przyczyny przestojów

W przypadku bazy danych SQL napotyka Przestój, aby zidentyfikować przyczynę jest wykonywana analiza. Jeśli jest dostępny, z powodu przestojów jest zgłaszany w historii kondycji części kondycji zasobu. Przyczyny przestojów są zazwyczaj publikowane w ciągu 30 minut po wystąpieniu zdarzenia.

#### <a name="planned-maintenance"></a>Planowana konserwacja

Infrastruktura platformy Azure okresowo przeprowadza planowaną konserwację — Uaktualnij składniki sprzętowe lub programowe w centrum danych. Gdy baza danych jest poddawany konserwacji, SQL może zakończyć niektóre istniejące połączenia i odmówić nowe. Błędy logowania występujące podczas planowanej konserwacji są zwykle przejściowe i [logika ponowień](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) pomaga zmniejszyć wpływ. Jeśli nadal występują błędy podczas logowania, skontaktuj się z pomocą techniczną.

#### <a name="reconfiguration"></a>Ponowne konfigurowanie

Reconfigurations są traktowane jako przejściowe warunki, a oczekuje się od czasu do czasu. Te zdarzenia mogą być wywoływane przez błędy ładowania równoważenia lub oprogramowanie i sprzęt. Dowolna aplikacja produkcyjna klienta, która łączy się z bazą danych w chmurze, powinna implementować niezawodną [logikę ponawiania](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)połączenia, ponieważ może to pomóc w ograniczeniu tych sytuacji i ogólnie mówiąc, że błędy będą widoczne dla użytkownika końcowego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [logice ponawiania prób w przypadku błędów przejściowych](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Rozwiązywanie problemów, diagnozowanie i zapobieganie błędom połączeń SQL)
- Dowiedz się więcej o [konfigurowaniu Resource Health alertów](../service-health/resource-health-alert-arm-template-guide.md)
- Zapoznaj się z omówieniem [Resource Health](../service-health/resource-health-overview.md)
- [Resource Health często zadawane pytania](../service-health/resource-health-faq.md)
