---
title: Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami platformy Azure | Microsoft Docs
description: Dowiedz się, jak typowe scenariusze rozliczeń i zarządzania kosztami są mapowane na różne interfejsy API.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443248"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenariusze automatyzacji dotyczące rozliczeń i zarządzania kosztami

W tym artykule wymieniono typowe scenariusze dotyczące rozliczeń i zarządzania kosztami platformy Azure. Te scenariusze są mapowane na interfejsy API, których można użyć. W ramach każdego mapowania scenariusz-interfejs API można znaleźć podsumowanie interfejsów API i oferowanych przez nich funkcji.

## <a name="common-scenarios"></a>Typowe scenariusze

Możesz użyć interfejsów API zarządzania rozliczeniami i kosztami w kilku scenariuszach, aby odpowiedzieć na pytania dotyczące kosztów i użycia. Oto konspekt typowych scenariuszy:

- **Uzgadnianie faktur**: Czy firma Microsoft obciąża mi właściwą kwotę?  Jaki jest mój rachunek i czy mogę go obliczyć samodzielnie?

- **Opłaty krzyżowe**: Teraz wiem, jak dużo opłaty są naliczane, kto w mojej organizacji musi płacić?

- **Optymalizacja kosztów**: Wiemy, ile z nich zostało naliczone. Jak mogę uzyskać więcej wydatków na platformę Azure?

- **Śledzenie kosztów**: Chcę zobaczyć, jak dużo wydatków i korzystamy z platformy Azure w miarę upływu czasu. Jakie są trendy? Jak można to zrobić lepiej?

- **Wydatki na platformę Azure w miesiącu**: Jaka jest aktualna liczba wydatków bieżącego miesiąca? Czy muszę wprowadzić wszelkie zmiany wydatków i/lub użycia platformy Azure? Kiedy w ciągu miesiąca zużywam platformę Azure?

- **Alerty**: Jak można skonfigurować użycie oparte na zasobach lub alerty pieniężne?

## <a name="scenario-to-api-mapping"></a>Mapowanie scenariuszy do interfejsu API

|         interfejs API        | Uzgadnianie faktur    | Opłaty krzyżowe    | Optymalizacja kosztów    | Śledzenie kosztów    | Midmonth wydatki    | Alerty    |
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
> Mapowanie scenariusz-interfejs API nie obejmuje interfejsów API użycia w przedsiębiorstwie. Jeśli to możliwe, Użyj ogólnych interfejsów API zużycia dla nowych scenariuszy programistycznych.

## <a name="api-summaries"></a>Podsumowania interfejsu API

### <a name="consumption"></a>Zużycie
Klienci sieci Web Direct i Enterprise mogą używać wszystkich następujących interfejsów API, z wyjątkiem sytuacji, w których zanotowano:

-   [Interfejs API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets) (*Tylko Klienci korporacyjni*): Utwórz budżety kosztów lub użycia dla zasobów, grup zasobów lub mierników rozliczeń. Po utworzeniu budżetów można skonfigurować alerty w celu powiadomienia o przekroczeniu zdefiniowanych progów budżetu. Można również skonfigurować akcje, które mają być wykonywane po osiągnięciu kwoty budżetu.

-   [Interfejs API opłat w witrynie Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Uzyskaj opłaty i dane użycia dla wszystkich zasobów portalu Azure Marketplace (oferty partnerów platformy Azure). Te dane mogą służyć do dodawania kosztów do wszystkich zasobów witryny Marketplace lub do badania kosztów/użycia dla określonych zasobów.

-   [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Tylko Klienci korporacyjni*): Pobierz ceny niestandardowe dla wszystkich liczników. Przedsiębiorstwa mogą używać tych danych w połączeniu z szczegółami użycia i informacjami o użyciu witryny Marketplace do obliczania kosztów przy użyciu danych użycia i portalu Marketplace. 

-   [Interfejs API zaleceń dotyczących rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Uzyskaj zalecenia dotyczące zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia pomagają analizować przewidywane oszczędności kosztów i kwoty zakupu. Aby uzyskać więcej informacji, zobacz [interfejsy API usługi Automatyzacja rezerwacji Azure](billing-reservation-apis.md).

-   [Interfejs API szczegółów rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Zapoznaj się z informacjami na temat zakupionych wcześniej rezerwacji maszyn wirtualnych, takich jak zużycie zarezerwowane w porównaniu z użyciem. Dane można wyświetlić na poziomie poszczególnych maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [interfejsy API usługi Automatyzacja rezerwacji Azure](billing-reservation-apis.md).

-   [Interfejs API podsumowań rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Zobacz zagregowane informacje na temat rezerwacji maszyn wirtualnych zakupionych przez Twoją organizację, takich jak ilości zarezerwowane w przypadku użycia w agregacji. Aby uzyskać więcej informacji, zobacz [interfejsy API usługi Automatyzacja rezerwacji Azure](billing-reservation-apis.md).

-   [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails): Uzyskaj informacje o opłatach i użyciu dla wszystkich zasobów platformy Azure od firmy Microsoft. Informacje są w postaci rekordów zawierających szczegóły użycia, które są obecnie emitowane raz na licznik dziennie. Korzystając z tych informacji, można dodać koszty dla wszystkich zasobów lub zbadać koszty/użycie dla określonych zasobów.

-   [Interfejs API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Uzyskaj stawki taryfowe, jeśli jesteś klientem bezpośrednio korzystającym z sieci Web. Następnie możesz użyć zwróconych informacji z informacjami o użyciu zasobów, aby ręcznie obliczyć oczekiwany rachunek. 

-   [Niesklasyfikowany interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)): Pobierz surowe informacje o użyciu przed rozpoczęciem zliczania/ładowania przez platformę Azure.

### <a name="billing"></a>Rozliczenia
-   [Interfejs API okresów rozliczeniowych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Określ okres rozliczeniowy do przeanalizowania wraz z identyfikatorami faktur dla tego okresu. Identyfikatorów faktur można używać razem z interfejsem API faktur.

-   [Interfejs API faktur](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Pobierz adres URL pobierania dla faktury dla okresu rozliczeniowego w postaci pliku PDF.

### <a name="enterprise-consumption"></a>Użycie w przedsiębiorstwie
Następujące interfejsy API są przeznaczone tylko dla przedsiębiorstw:

-   [Interfejs API podsumowania balansu](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Uzyskaj miesięczne podsumowanie informacji na temat bilansów, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Te informacje można uzyskać w bieżącym okresie rozliczeniowym lub w dowolnym okresie w przeszłości. Przedsiębiorstwa mogą używać tych danych do porównania z ręcznymi obliczanymi opłatami zbiorczymi. Ten interfejs API nie dostarcza informacji dotyczących zasobów ani zagregowanego widoku kosztów.

-   [Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Uzyskaj informacje na temat użycia platformy Azure (oferty firmy Microsoft) w bieżącym miesiącu, określonym okresie rozliczeniowym lub w niestandardowym okresie dat. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczania rachunków na podstawie stawki i zużycia. Przedsiębiorstwa mogą również używać informacji działu/organizacji w celu podzielenia kosztów między organizacjami. Dane zapewniają widok dotyczący użycia/kosztu specyficzny dla zasobów.

-   [Interfejs API kosztów sklepu Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Uzyskaj informacje na temat użycia platformy Azure (ofert partnerskich) w bieżącym miesiącu, określonym okresie rozliczeniowym lub w niestandardowym okresie dat. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczania rachunków na podstawie stawki i zużycia. Przedsiębiorstwa mogą również używać informacji działu/organizacji w celu podzielenia kosztów między organizacjami. Ten interfejs API udostępnia widok specyficzny dla zasobów (użycie/koszt).

-   [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Uzyskaj odpowiednią stawkę za każdy licznik dla danego okresu rejestracji i rozliczeń. Informacje o tym współczynniku można użyć w połączeniu z szczegółami użycia i informacjami o użyciu witryny Marketplace, aby ręcznie obliczyć oczekiwany rachunek.

-   [Interfejs API okresów rozliczeniowych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Pobierz listę okresów rozliczeniowych. Interfejs API udostępnia również właściwość, która wskazuje trasę interfejsu API dla czterech zestawów danych interfejsu API przedsiębiorstwa, które odnoszą się do okresu rozliczeniowego: BalanceSummary, UsageDetails, opłaty w witrynie Marketplace i arkusza cen.

-   [Interfejs API zaleceń dotyczących wystąpień zarezerwowanych](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Zapoznaj się z 7 dni, 30 dni lub 60 dni korzystania z maszyny wirtualnej i uzyskaj zaleceń dotyczących pojedynczych i udostępnionych zakupów. Za pomocą tego interfejsu API można analizować przewidywane oszczędności kosztów i zalecane kwoty zakupu. Aby uzyskać więcej informacji, zobacz [interfejsy API usługi Automatyzacja rezerwacji Azure](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Czym różnią się interfejsy API raportowania przedsiębiorstwa i interfejsy API użycia? Kiedy należy używać każdego z nich?
Te interfejsy API mają podobny zestaw funkcji i mogą odpowiedzieć na ten sam obszerny zestaw pytań w obszarze Zarządzanie rozliczeniami i kosztami. Ale są one ukierunkowane na różnych odbiorców: 

- Interfejsy API raportowania przedsiębiorstwa są dostępne dla klientów, którzy podpisali Umowa Enterprise z firmą Microsoft, która przyznaje im dostęp do wynegocjowanych zobowiązań pieniężnych i cen niestandardowych. Interfejsy API wymagają klucza, który można uzyskać z [Enterprise Portal](https://ea.azure.com). Opis tych interfejsów API można znaleźć w temacie [Omówienie interfejsów API raportowania dla klientów korporacyjnych](billing-enterprise-api.md).

- Interfejsy API użycia są dostępne dla wszystkich klientów z kilkoma wyjątkami. Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API użycia platformy Azure](billing-consumption-api-overview.md) i [Dokumentacja interfejsu API użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption/). Zalecamy używanie dostarczonych interfejsów API jako rozwiązania dla najnowszych scenariuszy programistycznych. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaka jest różnica między interfejsem API szczegółów użycia i interfejsem API użycia?
Te interfejsy API zapewniają zasadniczo różne dane:

- [Interfejs API szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails) zawiera informacje dotyczące użycia i kosztów na platformie Azure na wystąpienie miernika. Dostarczone dane zostały już przekazane przez system zliczania kosztów na platformie Azure i zostały do niego zastosowane koszty wraz z innymi możliwymi zmianami:

   - Zmiany dotyczące korzystania z przedpłat zobowiązań pieniężnych
   - Zmiany w koncie dla rozbieżności użycia odnalezionych przez platformę Azure

- [Interfejs API użycia](/previous-versions/azure/reference/mt219003(v=azure.100)) udostępnia surowe informacje o użyciu platformy Azure przed przekazaniem ich przez system pomiaru kosztów na platformie Azure. Te dane mogą nie mieć żadnej korelacji z ilością lub opłatą, która jest widoczna po systemie zliczania kosztów platformy Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaka jest różnica między interfejsem API faktur i interfejsem API szczegółów użycia?
Te interfejsy API zapewniają inny widok tych samych danych:

- [Interfejs API fakturowania](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) jest przeznaczony tylko dla klientów sieci Web Direct. Umożliwia comiesięczne zestawienie rachunku na podstawie zagregowanych opłat za każdy typ miernika. 

- [Interfejs API szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails) zawiera szczegółowy widok rekordów użycia/kosztów dla każdego dnia. Klienci z bezpośrednimi przedsiębiorstwami i sieci Web mogą korzystać z tej usługi.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaka jest różnica między interfejsem API arkusza cen i interfejsem API RateCard?
Te interfejsy API zapewniają podobne zestawy danych, ale mają różnych odbiorców:

- [Interfejs API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) udostępnia niestandardowe ceny, które zostały wynegocjowane dla klienta przedsiębiorstwa.

- [Interfejs API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) zapewnia publiczne ceny, które mają zastosowanie do bezpośrednich klientów internetowych.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o używaniu interfejsów API platformy Azure do programistycznego uzyskiwania wglądu w użycie platformy Azure, zobacz [Omówienie interfejsu API użycia platformy Azure](billing-consumption-api-overview.md) i [Omówienie interfejsu API rozliczeń platformy Azure](billing-usage-rate-card-overview.md).

- Aby porównać fakturę ze szczegółowym plikiem dziennego użycia i raportami dotyczącymi zarządzania kosztami w Azure Portal, zobacz artykuł [Opis rachunku na potrzeby Microsoft Azure](billing-understand-your-bill.md).

- Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).
