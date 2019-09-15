---
title: Analiza komercyjnego portalu Marketplace w centrum partnerskim
description: Dowiedz się, jak uzyskiwać dostęp do raportów analitycznych w celu monitorowania sprzedaży, oceny wydajności i optymalizowania ofert w portalu Marketplace.
author: chjenk
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 4357a91a87648885def69d8e75e19e38a5a3f751
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993715"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analiza komercyjnego portalu Marketplace w centrum partnerskim

Dowiedz się, jak uzyskiwać dostęp do raportów analitycznych w centrum partnerskim firmy Microsoft w celu monitorowania sprzedaży, oceny wydajności i optymalizowania ofert w portalu Marketplace. Jako partner możesz monitorować swoje oferty za pomocą wizualizacji danych i grafów szczegółowych obsługiwanych przez centrum partnerskie oraz znaleźć sposoby maksymalizowania sprzedaży. Udoskonalone narzędzia analityczne umożliwiają wykonywanie działań na wynikach wydajności i obsługę lepszych relacji z klientami i odsprzedawcami. 

Aby uzyskać dostęp do narzędzi do analiz Centrum partnerskiego, Otwórz pulpit nawigacyjny **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze komercyjny Portal Marketplace.

|**Pulpit nawigacyjny**|**Wyświetlana zawartość**|
|:---|:---|
|[Podsumowanie](#summary-dashboard)|Wykresy, trendy i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla ofert|
|[Zamówienie](#orders-dashboard)|Informacje o zamówieniach w formacie graficznym i do pobrania|
|[Klientów](#customer-dashboard)|Informacje o klientach, w tym trendy wzrostu, prezentowane w formacie graficznym i do pobrania|
|[Proces](#downloads-dashboard)|Lista żądań pobierania w ciągu ostatnich 30 dni|

## <a name="summary-dashboard"></a>Pulpit nawigacyjny podsumowania

Pulpit nawigacyjny **podsumowania** przedstawia przegląd w zależności od typu oferty. **Szczegółowe** informacje są wyświetlane w skrócie i zapewniają szeroką gamę działań sprzedaży oferowanych przez oferty. Możesz wizualizować te raporty przy użyciu pulpitu nawigacyjnego **Podsumowanie** :

- [Łączna liczba zamówień](#totals)
- [Łączna liczba klientów](#totals)
- [Geograficzna lokalizacja klientów](#customers-by-geography)
- [Trendy pojawiające się w oparciu o dane klienta i zamówienia](#growth-trend-chart)
- [Klient rankingów z najwyższymi zamówieniami](#customer-leaderboard)
- [Liczba zamówień zorganizowanych według nazwy oferty](#offers-by-orders)

### <a name="date-range"></a>Zakres dat

Wybór zakresu dat można znaleźć w prawym górnym rogu każdej strony. Dane wyjściowe wykresów stron **podsumowujących** można dostosować, wybierając zakres dat na podstawie ostatnich 3, 6 lub 12 miesięcy lub wybierając niestandardowy zakres dat o maksymalnym okresie wynoszącym 12 miesięcy. Domyślny zakres dat to sześć miesięcy.

![Pulpit nawigacyjny analizy Centrum partnerskiego](./media/analyze-dashboard.png)

### <a name="totals"></a>Sumy

Sekcja **sum** wyświetla liczbę utworzonych zamówień lub klientów nabytych w ciągu wybranego zakresu dat. 

- Wartość procentowa obok **sumy zamówień** i **łączna liczba klientów** reprezentuje stopień wzrostu w porównaniu do poprzedniego miesiąca. 
- Zielony trójkąt skierowany w górę wskazuje na pozytywny trend wzrostu. Czerwony trójkąt skierowany w dół wskazuje negatywny trend wzrostu względem poprzedniego miesiąca. 
- Trendy wzrostu kolejności i klientów są reprezentowane przez wykresy słupkowe i wyświetlają wartości dla każdego miesiąca przez umieszczenie kursora nad kolumnami wykresu.

![Analiza sum Centrum partnerskiego](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Klienci według lokalizacji geograficznej

Mapę cieplną **klienci według lokalizacji geograficznej** wyświetlają liczbę klientów na mapie światowej.

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Możesz powiększyć do określonej lokalizacji.
- Mapę cieplną zawiera dodatkową siatkę do wyświetlania szczegółów liczby klientów (lub [liczby zamówień](#orders-by-geography)) w określonej lokalizacji.
- Możesz wyszukać i wybrać kraj w siatce, aby powiększyć do lokalizacji na mapie. Przywróć widok oryginalny, naciskając przycisk Home ( **Strona główna** ) na mapie.
- **Nowy** klient zakupił jedną z ofert po raz pierwszy w miesiącu w ramach wybranego zakresu dat.

![Centrum partnerskie — analizowanie geograficznego klienta](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Wykres trendu wzrostu

Trendy można wyświetlać w oparciu o rozwój **utworzonych** lub **uzyskanych klientów**, wyświetlany miesiąc według miesiąca zgodnie z wybranym zakresem dat. Można przeanalizować te trendy, wybierając linki poniżej wykresu, co umożliwia przejście do odpowiedniej **kolejności** lub stron **klienta** .

![Centrum partnerskie — analizowanie trendów wzrostu](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Rankingów klienta

Najlepsi 50 klientów z największą liczbą zamówień są wyświetlane na *tablicy lidera*, posortowane według najwyższej liczby porządkowej i procentowej kolejności. 

- Wybierz klienta, aby wyświetlić jego szczegóły profilu, zamówienia uporządkowane według oferty lub zamówienia uporządkowane według typu licencji platformy Azure i kanału cenowego. 
- Wykres pierścieniowy **oferty wg zamówień** przedstawia cztery pierwsze oferty (według liczby porządkowej), a pozostałe oferty pogrupowane jako "REST All".

> [!NOTE]
> Dane osobowe klienta będą prezentowane tylko wtedy, gdy klient udostępnił zgodę. Te informacje można wyświetlić, jeśli zalogowano się na poziomie uprawnień roli **właściciela** . Użytkownicy z rolą **współautor** nie będą mogli wyświetlić tych informacji. [Dowiedz się więcej o rolach i uprawnieniach użytkowników](./manage-account.md#define-user-roles-and-permissions).

![Centrum partnerskie — analizowanie trendów wzrostu](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Oferty według zamówień

Wykres **oferty według zamówień** organizuje aktywne zamówienia zgodnie z nazwą oferty. 

- Możesz przeciągać od wycinków z lewego wykresu pierścieniowego do prawego wykresu pierścieniowego, aby wyświetlić więcej szczegółów dla oferty, która została przeciągnięta. Te dwa wykresy umożliwiają porównanie konkretnej oferty z wydajnością wszystkich innych ofert ("REST All"). 

![Centrum partnerskie — analizowanie ofert według zamówień](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Pulpit nawigacyjny zamówień

Pulpit nawigacyjny **zamówienia** w menu **Analizuj** zawiera bieżące zamówienia dla wszystkich ofert SaaS. Można wyświetlić graficzne reprezentacje:

- [Zamówienia sum](#order-totals)
- [Zamówienia według położenia geograficznego](#orders-by-geography)
- [Trendy dotyczące aktywnych i anulowanych zamówień](#trends-for-active-and-canceled-orders)
- [Zamówienia uporządkowane według typu licencji witryny Marketplace](#orders-by-marketplace-license-type)
- [Zamówienia zorganizowane przez nowych i istniejących klientów](#orders-by-customer-type)
- [Tabela szczegółów zamówienia](#order-details-table)

> [!NOTE]
> Istnieją różnice między sposobami wyświetlania raportów analizy w portal Cloud Partner (CPP) i nowym komercyjnym programie Marketplace w centrum partnerskim. Jednym z nich jest to, że informacje dostępne dla **sprzedającego** w programie CPP mają kartę **użycie zamówienia &** , która wyświetla dane dla ofert opartych na użyciu i nieopartych na użyciu. W centrum partnerskim strona **zamówienia** ma osobną kartę dla ofert SaaS.

### <a name="order-totals"></a>Zamówienia sum

Sekcja **sumy zamówień** przedstawia liczbę utworzonych zamówień, w tym zamówień **aktywnych** i **anulowanych** , w ramach wybranego [zakresu dat](#date-range). 

- Wartość procentowa obok **sumy zamówień** reprezentuje stopień wzrostu w porównaniu do poprzedniego miesiąca. 
- Zielony trójkąt skierowany w górę wskazuje na pozytywny trend wzrostu. Czerwony trójkąt skierowany w dół wskazuje negatywny trend wzrostu względem poprzedniego miesiąca. 
- Trendy wzrostu są reprezentowane przez wykresy słupkowe i wyświetlają wartości dla każdego miesiąca przez umieszczenie kursora nad kolumnami wykresu.

### <a name="orders-by-geography"></a>Zamówienia według położenia geograficznego

Mapę cieplną **Orders by geography** wyświetla liczbę zamówień na mapie światowej i działa tak samo jak **[klienci według lokalizacji geograficznej mapę cieplną](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Trendy dotyczące aktywnych i anulowanych zamówień

Wykres pierścieniowy **aktywnych zamówień przez oferty** organizuje wszystkie aktywne zamówienia zgodnie z ich nazwami ofert.

- Cztery pierwsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "REST All".
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty na grafie. 
- Umieszczenie kursora na wycinku na wykresie spowoduje wyświetlenie liczby zamówień i procentu oferty w porównaniu do łącznej liczby zamówień w ramach wszystkich ofert.
- **Trend zamówień według oferty** powoduje wyświetlenie trendów wzrostu miesięcznie. Kolumna month reprezentuje liczbę zamówień według nazwy oferty. Na wykresie liniowym jest wyświetlany trend procentu wzrostu wykreślony na osi z.
- Możesz użyć suwaka w górnej części wykresu, aby przewinąć w prawo i w lewo wzdłuż osi x i skupić się na określonych punktach danych.
- Możesz wyświetlić wykres trendu, wybierając konkretny element w legendzie.
- Możesz również wybrać, aby wyświetlić trendy i dane dla **anulowanych zamówień**. Wykres będzie działać w taki sam sposób jak w przypadku aktywnych zamówień.

![Analizowanie aktywnych zamówień w centrum partnerskim](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Zamówienia według typu licencji Marketplace
<!-- Section needs review and clarification!  -->
Na wykresie **Typ licencji w portalu Marketplace** jest wyświetlana liczba zamówień według miesięcy w oparciu o typ licencji i metodę rozliczania oferty portalu Marketplace. Typy licencji obejmują:

- **Rozliczenia za pomocą platformy Azure**: Firma Microsoft rozlicza klientów w Twoim imieniu w przypadku wybrania [sprzedaży oferty przez firmę Microsoft](./create-new-saas-offer.md#sell-through-microsoft) przy użyciu tego typu licencji. Typy płatności obejmują płatność zgodnie z rzeczywistym użyciem za pośrednictwem karty kredytowej lub fakturowania w przedsiębiorstwie.
- **Bring Your Own License**: Firma Microsoft nie rozlicza klientów za korzystanie z tego typu oferty portalu Marketplace. Wymienione jako **[Pobierz teraz (bezpłatnie)](./create-new-saas-offer.md#get-it-now-free)** w portalu Marketplace.
- **Bezpłatnie**: Firma Microsoft nie rozlicza klientów za korzystanie z tego typu oferty portalu Marketplace. Wymienione jako **[bezpłatna wersja próbna](./create-new-saas-offer.md##free-trial-listing)** w portalu Marketplace.
- **Firma Microsoft jako odsprzedawca**: Reprezentuje oferty sprzedane przez odsprzedawcy firmy Microsoft jako część **[programu Cloud Solution Provider (CSP)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Centrum partnerskie — analizowanie zamówień według typu licencji](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Zamówienia według typu klienta

Wykres słupkowy **typu zamówienia według klientów** przedstawia liczbę zamówień podzielonych między **nowych klientów** i **istniejących klientów**. 

- **Nowy klient** uzyskał co najmniej jedną ofertę po raz pierwszy w tym samym miesiącu kalendarzowym (oś y). **Istniejący klient** uzyskał wcześniej ofertę od Ciebie przed zaraportowanym miesiącem kalendarzowym (na osi y). 
- Dodatkowy wykres kołowy reprezentuje wszystkie zamówienia utworzone przez nowego lub istniejącego klienta dla wybranego zakresu dat.
- Na obu wykresach można wyświetlić tylko nowych lub tylko istniejących klientów, wybierając odpowiednią legendę.

![Centrum partnerskie — analizowanie zamówień według typu klienta](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Tabela szczegółów zamówienia

W **tabeli Order Details (szczegóły zamówienia** ) wyświetlana jest lista numerowana zamówień z 1000 góry posortowanych według daty nabycia.

- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do pliku TSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów przekracza 1000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Filtry można zastosować do **tabeli Order Details** , aby wyświetlić tylko te dane, które Cię interesują. Dane można filtrować według kraju, typu licencji platformy Azure, typu licencji Marketplace, typu oferty, stanu zamówienia, bezpłatnych identyfikatorów, identyfikatora subskrypcji witryny Marketplace, identyfikatora klienta i nazwy firmy.

![Szczegóły kolejności analizy Centrum partnerskiego](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Pulpit nawigacyjny klienta

Pulpit nawigacyjny **klienta** menu **Analizuj** zawiera dane dla klientów, którzy uzyskali oferty. Można wyświetlić graficzne reprezentacje:

- [Sumy klientów](#customer-totals)
- [Klienci według lokalizacji geograficznej](#customers-by-geography)
- [Trendy klientów](#customer-trends)
- [Klienci według zamówień](#customers-by-orders)
- [Tabela szczegółów klienta](#customer-details-table)

### <a name="customer-totals"></a>Sumy klientów

W sekcji **sumy klienta** wyświetlana jest liczba wszystkich klientów, w tym nowych, istniejących i zmienionych, w ramach wybranego [zakresu dat](#date-range).

- Procent wzrostu w porównaniu z poprzednim miesiącem jest wskazywany przez liczbę i wskaźnik w górę w kolorze czerwonym lub w dół na czerwono.
- Trendy wzrostu są reprezentowane przez wykresy słupkowe i wyświetlają wartości dla każdego miesiąca przez umieszczenie kursora nad kolumnami wykresu.

#### <a name="customer-types"></a>Typy klientów

Istnieją trzy typy klientów: nowe, istniejące i zmienione. 

- Nowy klient uzyskał co najmniej jedną ofertę po raz pierwszy w wybranym miesiącu.
- Istniejący klient uzyskał jedną lub większą liczbę ofert przed wybranym miesiącem.
- Zakupiony klient anulował wszystkie oferty.

### <a name="customer-trends"></a>Trendy klientów

Na wykresie **trendów dla klientów** jest wyświetlana liczba wszystkich klientów, w tym nowych, istniejących i zmienionych trendów wzrostu miesięcznie.

- Wykres liniowy reprezentuje ogólne wartości procentowe wzrostu klientów. 
- Kolumna month reprezentuje liczbę klientów ułożonych przez nowych, istniejących i zmienionych klientów.
- Liczba zmienionych klientów jest wyświetlana w ujemnym kierunku osi Y.
- Można wybrać konkretne elementy legendy, aby wyświetlić bardziej szczegółowe widoki. Na przykład wybierz pozycję nowi klienci w legendzie, aby wyświetlić tylko nowych klientów.
- Możesz użyć suwaka w górnej części wykresu, aby przewinąć w prawo i w lewo na osi x i skupić się na określonych punktach danych, aby wyświetlić bardziej szczegółowe informacje.
- Umieszczenie wskaźnika myszy nad kolumną wykresu spowoduje wyświetlenie szczegółów tylko dla tego miesiąca.

![Centrum partnerskie Analizuj trendy klientów](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Klienci według zamówień

Wykres **klienci według zamówień** wyświetla **najwyższego percentylu klienta** wzdłuż osi x, zgodnie z ich liczbą zamówień. Oś y Wyświetla liczbę zamówień klienta. Oś z (wykres liniowy) przedstawia skumulowaną wartość procentową łącznej liczby zamówień. Możesz wyświetlić szczegóły, umieszczając kursor nad punktami wzdłuż wykresu liniowego.

Na przykład można dowiedzieć się od tego, jakie dane zamówienia klienta stanowią, że pierwsze 30% klientów wnosi do 83% zamówień, co oznacza zamówienia 2 130.

![Centrum partnerskie Analizuj zamówienia klienta](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tabela szczegółów klienta

W **tabeli Szczegóły klienta** zostanie wyświetlona lista numerowanych głównych klientów 1000 posortowanych według daty pierwszego nabycia jednej z ofert.

- Informacje osobiste klienta będą dostępne tylko wtedy, gdy klient udostępnił zgodę. Te informacje można wyświetlić tylko wtedy, gdy użytkownik zalogował się przy użyciu poziomu uprawnień **właściciela** . [Dowiedz się więcej o rolach i uprawnieniach użytkowników](./manage-account.md#define-user-roles-and-permissions).
- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do pliku TSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów przekracza 1000, eksportowane dane będą asynchroniczne umieszczane na stronie pobierania przez następne 30 dni.
- Filtry można zastosować do tabeli, aby wyświetlić tylko te dane, które Cię interesują. Dane można filtrować według nazwy firmy, identyfikatora klienta, identyfikatora subskrypcji w portalu Marketplace, typu licencji platformy Azure, daty pozyskania, utraty daty, adresu E-mail klienta, kraju/województwa/miejscowości/pliku zip, języka klienta itd.

![Centrum partnerskie Analizuj szczegóły klienta](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Pobierz pulpit nawigacyjny

Na **pulpicie** nawigacyjnym menu **Analizuj** są wyświetlane żądania pobrania zawierające ponad 1000 wierszy danych klienta lub zamówienia.

Otrzymasz powiadomienie wyskakujące i wiadomość e-mail zawierającą link **do pulpitu nawigacyjnego Pobierz za** każdym razem, gdy wyślesz żądanie pobrania z ponad 1000 wierszy danych. Te pliki do pobrania są dostępne przez 30-dniowy okres, a następnie usuwane.

![Centrum partnerskie — analizowanie pobrań](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Słownik terminów dotyczących danych

| Nazwa atrybutu | Raporty | Definicja|
|---|---|---|
| Typ licencji platformy Azure | Klient, zamówienie | Typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure. Znany również jako kanał |
| Typ licencji platformy Azure: Cloud Solution Provider | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę portalu Marketplace za pomocą swojego dostawcy rozwiązań w chmurze, który działa jako odsprzedawca.|
| Typ licencji platformy Azure: Enterprise | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę w portalu Marketplace za pomocą Umowa Enterprise podpisanej bezpośrednio w firmie Microsoft.|
| Typ licencji platformy Azure: Przedsiębiorstwo przez odsprzedawcę  | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę portalu Marketplace przez odsprzedawcę, który ułatwia ich Umowa Enterprise firmie Microsoft.|  |
| Typ licencji platformy Azure: Płatność zgodnie z rzeczywistym użyciem| Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę Marketplace w ramach umowy "płatność zgodnie z rzeczywistym użyciem" podpisanej bezpośrednio z firmą Microsoft.||
| Nazwa wystąpienia chmury| Zamówienie| Microsoft Cloud, w którym wystąpiło wdrożenie maszyny wirtualnej.||
| Nazwa wystąpienia chmury: Globalne platformy Azure| Zamówienie| Publiczna globalna chmura firmy Microsoft.|| |
| Nazwa wystąpienia chmury: Azure Government | Zamówienie| Specyficzne dla instytucji rządowe chmury firmy Microsoft dla jednego z następujących rządów: Chiny, Niemcy lub Stany Zjednoczone Ameryki.| |
| Miasto klienta| Odbiorcy| Nazwa miasta podana przez klienta. Miasto może być inne niż miasto w subskrypcji platformy Azure klienta.||
| Język komunikacji klienta  | Odbiorcy| Język preferowany przez klienta do komunikacji.||
| Nazwa firmy klienta | Klient, zamówienie | Nazwa firmy podana przez klienta. Nazwa może różnić się od nazwy w subskrypcji platformy Azure klienta.|  |
| Kraj klienta | Klient, zamówienie | Nazwa kraju podana przez klienta. Kraj może być inny niż kraj w subskrypcji platformy Azure klienta.|  |
| Adres E-mail klienta| Odbiorcy| Adres e-mail dostarczony przez klienta końcowego. Adres e-mail może różnić się od adresu e-mail w subskrypcji platformy Azure klienta.||
| Imię klienta| Odbiorcy| Nazwa podana przez klienta. Nazwa może różnić się od nazwy podanej w subskrypcji platformy Azure klienta.| |
| Identyfikator klienta | Klient, zamówienie | Unikatowy identyfikator przypisany do klienta. Klient może mieć zero lub więcej subskrypcji portalu Azure Marketplace.|  |
| Kod pocztowy klienta  | Odbiorcy| Kod pocztowy dostarczony przez klienta. Kod może być inny niż kod pocztowy podany w subskrypcji platformy Azure klienta.| |
| Stan klienta| Odbiorcy| Stan (adres) dostarczony przez klienta. Stan może być inny niż nazwisko podane w subskrypcji platformy Azure klienta.| |
| Data pozyskania| Odbiorcy| Pierwszy dzień zakupionej oferty przez klienta.| |
| Data utraty| Odbiorcy| Ostatnia data anulowania ostatniego zakupionej oferty przez klienta.||
| Jest nowym klientem  | Zamówienie| Wartość będzie identyfikować nowego klienta przejmującego jedną lub większą liczbę ofert po raz pierwszy (lub nie). Wartość będzie równa "yes", jeśli w ramach tego samego miesiąca kalendarzowego dla "Data pozyskania". Wartość będzie równa "No", jeśli klient kupił dowolne oferty przed zgłoszonym miesiącem kalendarzowym. |
| Jest jednostką SKU wersji zapoznawczej| Zamówienie| Wartość będzie informować o tym, czy jest oznaczona jako wersja zapoznawcza. Wartość będzie równa "yes", jeśli jednostka SKU została odpowiednio oznaczona, a tylko subskrypcje platformy Azure autoryzowane przez użytkownika, można wdrożyć i korzystać z tego obrazu. Wartość będzie równa "No", jeśli jednostka SKU nie została zidentyfikowana jako "wersja zapoznawcza".  |
| Czy kontakt promocyjny jest wybierany| Odbiorcy| Ta wartość informuje o tym, czy klient aktywnie zabrał w celu skontaktowania się z wydawcami. W tej chwili nie prezentujemy opcji klientom, dlatego w całej tablicy podano wartość "No". Po wdrożeniu tej funkcji rozpocznie się aktualizowanie.|
| Typ licencji Marketplace| Zamówienie| Metoda rozliczeń oferty portalu Marketplace.||
| Typ licencji Marketplace: Rozliczanie za pomocą platformy Azure| Zamówienie| Firma Microsoft jest Twoim agentem dla tej oferty w witrynie Marketplace i wekslom klientów w Twoim imieniu. (Karta kredytowa PAYG lub faktura w przedsiębiorstwie)||
| Typ licencji Marketplace: Korzystanie z własnej licencji | Zamówienie| Maszyna wirtualna wymaga klucza licencji dostarczonego przez klienta do wdrożenia. Firma Microsoft nie rozliczy klientów za korzystanie z oferty w ten sposób w portalu Marketplace.||
| Typ licencji Marketplace: Wolne| Zamówienie| Oferta jest konfigurowana bezpłatnie dla wszystkich użytkowników. Firma Microsoft nie rozliczy klientów za użycie tej oferty.||
| Typ licencji Marketplace: Firma Microsoft jako odsprzedawca  | Zamówienie| Firma Microsoft jest odsprzedawcą dla tej oferty portalu Marketplace.|  |
| Identyfikator subskrypcji witryny Marketplace | Klient, zamówienie | Unikatowy identyfikator skojarzony z subskrypcją platformy Azure używany przez klienta do zakupu oferty portalu Marketplace. Identyfikator był dawniej identyfikatorem GUID subskrypcji platformy Azure.||
| Nazwa oferty  | Zamówienie| Nazwa oferty portalu Marketplace.|| |
| Typ oferty  | Zamówienie| Typ oferty Microsoft Marketplace.|||
| Typ oferty: Aplikacja zarządzana  | Porządek | Użyj usługi Azure App: Managed App — typ oferty, gdy wymagane są następujące warunki: Rozwiązanie oparte na subskrypcji można wdrożyć dla klienta przy użyciu maszyny wirtualnej lub całego rozwiązania opartego na IaaS. Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera. |
| Typ oferty: Azure Application| Porządek | Użyj typu oferty szablonu rozwiązania aplikacji platformy Azure, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza prostą maszyną wirtualną.||
| Typ oferty: Usługa konsultingowa| Zamówienie| Usługi doradcze w witrynie Azure Marketplace umożliwiają łączenie klientów z usługami w celu wspierania i zwiększania użycia platformy Azure.| |
| Typ oferty: Kontener | Zamówienie| Użyj typu oferty kontenera, gdy rozwiązanie jest obrazem kontenera Docker, który został zainicjowany jako usługa kontenera platformy Azure oparta na Kubernetes.||
| Typ oferty: Dynamics 365 Business Central| Zamówienie| Użyj tego typu oferty, jeśli Twoje rozwiązanie jest zintegrowane z usługą Dynamics 365 dla finansów i operacji| |
| Typ oferty: Dynamics 365 for Customer Engagement | Zamówienie| Użyj tego typu oferty, jeśli Twoje rozwiązanie jest zintegrowane z usługą Dynamics 365 na potrzeby zaangażowania klientów.||
| Typ oferty: Moduł usługi IoT Edge | Zamówienie| Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. |
| Typ oferty: Aplikacja Power BI | Zamówienie| Użyj typu oferty aplikacji Power BI podczas wdrażania aplikacji zintegrowanej z Power BI.|  |
| Typ oferty: Aplikacja SaaS| Zamówienie| Użyj typu oferty aplikacji SaaS, aby umożliwić klientowi kupowanie rozwiązań technicznych opartych na SaaS, w ramach subskrypcji.||
| Typ oferty: Maszyna wirtualna | Zamówienie| Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej.||
| Typ oferty: Visual Studio Marketplace rozszerzenie  | Zamówienie| Typ oferty dostępny wcześniej dla deweloperów rozszerzenia usługi Azure DevOps. Przechodzenie do przodu deweloperów rozszerzeń platformy Azure DevOps może sprzedawać swoje rozszerzenia bezpośrednio klientom. Oferty rozszerzeń można skonfigurować jako płatne lub w tym okres próbny. |
| Data anulowania zamówienia| Zamówienie| Data anulowania zamówienia witryny Marketplace.||
| Identyfikator zamówienia| Zamówienie| Unikatowy identyfikator zamówienia klienta usługi portalu Marketplace. Oferty oparte na użyciu maszyny wirtualnej nie są skojarzone z kolejnością.| |
| Zamówienie daty zakupu| Zamówienie| Data utworzenia zamówienia witryny Marketplace.|||
| Stan zamówienia| Zamówienie| Stan zamówienia witryny Marketplace w momencie ostatniego odświeżenia danych.|     |
| Stan zamówienia: Aktywne  | Zamówienie| Klient kupił zamówienie i nie anulował jego zamówienia.|         |
| Stan zamówienia: anulowane | Zamówienie| Klient kupił wcześniej zamówienie, a następnie anulował jego zamówienie.||
| Adres E-mail dostawcy| Odbiorcy| Adres e-mail dostawcy, który jest powiązany z relacją między firmą Microsoft a klientem końcowym. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych.|
| Nazwa dostawcy| Odbiorcy| Nazwa dostawcy występującego w relacji między firmą Microsoft a klientem końcowym. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych.|
| SKU| Zamówienie| Nazwa jednostki SKU zdefiniowana podczas publikowania. Oferta może mieć wiele jednostek SKU, ale jednostka SKU może być skojarzona tylko z jedną ofertą.||
| Data zakończenia okresu próbnego| Zamówienie| Data zakończenia okresu próbnego dla tego zamówienia lub zostanie ona zakończona.||
