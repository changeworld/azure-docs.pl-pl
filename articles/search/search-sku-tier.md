---
title: Wybierz warstwy cenowej lub jednostki SKU dla usługi Azure Search — usługa Azure Search
description: 'Usługa Azure Search można aprowizować w tych jednostkach SKU: Bezpłatna, podstawowa i standardowa, gdzie Standard jest dostępna w różne konfiguracje zasobów i poziomów wydajności.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b50d0c0ca9a4000cc0c725453a3ef04b4bed9275
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681577"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Wybierz warstwę cenową dla usługi Azure Search

W usłudze Azure Search [utworzony zasób](search-create-service-portal.md) cen warstwy lub jednostki SKU, który został rozwiązany przez okres istnienia usługi. Warstwy obejmują **bezpłatna**, **podstawowe**, **standardowa**, lub **zoptymalizowane pod kątem magazynu**.  **Standardowa** i **zoptymalizowane pod kątem magazynu** są dostępne w kilku konfiguracji i pojemności. 

Większość klientów zaczyna od **bezpłatna** warstwy do oceny, a następnie stopniowo rozwiązanie do jednego z wyższych warstw płatnych wdrożeń deweloperskim i produkcyjnym. Możesz wykonać wszystkie przewodników Szybki Start i samouczków w **bezpłatna** warstwy, w tym przypadku usługa cognitive search dużej ilości zasobów.

> [!NOTE]
> Warstwy usługi zoptymalizowane pod kątem magazynu są obecnie dostępne w wersji zapoznawczej w obniżonej cenie na potrzeby testowania i eksperymentowania w celu zbierania informacji zwrotnych. Ostateczna cena usługi zostanie ogłoszony później podczas tych warstwach są ogólnie dostępne. Odradzamy korzystający z tych warstw w przypadku aplikacji produkcyjnych.

Warstwy odzwierciedlają właściwości sprzętu obsługującego usługi (zamiast funkcji) i są zróżnicowane według:

+ Liczba indeksów, które można utworzyć
+ Rozmiar i prędkość partycje (magazynu fizycznego)

Mimo że wszystkich warstwach, w tym **bezpłatna** warstwie, zwykle oferują równoważności funkcji większe obciążenia można dyktowanie wymagania dla wyższej warstwy. Na przykład [indeksowania sztucznej Inteligencji dzięki usługom Cognitive Services](cognitive-search-concept-intro.md) ma umiejętności długotrwałych tego limitu czasu w bezpłatnej usługi, chyba że zestaw danych stanie się być mały.

> [!NOTE] 
> Wyjątkiem od równoważności funkcji jest [indeksatory](search-indexer-overview.md), które nie są dostępne na S3HD.
>

W obrębie warstwy, możesz [Dopasowywanie zasobów replik i partycji](search-capacity-planning.md) Aby zwiększyć lub zmniejszyć skalę. Możesz rozpocząć od jednego lub dwóch każdego i tymczasowo podnieść swoje moc obliczeniową dla duże obciążenia indeksowania. Możliwość dostrojenia poziomów zasobów w ramach warstwy zwiększa elastyczność, ale również lekko komplikuje analizy. Może być konieczne Sprawdź, czy niższej warstwy z wyższym zasobów/replikami oferuje lepszą wartość i wydajność niż wyższego poziomu z niższym zasoby. Aby dowiedzieć się, kiedy i dlaczego może dostosować pojemność, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Warstwy dla usługi Azure Search

Poniższa tabela zawiera listę dostępnych warstw. Inne źródła informacji warstwy obejmują [stronę z cennikiem](https://azure.microsoft.com/pricing/details/search/), [limity usług i danych](search-limits-quotas-capacity.md)i na stronie portalu podczas aprowizowania usługi.

|Warstwa | Pojemność |
|-----|-------------|
|Bezpłatna | Współużytkowana z innymi subskrybentami. Nieskalowalne, maksymalnie 3 indeksy i 50 MB miejsca. |
|Podstawowa | Dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę. Jedna partycja 2 GB i maksymalnie trzech replik. |
|1 standardowa (S1) | Z S1 na górę dedykowanych maszynach o większej pojemności magazynowania i przetwarzania na każdym poziomie. Rozmiar partycji wynosi 25 GB na partycję (max 300 GB na usługę) dla S1. |
|Standardowy 2 (warstwa S2) | Podobny do S1, ale z 100 GB/partycji (max 1,2 TB na usługę) |
|Standardowa 3 (S3) | 200 GB na partycję (max 2,4 TB na usługę) |
|Standardowa 3 wysoka gęstość (warstwa S3 — HD) | O wysokiej gęstości jest *hostingu tryb* S3. Używany sprzęt jest zoptymalizowany pod kątem dużej liczby mniejsze indeksów, przeznaczone dla scenariuszy wielodostępności. Wysoka gęstość S3 ma tego samego opłat za jednostki S3, ale sprzęt jest zoptymalizowany pod kątem pliku szybkie odczyty na dużą liczbę mniejszych indeksów.|
|Magazyn zoptymalizowany pod kątem 1 (L1) | 1 TB na partycję (max 12 TB na usługę) |
|Magazyn zoptymalizowany pod kątem 2 (L2) | 2 TB na partycję (max 24 TB na usługę) |

> [!NOTE] 
> Magazyn zoptymalizowany pod kątem warstw oferuje większą pojemność magazynu przy niższej cenie niż warstwy standardowa na TB. Podstawowy kosztem jest wyższa kwerendami, które należy sprawdzić, czy dla wymaganiom konkretnych aplikacji.  Aby dowiedzieć się więcej na temat tej warstwy zagadnienia związane z wydajnością, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Sposób działania rozliczeń

W usłudze Azure Search są naliczane opłaty w Azure Search na trzy sposoby, a istnieją składniki, stałe i zmienne. W tej sekcji omówiono z kolei w każdej części rozliczeń.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Koszty usługi podstawowej (stałe i zmienne)

Za samą usługę Minimalna opłata za to pierwszy jednostka wyszukiwania (1 repliki x 1 partycji), a ta wartość jest ustalona okres istnienia usługi, ponieważ usługi nie można uruchomić na każdej mniejszej niż ta konfiguracja. 

Poza minimum można dodać replik i partycji niezależnie. Na przykład można dodać tylko repliki lub tylko partycje. Przyrostowych wzrostu wydajności za pośrednictwem replik i partycji stanowi składnik kosztów zmiennej. 

Rozliczenie jest oparte na [formuły (repliki x partycje x współczynnik)](#search-units). Szybkość, z którą są naliczane, zależy od warstwy cenowej, którą wybierzesz.

Poniższy zrzut ekranu na cennikiem jednostek jest wskazywane bezpłatna, podstawowa i S1 (S2, S3, P1 i P2 nie są wyświetlane). Jeśli utworzono **podstawowe**, **standardowa**, lub **zoptymalizowane pod kątem magazynu** service, koszt miesięczny czy średnia wartość, która pojawia się dla *cena-1*i *cena 2* odpowiednio. Dla każdej warstwy obniżyć koszty jednostki, ponieważ przekracza w każdej warstwie kolejnych możliwości obliczeniowe, możliwości i magazynu. Stawki dla usługi Azure Search są publikowane w [usługi Azure Search stronę z cennikiem](https://azure.microsoft.com/pricing/details/search/).

![Na cennikiem jednostek](./media/search-sku-tier/per-unit-pricing.png "na cennikiem jednostek")

Gdy wyceny rozwiązania wyszukiwania, należy zauważyć, że ceny i wydajności nie są liniowe (podwojenie pojemności, więcej niż wartości podwójnej precyzji kosztów). Na przykład jak działa formuły zobacz ["How to przydzielenie replik i partycji"](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).


### <a name="2-data-egress-charges-during-indexing"></a>2. Opłaty za wyjście danych podczas indeksowania

Korzystanie z [indeksatorów usługi Azure Search](search-indexer-overview.md) może doprowadzić do rozliczeń wpływ w zależności od tego, gdzie znajdują się usługi. Jeśli utworzysz usługę Azure Search w tym samym regionie, w którym znajdują się dane, można wyeliminować opłaty za wyjście danych. Poniższe punkty mają z [przepustowości stronę z cennikiem](https://azure.microsoft.com/pricing/details/bandwidth/).

+ Microsoft nie pobierać wszystkie przychodzące dane do dowolnej usługi na platformie Azure lub wszelkie dane wychodzące z usługi Azure Search.

+ W przypadku rozwiązań z wieloma usługami nie istnieją żadne opłaty za dane wykraczania poza granice sieci, w przypadku wszystkich usług w tym samym regionie.

Opłaty za dane wychodzące w przypadku usług w różnych regionach. Takie opłaty nie są częścią rachunku Azure Search per se, ale są wymienione w tym miejscu, ponieważ używasz danych lub indeksatory wzbogacony sztucznej Inteligencji do pobierania danych z różnych regionów, zostanie wyświetlony tych kosztów zostaną uwzględnione w ogólną kwotę rachunku. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. AI-wzbogacone indeksowanie przy użyciu usług Cognitive Services

Dla [indeksowania sztucznej Inteligencji dzięki usługom Cognitive Services](cognitive-search-concept-intro.md), należy zaplanować na dołączanie płatnych usług Cognitive Services na S0 warstwa cenowa zasobu do przetwarzania zgodnie z rzeczywistym użyciem. Nie ma skojarzonych z dołączania usług Cognitive Services żadnych "kosztów stałej". Płacisz tylko za przetwarzanie, których potrzebujesz.

Wyodrębnianie obrazu podczas łamania dokumentów jest opłata za usługę Azure Search, rozliczane na podstawie liczby obrazów wyodrębnione ze swoich dokumentów. Wyodrębnianie tekstu jest aktualnie wolne. 

Inne wzbogacenia, takie jak przetwarzanie języka naturalnego opierają się na [wbudowanych umiejętności poznawcze](cognitive-search-predefined-skills.md) są rozliczane przy użyciu zasobu usług Cognitive Services, przy użyciu stawki stosowanej tak, jakby były wykonywane zadania bezpośrednio za pomocą usług Cognitive Services. Aby uzyskać więcej informacji, zobacz [dołączenia zasobu usług Cognitive Services, za pomocą zestawu umiejętności](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Rozliczanie na podstawie jednostek wyszukiwania

W przypadku operacji usługi Azure Search najważniejszych rozliczeń pojęciem jest *jednostek wyszukiwania* (SU). Ponieważ usługa Azure Search jest zależna od replik i partycji w celu indeksowania i zapytania, nie ma sensu do rozliczania za tylko jednej z nich. Zamiast tego są naliczane złożonego obu tych elementów. 

SU jest wynikiem *repliki* i *partycje* używanych przez usługę: **`(R X P = SU)`**

Każda usługa rozpoczyna się od jednej operacji SU (jedna replika pomnożona przez jedną partycję) jako minimum. Maksymalna liczba dla dowolnej usługi to 36 jednostek wyszukiwania, które można osiągnąć na wiele sposobów: replik partycji 6 x 6 lub 3 partycji x 12 replik, kilka. Jest to często używa się mniej niż łączna pojemność. Na przykład repliki 3, 3 partycji usługi rozliczane jako 9 SUs. Możesz przejrzeć [ten wykres](search-capacity-planning.md#chart) się prawidłowe kombinacje rzut oka.

Stawka rozliczeniowa jest **godzinowe za SU**, z każdą warstwą o coraz wyższego współczynnika. Wyższe warstwy pochodzą z partycjami większych i szybszej mające wpływ na ogólną wyższa stawka godzinowa dla tej warstwy. Według stawek dla poszczególnych warstw można znaleźć na [— szczegóły cennika](https://azure.microsoft.com/pricing/details/search/). 

Większość klientów Przenieś tylko część całkowitą pojemność w trybie online, zawierający pozostałe w rezerwie. W zakresie rozliczeń, jest liczba partycji i replik przenieść online, obliczeniowe przy użyciu formuły SU, która określa, co faktycznie opłaty są naliczane za godzinę.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Okres rozliczeniowy wyodrębniania obrazu w usłudze wyszukiwania poznawczego

Jeśli jest wyodrębniana obrazy z plików w usługa cognitive search indeksowanie potoku, opłaty są naliczane dla tej operacji, na rachunku Azure Search. Parametr, który wyzwala wyodrębniania obrazu jest **imageAction** w [konfiguracji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Jeśli **imageAction** jest ustawiona na wartość none (wartość domyślna), nie będą naliczane opłaty, do wyodrębnienia obrazu.

Ceny mogą ulec zmianie, ale zawsze jest udokumentowany na [— szczegóły cennika](https://azure.microsoft.com/pricing/details/search/) strony usługi Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Okres rozliczeniowy wbudowanych umiejętności programowania w usłudze wyszukiwania poznawczego

Po skonfigurowaniu wzbogacony potok, wszelkie [wbudowanych umiejętności](cognitive-search-predefined-skills.md) używane w potoku są oparte na modeli uczenia maszynowego. Modele te są dostarczane przez usługi Cognitive Services. Użycie tych modeli podczas indeksowania jest rozliczana przy użyciu stawki stosowanej tak, jakby było żądanego zasobu bezpośrednio.

Załóżmy na przykład potok składający się z optyczne rozpoznawanie znaków (OCR) względem zeskanowany obraz JPEG plików, w których tekst wynikowy są przesyłane do indeksu usługi Azure Search dla kwerend wyszukiwania w dowolnej postaci. Obejmuje indeksatora z potokiem indeksowania [umiejętności optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md), i że będzie umiejętności [dołączone do zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Po uruchomieniu indeksatora, opłaty są widoczne na rachunku poznawczych, optyczne rozpoznawanie znaków wykonywania.

## <a name="tips-for-reducing-costs"></a>Porady dotyczące zmniejszania kosztów

Nie można zamknąć usługę, aby zmniejszyć rachunek. Dedykowane zasoby są operacyjnej 24-7, przydzielone do usługi wyłącznego użytku przez okres istnienia usługi. Jedynym sposobem, aby zmniejszyć rachunek jest dzięki zmniejszeniu replik i partycji na niskim poziomie, która zapewnia akceptowalny poziom wydajności i [zgodności z umową SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeden poziom pozwala zmniejszyć koszty jest Wybieranie warstwy przy użyciu niższe stawki godzinowej. Stawki godzinowe S1 jest niższa od stawek S2 lub S3. Przy założeniu, że możesz aprowizować usługę, której celem jest niższy koniec swojej projekcji obciążenia, gdy wykraczają poza możliwości usługi, można utworzyć drugi usługi większych warstwowego, ponowną kompilację indeksów w tej drugiej usługi, a następnie usuń pierwszy z nich. 

Jeśli wykonano planowania pojemności serwerów lokalnych, wiesz, jest często "Kup", aby można było obsłużyć przewidywany wzrost. Ale z usługą w chmurze można próbować oszczędności więcej agresywnie, ponieważ nie ma ograniczenia określonych zakup. Zawsze możesz przełączyć do wyższej warstwy usługi, jeśli bieżący jest niewystarczająca.

### <a name="capacity-drill-down"></a>Pojemność Przechodzenie do szczegółów

W usłudze Azure Search postacią pojemności *replik* i *partycje*. 

+ Repliki są wystąpień usługi wyszukiwania, w której każda replika hostem jedną ze zrównoważonym obciążeniem kopię indeksu. Na przykład usługi za pomocą 6 replik zawiera 6 kopii każdego indeksu załadowane w usłudze. 

+ Partycje przechowywania indeksy i automatycznie podziału danych z możliwością wyszukiwania: dwie partycje podzielić indeksu w wysokości równej połowie, trzech partycjach trzecie i tak dalej. Pod względem wydajności *rozmiar partycji* jest głównej różnicującego funkcji w warstwach.

> [!NOTE]
> Wszystkie **standardowa** i **zoptymalizowane pod kątem magazynu** warstwy obsługi [partycje i repliki elastyczne kombinacje](search-capacity-planning.md#chart) aby można było [wagi systemu dla danej szybkości lub Magazyn](search-performance-optimization.md) , zmieniając równowagi. **Podstawowe** zapewnia trzy repliki dla wysokiej dostępności, ale ma tylko jedną partycję. **Bezpłatne** warstwy są oferowane dedykowanych zasobów: przetwarzanie zasoby są współużytkowane przez wielu subskrybentów.

### <a name="more-about-service-limits"></a>Więcej informacji na temat limity usługi

Usług zasobów hosta, takich jak indeksy, indeksatory i tak dalej. Każda warstwa nakłada [limitów usług](search-limits-quotas-capacity.md) ilości zasobów, możesz utworzyć. W efekcie limit liczby indeksów (i innych obiektów) jest druga funkcja różnicujący w warstwach. Podczas przeglądania każdej opcji w portalu, należy pamiętać, limity liczby indeksów. Inne zasoby, takie jak indeksatorów, źródła danych i dokładniejsze, jest ustalana na podstawie limitów indeksu.

## <a name="consumption-patterns"></a>Wzorce użycia

Większość klientów zaczyna od **bezpłatna** usługi, które prowadzą przez czas nieokreślony, a następnie wybierz jedno z **standardowa** lub **zoptymalizowane pod kątem magazynu** warstwy dla rozwoju poważne lub obciążenia produkcyjne. 

![Warstwy usługi Azure search](./media/search-sku-tier/tiers.png "usługi Azure search warstw cenowych")

Na obu końcach **podstawowe** i **wysoka gęstość S3** istnieje pod kątem wzorców ważne, ale nietypowe użycie. **Podstawowe** dla obciążeń produkcyjnych małych: oferuje umowy SLA, dedykowane zasoby, wysoką dostępność, ale skromną magazynu nadmiarów na łącznie 2 GB. Ta warstwa została zaprojektowana dla klientów który konsekwentnie w obszarze wykorzystywanych dostępnej pojemności. Na końcu daleko **wysoka gęstość S3** jest w przypadku obciążeń typowy dla niezależnych dostawców oprogramowania, partnerów, [rozwiązań wielodostępnych](search-modeling-multitenant-saas-applications.md), lub dowolna Konfiguracja wywoływania dla dużej liczby małych indeksów. Często jest oczywiste kiedy **podstawowe** lub **wysoka gęstość S3** warstwa jest odpowiednie, ale jeśli chcesz, aby potwierdzenia możesz wysłać do [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z platformy Azure Obsługa](https://azure.microsoft.com/support/options/) dalszych informacji.

Przesunięcie fokus do najczęściej używanych warstwy standardowa, **S1 S3** postępu hakerów oraz rośnie liczba poziomów wydajności przy użyciu punktów szczytowym na rozmiar partycji i maksymalne wartości na liczbę indeksów, indeksatorów i zasoby następstwem są:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Rozmiar partycji|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limity indeksu i indeksatora| 50 | 200 | 200 |  |  |  |  |

**S1** jest wspólnego rozwiązaniem, gdy dedykowanych zasobów i wielu partycjach stają się koniecznością. Wraz z partycjami 25 GB do 12 partycjami, usługa na limit **S1** to całkowita liczba 300 GB Jeśli zmaksymalizować partycji za pośrednictwem replik (zobacz [przydzielić partycje i repliki](search-capacity-planning.md#chart) dla bardziej równoważenia — kompozycje.)

Strony portalu i cen Przenieś fokus na rozmiar partycji i magazynu, ale dla poszczególnych warstw, wszystkie zasoby obliczeniowe możliwości (pojemności dysku, szybkości procesorów) wzrastają liniowo z ceną. **S2** repliki jest szybsza niż **S1**, i **S3** jest szybsza niż **S2**. **S3** warstwy Przerwij zwykle liniowa cenach obliczeniowych ze wzorca do nieproporcjonalnie szybciej operacji We/Wy. Jeśli spodziewasz się operacji We/Wy jako wąskie gardło **S3** daje znacznie więcej operacji We/Wy niż niższych warstwach.

**S3** i **wysoka gęstość S3** są wspierane przez infrastrukturę identyczne dużej pojemności, ale każdy jeden osiągnie maksymalny limit na różne sposoby. **S3** jest przeznaczony dla mniejszej liczby indeksów bardzo duże. W efekcie maksymalny limit jest powiązany z zasobów (2,4 TB dla każdej usługi). **Wysoka gęstość S3** jest przeznaczony dla dużej liczby indeksów bardzo mała. Na 1000 indeksów **wysoka gęstość S3** osiągnięty limit w formie ograniczenia indeksu. Jeśli jesteś **wysoka gęstość S3** klienta, który wymaga więcej niż 1000 indeksów, skontaktuj się z Microsoft Support informacji dotyczące dalszego postępowania.

> [!NOTE]
> Wcześniej limity dokumentów zostały jest brany pod uwagę, ale nie mają już zastosowania dla nowych usług. Aby uzyskać więcej informacji o warunkach, w których nadal mają zastosowanie limity dokumentów, zobacz [limitów usług: dokumentu limity](search-limits-quotas-capacity.md#document-limits).
>

Magazyn zoptymalizowany pod kątem warstw **L1 L2**, są doskonałe do zastosowań o wymagania dotyczące dużych ilości danych, ale stosunkowo małej liczbie użytkowników końcowych, w której minimalizacja kwerendami nie jest najwyższy priorytet.  

|  | L1 | PAMIĘCI PODRĘCZNEJ L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Rozmiar partycji|  1 TB | 2 TB |  |  |  |  |  |
| limity indeksu i indeksatora| 10 | 10 |  |  |  |  |  |

*L2* oferuje dwa razy całkowitej pojemności pamięci masowej *L1*.  Wybieranie warstwy oparte na maksymalną ilość danych, które Twoim zdaniem potrzebuje Twojego indeksu.  *L1* warstwy partycje skalowania się wielokrotność 1 TB, maksymalnie do 12 TB, gdy *L2* zwiększenia 2 TB dla każdej partycji maksymalnie 24 TB.

## <a name="evaluate-capacity"></a>Ocena wydajności

Wydajność i koszty, z którym jest uruchomiona usługa go ręcznie dostępnych. Warstwy nakładają ograniczenia dotyczące dwa poziomy (magazyn i zasoby), więc możesz pomyśleć o obu ponieważ każdego z nich osiągniesz najpierw jest obowiązywać limit. 

Uwarunkowania biznesowe wymagają zwykle liczba indeksów, które będą potrzebne. Na przykład globalnego indeksu dla dużych repozytorium dokumentów lub może być wiele indeksów, zależnie od regionu, aplikacji lub niszowe biznesowych.

Aby określić rozmiar indeksu, musisz mieć [utworzenia takiego](search-create-index-portal.md). Struktura danych w usłudze Azure Search jest głównie [odwrócony indeksu](https://en.wikipedia.org/wiki/Inverted_index), który ma inną charakterystykę niż źródła danych. Odwrócony indeksu rozmiar i złożoność są określane przez zawartość, niekoniecznie ilość danych, które kanał do niego. Źródłem dużych ilości danych dzięki nadmiarowości ogromnych może spowodować indeks mniejszych niż mniejszy zestaw danych zawierające wysoce zmienna zawartość. W efekcie jest rzadko można wywnioskować rozmiar indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Mimo że szacowania przyszłe potrzeby indeksów i przechowywania może sprawiać wrażenie wątpliwości, warto zrobić. Jeśli pojemność warstwy okaże się zbyt niska, będzie trzeba aprowizować nową usługę w wyższej warstwie i następnie [ponowne ładowanie indeksów](search-howto-reindex.md). Nie istnieje żadne uaktualnienia w miejscu, samej usługi z jedną jednostką SKU do innego.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Krok 1: Twórz szacunkowe, korzystając z bezpłatnej warstwy

Jedno z podejść do oceny wydajności jest rozpoczęcie od **bezpłatna** warstwy. Pamiętamy **bezpłatna** usługa oferuje maksymalnie 3 indeksów, 50 MB miejsca, a 2 minuty, godziny indeksowania. Może stanowić wyzwanie, aby oszacować rozmiar indeksu przewidywane, za pomocą tych ograniczeń, ale poniższy przykład ilustruje sposób podejścia:

+ [Tworzenie usługi w warstwie bezpłatna](search-create-service-portal.md)
+ Przygotowanie mały, reprezentatywny zestaw danych (przyjmowane jest pięć tysięcy dokumentów i rozmiar próbki dziesięć procent)
+ [Tworzenie indeksu początkowego](search-create-index-portal.md) i zanotuj jego rozmiar w portalu (przyjmowane jest 30 MB)

Przy założeniu, że próbka miała wartość przedstawiciel i dziesięć procent całe źródło danych, indeksu 30 MB staje się średnio 300 MB, jeśli wszystkie dokumenty są indeksowane. Korzystając z tym numerem wstępnego, może być double kwota w budżecie dwa indeksy rozwoju i produkcji, łącznie z 600 MB w wymagania dotyczące magazynu. Jest to prosty sposób spełnione przez **podstawowe** warstwy, aby rozpocząć istnieje.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Krok 2: Twórz oszacowania dostosowany za pomocą płatnych warstwy

Niektórzy klienci preferują rozpoczynać dedykowane zasoby, które można dopasować większy próbkowania i czasy przetwarzania, a następnie twórz realistyczne oszacowania ilości indeksu, rozmiar i woluminy zapytania podczas tworzenia. Początkowo usługa jest przygotowany na podstawie szacunku podjęcie, a następnie jak dojrzewa projektu tworzenia oprogramowania, zespoły zwykle wiedzą, czy istniejącej usługi jest powyżej lub poniżej pojemności dla obciążeń produkcyjnych przewidywany. 

1. [Sprawdź limity usługi w każdej warstwie](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) do określenia, czy niższych warstwach mogą obsługiwać ilość indeksów należy. Między **podstawowe**-**S1**-**S2** warstwy, limity indeksu są odpowiednio 15 – 50-200.  **Zoptymalizowane pod kątem magazynu** warstwa ma limit 10 indeksów, ponieważ jest ono projektanta w celu obsługi małej liczbie bardzo dużych indeksów.

1. [Tworzenie usługi w warstwie płatnej](search-create-service-portal.md):

    + Rozpocznij niskie, na **podstawowe** lub **S1** przypadku na początku swoje nauki.
    + Rozpocznij wysoka, na **S2** lub nawet **S3**, jeśli na dużą skalę obciążeń indeksowania i zapytania są oczywiste.
    + Zoptymalizowane pod kątem, magazynu na **L1** lub **L2**, jeśli indeksujesz dużą ilość danych, a obciążenie zapytaniami jest stosunkowo niska, takich jak wewnętrznej aplikacji biznesowej.

1. [Tworzenie indeksu początkowego](search-create-index-portal.md) ustalenie, jak tłumaczy źródło danych do indeksu. To jest jedynym sposobem, aby oszacować rozmiar indeksu.

1. [Monitorowanie magazynu, limity, wolumin zapytań i opóźnień](search-monitor-usage.md) w portalu. Portal zawiera zapytań dla poszczególnych zapytań drugi, ograniczenia i czas reakcji wyszukiwania; wszystkie z nich może pomóc w podjęciu decyzji, jeśli wybrano właściwą warstwę. Oprócz metrykami w portalu, można skonfigurować monitorowania dokładnego, takich jak analiza przeglądowe, włączając [analiza ruchu wyszukiwania](search-traffic-analytics.md). 

Numer indeksu i rozmiaru są równie istotne dla analizy, ponieważ maksymalny limit osiągnięcia skonfigurowanych za pomocą pełnego wykorzystania pamięci (partycji) lub maksymalnych limitów zasobów (indeksów, indeksatorów i tak dalej), osiągnięta jako pierwsza. Portal pomaga śledzić, przedstawiająca bieżące użycie i maksymalnych limitów obok siebie na stronie Przegląd.

> [!NOTE]
> Wymagania dotyczące magazynu mogą być nadmiernie nadmuchany, jeśli dokumenty zawierają nadmiarowe dane. W idealnym przypadku dokumenty zawierają tylko dane potrzebne do środowiska wyszukiwania. Dane binarne nie umożliwia wyszukiwanie i powinny być przechowywane oddzielnie (np. w usłudze Azure storage tabelą lub obiektem blob) za pomocą pola w indeksie, aby pomieścić URL odwołania do danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu jest 16 MB lub mniej if są zbiorcze przekazywanie wielu dokumentów w jedno żądanie. [Limity usługi Azure Search Service](search-limits-quotas-capacity.md) zawiera więcej informacji.
>

**Zagadnienia dotyczące wolumin zapytań**

Zapytań na sekundę (QPS) jest metryk, który przejmie wyeksponowanie podczas dostosowywania wydajności, ale zwykle nie jest kwestią warstwy jeśli oczekujesz, że zapytania wysoki woluminu na początku.

Warstwy standardowa może dostarczać równoważenie replik partycji, obsługa skrócenia czasu przetwarzania zapytania za pomocą dodatkowych replik dla obciążenia równoważenia i dodatkowe partycje do przetwarzania równoległego. Po aprowizacji usługi można dostrajanie wydajności.

Klienci, którzy oczekują woluminy silne stałą zapytania od samego początku należy wziąć pod uwagę wyższe **standardowa** warstw, wspierane przez sprzętowe bardziej wydajne. Można następnie podjąć partycje i repliki w trybie offline lub nawet przełączać z usługą dolnej warstwy, jeśli woluminy zapytania nie powiodły się zmaterializowania. Aby uzyskać więcej informacji na temat sposobu obliczania przepływności zapytań, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).

Magazyn zoptymalizowany pod kątem warstw zwarte kierunku obciążeń dużych ilości danych, obsługa więcej ogólną indeks magazynu dostępnego, których wymagania dotyczące opóźnień zapytania są nieco obniżone.  Dodatkowe repliki powinny nadal używany na potrzeby ładowania równoważenia i dodatkowe partycje do przetwarzania równoległego. Po aprowizacji usługi można dostrajanie wydajności.

**Umowy dotyczące poziomu usług**

**Bezpłatna** funkcje warstwy i w wersji zapoznawczej nie są dostarczane z [umowami dotyczącymi poziomu (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). W przypadku wszystkich płatnych warstw umowy SLA zaczną obowiązywać podczas aprowizowania nadmiarowości wystarczające dla Twojej usługi. Co najmniej dwiema replikami są wymagane dla zapytania (odczyt) umowy SLA. Co najmniej trzy repliki są wymagane dla zapytań i indeksowania SLA (odczyt zapis). Liczba partycji nie jest brany pod uwagę umowy SLA. 

## <a name="tips-for-tier-evaluation"></a>Porady dotyczące oceny warstwy

+ Dowiedz się, jak zbudować efektywne indeksy i metodologie odświeżania, które są co najmniej impactful. Firma Microsoft zaleca [analiza ruchu wyszukiwania](search-traffic-analytics.md) dla wglądowi w działania zapytania.

+ Zezwalaj metryki skompilować wokół zapytań i zbierać dane dotyczące wzorców użycia (zapytania w godzinach, indeksowanie poza godzinami szczytu), a następnie użyć tych danych w celu nakazania przyszłej obsługi udostępniania decyzje. Chociaż nie jest praktyczne w erze godzinową lub dzienną, można dynamicznie zmieniać partycji i zasobów, aby obsłużyć zaplanowanych zmian w woluminach zapytania lub nieplanowane, ale stałego zmian, jeśli poziom przechowywania wystarczająco długi gwarantowało to podejmowania działań.

+ Należy pamiętać, że Jedyną wadą — aprowizacja jest, może być konieczne zatrzymywania usługi, jeśli rzeczywiste wymagania są większe niż szacowany. Aby uniknąć przerw w działaniu usługi, czy utworzyć nową usługę w tej samej subskrypcji na wyższą warstwę i uruchom go obok siebie, dopóki wszystkie żądania i aplikacje docelowe nowy punkt końcowy.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynać **bezpłatna** warstwy i twórz początkowego indeksu, aby zrozumieć jego właściwości za pomocą podzestawu danych. Struktury danych w usłudze Azure Search jest odwróconą indeksu, gdzie jest to rozmiar i złożoność odwróconą indeksu jest określana przez zawartość. Należy pamiętać o tym, czy zawartość wysoce nadmiarowe wydłuża indeks mniejszych niż wysoce nieregularne zawartość. W efekcie jest cechy zawartości, a nie rozmiaru zestawu danych, który określa wymagania dotyczące magazynu indeksu.

Po utworzeniu pomysłu rozmiar indeksu [aprowizować usługę płatnych](search-create-service-portal.md) w jednej z warstw omówionych w tym artykule, albo **podstawowe**, **standardowa**, lub **Zoptymalizowane pod kątem magazynu** warstwy. Zwalnia wszelkie sztuczne ograniczenia rozmiaru danych i [odbudowanie indeksu](search-howto-reindex.md) obejmujący wszystkie dane rzeczywiście chcesz można wyszukać.

[Przydziel partycje i repliki](search-capacity-planning.md) zgodnie z potrzebami, aby korzystać z wydajności i skalowania, potrzebujesz.

Jeśli wydajność i pojemność są w dobrym stanie, wszystko będzie gotowe. W przeciwnym razie ponownie utworzyć usługę wyszukiwania na inną warstwę, pasującą więcej ściśle z Twoimi potrzebami.

> [!NOTE]
> Aby uzyskać pomoc na swoje pytania, Opublikuj [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z działem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/).
