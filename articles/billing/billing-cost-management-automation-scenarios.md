---
title: Rozliczenia platformy Azure i scenariuszach automatyzacji zarządzania kosztami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak często spotykane rozliczenia i kosztów zarządzania, które scenariusze są mapowane na różne interfejsy API.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 064f141680e62d7102d7c3332e4d93efd6c28037
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258750"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scenariuszach automatyzacji rozliczeń i kosztów zarządzania

Typowe scenariusze dotyczące rozliczeń i kosztów zarządzania zostały zidentyfikowane poniżej i mapowany do różnych interfejsów API, który może służyć w tych scenariuszach. Podsumowanie wszystkich interfejsów API dostępna i funkcje, które oferują one znajdują się poniżej scenariusza mapowania interfejsów API.

## <a name="common-scenarios"></a>Typowe scenariusze

Można użyć rozliczenia i kosztów w kilku scenariuszach odpowiedzi na pytania dotyczące kosztów i związanych z użyciem interfejsów API zarządzania.  Konspekt typowych scenariuszy znajduje się poniżej.

- **Faktury uzgadniania** — czy Microsoft jest opłata w wysokości mnie odpowiednim proporcjom?  Co to jest Mój rachunek, i można obliczyć je samodzielnie?

- **Krzyżowe opłaty** — teraz, aby sprawdzić, ile opłata została naliczona, kto w organizacji, trzeba płacić?

- **Koszt optymalizacji** -wiem, jak duża już został opłata. Jednakże jak można uzyskać lepiej wykorzystać pieniędzy, które spędzam na platformie Azure?

- **Śledzenie kosztów** — chcę zobaczyć, ile jestem wydatków i korzystanie z platformy Azure wraz z upływem czasu. Jakie są trendy? Jak mogę wykonywać czynności lepiej?

- **Wydatki na platformę Azure w miesiącu** — ile mojego bieżącego miesiąca jest jego wydatków do daty? Należy wprowadzać żadnych zmian w mojej wydatków i/lub użycie platformy Azure? Gdy w miesiącu mam I korzystanie z platformy Azure najczęściej?

- **Konfigurowanie alertów** — chcę skonfigurować opartego na zasobach zużycia lub pieniężnych na podstawie Alertowanie.

## <a name="scenario-to-api-mappings"></a>Scenariusz mapowania interfejsów API

|         Scenariusz/interfejsu API        | Uzgadnianie faktury    | Krzyżowe opłaty    | Optymalizacji kosztów    | Śledzenie kosztów    | Opublikowany w połowie miesiąca wydatków    | Alerty    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budżety                     |                           |                  |           X          |                  |                    |     X     |
| Rynków                |             X             |         X        |           X          |         X        |          X         |     X     |
| Arkusz cen                 |             X             |         X        |           X          |         X        |          X         |           |
| Zalecenia dotyczące rezerwacji |                           |                  |           X          |                  |                    |           |
| Szczegóły rezerwacji         |                           |                  |           X          |         X        |                    |           |
| Podsumowanie rezerwacji       |                           |                  |           X          |         X        |                    |           |
| Szczegółowe zestawienie użycia               |             X             |         X        |           X          |         X        |          X         |     X     |
| Okresy fakturowania             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| Usługi RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Użycie nieklasyfikowane               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenariusz mapowania interfejsów API poniżej nie uwzględniają interfejsów API przez przedsiębiorstwa. Jeśli to możliwe, na użytek ogólnych interfejsów API użycie nowych scenariuszy programowania.

## <a name="api-summaries"></a>Interfejs API podsumowania

### <a name="consumption"></a>Zużycie
(*Bezpośredni klienci sieci web i klientów korporacyjnych dla wszystkich interfejsów API, z wyjątkiem tych o nazwie out poniżej*)

-   **Budżetów** (*tylko klienci korporacyjni*): Użyj [API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets) do Tworzenie budżetów kosztów lub użycia zasobów, grup zasobów lub liczników rozliczeń. Po utworzeniu budżetów alerty można skonfigurować do wysyłania powiadomień o przekroczeniu progów budżetu zdefiniowanych przez użytkownika. Można również skonfigurować akcje po przekroczeniu budżetu.
-   **Rynków**: Użyj [API opłaty w portalu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) można pobrać opłat i dane użycia dla wszystkich zasobów w portalu Marketplace (Azure 3rd ofert innych firm). Te dane mogą służyć do dodawania kosztów wszystkich zasobów w witrynie Marketplace lub zbadać koszty/użycia, na określone zasoby.
-   **Arkusz cen** (*tylko klienci korporacyjni*): Klienci korporacyjni mogą użyć [interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) można pobrać ich niestandardowe ceny dla wszystkich liczników. Przedsiębiorstwa mogą używać tych danych w połączeniu z szczegóły i rynków przeznaczonych użycie informacji o użyciu do obliczenia przy użyciu dane dotyczące użycia i portalu marketplace. 
-   **Zalecenia dotyczące rezerwacji**: Użyj [interfejsu API zaleceń rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) można pobrać zaleceń zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia są przeznaczone do pozwala klientom na analizowanie oczekiwanych oszczędności i kwot zakupu. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).
-   **Szczegóły rezerwacji**: Użyj [API szczegóły rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) Aby wyświetlić informacje dotyczące zakupione wcześniej rezerwacji maszyny Wirtualnej, takie jak ilość użycia jest zarezerwowany i jaka jest używana. Możesz zobaczyć dane w poszczególnych maszyn wirtualnych — szczegóły na poziomie. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).
-   **Podsumowanie rezerwacji**: Użyj [API podsumowań z okresu rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) wyświetlić zagregowane informacje o rezerwacji maszyn wirtualnych zakupionych Twojej organizacji, takich jak ile użycie jest zarezerwowany i jaka jest używana w agregacji. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).
-   **Szczegóły użycia**: Użyj [interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails) na pobieranie opłat i danych użycia na platformie Azure wszystkie 1 zasoby innych firm. Informacje są w formie rekordów szczegółów użycia, które obecnie są emitowane raz na metr dziennie. Informacje mogą służyć do dodawania kosztów wszystkich zasobów lub zbadać koszty/użycia, na określone zasoby.
-   **Usługi RateCard**: Można użyć bezpośrednich klientów sieci Web [interfejsu API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) można pobrać ich stawki miernika. Zwracane informacje mogli używać z informacjami o użycie zasobów na potrzeby ręcznego obliczania rachunki. 
-   **Niesklasyfikowaną zawartość użycia**: Możesz użyć [nieklasyfikowane interfejs API użycia](https://msdn.microsoft.com/library/azure/mt219003.aspx) można pobrać informacji o nieprzetworzone obciążenie, zanim wszystkie pomiaru/ładowania wykonywane przez usługę Azure.

### <a name="billing"></a>Rozliczenia
-   **Okresy fakturowania**: Użyj [interfejs API rozliczeń okresy](https://docs.microsoft.com/rest/api/billing/billingperiods) ustalenie okresu rozliczeniowego do przeanalizowania wraz z faktury identyfikatory dla tego okresu. Faktury identyfikatory mogą być używane z faktury interfejs API przedstawiony poniżej. 
-   **Faktury**: Użyj [API faktur](https://docs.microsoft.com/rest/api/billing/invoices) można pobrać adresu URL pobierania faktur w danym okresie rozliczeniowym w postaci pliku PDF.

### <a name="enterprise-consumption"></a>Użycie przedsiębiorstwa
*(Wszystkie interfejsy API przedsiębiorstwa tylko)*

-   **Podsumowanie salda**: Użyj [API podsumowania salda](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) uzyskać podsumowanie informacji na temat salda, nowe zakupy, opłaty za usługę Azure Marketplace, korekt i opłaty za użycie nadwyżkowe co miesiąc. Możesz uzyskać te informacje w bieżącym okresie rozliczeniowym lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą używać tych danych do porównania z ręcznie obliczeniowe opłaty podsumowania. Ten interfejs API zawiera informacje specyficzne dla zasobu lub zagregowany widok kosztów.
-   **Szczegóły użycia**: Użyj [interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) można pobrać strony 1. użycie platformy Azure szczegółowe informacje dotyczące bieżącego miesiąca, określonego okresu rozliczeniowego lub niestandardowego okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczenia rachunku na podstawie szybkości i zużycia i można również użyć informacji o organizacji/dziale obecny atrybut kosztów między różnymi organizacjami. Dane zawiera widok specyficznych dla zasobów użycia i kosztów.
-   **Opłata za magazyn Marketplace**: Użyj [interfejsu API opłaty Store Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) można pobrać strony 3 użycie platformy Azure szczegółowe informacje dotyczące bieżącego miesiąca, określonego okresu rozliczeniowego lub niestandardowego okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczenia rachunku na podstawie szybkości i zużycia i można również użyć informacji o organizacji/dziale obecny atrybut kosztów między różnymi organizacjami. Opłata za magazyn Marketplace interfejsu API zawiera widok specyficznych dla zasobów użycia i kosztów.
-   **Arkusz cen**: [Interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) przewiduje stawkę w ramach każdego miernika danego rejestracji i okres rozliczeniowy. Te informacje o szybkości może służyć w połączeniu z szczegóły i rynków przeznaczonych użycie informacji o użyciu do obliczania ręcznego rachunki.
-   **Okresy fakturowania**: Użyj [interfejs API rozliczeń okresy](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) można pobrać listy rozliczeń okresy wraz z właściwością, wskazując interfejsu API trasy dla czterech zestawów danych interfejsu API przedsiębiorstwa, które odnoszą się do tego okresu rozliczeniowego — BalanceSummary, UsageDetails, w portalu Marketplace Naliczanie opłat i arkusza cen.
-   **Zalecenia dotyczące platformy Azure rezerwacji**: [Zarezerwowane wystąpienia interfejsu API zaleceń](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) patrzy na 7 dni przez klienta, 30 dni lub 60 dni użytkowania maszyny wirtualnej i oferuje zalecenia dotyczące pojedynczego i udostępnione zakupu. Wystąpienie zarezerwowane, interfejs API umożliwia klientom analizowanie oczekiwano oszczędności kosztów i zalecane kwoty zakupu. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaka jest różnica między API użycia i interfejsy API raportowania Enterprise? Po każdym użyć?
Te interfejsy API mają podobny zestaw funkcji i pozwala uzyskać odpowiedzi na tej samej szerokiej gamy pytań w przestrzeni zarządzania rozliczeń i kosztów. Jednak każdy interfejs API jest przeznaczony dla różnych odbiorców: 

- **Interfejsy API raportowania usługi Enterprise**: Te interfejsy API są dostępne dla klientów, którzy mają podpisaną umowę Enterprise z firmą Microsoft, który przyznaje im dostęp do wynegocjowanym zobowiązań pieniężnych i ceny niestandardowe. Interfejsy API wymagają klucza do wykorzystania, że można uzyskać z [Enterprise Portal](https://ea.azure.com). Aby uzyskać opis tych interfejsów API, zobacz [Przegląd raportowania interfejsów API dla klientów korporacyjnych](billing-enterprise-api.md).

- **Użycie interfejsów API**: Te interfejsy API są dostępne dla wszystkich klientów z pewnymi wyjątkami. Aby uzyskać więcej informacji, zobacz [Przegląd użycia platformy Azure, interfejsu API](billing-consumption-api-overview.md) i [dokumentacja interfejsu API użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption/). Podana interfejsy API są zalecanym rozwiązaniem dla najnowszych scenariuszy projektowych. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaka jest różnica między interfejs API szczegółów użycia i interfejs API użycia?
Interfejsy API stanowią danych różni się:

- **Szczegóły użycia**: [Interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails) Azure udostępnia informacje użycia i kosztów dla każdego wystąpienia licznika. Dostarczonych danych został już przekazany przez kosztów platformy Azure systemu zbierania danych i ma koszt dotyczy wraz z innymi możliwych zmian:

    - Zmiany do konto do użycia z zobowiązaniami pieniężnymi przedpłacone
    - Zmiany do konta dla rozbieżności użycia odnalezionych przez platformę Azure

- **Użycie**: [Interfejs API użycia](https://msdn.microsoft.com/library/Mt219003.aspx) zapewnia kosztów systemu pomiarów informacji pierwotne użycia platformy Azure, przed przekazaniem za pośrednictwem platformy Azure. Te dane nie mogą mieć wszelka korelacja z ilością użycia lub jest rozładowany, która jest widoczna po platformy Azure jest opłata w wysokości systemu pomiarów.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaka jest różnica między faktury interfejsu API i interfejs API szczegółów użycia?
Interfejsy API stanowią inny widok tych samych danych. [API faktury](https://docs.microsoft.com/rest/api/billing/invoices) jest bezpośredni klienci sieci Web tylko dla klientów i dostarcza miesięcznych rozwinięcia na rachunku na podstawie łącznej opłat dla każdego typu miernika. [Interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails) zawiera szczegółowy widok rekordów użycia i kosztów za każdy dzień i mogą być używane przez klientów przedsiębiorstwa i bezpośredni klienci sieci Web.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaka jest różnica między interfejsu API RateCard i interfejsu API arkusza cen?
Te interfejsy API zapewniają podobne zestawy danych, ale mają różnych grup odbiorców. Poniższe informacje.

- Interfejs API arkusza cen: [Interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) zapewnia wynegocjowanym niestandardowe cen dla klientów firmowych.
- Interfejs API usługi RateCard: [Interfejsu API RateCard](https://msdn.microsoft.com/library/mt219005.aspx) udostępnia publiczne dostępnego z cennika, który ma zastosowanie do bezpośrednich klientów internetowych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak programowo uzyskać wgląd w użycie platformy Azure przy użyciu interfejsów API platformy Azure, zobacz [omówienie interfejsu API usługi Azure zużycie](billing-consumption-api-overview.md) i [omówienie interfejsu API rozliczeń platformy Azure](billing-usage-rate-card-overview.md).
- Aby porównać fakturze za pomocą szczegółowych codzienne pliku użycia i raportów rozwiązania cost management w witrynie Azure portal, zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md)
- Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
