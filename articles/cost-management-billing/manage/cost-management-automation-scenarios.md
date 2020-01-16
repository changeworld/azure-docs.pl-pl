---
title: Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami na platformie Azure | Microsoft Docs
description: Dowiedz się, jak typowe scenariusze rozliczeń i zarządzania kosztami są mapowane na różne interfejsy API.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992818"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami

W tym artykule wymieniono typowe scenariusze dotyczące rozliczeń i zarządzania kosztami na platformie Azure. Scenariusze te są mapowane na dostępne interfejsy API. W ramach każdego mapowania scenariusza na interfejs API można znaleźć podsumowanie interfejsów API i udostępnianych przez nich funkcji.

## <a name="common-scenarios"></a>Typowe scenariusze

Interfejsów API rozliczeń i zarządzania kosztami można używać w kilku scenariuszach, aby uzyskać odpowiedzi na pytania dotyczące kosztów i użycia. Oto zarys typowych scenariuszy:

- **Uzgadnianie faktur**: czy w firmie Microsoft naliczane są odpowiednie kwoty?  Jaki jest mój rachunek i czy mogę go obliczyć samodzielnie?

- **Opłaty krzyżowe**: teraz, gdy wiemy, jak jest naliczana opłata, kto w mojej organizacji musi płacić?

- **Optymalizacja kosztów**: wiemy, ile z nich zostało naliczone. Jak zoptymalizować wydatki na platformę Azure?

- **Śledzenie kosztów**: Chcę zobaczyć, jak dużo wydatków i z nich korzystamy na platformie Azure. Jakie są trendy? Jak lepiej korzystać z zasobów?

- **Wydatki na platformę Azure w miesiącu**: Jaka jest aktualna liczba wydatków bieżącego miesiąca? Czy należy wprowadzić jakieś zmiany dotyczące wydatków i/lub używania platformy Azure? W jakie dni miesiąca użycie platformy Azure jest największe?

- **Alerty**: Jak mogę skonfigurować użycie oparte na zasobach lub alerty pieniężne?

## <a name="scenario-to-api-mapping"></a>Mapowanie scenariuszy na interfejsy API

|         API        | Uzgadnianie faktur    | Opłaty łączone    | Optymalizacja kosztów    | Śledzenie kosztów    | Wydatki w środku miesiąca    | Alerty    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budżety                     |                           |                  |           X          |                  |                    |     X     |
| Opłaty za korzystanie z witryny Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Arkusz cen                 |             X             |         X        |           X          |         X        |          X         |           |
| Rekomendacje dotyczące rezerwacji |                           |                  |           X          |                  |                    |           |
| Szczegóły rezerwacji         |                           |                  |           X          |         X        |                    |           |
| Podsumowania rezerwacji       |                           |                  |           X          |         X        |                    |           |
| Szczegóły użycia               |             X             |         X        |           X          |         X        |          X         |     X     |
| Okresy rozliczeniowe             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nieklasyfikowane użycie               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Mapowanie scenariuszy na interfejsy API nie obejmuje interfejsów API użycia w przypadku subskrypcji Enterprise. Jeśli to możliwe, w nowych scenariuszach programistycznych używaj ogólnych interfejsów API użycia.

## <a name="api-summaries"></a>Podsumowania interfejsów API

### <a name="consumption"></a>Zużycie
Klienci korzystający z subskrypcji Web Direct i Enterprise mogą używać następujących interfejsów API, o ile nie zaznaczono inaczej:

-   [Interfejs API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets) (*tylko Klienci korporacyjni*): Tworzenie budżetów kosztów i użycia dla zasobów, grup zasobów lub mierników rozliczeń. Po utworzeniu budżetów można skonfigurować alerty umożliwiające powiadamianie o przekroczeniu zdefiniowanych progów budżetów. Można również skonfigurować akcje, które mają być wykonywane po osiągnięciu kwoty budżetu.

-   [Interfejs API opłat w witrynie Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): pobieranie danych dotyczących obciążeń i użycia we wszystkich zasobach portalu Azure Marketplace (oferty partnerów platformy Azure). Danych tych można użyć do sumowania kosztów wszystkich zasobów witryny Marketplace lub analizowania kosztów/użycia określonych zasobów.

-   [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*tylko Klienci korporacyjni*): uzyskiwanie niestandardowych cen dla wszystkich liczników. Przedsiębiorstwa mogą łączyć te dane z danymi użycia oraz informacjami o użyciu witryny Marketplace w celu obliczania kosztów.

-   [Interfejs API zaleceń dotyczących rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Uzyskaj zalecenia dotyczące zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia ułatwiają analizowanie przewidywanych oszczędności i kwot zakupów. Aby uzyskać więcej informacji, zobacz temat [APIs for Azure reservation automation](../reservations/reservation-apis.md) (Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure).

-   [Interfejs API szczegółów rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Zobacz informacje dotyczące zakupionych wcześniej rezerwacji maszyn wirtualnych, takich jak zużycie zarezerwowane w porównaniu z ilością. Dane można wyświetlić na poziomie poszczególnych maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz temat [APIs for Azure reservation automation](../reservations/reservation-apis.md) (Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure).

-   [Interfejs API podsumowań rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Zobacz zagregowane informacje dotyczące rezerwacji maszyn wirtualnych zakupionych przez Twoją organizację, takich jak ilości zarezerwowane w przypadku użycia w agregacji. Aby uzyskać więcej informacji, zobacz temat [APIs for Azure reservation automation](../reservations/reservation-apis.md) (Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure).

-   [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails): Uzyskaj informacje o opłatach i użyciu dla wszystkich zasobów platformy Azure od firmy Microsoft. Informacje te mają postać szczegółowych rekordów i aktualnie są udostępniane raz dziennie dla jednego miernika. Informacji tych można użyć do sumowania kosztów wszystkich zasobów lub analizowania kosztów/użycia określonych zasobów.

-   [Interfejs API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Uzyskaj stawki zliczania, jeśli jesteś klientem bezpośrednio korzystającym z sieci Web. Zwrócone informacje można połączyć z informacjami o użyciu zasobów w celu ręcznego obliczenia kwoty rachunku.

-   [Nietaryfowy interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)): Pobierz surowe informacje o użyciu, zanim platforma Azure wykonuje pomiary/obciążenie.

### <a name="billing"></a>Rozliczenia
-   [Interfejs API okresów rozliczeniowych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Określ okres rozliczeniowy do przeanalizowania wraz z identyfikatorami faktur dla tego okresu. Identyfikatorów faktur można używać razem z interfejsem API faktur.

-   [Interfejs API faktur](/rest/api/billing/2019-10-01-preview/invoices): Pobierz adres URL pobierania dla faktury dla okresu rozliczeniowego w postaci pliku PDF.

### <a name="enterprise-consumption"></a>Użycie w ramach subskrypcji Enterprise
Następujące interfejsy API są dostępne tylko w przypadku korzystania z subskrypcji Enterprise:

-   [Interfejs API podsumowania balansu](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Uzyskaj miesięczne podsumowanie informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Informacje te mogą dotyczyć bieżącego okresu rozliczeniowego lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą porównać te dane z ręcznie obliczonymi podsumowaniami opłat. Ten interfejs API nie udostępnia informacji dotyczących określonych zasobów ani zagregowanego widoku kosztów.

-   [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Uzyskaj informacje na temat użycia platformy Azure (oferty firmy Microsoft) w bieżącym miesiącu, określonym okresie rozliczeniowym lub w niestandardowym okresie dat. Za pomocą tych danych przedsiębiorstwa mogą ręcznie obliczyć rachunki na podstawie stawki i zużycia. Przedsiębiorstwa mogą również użyć informacji dla poszczególnych działów/organizacji w celu podzielenia kosztów na organizacje. Dane te zapewniają wgląd w użycie/koszty z podziałem na zasoby.

-   [Interfejs API z opłatą w sklepie Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Uzyskaj informacje na temat użycia platformy Azure (ofert partnerskich) w bieżącym miesiącu, określonym okresie rozliczeniowym lub w niestandardowym okresie dat. Za pomocą tych danych przedsiębiorstwa mogą ręcznie obliczyć rachunki na podstawie stawki i zużycia. Przedsiębiorstwa mogą również użyć informacji dla poszczególnych działów/organizacji w celu podzielenia kosztów na organizacje. Ten interfejs API zapewnia wgląd w użycie/koszty z podziałem na zasoby.

-   [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Uzyskaj odpowiednią stawkę za każdy licznik dla danego okresu rejestracji i rozliczeń. Informacje o stawce można łączyć z danymi użycia oraz informacjami o użyciu witryny Marketplace w celu ręcznego obliczenia kwoty rachunku.

-   [Interfejs API okresów rozliczeniowych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Pobierz listę okresów rozliczeniowych. Interfejs API udostępnia również właściwość, która wskazuje trasę interfejsu API dla czterech zestawów danych interfejsu API przedsiębiorstwa, które odnoszą się do okresu rozliczeniowego: BalanceSummary, UsageDetails, opłaty w witrynie Marketplace i arkusza cen.

-   [Interfejs API zaleceń dotyczących wystąpień zarezerwowanych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Spójrz na 7 dni, 30 dni lub 60 dni użytkowania maszyn wirtualnych i uzyskaj pojedyncze i udostępnione zalecenia dotyczące zakupu. Za pomocą tego interfejsu API można analizować przewidywane oszczędności i zalecane kwoty zakupów. Aby uzyskać więcej informacji, zobacz temat [APIs for Azure reservation automation](../reservations/reservation-apis.md) (Interfejsy API na potrzeby automatyzacji rezerwacji platformy Azure).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Czym różnią się interfejsy API raportowania korporacyjnego od interfejsów API użycia? Kiedy używa się tych interfejsów?
Te interfejsy API mają podobny zestaw funkcji i umożliwiają odpowiadanie na wiele tych samych pytań w obszarze rozliczeń i zarządzania kosztami. Są one jednak przeznaczone dla różnych użytkowników:

- Interfejsy API raportowania korporacyjnego są dostępne dla klientów, którzy podpisali umowę Enterprise Agreement z firmą Microsoft i mogą korzystać z negocjowanych zobowiązań pieniężnych i niestandardowych cen. Interfejsy API wymagają klucza, który można uzyskać w witrynie [Enterprise Portal](https://ea.azure.com). Opis tych interfejsów API można znaleźć w temacie [Przegląd interfejsów API raportowania dla klientów korporacyjnych](enterprise-api.md).

- Interfejsy API użycia są dostępne dla wszystkich klientów z kilkoma wyjątkami. Aby uzyskać więcej informacji, zobacz [Przegląd interfejsu API użycia platformy Azure](consumption-api-overview.md) i [Dokumentacja interfejsu API użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption/). W najnowszych scenariuszach programistycznych zalecamy używanie udostępnionych interfejsów API.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Czym różni się interfejs API szczegółów użycia od interfejsu API użycia?
Te interfejsy API udostępniają zasadniczo różne dane:

- [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails) udostępnia informacje o użyciu i kosztach platformy Azure dotyczące wystąpienia miernika. Udostępnione dane zostały już przetworzone przez system zliczania kosztów na platformie Azure, a koszty — wraz z ewentualnymi innymi zmianami — zostały zastosowane:

   - Zmiany na koncie dotyczące korzystania z przedpłaconych zobowiązań pieniężnych
   - Zmiany na koncie dotyczące rozbieżności użycia wykrytych przez platformę Azure

- [Interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)) udostępnia nieprzetworzone informacje o użyciu platformy Azure przed ich przekazaniem do systemu zliczania kosztów na platformie Azure. Dane te nie muszą być skorelowane z danymi użycia lub kwotą opłaty, które są generowane przez system pomiaru opłat platformy Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Czym różni się interfejs API faktur od interfejsu API szczegółów użycia?
Te interfejsy API udostępniają różne widoki tych samych danych:

- [Interfejs API faktur](/rest/api/billing/2019-10-01-preview/invoices) jest przeznaczony tylko dla klientów z subskrypcją Web Direct. Udostępnia on comiesięczne zestawienie na rachunku oparte na zagregowanych opłatach dla każdego typu miernika.

- [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails) udostępnia szczegółowy widok rejestru użycia/kosztów dla każdego dnia. Mogą z niego korzystać zarówno klienci z subskrypcją Enterprise, jak i subskrypcją Web Direct.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Czym różni się interfejs API arkusza cen od interfejsu API usługi RateCard?
Te interfejsy API udostępniają podobne zestawy danych, ale są przeznaczone dla różnych użytkowników:

- [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) udostępnia niestandardowy cennik, wynegocjowany dla klienta z subskrypcją Enterprise.

- [Interfejs API usługi RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) udostępnia publiczny cennik, przeznaczony dla klientów z subskrypcją Web Direct.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o używaniu interfejsów API platformy Azure do programowego uzyskiwania wglądu w użycie platformy Azure, zobacz [Przegląd interfejsu API użycia platformy Azure](consumption-api-overview.md) i [Omówienie interfejsu API rozliczeń platformy Azure](usage-rate-card-overview.md).

- Aby porównać fakturę z zawartością pliku szczegółowego dziennego użycia i raportami zarządzania kosztami w witrynie Azure Portal, zobacz [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](../understand/review-individual-bill.md).

- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
