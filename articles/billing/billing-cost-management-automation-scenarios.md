---
title: Scenariusze automatyzacji zarządzania kosztów i rozliczenia Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak często spotykane rozliczeń i kosztów zarządzania scenariusze są mapowane na różne interfejsów API.
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
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247670"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scenariuszach automatyzacji rozliczeń i kosztów zarządzania

Typowe scenariusze dotyczące rozliczeń i kosztu miejsca zarządzania są zidentyfikowane poniżej i zamapowane na różne interfejsów API, które mogą być używane w tych scenariuszach. Podsumowanie wszystkich interfejsów API dostępne i funkcje, które oferują znajdują się poniżej scenariuszu do mapowania interfejsu API. 

## <a name="common-scenarios"></a>Typowe scenariusze 

Można użyć rozliczeń i kosztów zarządzania interfejsów API w różnych scenariuszach odpowiedzi kosztów i użycia powiązanych pytania.  Poniżej znajduje się omówienie typowych scenariuszy.

- **Faktura uzgadniania** -Microsoft zostało obciążania mnie jednostkom?  Co to jest mojego rachunku i można obliczyć go samodzielnie?

- **Krzyżowe opłat** — teraz, aby sprawdzić, ile I używam dodatkowych opłat, kto w organizacji musi zwrócić?

- **Koszt optymalizacji** -sprawdzić ile I już została obciążona, jednak jak zapewnić więcej poza pieniędzy I am wydatków na platformie Azure?

- **Śledzenie kosztów** — Aby wyświetlić ile am I wydatków i korzysta z platformy Azure w czasie. Co to są trendów? Jak można I realizacji lepiej?

- **Spędzają na platformie Azure w miesiącu** — jaka jest mojego bieżącego miesiąca na spędzają na datę? Należy wprowadzić zmiany w mojej wydatków i/lub użycia usługi Azure? Kiedy w miesiącu am I korzystanie z usługi Azure najbardziej?

- **Konfigurowanie alertów** — chcę skonfigurować zużycie zasobów na podstawie lub pieniężne na podstawie alertów.

## <a name="scenario-to-api-mappings"></a>Scenariusz mapowania interfejsu API

|         Scenariusz/interfejsu API        | Uzgadnianie faktury    | Krzyżowe opłat    | Koszt optymalizacji    | Śledzenie kosztów    | Miesiąc środkowy spędzają    | Alerty    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budżety                     |                           |                  |           X          |                  |                    |     X     |
| Rynków                |             X             |         X        |           X          |         X        |          X         |     X     |
| Arkusz cen                 |             X             |         X        |           X          |         X        |          X         |           |
| Zalecenia dotyczące zastrzeżenia |                           |                  |           X          |                  |                    |           |
| Szczegóły zastrzeżenia         |                           |                  |           X          |         X        |                    |           |
| Podsumowanie rezerwacji       |                           |                  |           X          |         X        |                    |           |
| Szczegóły użycia               |             X             |         X        |           X          |         X        |          X         |     X     |
| Okresy rozliczeń             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Użycie nieocenione               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenariusz poniżej mapowania interfejsu API nie dołączaj interfejsów API zużycie przedsiębiorstwa. Jeśli to możliwe, należy korzystać z ogólnych interfejsów API zużycie rozwiązującą net nowe scenariusze programowania, przenoszenie do przodu.

## <a name="api-summaries"></a>Interfejs API podsumowania

### <a name="consumption"></a>Zużycie
(*Web bezpośrednie + przedsiębiorstwa dla wszystkich interfejsów API, z wyjątkiem tych o nazwie limit poniżej*)

-   **Budżetów** (*tylko klienci korporacyjni*): Użyj [API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets) do tworzenia budżetów koszt lub użycia zasobów, grupy zasobów lub metod rozliczeń.  Po utworzeniu budżetów alerty można skonfigurować do wysyłania powiadomień o przekroczeniu progów budżetu zdefiniowanych przez użytkownika. Występuje po osiągnięciu kwoty budżetu można również skonfigurować akcje.
-   **Rynków**: Użyj [API opłat Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) można pobrać bezpłatnie i dane użycia na wszystkie zasoby w witrynie Marketplace (Azure 3 ofert strony). Te dane można używane do dodawania kosztów przez wszystkie zasoby w witrynie Marketplace lub zbadać koszty/użycie na określonych zasobów.
-   **Arkusz cen** (*tylko klienci korporacyjni*): klienci korporacyjni mogą użyć [API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet) można pobrać ich niestandardowych ceny dla wszystkich liczników. Przedsiębiorstwa można użyć tych danych w połączeniu z użycia szczegóły i rynków użycie informacji do obliczeń kosztów przy użyciu danych użycia i marketplace. 
-   **Zalecenia dotyczące zastrzeżenia**: Użyj [API zalecenia rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) Aby uzyskać zalecenia dotyczące zakupów maszyny Wirtualnej zarezerwowana wystąpień. Zalecenia są przeznaczone do pozwala analizować oczekiwanych oszczędności i kwoty zakupów.
-   **Szczegóły rezerwacji**: Użyj [API szczegóły rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) Aby wyświetlić informacje o wcześniej zakupionych zastrzeżenia maszyny Wirtualnej, takie jak ilość zużycia jest zarezerwowany i jaka jest rzeczywiście używane. Można wyświetlić dane w poszczególnych maszyn wirtualnych-szczegóły na poziomie.
-   **Podsumowanie rezerwacji**: Użyj [API podsumowania rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) Aby wyświetlić informacje agregacji na wcześniej zakupionych zastrzeżenia maszyny Wirtualnej, takie jak ilość zużycia jest zarezerwowany i jaka jest rzeczywiście używany w wartości zagregowanej. 
-   **Szczegóły użycia**: Użyj [API szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails) można pobrać bezpłatnie i użycie wszystkich Azure 1 zasobów firmy. Informacje są w formie rekordów szczegółów użycia, które są aktualnie emitowane raz na metr na dzień. Informacje mogą służyć do dodawania kosztów we wszystkich zasobów lub zbadać koszty/użycie na określonych zasobów.
-   **RateCard**: bezpośrednie sieci Web mogą używać [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) uzyskać ich stawki miernika. Następnie mogą używać zwróconych informacji z ich informacje o użyciu zasobów można ręcznie obliczyć oczekiwany rachunek. 
-   **Niesklasyfikowaną zawartość użycia**: można użyć [nieocenione użycia interfejsu API](https://msdn.microsoft.com/library/azure/mt219003.aspx) uzyskiwania informacji nieprzetworzone obciążenie, przed żadnych pomiaru/ładowania wykonywane przez platformę Azure.

### <a name="billing"></a>Rozliczenia
-   **Okresy rozliczeń**: Użyj [rozliczeń API okresów](https://docs.microsoft.com/rest/api/billing/billingperiods) ustalenie okresu rozliczeniowego do analizy, wraz z faktury identyfikatora dla tego okresu. Faktury identyfikatory mogą być używane z faktury interfejs API przedstawiony poniżej. 
-   **Faktury**: Użyj [API faktury](https://docs.microsoft.com/rest/api/billing/invoices) można uzyskać adres URL pobierania faktury dla danego okresu rozliczeniowego w formacie PDF.

### <a name="enterprise-consumption"></a>Zużycie Enterprise
*(Wszystkie interfejsy API dla wersji Enterprise tylko)*

-   **Podsumowanie Saldo**: Użyj [saldo podsumowanie interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) Aby uzyskać podsumowanie informacji na temat salda, nowych zakupów, opłaty za usługę Azure Marketplace, zmiany i nadwyżkowe opłat co miesiąc. Możesz uzyskać te informacje dla bieżącego okresu rozliczeniowego lub dowolnym okresie w przeszłości. Przedsiębiorstwa mogą używać tych danych do przeprowadzenia porównania z ręcznie obliczeniowej podsumowania opłat. Ten interfejs API nie zawiera informacji na temat określonego zasobu lub zagregowany widok kosztów.
-   **Szczegóły użycia**: Użyj [API szczegóły użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) uzyskanie 1. strona Azure użycia szczegółowe informacje dotyczące bieżącego miesiąca, określonym okresie rozliczeniowym lub niestandardowe okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczania rachunku na podstawie współczynnika i zużycia i można również użyć informacji działu/organizacji obecny atrybut kosztów między organizacjami. Dane zawiera widok określonych zasobów użycia/kosztów.
-   **Opłata magazynu Marketplace**: Użyj [Marketplace magazynu opłat API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) można pobrać strony 3 Azure użycia szczegółowe informacje dotyczące bieżącego miesiąca, określonym okresie rozliczeniowym lub niestandardowe okresu. Przedsiębiorstwa mogą używać tych danych do ręcznego obliczania rachunku na podstawie współczynnika i zużycia i można również użyć informacji działu/organizacji obecny atrybut kosztów między organizacjami. Opłata interfejsu API magazynu Marketplace zawiera widok określonych zasobów użycia/kosztów.
-   **Arkusz cen**: [API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) udostępnia stawkę dla każdego licznika dla danego rejestracji i okresu rozliczeniowego. Te informacje szybkość można w połączeniu z użycia szczegóły rynków użycia informacji o i ręcznie obliczyć oczekiwany rachunek.
-   **Okresy rozliczeń**: Użyj [rozliczeń API okresów](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) można pobrać listy rozliczeń okresów wraz z właściwością wskazujący trasę interfejsu API dla cztery zestawy danych przedsiębiorstwa interfejsu API, które odnoszą się do tego okresu rozliczeniowego - BalanceSummary, UsageDetails opłat w witrynie Marketplace i arkusza cen.
-   **Zastrzeżone zalecenia wystąpienia**: [zastrzeżone API zalecenia wystąpienia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) analizuje klienta 7 dni, 30 dni lub 60 dni wykorzystania maszyny wirtualnej i zalecenia Single i udostępnionych zakupu. Zarezerwowane wystąpienia, które interfejs API umożliwia klientom analizowanie oczekiwano oszczędności kosztów i zalecane kwoty zakupu.

## <a name="next-steps"></a>Następne kroki

- Informacje o programowane uzyskiwanie wglądu w wykorzystanie sieci platformy Azure za pomocą interfejsów API usługi Azure rozliczeń, zobacz [Przegląd rozliczeń interfejsu API Azure](billing-usage-rate-card-overview.md).
