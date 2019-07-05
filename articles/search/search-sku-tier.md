---
title: Wybierz warstwy cenowej lub jednostki SKU dla usługi Azure Search — usługa Azure Search
description: 'Usługa Azure Search można udostępnić w tych jednostkach SKU: Bezpłatna, podstawowa i standardowa i Standard jest dostępna w różne konfiguracje zasobów i poziomów wydajności.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 892a79f898e2448096ad4b252a18e0713bb32e52
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485307"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Wybierz warstwę cenową dla usługi Azure Search

Podczas tworzenia usługi Azure Search [utworzony zasób](search-create-service-portal.md) cen warstwy (lub jednostki SKU), zostanie rozwiązany przez okres istnienia usługi. Warstwy obejmują bezpłatna, podstawowa, standardowa i zoptymalizowane pod kątem magazynu. Standardowe i zoptymalizowane pod kątem magazynu są dostępne za pomocą kilku konfiguracji i pojemności.

Większość klientów uruchomić w ramach warstwy bezpłatna, dzięki czemu mogą oni korzystać z usługi. Po wydaniu wersji ewaluacyjnej, jest często jest tworzony drugi usługi w jednym z wyższej warstwy dla programowania i produkcyjnych. Za pomocą warstwy bezpłatnej, włącznie z tymi dla wyszukiwania kognitywnego dużej ilości zasobów, możesz wykonać wszystkie przewodników Szybki Start i samouczków.

> [!NOTE]
> Począwszy od 1 lipca wszystkie warstwy są ogólnie dostępne, w tym warstwa zoptymalizowane pod kątem magazynu. Wszystkie ceny można znaleźć na [— szczegóły cennika](https://azure.microsoft.com/pricing/details/search/) strony.

Warstwy odzwierciedlają właściwości sprzętu obsługującego usługi (zamiast funkcji) i są zróżnicowane według:

+ Liczba indeksów, które można utworzyć.
+ Rozmiar i prędkość partycji (składnik pamięci fizycznej).

Mimo że wszystkie warstwy, w tym warstwy bezpłatnej, oferują zazwyczaj równoważności funkcji, większe obciążenia można dyktowanie potrzebę wyższej warstwy. Na przykład [wzbogacania sztucznej Inteligencji dzięki usługom Cognitive Services](cognitive-search-concept-intro.md) ma umiejętności długotrwałych tego limitu czasu w bezpłatnej usługi, chyba że zestaw danych jest niewielki.

> [!NOTE] 
> Wyjątkiem od równoważności funkcji jest [indeksatory](search-indexer-overview.md), które nie są dostępne na wysoka gęstość S3.
>

W obrębie warstwy, możesz [Dopasowywanie zasobów replik i partycji](search-capacity-planning.md) Aby zwiększyć lub zmniejszyć skalę. Może rozpoczynać się jeden lub dwa każdego z nich i tymczasowo podnieść swoje moc obliczeniową dla duże obciążenia indeksowania. Możliwość dostrojenia poziomów zasobów w ramach warstwy zwiększa elastyczność, ale również lekko komplikuje analizy. Może być konieczne Sprawdź, czy niższej warstwy za pomocą więcej zasobów/replik oferuje lepszą wartość i wydajność niż wyższego poziomu z mniejszą liczbą zasobów. Aby dowiedzieć się, kiedy i dlaczego może dostosować pojemność, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Warstwy dla usługi Azure Search

Poniższa tabela zawiera listę dostępnych warstw. Można znaleźć więcej informacji na temat różnych warstw na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/search/)w [limitów w usłudze Azure Search usług](search-limits-quotas-capacity.md) artykuł i w portalu stron, gdy alokujesz usługi.

|Warstwa | Pojemność |
|-----|-------------|
|Wolne | Współużytkowana z innymi subskrybentami. Nie jest skalowalna. Ograniczone do trzech indeksów i 50 MB miejsca. |
|Podstawowa | Dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę. Jedna partycja 2 GB i maksymalnie trzech replik. |
|1 standardowa (S1) | Warstwy S1 i nowszej, dedykowanych maszyn o większej pojemności magazynowania i przetwarzania na każdym poziomie. Warstwa S1 rozmiar partycji wynosi 25 GB na partycję (z maksymalnie 300 GB na usługę). |
|Standardowy 2 (warstwa S2) | Podobny do S1, ale z partycji o pojemności 100 GB (i maksymalnie 1,2 TB na usługę). |
|Standardowa 3 (S3) | Partycje 200 GB (dłuższą 2,4 TB na usługę). |
|Standardowa 3 o wysokiej gęstości S3 | O wysokiej gęstości jest *hostingu tryb* S3. Używany sprzęt jest zoptymalizowany pod kątem dużej liczby mniejsze indeksów i jest przeznaczona dla scenariuszy wielodostępności. Wysoka gęstość S3 ma tego samego opłat za jednostki S3, ale sprzęt jest zoptymalizowany pod kątem pliku szybkie odczyty na dużą liczbę mniejszych indeksów.|
|Magazyn zoptymalizowany pod kątem 1 (L1) | Partycje 1 TB (z maksymalnie 12 TB na usługę). |
|Magazyn zoptymalizowany pod kątem 2 (L2) | Partycje 2 TB (z maksymalnie 24 TB na usługę). |

> [!NOTE] 
> Magazyn zoptymalizowany pod kątem warstw oferuje większą pojemność magazynu przy niższej cenie niż warstwy standardowa na TB. Podstawowy kosztem jest wyższa kwerendami, które należy sprawdzić, czy dla wymaganiom konkretnych aplikacji.  Aby dowiedzieć się więcej na temat zagadnienia dotyczące wydajności dla tej warstwy, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Sposób działania rozliczeń

Istnieją trzy sposoby zwiększy ona kosztów w usłudze Azure Search. W tej sekcji opisano trzy składniki rozliczeń: 

+ koszt usługi podstawowej
+ opłaty za ruch wychodzący (lub przepustowości) danych
+ Wzbogacenia sztucznej Inteligencji

### <a name="core-service-costs-fixed-and-variable"></a>Koszty usługi podstawowej (stałe i zmienne)

Za samą usługę Minimalna opłata za to pierwszy jednostka wyszukiwania (1 repliki x 1 partycji). To minimum jest ustalona okres istnienia usługi, ponieważ usługa nie można uruchomić na każdej mniejszej niż tej konfiguracji.

Poza minimum można dodać replik i partycji niezależnie. Na przykład można dodać tylko repliki lub tylko partycje. Przyrostowych wzrostu wydajności za pośrednictwem replik i partycji tworzą składnik kosztów zmiennej.

Rozliczenie jest oparte na [formuły (repliki x partycje x współczynnik)](#search-units). Szybkość, z którą są naliczane za zależy od warstwy cenowej, którą wybierzesz.

Poniższy zrzut ekranu ceny jednostkowej wskazuje bezpłatna, podstawowa i S1. (S2, S3, P1 i P2 nie są wyświetlane.) Jeśli tworzysz podstawowej usługi, koszt miesięczny będzie średnia wartość, która pojawia się dla *cen 1*. Usługi Standard, koszt miesięczny będzie średnia wartość, która pojawia się dla *cena 2*. Koszt jednostkowy zwiększyć dla każdej warstwy, ponieważ możliwości obliczeniowe, możliwości i magazynu jest większa w każdej warstwie następujących po sobie. Stawki dla usługi Azure Search są dostępne na [usługi Azure Search stronę z cennikiem](https://azure.microsoft.com/pricing/details/search/).

![Ceny jednostkowej](./media/search-sku-tier/per-unit-pricing.png "ceny na jednostkę")

Podczas szacowania kosztów w rozwiązanie do wyszukiwania, nadal należy pamiętać, że ceny i wydajności nie są liniowe. (Więcej niż podwojenie pojemności podwaja kosztów). Na przykład jak działa formuły zobacz [sposobu przydzielania replik i partycji](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Rozliczanie na podstawie jednostek wyszukiwania

Najważniejsze rozliczeń pojęciem operacji usługi Azure Search jest *jednostek wyszukiwania* (SU). Ponieważ usługa Azure Search jest zależna od replik i partycji w celu indeksowania i zapytania, nie ma sensu do rozliczania za tylko jednej z nich. Zamiast tego są naliczane złożonego obu tych elementów.

SU jest wynikiem *replik* i *partycje* używanych przez usługę: **(R x P = SU)** .

Każda usługa rozpoczyna się od jednej operacji SU (jedna replika pomnożona przez jedną partycję) jako minimum. Maksymalna liczba dla dowolnej usługi to 36 jednostek wyszukiwania. Ta wartość maksymalna jest osiągalna na wiele sposobów: replik partycji 6 x 6 lub 3 partycji x 12 repliki, na przykład. Jest to często używa się mniej niż całkowita pojemność (na przykład repliki 3, 3 partycji usługa jest rozliczana jak 9 SUs). Zobacz [kombinacji partycji i replik](search-capacity-planning.md#chart) wykres prawidłowe kombinacje.

Stawka rozliczeniowa to co godzinę na SU. Każda warstwa ma stopniowo wyższym. Wyższe warstwy pochodzi z partycjami większych i szybszej i przyczynia się to do ogólnie wyższe stawki godzinowej dla tej warstwy. Stawki dla każdej warstwy można przeglądać na [cennik](https://azure.microsoft.com/pricing/details/search/) strony.

Większość klientów Przenieś tylko część całkowitą pojemność w trybie online, zawierający pozostałe w rezerwie. Do rozliczeń, numer partycji i replik, które można przełączyć do trybu online, obliczana na podstawie SU formuły, określa, jakie opłaty są naliczane za godzinę.

### <a name="data-egress-charges-during-indexing"></a>Opłaty za wyjście danych podczas indeksowania

Za pomocą [indeksatorów usługi Azure Search](search-indexer-overview.md) mogą mieć wpływ na rozliczenia, w zależności od lokalizacji usługi. Jeśli utworzysz usługę Azure Search w tym samym regionie, w którym znajdują się dane, można wyeliminować opłaty za wyjście danych. Poniżej przedstawiono niektóre informacje z [przepustowości stronę z cennikiem](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft nie nalicza opłat wszystkie przychodzące dane do dowolnej usługi na platformie Azure lub wszelkie dane wychodzące z usługi Azure Search.

+ W przypadku rozwiązań wielofunkcyjnych nie ma opłat dla danych wykraczania poza granice sieci, w przypadku wszystkich usług w tym samym regionie.

Opłaty za dane wychodzące w przypadku usług w różnych regionach. Te opłaty nie są częścią rachunku Azure Search. Są one wymienione tu ponieważ używasz danych lub indeksatory wzbogacony sztucznej Inteligencji do pobierania danych z różnych regionów, zostanie wyświetlony kosztów zostaną uwzględnione w ogólną kwotę rachunku.

### <a name="ai-enrichments-with-cognitive-services"></a>Wzbogacenia sztucznej Inteligencji dzięki usługom Cognitive Services

Dla [wzbogacania sztucznej Inteligencji dzięki usługom Cognitive Services](cognitive-search-concept-intro.md), należy zaplanować dołączyć płatnych zasobów usług Azure Cognitive Services, w tym samym regionie, co usługi Azure Search w warstwie cenowej S0 przetwarzania zgodnie z rzeczywistym użyciem. Nie ma żadnych stałych kosztów, skojarzone z dołączania usług Cognitive Services. Płacisz tylko za przetwarzanie, których potrzebujesz.

Wyodrębniania obrazu podczas łamania dokumentów są opłaty za usługę Azure Search. Opłata jest naliczana według liczby obrazów wyodrębnione ze swoich dokumentów. Wyodrębnianie tekstu jest aktualnie wolne.

Inne wzbogacenia, takich jak przetwarzanie języka naturalnego opierają się na [wbudowanych umiejętności poznawcze](cognitive-search-predefined-skills.md) i rozliczane przy użyciu zasobu usług Cognitive Services. Opłaty są naliczane przy użyciu stawki stosowanej tak, jakby zadanie było wykonywane bezpośrednio za pomocą usług Cognitive Services. Aby uzyskać więcej informacji, zobacz [dołączenia zasobu usług Cognitive Services, za pomocą zestawu umiejętności](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Okres rozliczeniowy wyodrębniania obrazu w usłudze wyszukiwania poznawczego

Jeśli obrazy są wyodrębniane z plików w usługa cognitive search indeksowanie potoku, użytkownik zostanie naliczona opłata tej operacji na rachunku Azure Search. W [konfiguracji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** jest parametr, który wyzwala wyodrębniania obrazu. Jeśli **imageAction** jest równa "none" (domyślnie), Opłata zostanie naliczona do wyodrębnienia obrazu.

Ceny mogą ulec zmianie. Jest udokumentowany na [cennik](https://azure.microsoft.com/pricing/details/search/) strony usługi Azure Search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Okres rozliczeniowy wbudowanych umiejętności programowania w usłudze wyszukiwania poznawczego

Po skonfigurowaniu wzbogacony potok, wszelkie [wbudowanych umiejętności](cognitive-search-predefined-skills.md) używane w potoku są oparte na modeli uczenia maszynowego. Modele te są dostarczane przez usługi Cognitive Services. Jeśli używasz tych modeli podczas indeksowania opłata jest naliczana przy użyciu stawki stosowanej jako użytkownik będzie, jeśli możesz bezpośrednio żądanego zasobu.

Załóżmy na przykład istnienie potoku, który używa skanowanych plików JPEG optyczne rozpoznawanie znaków (OCR) i tekst wynikowy są przesyłane do indeksu usługi Azure Search dla kwerend wyszukiwania w dowolnej postaci. Obejmuje indeksatora z potokiem indeksowania [umiejętności optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md), i że będzie umiejętności [dołączone do zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Po uruchomieniu indeksatora, opłaty za wykonanie optyczne rozpoznawanie znaków pojawi się na rachunku poznawczych.

## <a name="tips-for-reducing-costs"></a>Porady dotyczące zmniejszania kosztów

Nie można zamknąć usługę, aby zmniejszyć rachunek. Dedykowane zasoby są zawsze operacyjne, przydzielone do wyłącznego użytku przez okres istnienia usługi. Jedynym sposobem, aby zmniejszyć rachunek jest zmniejszenie replik i partycji do poziomu, która zapewnia akceptowalny poziom wydajności i [zgodności z umową SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Jest jednym ze sposobów, aby ograniczyć koszty wyboru warstwy przy użyciu niższe stawki godzinowej. Stawki godzinowe S1 jest niższa od stawek S2 lub S3. Zakładając, że można aprowizować usługi dolnym końcem swoich projekcje obciążenia, jeśli wykraczają poza możliwości usługi, można utworzyć drugi usługi większych warstwowego, ponowną kompilację indeksów w usłudze drugiego, a następnie usuń pierwszy z nich.

Jeśli zostały wykonane planowania pojemności dla serwerów lokalnych, wiesz, że często "Kup" Aby można było obsłużyć przewidywany wzrost. Dzięki usłudze w chmurze można próbować oszczędności więcej agresywnie ponieważ one nie zablokowany określonych zakup. Zawsze możesz przełączyć do wyższej warstwy usługi Jeśli aktualny plan nie jest wystarczające.

### <a name="capacity"></a>Pojemność

W usłudze Azure Search postacią pojemności *replik* i *partycje*.

+ Repliki są wystąpieniami usługi wyszukiwania. Każda replika hostuje jedną ze zrównoważonym obciążeniem kopię indeksu. Na przykład usługi za pomocą sześciu replik zawiera sześć kopii każdego indeksu załadowane w usłudze.

+ Partycje przechowywania indeksy i automatycznie podziału danych z możliwością wyszukiwania. Dwie partycje podzielić indeksu w połowie, trzy partycje podzielić ją na trzecie i tak dalej. Pod względem wydajności *rozmiar partycji* jest głównej różnicującego funkcja między warstwami.

> [!NOTE]
> Obsługuje wszystkie warstwy standardowa i zoptymalizowane pod kątem magazynu [elastyczne kombinacji replik i partycji](search-capacity-planning.md#chart) można więc [optymalizowania systemu dla szybkości lub magazynu](search-performance-optimization.md) , zmieniając równowagi. Warstwa podstawowa oferuje maksymalnie trzech replik zapewniających wysoką dostępność, ale ma tylko jedną partycję. Warstwy bezpłatna nie zapewniają dedykowanych zasobów: przetwarzanie zasoby są współużytkowane przez wielu subskrybentów.

### <a name="more-about-service-limits"></a>Więcej informacji na temat limity usługi

Zasoby hosta, takich jak indeksy i indeksatory usług. Każda warstwa nakłada [limitów usług](search-limits-quotas-capacity.md) liczby zasobów, możesz utworzyć. Maksymalna liczba indeksów (i innych obiektów) więc Druga funkcja różnicujący między warstwami. Podczas przeglądania każdej opcji w portalu, należy pamiętać, limity liczby indeksów. Inne zasoby, takie jak indeksatorów, źródła danych i dokładniejsze, są umieszczane limity indeksu.

## <a name="consumption-patterns"></a>Wzorce użycia

Większość klientów rozpoczynać bezpłatnej usługi, które prowadzą przez czas nieokreślony, i następnie wybierz jedno z warstwy standardowa lub zoptymalizowane pod kątem magazynu, w przypadku poważnych obciążeń programowania lub produkcji.

![Usługa Azure Search warstw cenowych](./media/search-sku-tier/tiers.png "warstw cenowych w usłudze Azure Search")

Na końcach niski i wysoki Basic i wysoka gęstość S3 są ważne, ale nietypowe użycie wzorców. (Podstawowa) to w przypadku obciążeń produkcyjnych małe. Oferuje umowy SLA, dedykowanych zasobów i wysoką dostępność, ale zapewnia skromną magazynu nadmiarów na łącznie 2 GB. Ta warstwa została zaprojektowana w celu klienci, którzy stale underutilize dostępnej pojemności. Na końcu wysoka, wysoka gęstość S3 jest w przypadku obciążeń typowe dla niezależnych dostawców oprogramowania, partnerów, [rozwiązań wielodostępnych](search-modeling-multitenant-saas-applications.md), lub konfiguracji, która wymaga dużej liczby małych indeksów. Często to oczywiste, gdy jest to podstawowa lub wysoka gęstość S3 jest właściwą warstwę. Jeśli chcesz, aby potwierdzenie, możesz wysłać do [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) wskazówki.

Najczęściej używanych warstwy standardowa S1 za pośrednictwem S3, tworzą postępu z coraz większe poziomy wydajności. Istnieją punkty szczytowym na rozmiar partycji i ograniczenia dotyczące liczby indeksów, indeksatorów i następstwem zasobów:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Rozmiar partycji|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limity indeksu i indeksatora| 50 | 200 | 200 |  |  |  |  |

S1 jest wspólnego rozwiązaniem dla klientów, którzy potrzebują dedykowanych zasobów i wiele partycji. S1 oferuje partycje 25 GB i maksymalnie 12 partycjami, zapewniając za daną usługę limit wynoszący 300 GB, jeśli zmaksymalizować partycji za pośrednictwem replik. (Zobacz [przydzielić partycje i repliki](search-capacity-planning.md#chart) więcej równoważenia alokacje.)

Strony portalu i cen Przenieś fokus na rozmiar partycji i magazynu, ale dla poszczególnych warstw, wszystkie zasoby obliczeniowe możliwości (pojemności dysku, szybkości procesorów) jest ogólnie zwiększenie liniowo z ceną. S2 repliki jest szybsza niż S1, a warstwa S3 jest szybsza niż S2. Warstwy S3 przerywanie liniowy wzorzec cenach obliczeniowych za pomocą nieproporcjonalnie szybciej operacji We/Wy. Jeśli spodziewasz się operacji We/Wy jako wąskie gardło, należy pamiętać o tym, uzyskać znacznie więcej operacji We/Wy przy użyciu S3 nie możesz uzyskać dzięki niższych warstwach.

S3 oraz wysoka gęstość S3 są wspierane przez identyczne infrastruktury o dużej pojemności, ale znajdą ich maksymalny limit na różne sposoby. S3 jest przeznaczony dla mniejszej liczby indeksów bardzo duże, dlatego maksymalny limit jest powiązany z zasobów (2,4 TB dla każdej usługi). Wysoka gęstość S3 jest przeznaczona dla dużej liczby indeksów bardzo mała. Na 1000 indeksów wysoka gęstość S3 osiągnięty limit w formie ograniczenia indeksu. Jeśli potrzebujesz więcej niż 1000 indeksów jesteś klientem wysoka gęstość S3, skontaktuj się z Microsoft Support informacji na temat kontynuować.

> [!NOTE]
> Limity dokumentów zostały jest brany pod uwagę w tym samym czasie, ale nie będą one już zastosowania do nowych usług. Aby uzyskać informacji o warunkach, w których nadal mają zastosowanie limity dokumentów, zobacz [dokumentu limity](search-limits-quotas-capacity.md#document-limits).
>

Optymalizacja pod kątem warstw magazynowania, P1 i P2, są idealne dla aplikacji z wymaganiami dużych ilości danych, ale stosunkowo małej liczbie użytkowników końcowych, minimalizuje opóźnienie kwerendy nie ma najwyższy priorytet.  

|  | L1 | PAMIĘCI PODRĘCZNEJ L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Rozmiar partycji|  1 TB | 2 TB |  |  |  |  |  |
| limity indeksu i indeksatora| 10 | 10 |  |  |  |  |  |

L2 oferuje dwa razy ogólną pojemność L1.  Wybieranie warstwy oparte na maksymalną ilość danych, które uważasz, że potrzebuje Twojego indeksu. Partycje warstwy L1 skalowanie w górę wielokrotność 1 TB pojemności do maksymalnie 12 TB. Partycje L2 jest zwiększenie o 2 TB dla każdej partycji maksymalnie 24 TB.

## <a name="evaluating-capacity"></a>Ocena wydajności

Wydajność i koszty obsługi usługi są bezpośrednio powiązane. Warstwy nakłada ograniczenia na dwóch poziomach: Magazyn i zasoby. Możesz pomyśleć o obu ponieważ jednego z tych limitów osiągniesz najpierw jest obowiązywać limit.

Uwarunkowania biznesowe wymagają zwykle liczba indeksów, które będą potrzebne. Na przykład możesz potrzebować indeksu globalnego dla dużej repozytorium dokumentów. Lub możesz potrzebować więcej indeksów, zależnie od regionu, aplikacji lub niszowe biznesowych.

Aby określić rozmiar indeksu, musisz mieć [utworzenia takiego](search-create-index-portal.md). Struktura danych w usłudze Azure Search jest głównie [odwrócony indeksu](https://en.wikipedia.org/wiki/Inverted_index) struktury, która ma różne charakterystyki niż źródła danych. Odwrócony indeksu rozmiar i złożoność są określane przez zawartość, niekoniecznie według ilości danych, które kanał do niego. Źródłem dużych ilości danych dzięki nadmiarowości wysokiej może spowodować indeks mniejszych niż mniejszy zestaw danych, który zawiera zawartość bardzo zmienny. Dlatego jest rzadko można wywnioskować rozmiar indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Mimo że szacowania przyszłe potrzeby indeksów i przechowywania może sprawiać wrażenie wątpliwości, warto zrobić. Jeśli pojemność warstwy okaże się zbyt niska, musisz aprowizować nowe usługi na wyższą warstwę i następnie [ponowne ładowanie indeksów](search-howto-reindex.md). Nie istnieje żadne uaktualnienia w miejscu usługi z jedną jednostką SKU do innego.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Krok 1: Tworzenie szacunkowe przy użyciu warstwy bezpłatna

Jest jednym z podejść do oceny wydajności można uruchomić w ramach warstwy bezpłatna. Należy pamiętać, że bezpłatna usługa oferuje maksymalnie trzech indeksów, 50 MB miejsca, a 2 minuty, godziny indeksowania. Aby oszacować rozmiar indeksu przewidywane, za pomocą tych ograniczeń może stanowić wyzwanie. Poniżej przedstawiono podejście, które można wykonać:

+ [Tworzenie usługi w warstwie bezpłatna](search-create-service-portal.md).
+ Przygotuj mały, reprezentatywny zestaw danych (na przykład 5000 dokumentów i rozmiar próbki 10 procent).
+ [Tworzenie indeksu początkowego](search-create-index-portal.md) i zanotuj jego rozmiar w portalu (na przykład 30 MB).

W przypadku przykładu pełnomocnika i 10 procent całe źródło danych, indeksu 30 MB staje się średnio 300 MB, jeśli wszystkie dokumenty są indeksowane. Korzystając z tym numerem wstępnego, może być double kwota w budżecie dwa indeksy (rozwoju i produkcji). Daje w sumie 600 MB w wymagania dotyczące magazynu. To wymaganie jest spełnione przez warstwy podstawowa, łatwo, więc ma rozpocząć.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Krok 2: Tworzenie szacowania dostosowany przy użyciu płatnej warstwie

Niektórzy klienci preferują rozpoczynać dedykowane zasoby, które mogą pomieścić większych próbkowania i czasy przetwarzania i następnie twórz realistyczne oszacowania ilości indeksu, rozmiar i woluminy zapytania podczas programowania. Początkowo usługa jest aprowizowana na podstawie szacunku podjęcie. Następnie jak dojrzewa projektu tworzenia oprogramowania, zespoły zwykle wiedzą, czy istniejącej usługi jest powyżej lub poniżej pojemności dla obciążeń produkcyjnych przewidywany.

1. [Sprawdź limity usługi w każdej warstwie](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) do określenia, czy w niższych warstwach mogą obsługiwać liczba indeksów należy. W warstwach Basic, S1 i S2 indeksu limity są 15, 50 i 200, odpowiednio. Warstwa zoptymalizowane pod kątem magazynu obowiązuje limit 10 indeksów, ponieważ zostało zaprojektowane do obsługi małej liczbie bardzo dużych indeksów.

1. [Tworzenie usługi w warstwie płatnej](search-create-service-portal.md):

    + Rozpocznij niskie, Basic lub S1, jeśli na początku swoje nauki.
    + Rozpocznij wysoka, S2 lub S3 nawet, jeśli wiesz, że zamierzasz na dużą skalę obciążeń indeksowania i zapytania.
    + Rozpoczynać zoptymalizowane pod kątem magazynu, P1 lub P2, jeśli masz indeksowanie dużych ilości danych, a obciążenie zapytaniami jest stosunkowo niska, jak za pomocą wewnętrznej aplikacji biznesowej.

1. [Tworzenie indeksu początkowego](search-create-index-portal.md) ustalenie, jak tłumaczy źródło danych do indeksu. To jest jedynym sposobem, aby oszacować rozmiar indeksu.

1. [Monitorowanie magazynu, limity, wolumin zapytań i opóźnień](search-monitor-usage.md) w portalu. W portalu jest wyświetlany zapytań dla poszczególnych zapytań drugi, ograniczenia i czas reakcji wyszukiwania. Wszystkie te wartości mogą pomóc w podjęciu decyzji, jeśli wybrano właściwą warstwę. Można także skonfigurować monitorowania dokładnego wartości, takich jak analiza przeglądowe włączając [analiza ruchu wyszukiwania](search-traffic-analytics.md).

Numer indeksu i rozmiaru są równie ważne analizy. Jest to spowodowane maksymalnych limitów osiągnięcia skonfigurowanych za pomocą pełnego wykorzystania pamięci (partycji) lub maksymalnych limitów zasobów (indeksów, indeksatorów i tak dalej), osiągnięta jako pierwsza. Portal pomaga śledzić, przedstawiająca bieżące użycie i maksymalnych limitów obok siebie na stronie Przegląd.

> [!NOTE]
> Wymagania dotyczące magazynu może być zwiększony, jeśli dokumenty zawierają nadmiarowe dane. W idealnym przypadku dokumenty zawierają tylko dane, które należy środowiska wyszukiwania. Dane binarne nie można wyszukiwać i powinny być przechowywane oddzielnie (być może w usłudze Azure storage tabelą lub obiektem blob). Następnie można dodać pola w indeksie, aby pomieścić URL odwołania do danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu jest 16 MB lub mniej Jeśli jesteś zbiorcze przekazywanie wielu dokumentów w jedno żądanie. Aby uzyskać więcej informacji, zobacz [limitów w usłudze Azure Search usług](search-limits-quotas-capacity.md).
>

**Zagadnienia dotyczące wolumin zapytań**

Zapytania na sekundę (QPS) jest ważną metryką podczas dostosowywania wydajności, ale zazwyczaj jest tylko kwestia warstwy jeśli oczekujesz, zapytania wysoki woluminu na początku.

Warstwy standardowa zapewniają równoważenie replik i partycji. Można zwiększyć przetwarzania zapytania, dodając repliki dla równoważenia obciążenia lub dodawanie partycji do przetwarzania równoległego. Po aprowizacji usługi użytkownik może następnie poprawiać wydajność.

Jeśli oczekujesz, że woluminy wysokiej stałą zapytania od samego początku, należy rozważyć wyższe warstwy standardowa, wspierane przez sprzętowe bardziej wydajne. Można następnie podjąć partycje i repliki w trybie offline lub nawet przełączać do niższej warstwy usługi, jeśli nie występują te woluminy zapytania. Aby uzyskać więcej informacji na temat sposobu obliczania przepływności zapytań, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).

Zoptymalizowane pod kątem magazynu warstwy są przydatne w przypadku obciążeń dużych ilości danych, obsługa większy magazyn na indeks całkowity dostępny po wymagania dotyczące opóźnień zapytania są mniej ważne. Dodatkowe repliki należy nadal używać dla obciążenia równoważenia i dodatkowe partycje do przetwarzania równoległego. Po aprowizacji usługi użytkownik może następnie poprawiać wydajność.

**Umowy dotyczące poziomu usług**

Bezpłatne funkcje warstwy i w wersji zapoznawczej nie zapewniają [usługi i poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). W przypadku wszystkich płatnych warstw umowy SLA zaczną obowiązywać podczas aprowizowania nadmiarowości wystarczające dla Twojej usługi. Musisz mieć co najmniej dwiema replikami zapytania (odczyt) umowy SLA. Musisz mieć co najmniej trzech replik dla zapytań i indeksowania umowy SLA (odczyt zapis). Liczba partycji nie ma wpływu na umowy SLA.

## <a name="tips-for-tier-evaluation"></a>Porady dotyczące oceny warstwy

+ Dowiedz się, jak zbudować efektywne indeksy oraz metody odświeżania, które mają co najmniej wpływ. Użyj [analiza ruchu wyszukiwania](search-traffic-analytics.md) uzyskać wgląd w działania zapytania.

+ Zezwalaj na metryki zbudować wokół zapytań i zbierać dane dotyczące wzorców użycia (zapytania w godzinach, indeksowanie poza godzinami szczytu). Aby ułatwić podjęcie decyzji inicjowania obsługi usługi, należy użyć tych danych. Chociaż nie jest praktyczne w erze godzinową lub dzienną, można dynamicznie zmieniać partycji i zasobów, aby obsłużyć zaplanowanych zmian w woluminach zapytania. Również może obsłużyć nieplanowane, ale stałego zmiany, jeśli poziom przechowywania wystarczająco długi gwarantowało to podejmowania działań.

+ Należy pamiętać, że Jedyną wadą underprovisioning jest, może być konieczne zatrzymywania usługi, jeśli rzeczywiste wymagania są większe niż swoje prognozy. Aby uniknąć przerw w działaniu usługi, czy utworzyć nową usługę w tej samej subskrypcji na wyższą warstwę i uruchom go obok siebie, dopóki wszystkie żądania i aplikacje docelowe nowy punkt końcowy.

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij od warstwy bezpłatna i utworzyć indeks na potrzeby początkowego za pomocą podzestaw danych, aby poznać jego właściwości. Struktura danych w usłudze Azure Search jest struktura odwróconą indeksu. Rozmiar i złożoność odwróconą indeksu zależy od zawartości. Należy pamiętać o tym, czy zawartość wysoce nadmiarowe wydłuża indeks mniejszych niż wysoce nieregularne zawartość. Aby określić wymagania dotyczące magazynu indeksu, cechy zawartości, a nie rozmiaru zestawu danych.

Po utworzeniu początkowego oszacować rozmiar indeksu [aprowizować usługę płatnych](search-create-service-portal.md) na jednej z warstw omówionych w tym artykule: Basic, Standard lub zoptymalizowane pod kątem magazynu. Zwalnia wszelkie sztuczne ograniczenia rozmiaru danych i [odbudowanie indeksu](search-howto-reindex.md) obejmujący wszystkie dane, które mają być wyszukiwanie.

[Przydziel partycje i repliki](search-capacity-planning.md) zgodnie z potrzebami, aby korzystać z wydajności i skalowania, potrzebujesz.

Jeśli wydajność i pojemność są w dobrym stanie, wszystko będzie gotowe. W przeciwnym razie ponownie utworzyć usługę wyszukiwania na inną warstwę, pasującą więcej ściśle z Twoimi potrzebami.

> [!NOTE]
> Jeśli masz pytania, Opublikuj [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).
