---
title: Użyj usługi Azure Resource Health, aby monitorować kondycję bazy danych SQL | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Health, aby monitorować kondycję bazy danych SQL, pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure wpłynie na Twoje zasoby programu SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483069"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Umożliwia Rozwiązywanie problemów z łącznością dla usługi Azure SQL Database Resource Health

## <a name="overview"></a>Przegląd

[Usługa Resource Health](../service-health/resource-health-overview.md#getting-started) dla bazy danych SQL Database pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure wpłynie na Twoje zasoby SQL. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

![Przegląd](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontrole kondycji

Kondycja zasobu określa kondycję zasobu bazy danych SQL, sprawdzając sukcesów i niepowodzeń logowania się do zasobu. Obecnie usługa kondycja zasobu dla zasobu bazy danych SQL sprawdza niepowodzenia logowania z powodu błędu systemu, a nie błąd użytkownika. Stan kondycji zasobu jest aktualizowany co 1 – 2 minutach.

## <a name="health-states"></a>Stany kondycji

### <a name="available"></a>Dostępne

Stan **dostępne** oznacza, że kondycja zasobu nie wykrył niepowodzeń logowania z powodu błędów systemowych na zasobu bazy danych SQL.

![Dostępne](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Obniżono poziom

Stan **Obniżona sprawność** oznacza, że usługa Resource Health zarejestrowała w większości zakończone sukcesem próby zalogowania się, ale wystąpiły pewne błędy. Są to najbardziej prawdopodobne błędy przejściowe logowania. Aby zmniejszyć wpływ problemów z łącznością powodowanych przez błędy przejściowe logowania, zaimplementuj [Logika ponawiania próby](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) w kodzie.

![Obniżono poziom](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niedostępny

Stan **Unavailable** oznacza, że usługa Resource Health wykrył nieudanych prób zalogowania spójne do zasobu bazy danych SQL. Jeśli zasób pozostaje w tym stanie przez dłuższy czas, skontaktuj się z pomocy technicznej.

![Niedostępny](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Nieznane

Stan kondycji **nieznany** wskazuje, że kondycja zasobu nie odebrał informacji na temat tego zasobu na więcej niż 10 minut. Mimo że ten stan nie jest ostateczną wskazanie stan zasobu, to ważny punkt danych w proces rozwiązywania problemów. Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na dostępne po kilku minutach. Jeśli występują problemy z zasobem, Nieznana kondycja może sugerować, że zdarzenie na platformie ma wpływ na zasób.

![Nieznane](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informacje historyczne

Możesz uzyskać dostęp do 14 dni historii kondycji w historii kondycji części Resource Health. Sekcji zawiera również z powodu przestojów (jeśli jest dostępna) dla przestoje zgłoszone przez Resource Health. Obecnie w platformie Azure wyświetlany jest czas przestoju zasobu bazy danych SQL database na szczegółowości dwie minuty. W rzeczywistości przestój trwa mniej niż minutę prawdopodobnie — średnia 8s.

### <a name="downtime-reasons"></a>Przyczyny przestojów

W przypadku bazy danych SQL napotyka Przestój, aby zidentyfikować przyczynę jest wykonywana analiza. Jeśli jest dostępny, z powodu przestojów jest zgłaszany w historii kondycji części kondycji zasobu. Przyczyny przestojów są zazwyczaj publikowane w ciągu 30 minut po wystąpieniu zdarzenia.

#### <a name="planned-maintenance"></a>Planowana konserwacja

Infrastruktura platformy Azure okresowo przeprowadza planowaną konserwację — Uaktualnij składniki sprzętowe lub programowe w centrum danych. Gdy baza danych jest poddawany konserwacji, SQL może zakończyć niektóre istniejące połączenia i odmówić nowe. Niepowodzenia logowania podczas planowanej konserwacji mają zwykle charakter przejściowy i [Logika ponawiania próby](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) pomaga zredukować wpływ. Jeśli nadal występują błędy podczas logowania, skontaktuj się z pomocą techniczną.

#### <a name="reconfiguration"></a>Ponowne konfigurowanie

Reconfigurations są traktowane jako przejściowe warunki, a oczekuje się od czasu do czasu. Te zdarzenia mogą być wywoływane przez błędy ładowania równoważenia lub oprogramowanie i sprzęt. Każda aplikacja kliencka produkcji, który nawiązuje połączenie z bazą danych w chmurze powinny implementować niezawodne połączenia [Logika ponawiania próby](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), ponieważ może ułatwić uniknięcie sytuacji i powinien ogólnie przezroczystego błędy użytkownikowi końcowemu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Logika ponawiania próby dla błędów przejściowych](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Rozwiązywanie problemów, diagnozowanie i zapobieganie błędom połączeń SQL)
- Dowiedz się więcej o [Konfigurowanie alertów dotyczących kondycji zasobu](../service-health/resource-health-alert-arm-template-guide.md)
- Zapoznaj się z omówieniem programu [Resource Health](../service-health/resource-health-overview.md)
- [Usługa Resource Health — często zadawane pytania](../service-health/resource-health-faq.md)
