---
title: Wybierz warstwę cenową lub jednostki SKU dla usługi Azure Search | Dokumentacja firmy Microsoft
description: 'Usługa wyszukiwanie Azure można udostępnić w tych jednostki SKU: bezpłatne, Basic i Standard, gdzie Standard jest dostępna w różne konfiguracje zasobów i pojemności, poziomów.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/12/2018
ms.author: heidist
ms.openlocfilehash: 5454e659d488c84de32a15de65226bc3e1b07dfe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Wybierz warstwę cenową dla usługi wyszukiwanie Azure

W usłudze Azure Search [usługi zostanie zainicjowana](search-create-service-portal.md) w określonych warstwę cenową lub jednostki SKU. Dostępne są następujące opcje **wolne**, **podstawowe**, lub **standardowe**, gdzie **standardowe** są dostępne w wielu konfiguracjach i pojemności. 

Celem tego artykułu jest pomocne w wyborze warstwy. Uzupełnia on [cennikiem](https://azure.microsoft.com/pricing/details/search/) i [ograniczenia usługi](search-limits-quotas-capacity.md) strony z szyfrowanego rozliczeń pojęcia i wzorce użycia skojarzone z różnych warstw. Sugeruje również metody iteracyjnej, aby zrozumieć, w której warstwie najlepiej odpowiada Twoim potrzebom. 

Warstwy określić wydajność, nie funkcji. Jeśli pojemność warstwy okaże się zbyt niska, należy udostępnić nową usługę w wyższej warstwy, a następnie [ponowne załadowanie z indeksów](search-howto-reindex.md). Nie ma żadnych uaktualnienia w miejscu z jednego identyfikatora jednostki Magazynowej tej samej usługi do innego.

Dostępność funkcji nie wchodzi to warstwy głównej. Wszystkie warstwy, w tym **wolne** warstwy, oferują parzystość funkcji, z wyjątkiem indeksatorze obsługi S3HD. Jednak ograniczenia indeksowanie i zasobów skutecznie ograniczyć zakres użycia funkcji. Na przykład [kognitywnych wyszukiwania](cognitive-search-concept-intro.md) indeksowania ma umiejętności długotrwałe tego limitu czasu na darmowa chyba, że zestaw danych stanie się być bardzo mała.

> [!TIP]
> Większość klientów zaczyna **wolne** warstwy do oceny, a następnie uzyskać dyplom do **standardowe** do tworzenia aplikacji. Po wybraniu warstwy i [alokowanie usługi wyszukiwania](search-create-service-portal.md), możesz [zwiększenie liczby replik i partycja](search-capacity-planning.md) dostrajania wydajności. Aby uzyskać więcej informacji na temat kiedy i dlaczego może dopasować pojemności, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Pojęcia dotyczące rozliczeń

Pojęcia, które należy zrozumieć wyboru warstwy obejmują definicje pojemności, ograniczenia usługi i jednostki usługi. 

### <a name="capacity"></a>Pojemność

Pojemność mają strukturę jako *replik* i *partycji*. Repliki są wystąpieniami usługi wyszukiwania, której każdej repliki hostem jednej kopii indeksu równoważeniem obciążenia. Na przykład usługi z replikami 6 ma 6 kopie każdy indeks załadowany w usłudze. Partycje przechowywania indeksy i automatycznie podziału danych z możliwością: dwie partycje podzielone indeksu w połowie, trzy partycje trzecie i tak dalej. Pod względem wydajności *rozmiar partycji* jest głównych funkcji różnego między warstwami.

> [!NOTE]
> Wszystkie **standardowe** warstwy obsługi [repliki kombinacji elastyczne i partycji](search-capacity-planning.md#chart) , co pozwala [wagi systemu dla szybkości lub magazynu](search-performance-optimization.md) zmieniając saldo. **Podstawowe** zapewnia trzy repliki dla wysokiej dostępności, ale ma tylko partycji. **Bezpłatne** warstw nie udostępniają zasobów dedykowanych: obliczeniowych, zasobów są współużytkowane przez wiele bezpłatnych usług.

### <a name="limits"></a>Limity

Usługi host zasoby, takie jak indeksów, indeksatorów i tak dalej. Każda warstwa nakłada [usługi limity](search-limits-quotas-capacity.md) liczby zasobów, można utworzyć. Jako takie ograniczenie liczby indeksów (i innych obiektów) jest drugi funkcja różnego między warstwami. Podczas przeglądania poszczególnych opcji w portalu, należy pamiętać, limity liczba indeksów. Inne zasoby, takie jak indeksatorów, źródła danych i skillsets, jest ustalana na podstawie limitów indeksu.

### <a name="search-units"></a>Jednostki wyszukiwania

Najważniejsze pojęcia rozliczeń zrozumienie jest *jednostka wyszukiwania* ("SU"), która jest rozliczeniowym jednostki dla usługi Azure Search. Ponieważ usługi Azure Search jest zależna od zarówno repliki i partycje funkcji, go nie ma sensu do jednego lub drugiego. Zamiast tego rozliczeń jest oparta na złożone obu. Formulaically, SU jest produktu repliki i partycje używane przez usługę: (R X P = SU). Co najmniej każda usługa rozpoczyna się od 1 SU (jedna replika pomnożona przez jednej partycji), ale bardziej realistyczne modelu może być repliki 3, 3-partycji usługi rozliczane jako 9 SUs. 

Mimo że każda warstwa oferuje stopniowo lepszą wydajność, można przełączyć część całkowitej pojemności w trybie online, zawierający pozostałe rezerwy. Pod względem rozliczeń, jest liczba partycji i replik Przełącz online, obliczeniową przy użyciu formuły SU, określający, co faktycznie płatności.

Szybkość rozliczeń odbywa się co godzinę dla SU, w poszczególnych warstwach mających różne stawki. Ocenia dla każdej warstwy można znaleźć na [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).

## <a name="consumption-patterns"></a>Wzorce użycia

Większość klientów zaczyna **wolne** usługi, które prowadzą przez czas nieokreślony, a następnie wybierz jedną z **standardowe** warstw poważne obciążeń rozwoju lub produkcji. 

![Usługa Azure search warstw](./media/search-sku-tier/tiers.png "wyszukiwanie Azure warstw cenowych")

Na jednym z końców **podstawowe** i **S3 HD** istnieje dla zużycia ważne, ale nietypowe wzorce. **Podstawowe** jest w przypadku obciążeń produkcyjnych małych: oferuje umowy SLA, dedykowane zasobów, wysokiej dostępności, ale magazynu niewielkie, nadmiarów na łącznie 2 GB. Ta warstwa została zaprojektowana dla klientów kto stale w obszarze wykorzystywanych dostępnej pojemności. Na końcu daleko **S3 HD** jest przeznaczony dla obciążeń typowe dla niezależnych dostawców oprogramowania, partnerów, [rozwiązania wielodostępnej](search-modeling-multitenant-saas-applications.md), lub żadnej konfiguracji wywoływania dla dużą liczbę małych indeksów. Często jest oczywiste kiedy **podstawowe** lub **S3 HD** warstwa jest rozwiązanie właściwe, ale jeśli chcesz potwierdzenia możesz wysłać do [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z platformy Azure Obsługa](https://azure.microsoft.com/support/options/) dalsze wskazówki.

Przesunięcie fokus do warstwy standardowa najczęściej używanych **S1 S3** są coraz większe poziomy wydajności z punktami modulacją na rozmiar partycji i maksymalne wartości na liczbę indeksów, indeksatorów i następstwem zasobów postępu:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Rozmiar partycji|  25 GB | 100 GB | 250 GB |  |  |  |  |
| Indeks i indeksatora| 50 | 200 | 200 |  |  |  |  |

**S1** jest typowe rozwiązaniem, gdy dedykowany zasoby stać się to konieczne. Z partycjami 25 GB dla partycji do 12, limit na usługi **S1** jest sumą 300 GB po maksymalizacji za pośrednictwem replik partycji (zobacz [przydzielić partycji i replik](search-capacity-planning.md#chart) więcej realistyczne i zrównoważonym kompozycji.)

Poza magazynu i limity innych aspektów możliwości usługi są jednolite między warstwami. Replik, które są wystąpieniami klasy aparat wyszukiwania (Obsługa operacji indeksowania i zapytań), nie różnią się zależnie od warstwy: **S1** repliki jest taka sama jak **S3** repliki. Podobnie ładunków żądań i odpowiedzi, zapytania na sekundę, przepływności i maksymalny czas wykonywania również nie różnią się zależnie od warstwy.

**S3** i **S3 HD** obsługiwanych przez infrastrukturę identyczne dużej pojemności, ale każdy jedną osiągnie maksymalny limit na różne sposoby. **S3** celem mniejszą liczbę bardzo dużych indeksów. W efekcie jej maksymalny limit jest powiązany z zasobów (2.4 TB dla każdej usługi). **S3 HD** celem duża liczba indeksów bardzo mała. W indeksach 1000 **S3 HD** osiągnięty limit w formie ograniczenia indeksu. Jeśli jesteś **S3 HD** klienta, który wymaga więcej niż 1000 indeksów, skontaktuj się z Microsoft Support informacji na temat kontynuować.

> [!NOTE]
> Wcześniej limity dokumentów zostały wchodzi w grę, ale nie mają już zastosowania do większości usług wyszukiwanie Azure udostępnione po 2018 stycznia. Aby uzyskać więcej informacji o warunkach, dla których nadal stosować limity dokumentów, zobacz [usługi limity: dokument limity](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Ocena wydajności

Pojemność i kosztów uruchomiona usługa go ręcznie dostępnych. Warstw nałożyć ograniczenia na dwa poziomy (magazynu i zasobów), więc możesz pomyśleć o zarówno ponieważ którykolwiek przejściu najpierw jest skuteczne limit. 

Uwarunkowania biznesowe wymagają zazwyczaj liczba indeksów, które mają być. Na przykład indeksu globalnego dla dużych repozytorium dokumentów lub możliwe, że wiele indeksów, zależnie od regionu, aplikacji lub niszowych biznesowych.

Aby określić rozmiar indeksu, trzeba [utworzyć](search-create-index-portal.md). Struktury danych w usłudze Azure Search jest głównie [odwrócony indeksu](https://en.wikipedia.org/wiki/Inverted_index), który ma inną charakterystykę niż źródła danych. Odwrócony indeksu rozmiar i złożoność są określane przez zawartość, niekoniecznie ilość danych źródła do niego. Źródło dużej ilości danych z nadmiarowością ogromną może spowodować indeks mniejsze niż mniejsze wysokiej zmiennej zawartością zestawu danych.  Tak jest rzadko można wnioskować o rozmiar indeksu na podstawie rozmiaru oryginalnego zestawu danych.

### <a name="preliminary-estimates-using-the-free-tier"></a>Szacuje wstępnego za pomocą warstwa bezpłatna

Jeden stosowanych podczas szacowania pojemności jest zacząć **wolne** warstwy. Odwołania, który **wolne** usługa zawiera maksymalnie 3 indeksów, 50 MB pamięci masowej i 2 minuty indeksowania czasu. Może być wyzwaniem, aby oszacować rozmiar index projektowane, z tych warunków ograniczających, lecz w poniższym przykładzie przedstawiono podejście:

+ [Utwórz bezpłatne usługi](search-create-service-portal.md)
+ Przygotowanie mały, reprezentatywny zestaw danych (przyjmowane jest pięć tysięcy dokumentów i rozmiar próbki dziesięć procent)
+ [Tworzenie indeksu początkowego](search-create-index-portal.md) i zanotuj jego rozmiaru w portalu (przyjmowane jest 30 MB)

Zakładając, że próbki został zarówno przedstawiciel i dziesięć procent całego źródła danych, indeksu 30 MB staje się około 300 MB. Jeśli wszystkie dokumenty zostaną zindeksowane. Dzięki tylu wstępnego, może być double wielkość w budżecie dwa indeksy (Programowanie i produkcji), łącznie z 600 MB w wymagania dotyczące magazynu. Jest to prosty sposób spełniać **podstawowe** warstwy, czy istnieje uruchamiania.

### <a name="advanced-estimates-using-a-billable-tier"></a>Zaawansowane szacuje przy użyciu rozliczeniowy warstwy

Niektórzy klienci wolą rozpoczynać się od zasobów dedykowanych próbkowania większych i czas przetwarzania, a następnie opracowywać realistyczne oszacowania ilości indeksu, rozmiar i woluminy zapytania podczas tworzenia. Początkowo usługi zostanie zainicjowana na podstawie szacunku najlepsze dopasowanie, a następnie miarę rozwoju opracowywanego projektu, zespoły zwykle pewności, czy istniejąca usługa jest powyżej lub poniżej pojemności dla obciążeń produkcyjnych planowanego. 

1. [Przejrzyj ograniczenia usługi w poszczególnych warstwach](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) ustalenie, czy niższych warstwach może obsługiwać liczbę indeksów należy. Między **podstawowe**-**S1**- **S2** warstw, limity indeksu są odpowiednio 15 – 50-200.

1. [Tworzenie usługi w warstwie rozliczeniowy](search-create-service-portal.md):

    + Uruchom niskie, na **podstawowe** lub **S1** Jeśli jesteś na początku użytkownika na naukę.
    + Uruchom wysoka, w **S2** lub nawet **S3**, jeśli dużych obciążeń indeksowanie i zapytania są oczywiste.

1. [Tworzenie indeksu początkowego](search-create-index-portal.md) ustalenie, jak tłumaczy źródła danych do indeksu. To jest jedynym sposobem oszacowanie rozmiaru indeksu.

1. [Monitorowanie magazynu, ograniczenia usługi, wolumin zapytania i opóźnienia](search-monitor-usage.md) w portalu. Portal zawiera zapytania na zapytania drugie, ograniczeniem przepustowości i opóźnień wyszukiwania; które mogą pomóc zdecydować, czy jesteś w prawo warstwy. Jako uzupełnienie metryki portalu można skonfigurować monitorowania dokładnego, takich jak przeglądowych analizy, włączając [analizy ruchu wyszukiwania](search-traffic-analytics.md). 

Indeks liczby i rozmiaru są zastosowanie również do analizy, ponieważ maksymalna osiągnięciu limitu za pomocą pełnego wykorzystania przestrzeni dyskowej (partycji) lub maksymalnych zasobów (indeksów, indeksatorów i tak dalej), zależnie od zostanie osiągnięty jako pierwszy. Portalu pomaga śledzić, wyświetlane jest bieżące użycie i maksymalną wartością obok siebie na stronie przeglądu.

> [!NOTE]
> Wymagania dotyczące magazynu może być nadmiernie nadmuchany, jeśli dokumenty zawierają nadmiarowe dane. W idealnym przypadku dokumenty zawierają tylko dane potrzebne w celu uzyskania wyników wyszukiwania. Dane binarne jest niemożliwych i powinny być przechowywane oddzielnie (np. w tabeli lub obiektu blob magazynu Azure) za pomocą pola w indeksie, aby pomieścić URL odwołania do danych zewnętrznych. Maksymalny rozmiar poszczególnych dokumentu jest 16 MB lub mniej Jeśli jesteś zbiorczego przekazywania wielu dokumentów w jedno żądanie. [Usługa limity w usłudze Azure Search](search-limits-quotas-capacity.md) zawiera więcej informacji.
>

**Zagadnienia dotyczące woluminu zapytania**

Zapytania na sekundę (QPS) jest metrykę uzyska dostępność podczas dostrajania wydajności, ale zazwyczaj nie wchodzi to warstwy, chyba że oczekiwane woluminie bardzo duże zapytania na początku.

Wszystkie warstwy standardowej można zapewnia replik na partycje, obsługa skrócenia zapytania za pomocą dodatkowych replik ładowania równoważenia i dodatkowe partycje w celu równoległego przetwarzania. Po zainicjowaniu obsługi usługi można dostosować wydajność.

Klient, który oczekuje strong przez dłuższy czas woluminów od samego początku należy wziąć pod uwagę wyższe warstw, obsługiwana przez sprzęt bardziej zaawansowanych zapytań. Następnie przełącz partycji i replik w trybie offline lub nawet przełączenie do dolnej warstwy usługi, w przypadku tych woluminów zapytania nie zmaterializowania. Aby uzyskać więcej informacji na temat obliczania przepływności zapytania, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).


**Umowy dotyczące poziomu usług**

**Wolne** warstwy i Podgląd funkcji nie pochodzą z [umowy dotyczące poziomu (SLA) usług](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Dla wszystkich warstw rozliczeniowy umowy SLA zaczną obowiązywać podczas obsługi administracyjnej nadmiarowość wystarczające dla Twojej usługi. Co najmniej dwa repliki są wymagane do umowy SLA kwerendy (odczyt). Co najmniej trzech repliki są wymagane dla zapytań i indeksowania SLA (odczytu i zapisu). Liczba partycji nie jest brany pod uwagę umowy dotyczącej poziomu usług. 

## <a name="tips-for-tier-evaluation"></a>Porady dotyczące oceny warstwy

+ Dowiedz się, jak tworzyć wydajne indeksy i metodologii odświeżania, które są co najmniej impactful. Firma Microsoft zaleca [analizy ruchu wyszukiwania](search-traffic-analytics.md) dla wglądowi w działaniu zapytania.

+ Zezwalaj na metryki skompilować wokół zapytania i zbierać dane wokół wzorców użycia (zapytań w godzinach pracy indeksowania poza godzinami szczytu) i użyć tych danych do informowania przyszłych udostępniania decyzji dotyczących usługi. Gdy nie jest praktyczne na poziomie godzinowo lub dziennie, można dynamicznie zmieniać, partycji i zasobów, aby pomieścić planowane zmiany w woluminach zapytania lub nieplanowane, ale zmiany utrzymujących Jeśli poziom przechowywania wystarczająco długi, aby zagwarantować podejmowania działań.

+ Należy pamiętać, że Jedyną wadą interfejsu w obszarze obsługi jest czy może być konieczne zerwanie usługi, jeśli rzeczywiste wymagania są większe niż szacowane. Aby uniknąć przerw w działaniu usługi, czy tworzenie nowej usługi w tej samej subskrypcji wyższego poziomu i uruchom go obok siebie, dopóki wszystkie aplikacje i żądań docelowego nowy punkt końcowy.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynać **wolne** warstwy i tworzenie początkowej indeksu przy użyciu podzbiór danych, aby poznać jego właściwości. Struktury danych w usłudze Azure Search jest odwrócony indeks, w przypadku, gdy rozmiar i złożoność odwrócony indeksu zależy od zawartości. Należy pamiętać, że zawartości wysokiej nadmiarowe wydłuża indeks mniejsze niż wysokiej nieregularne zawartość. Tak jest właściwości zawartości, a nie rozmiaru zestawu danych, który określa wymagania dotyczące magazynu w indeksie.

Po początkowej wiadomo o rozmiarze indeksu [alokowanie usługi rozliczeniowy](search-create-service-portal.md) w jednej z warstw omówione w tym artykule, albo **podstawowe** lub **standardowe** warstwy. Zmniejszą ograniczeń sztuczne na podzbiorów danych i [odbudowanie indeksu](search-howto-reindex.md) uwzględnienie wszystkich danych, konieczne jest można wyszukiwać.

[Przydziel partycji i replik](search-capacity-planning.md) w razie potrzeby można pobrać wydajności i skalowania wymagane.

Jeśli wydajność i pojemność są poprawnie, wszystko będzie gotowe. W przeciwnym razie ponownie Utwórz usługę wyszukiwania na inną warstwę, która więcej zostaje dokładnie wyrównana z potrzeb.

> [!NOTE]
> Więcej pomocy przy pytania, można ogłosić [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).