---
title: Scenariusze automatyzacji dla rozliczeń platformy Azure i zarządzania kosztami | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 0d2b0f2d3fad318ac0152d92fe92614d8dadda1e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792824"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenariuszach automatyzacji do rozliczeń i kosztów zarządzania

W tym artykule wymieniono typowe scenariusze dotyczące rozliczeń platformy Azure i zarządzania kosztami. Jest on mapowany tych scenariuszach interfejsów API, które można użyć. W ramach każdego mapowania scenariusz do interfejsu API można znaleźć podsumowanie interfejsów API i funkcje, które oferują one.

## <a name="common-scenarios"></a>Typowe scenariusze

Można użyć rozliczenia i kosztów w kilku scenariuszach odpowiedzi na pytania dotyczące kosztów i związanych z użyciem interfejsów API zarządzania. Poniżej przedstawiono zarys typowych scenariuszy:

- **Faktury uzgadniania**: Czy Microsoft jest opłata w wysokości mnie odpowiednim proporcjom?  Co to jest Mój rachunek, a następnie może obliczyć je samodzielnie?

- **Opłaty za między**: Teraz, aby sprawdzić, ile opłata została naliczona, kto w organizacji, trzeba płacić?

- **Koszt optymalizacji**: Wiem, jak duża już został opłata. Jak mogę uzyskać lepiej wykorzystać pieniędzy, które spędzam na platformie Azure?

- **Śledzenie kosztów**: Chcę zobaczyć, ile jestem wydatków i korzystanie z platformy Azure wraz z upływem czasu. Jakie są trendy? Jak można zrobić lepiej?

- **Wydatków w miesiącu na platformie Azure**: Jaka jest mojego bieżącego miesiąca wydatków do daty Należy wprowadzać żadnych zmian w mojej wydatków i/lub użycie platformy Azure? Gdy w miesiącu mam I korzystanie z platformy Azure najczęściej?

- **Alerty**: Jak mogę skonfigurować opartego na zasobach zużycia lub alerty oparte na pieniężnych?

## <a name="scenario-to-api-mapping"></a>Mapowanie scenariusz do interfejsu API

|         Interfejs API        | Uzgadnianie faktury    | Opłaty za Cross    | Optymalizacja kosztów    | Śledzenie kosztów    | Midmonth wydatków    | Alerty    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budżety                     |                           |                  |           X          |                  |                    |     X     |
| Opłaty za korzystanie z witryny Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Arkusz cen                 |             X             |         X        |           X          |         X        |          X         |           |
| Rekomendacje dotyczące rezerwacji |                           |                  |           X          |                  |                    |           |
| Szczegóły rezerwacji         |                           |                  |           X          |         X        |                    |           |
| Podsumowania rezerwacji       |                           |                  |           X          |         X        |                    |           |
| Szczegóły użycia               |             X             |         X        |           X          |         X        |          X         |     X     |
| Okresy fakturowania             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| Usługi RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Użycie nieklasyfikowane               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Mapowanie scenariusz do interfejsu API nie zawiera interfejsów API przez przedsiębiorstwa. Jeśli to możliwe, na użytek ogólnych interfejsów API użycie nowych scenariuszy programowania.

## <a name="api-summaries"></a>Interfejs API podsumowania

### <a name="consumption"></a>Zużycie
Bezpośrednio w sieci Web i Enterprise klienci mogą używać wszystkich następujące interfejsy API, z wyjątkiem wskazanych wyjątków:

-   [Budżetów API](https://docs.microsoft.com/rest/api/consumption/budgets) (*tylko klienci korporacyjni*): Tworzenie budżetów kosztów lub użycia zasobów, grup zasobów lub liczników rozliczeń. Po utworzeniu budżetów można skonfigurować alerty powiadamiające o po po przekroczeniu zdefiniowanych progów budżetu. Można również skonfigurować akcje występują, gdy został osiągnięty kwoty budżetu.

-   [Portal Marketplace opłaty za interfejs API](https://docs.microsoft.com/rest/api/consumption/marketplaces): Pobierz opłaty i dane użycia dla wszystkich zasobów w portalu Azure Marketplace, (ofert partnerów platformy Azure). Do dodawania kosztów wszystkich zasobów w witrynie Marketplace lub zbadać koszty/użycia, na określonych zasobach, można użyć tych danych.

-   [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*tylko klienci korporacyjni*): Uzyskaj niestandardowe ceny dla wszystkich liczników. Przedsiębiorstw można użyć tych danych w połączeniu ze szczegółami użycia i informacje dotyczące użycia w portalu marketplace do obliczania kosztów na podstawie danych użycia i Portal marketplace. 

-   [Interfejs API zaleceń rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Uzyskaj zalecenia zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia ułatwiają analizowanie oczekiwanych oszczędności i kwot zakupu. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).

-   [Rezerwacja szczegóły interfejsu API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Zobacz informacje dotyczące zakupione wcześniej rezerwacji maszyny Wirtualnej, takie jak ilość użycia jest zarezerwowany i jaka jest używana. Możesz zobaczyć dane w poszczególnych maszyn wirtualnych — szczegóły na poziomie. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).

-   [Podsumowanie rezerwacji interfejsu API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Zobacz zagregowane informacje o rezerwacji maszyny Wirtualnej, które zakupiono Twojej organizacji, takich jak ile użycie jest zarezerwowany i jaka jest używana w agregacji. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).

-   [Interfejs API ze szczegółami użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails): Wymaga to opłat ani użycie informacji na temat wszystkich zasobów platformy Azure można uzyskać od firmy Microsoft. Informacje są w formie rekordów szczegółów użycia, które obecnie są emitowane raz na metr dziennie. Informacje umożliwia dodanie koszty wszystkich zasobów lub zbadać koszty/użycia, na określonych zasobach.

-   [Interfejs API usługi RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Jeśli jesteś klientem bezpośredni klienci sieci Web, należy uzyskać stawki miernika. Następnie służy pobranych informacjach o informacje o użyciu zasobów do obliczania ręcznego rachunki. 

-   [Niesklasyfikowaną zawartość interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)): Uzyskać informacje o użyciu raw, aby platforma Azure zrobi, wszelkie pomiaru/ładowania.

### <a name="billing"></a>Rozliczenia
-   [Okresy fakturowania API](https://docs.microsoft.com/rest/api/billing/billingperiods): Określ okresu rozliczeniowego do przeanalizowania wraz z faktury identyfikatory dla tego okresu. Za pomocą faktury identyfikatory interfejsu API faktur.

-   [Faktury API](https://docs.microsoft.com/rest/api/billing/invoices): Pobierz adres URL pobierania faktur dla okresu rozliczeniowego w postaci pliku PDF.

### <a name="enterprise-consumption"></a>Użycie przedsiębiorstwa
Następujące interfejsy API są tylko dla przedsiębiorstw:

-   [Saldo podsumowanie interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Comiesięczny uzyskać podsumowanie informacji na temat salda, nowe zakupy, opłaty za usługę Azure Marketplace, korekt i opłaty za użycie nadwyżkowe. Możesz uzyskać te informacje w bieżącym okresie rozliczeniowym lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą używać tych danych do porównania z ręcznie obliczeniowe opłaty podsumowania. Ten interfejs API zawiera informacje specyficzne dla zasobu lub zagregowany widok kosztów.

-   [Interfejs API ze szczegółami użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Uzyskaj informacje na temat użycia platformy Azure (z ofertami firmy Microsoft) dla bieżącego miesiąca, określonego okresu rozliczeniowego lub niestandardowego okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczenia są naliczane na podstawie szybkości i zużycia. Przedsiębiorstwa można również użyć informacji organizacji/dziale atrybut kosztów między różnymi organizacjami. Dane zawiera widok specyficznych dla zasobów użycia i kosztów.

-   [Opłata portalu Marketplace Store interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Uzyskaj informacje na temat użycia platformy Azure (ofert partnerów) dla bieżącego miesiąca, określonego okresu rozliczeniowego lub niestandardowego okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczenia są naliczane na podstawie szybkości i zużycia. Przedsiębiorstwa można również użyć informacji organizacji/dziale atrybut kosztów między różnymi organizacjami. Ten interfejs API udostępnia widok specyficznych dla zasobów użycia i kosztów.

-   [Ceny interfejsu API arkusza](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Uzyskaj stawkę w ramach każdego miernika dla danego rejestracji i okresu rozliczeniowego. Szybkość informacji w połączeniu ze szczegółami użycia i informacje dotyczące użycia w portalu marketplace służy do ręcznego obliczania rachunki.

-   [Okresy fakturowania API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Pobierz listę okresów rozliczeniowych. Interfejs API umożliwia także właściwości, która wskazuje trasy interfejsu API dla czterech zestawów danych interfejsu API przedsiębiorstwa, które odnoszą się do okresu rozliczeniowego: BalanceSummary UsageDetails, opłatach w portalu Marketplace i arkusza cen.

-   [Interfejs API zaleceń wystąpienia zarezerwowane](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Spójrz na 7 dni w ciągu 30 dni lub 60 dni użytkowania maszyny wirtualnej i otrzymuj rekomendacje z jednym i udostępnione zakupu. Ten interfejs API umożliwia analizowanie oczekiwanych oszczędności i zalecane kwoty zakupu. Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaka jest różnica między API użycia i interfejsy API raportowania Enterprise? Po każdym użyć?
Te interfejsy API mają podobny zestaw funkcji i pozwala uzyskać odpowiedzi na tej samej szerokiej gamy pytań w przestrzeni zarządzania rozliczeń i kosztów. Jednak ich celem są różne grupy odbiorców: 

- Interfejsy API raportowania usługi Enterprise są dostępne dla klientów, którzy mają podpisaną umowę Enterprise z firmą Microsoft, który przyznaje im dostęp do wynegocjowanym zobowiązań pieniężnych i ceny niestandardowe. Interfejsy API wymagają klucza, który można pobrać z [Enterprise Portal](https://ea.azure.com). Aby uzyskać opis tych interfejsów API, zobacz [Przegląd raportowania interfejsów API dla klientów korporacyjnych](billing-enterprise-api.md).

- Użycie interfejsy API są dostępne dla wszystkich klientów z pewnymi wyjątkami. Aby uzyskać więcej informacji, zobacz [Przegląd użycia platformy Azure, interfejsu API](billing-consumption-api-overview.md) i [dokumentacja interfejsu API użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption/). Firma Microsoft zaleca podana interfejsów API jako rozwiązanie dla najnowszych scenariuszy projektowych. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaka jest różnica między interfejs API szczegółów użycia i interfejs API użycia?
Interfejsy API stanowią danych różni się:

- [Interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails) Azure udostępnia informacje użycia i kosztów dla każdego wystąpienia licznika. Udostępnione dane został już przekazany przez koszt pomiaru użytkowania systemu Azure i ma koszt zastosowano, wraz z innymi możliwych zmian:

   - Zmiany do konto do użycia z zobowiązaniami pieniężnymi przedpłacone
   - Zmiany do konta dla rozbieżności użycia odnalezionych przez platformę Azure

- [Interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)) informacje pierwotne użycia platformy Azure, przed przekazaniem kosztów pomiaru użytkowania systemu Azure. Te dane mogą nie mieć wszelka korelacja z ilością użycia lub jest rozładowany, która jest widoczna po platformy Azure jest opłata w wysokości systemu pomiarów.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaka jest różnica między faktury interfejsu API i interfejs API szczegółów użycia?
Interfejsy API stanowią inny widok tych samych danych:

- [API faktury](https://docs.microsoft.com/rest/api/billing/invoices) jest bezpośredni klienci sieci Web tylko dla klientów. Zapewnia miesięczny pakiet zbiorczy na rachunku na podstawie łącznej opłat dla każdego typu miernika. 

- [Interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails) zawiera szczegółowy widok rekordów użycia i kosztów dla każdego dnia. Przedsiębiorstwa i bezpośredni klienci sieci Web klientów, można go użyć.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaka jest różnica między interfejsu API RateCard i interfejsu API arkusza cen?
Te interfejsy API zapewniają podobne zestawy danych, ale ma z różnych grup odbiorców:

- [Interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) zapewnia wynegocjowanym niestandardowe cen dla klientów firmowych.

- [Interfejsu API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) zapewnia publicznego cen dotyczą bezpośrednich klientów internetowych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak programowo uzyskać wgląd w użycie platformy Azure przy użyciu interfejsów API platformy Azure, zobacz [omówienie interfejsu API usługi Azure zużycie](billing-consumption-api-overview.md) i [omówienie interfejsu API rozliczeń w usłudze Azure](billing-usage-rate-card-overview.md).

- Aby porównać fakturze za pomocą szczegółowych codzienne pliku użycia i raportów rozwiązania cost management w witrynie Azure portal, zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md).

- Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
