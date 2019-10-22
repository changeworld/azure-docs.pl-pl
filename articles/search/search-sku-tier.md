---
title: Wybierz warstwę cenową lub jednostkę SKU dla usługi Azure Search Service — Azure Search
description: 'Azure Search można obsługiwać w tych jednostkach SKU: bezpłatna, podstawowa i standardowa, a warstwa standardowa jest dostępna w różnych konfiguracjach zasobów i poziomach pojemności.'
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 32ec97ce923c1cffd92fa6522f30abf7ea87fff7
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72331189"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Wybierz warstwę cenową dla Azure Search

Podczas tworzenia usługi Azure Search [zasób jest tworzony](search-create-service-portal.md) w warstwie cenowej (lub jednostce SKU), która została rozwiązany przez okres istnienia usługi. Warstwy obejmują bezpłatne, podstawowe, standardowe i zoptymalizowane pod kątem magazynu. Zoptymalizowane pod kątem Standard i Storage są dostępne z kilkoma konfiguracjami i pojemnościami.

Większość klientów zaczyna się od warstwy Bezpłatna, aby umożliwić jej ocenę usługi. Po zakończeniu oceny często można utworzyć drugą usługę w jednej z wyższych warstw na potrzeby wdrożeń deweloperskich i produkcyjnych.

Mimo że wszystkie warstwy, w tym warstwa Bezpłatna, zazwyczaj parzystość funkcji oferty, większe obciążenia mogą dyktować potrzebę wyższych warstw. Na przykład [wzbogacanie AI z Cognitive Services](cognitive-search-concept-intro.md) ma długotrwałe umiejętności, które przekraczają limit czasu dla bezpłatnej usługi, chyba że zestaw danych jest mały.

> [!NOTE] 
> Wyjątkiem od funkcji parzystości jest [indeksatory](search-indexer-overview.md), które nie są dostępne w systemie S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Dostępne warstwy

Warstwy odzwierciedlają charakterystykę sprzętu obsługującego usługę (a nie funkcje) i są zróżnicowane przez:

+ Liczba indeksów i indeksatorów, które można utworzyć
+ Rozmiar i szybkość partycji (magazyn fizyczny)

Wybrana warstwa określa stawkę naliczaną. Poniższy zrzut ekranu z Azure Portal zawiera dostępne warstwy, minus ceny (które można znaleźć w portalu i na [stronie cennika](https://azure.microsoft.com/pricing/details/search/)). Warstwy **bezpłatna**, **podstawowa**i **standardowa** są najczęściej spotykane.

**Bezpłatna** tworzy ograniczoną usługę wyszukiwania w klastrze, udostępnianą innym subskrybentom. Możesz wykonywać małe projekty, w tym Przewodniki Szybki Start i samouczki, ale nie można skalować usługi ani uruchamiać znaczących obciążeń. **Podstawowa** i **standardowa** są najczęściej używanymi warstwami do obciążania, a **standardem** domyślnym.

![Warstwy cenowe Azure Search](media/search-sku-tier/tiers.png "Warstwy cenowe Azure Search")

Niektóre warstwy są zoptymalizowane pod kątem niektórych typów pracy. Na przykład **standardowa 3 wysoka gęstość (S3 HD)** jest *trybem hostingu* dla S3, gdzie podstawowy sprzęt jest zoptymalizowany pod kątem dużej liczby mniejszych indeksów i jest przeznaczony dla scenariuszy wielodostępnych. Funkcja S3 HD ma taką samą opłatą za jednostkę jak S3, ale sprzęt jest zoptymalizowany pod kątem szybkiego odczytu plików na dużej liczbie mniejszych indeksów.

Warstwy **zoptymalizowane pod kątem magazynu** oferują większą pojemność magazynu przy niższej cenie za TB niż warstwy standardowe. Najważniejszym kompromisem jest wyższy poziom opóźnienia zapytań, który należy zweryfikować w celu spełnienia określonych wymagań aplikacji.  Aby dowiedzieć się więcej na temat zagadnień dotyczących wydajności tej warstwy, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

Więcej informacji o różnych warstwach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/search/), w [granicach usługi w Azure Search](search-limits-quotas-capacity.md) artykule oraz na stronie portalu podczas aprowizacji usługi.

## <a name="billable-events"></a>Zdarzenia do rozliczenia

Rozwiązanie oparte na Azure Search może ponosić koszty w następujący sposób:

+ Podstawowa koszt usługi w ramach minimalnej konfiguracji (Tworzenie usługi)
+ Koszt przyrostowy podczas skalowania w górę (Dodaj repliki lub partycje)
+ Opłaty za przepustowość (wychodzący transfer danych) 
+ Wyszukiwanie poznawcze (Dołącz Cognitive Services do wzbogacania AI, Azure Storage for Knowledge Store)

### <a name="service-costs"></a>Koszty usług

W przeciwieństwie do maszyn wirtualnych lub innych zasobów, które mogą być "wstrzymane" aby uniknąć opłat, usługa Azure Search jest zawsze dostępna na sprzęcie dedykowanym do wyłącznego użytku. W związku z tym tworzenie usługi jest zdarzeniem rozliczanym rozpoczynającym się podczas tworzenia usługi i kończącym się po usunięciu usługi. 

Opłata minimalna to pierwsza jednostka wyszukiwania (jedna replika x jedna partycja) w stawce płatnej. Ta wartość minimalna jest ustalana w okresie istnienia usługi, ponieważ usługa nie może być uruchomiona na żadnym serwerze niższym niż ta konfiguracja. Poza minimalnym można dodawać repliki i partycje niezależnie od siebie. Przyrostowe zwiększenie wydajności za pośrednictwem replik i partycji spowoduje zwiększenie rachunku na podstawie następującej formuły: [(repliki x partycje x)](#search-units), gdzie opłata naliczana zależy od wybranej warstwy cenowej.

W przypadku szacowania kosztów rozwiązania wyszukiwania należy pamiętać, że ceny i pojemność nie są liniowe. (Podwojenie pojemności więcej niż podwaja koszt). Aby zapoznać się z przykładem sposobu działania formuły, zobacz [Jak przydzielić repliki i partycje](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Opłaty za przepustowość

Użycie [indeksatorów Azure Search](search-indexer-overview.md) może wpłynąć na rozliczenia w zależności od lokalizacji usług. Opłaty za wychodzące dane można wyeliminować całkowicie, jeśli utworzysz usługę Azure Search w tym samym regionie, w którym zawarto dane. Poniżej przedstawiono niektóre informacje na [stronie cennika dotyczące przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Firma Microsoft nie nalicza opłat za żadne dane przychodzące do żadnej usługi na platformie Azure ani do żadnych danych wychodzących z Azure Search.
+ W rozwiązaniach z obsługą wielousług nie jest naliczana opłata za dane przecinające sieć, gdy wszystkie usługi znajdują się w tym samym regionie.

Opłaty są naliczane za dane wychodzące, jeśli usługi znajdują się w różnych regionach. Opłaty te nie są faktycznie częścią Azure Search rachunku. Są one wymienione w tym miejscu, ponieważ w przypadku korzystania z danych lub indeksatorów wzbogaconych przez program AI do ściągania danych z różnych regionów widoczne są koszty związane z ogólnym rozliczeniami.

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Programowe przeszukiwanie i wzbogacanie plików AI z Cognitive Services

W przypadku [wzbogacania AI z Cognitive Services](cognitive-search-concept-intro.md)należy zaplanować [dołączenie do rozliczanego zasobu Cognitive Services platformy Azure](cognitive-search-attach-cognitive-services.md)w tym samym regionie co Azure Search, w warstwie cenowej S0 na potrzeby przetwarzania płatności zgodnie z rzeczywistym użyciem. Nie ma stałego kosztu związanego z dołączaniem Cognitive Services. Płacisz tylko za potrzebne przetwarzanie.

| Operacja | Wpływ rozliczeń |
|-----------|----------------|
| Łamanie dokumentów, Wyodrębnianie tekstu | Bezpłatnie |
| Łamanie dokumentów, wyodrębnianie obrazów | Opłaty są naliczane zgodnie z liczbą obrazów wyodrębnionych z dokumentów. W [konfiguracji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** jest parametrem, który wyzwala wyodrębnianie obrazów. Jeśli **imageAction** jest ustawiona na wartość "none" (domyślnie), nie zostanie naliczona opłata za Wyodrębnianie obrazu. Szybkość wyodrębniania obrazu jest udokumentowana na stronie [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/) Azure Search.|
| [Wstępnie utworzone umiejętności poznawcze](cognitive-search-predefined-skills.md) | Opłaty są naliczane według tej samej stawki, co w przypadku, gdy zadanie zostało wykonane przy użyciu Cognitive Services bezpośrednio. |
| Umiejętności niestandardowe | Niestandardowa umiejętność zapewnia funkcjonalność. Koszt użycia niestandardowej umiejętności zależy wyłącznie od tego, czy kod niestandardowy wywołuje inne usługi taryfowe. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formuła rozliczeń (R x P = SU)

Najważniejszym koncepcją rozliczeniową do zrozumienia dla operacji Azure Search jest *Jednostka wyszukiwania* (Su). Ponieważ Azure Search zależy od obu replik i partycji na potrzeby indeksowania i zapytań, nie ma sensu rozliczania tylko jednego lub drugiego. Zamiast tego rozliczenia opierają się na złożonym z nich.

SU to iloczyn *replik* i *partycji* używanych przez usługę: **(R x P = Su)** .

Każda usługa rozpoczyna się od jednego SU (jedna replika pomnożona przez jedną partycję) jako minimum. Wartość maksymalna dla każdej usługi to 36 usług SUs. Tę wartość maksymalną można osiągnąć na wiele sposobów: 6 partycji x 6 replik lub 3 partycji x 12 replik, na przykład. Jest to typowe użycie mniejsze niż całkowita pojemność (na przykład 3-Replica, 3-podzielone usługi są rozliczane jako 9 usług SUs). Zobacz wykres [kombinacji partycji i repliki](search-capacity-planning.md#chart) dla prawidłowych kombinacji.

Stawka rozliczeniowa jest naliczana co godzinę za pomocą funkcji SU. Każda warstwa ma stopniowo wyższą stawkę. Wyższe warstwy są dostarczane z większymi i speediermi partycjami, a to wpływa na ogólną wyższą stawkę godzinową dla tej warstwy. Stawki dla każdej warstwy można wyświetlić na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) .

Większość klientów uzyskuje zaledwie część całkowitej pojemności online, utrzymując pozostałe w rezerwie. W przypadku rozliczeń liczba partycji i replik przetworzonych w trybie online, obliczone przez formułę SU, decyduje o godzinie płatności.

## <a name="how-to-manage-and-reduce-costs"></a>Jak zarządzać kosztami i je obniżyć

Oprócz następujących sugestii zapoznaj się z tematem [rozliczeń i zarządzania kosztami](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Utwórz wszystkie zasoby w tym samym regionie lub w możliwie najmniejszej liczbie regionów, aby zminimalizować lub wyeliminować opłaty za przepustowość.

- Konsoliduj wszystkie usługi do jednej grupy zasobów, takiej jak Azure Search, Cognitive Services i inne usługi platformy Azure używane w rozwiązaniu. W Azure Portal Znajdź grupę zasobów i Użyj poleceń **Cost Management** , aby uzyskać wgląd w rzeczywiste i przewidywane wydatki.

- Rozważ użycie aplikacji sieci Web platformy Azure dla aplikacji frontonu, aby żądania i odpowiedzi były przechowywane w ramach granicy centrum danych.

- Skalowanie w górę dla operacji intensywnie korzystających z zasobów, takich jak indeksowanie, a następnie korygowanie w dół na potrzeby zwykłych obciążeń zapytań. Rozpocznij od minimalnej konfiguracji Azure Search (jeden element SU składający się z jednej partycji i jednej repliki), a następnie Monitoruj aktywność użytkowników, aby identyfikować wzorce użycia, które wskazują potrzebę większej pojemności. Jeśli istnieje przewidywalny wzorzec, można synchronizować skalę z aktywnością (należy napisać kod w celu zautomatyzowania tego).

Nie można wyłączyć usługi wyszukiwania, aby zmniejszyć rozliczenia. Dedykowane zasoby są zawsze operacyjne, przydzielane do wyłącznego użytku przez okres istnienia usługi. W odniesieniu do samej usługi jedynym sposobem obniżenia poziomu rachunku jest zredukowanie replik i partycji na poziom, który nadal zapewnia akceptowalną wydajność i zgodność z umową [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), lub tworzenie usługi w niższej warstwie (S1 stawki godzinowe są niższe niż w przypadku stawek S2 i S3). Przy założeniu, że usługa zostanie zainicjowana na niższym końcu projekcji obciążenia, w przypadku skalowalność usługi można utworzyć drugą usługę o większej warstwie, ponownie skompilować indeksy w drugiej usłudze, a następnie usunąć pierwszy z nich.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak oszacować wymagania dotyczące pojemności

W Azure Search pojemność jest uporządkowana jako *repliki* i *partycje*.

+ Repliki to wystąpienia usługi wyszukiwania. Każda replika obsługuje jedną kopię indeksu o zrównoważonym obciążeniu. Na przykład usługa mająca sześć replik ma sześć kopii każdego indeksu załadowanego w usłudze.

+ Partycje przechowują indeksy i automatycznie dzielą dane z możliwością wyszukiwania. Dwie partycje dzielą indeks na pół, trzy partycje dzielą go na trzecie i tak dalej. W odniesieniu do pojemności *rozmiar partycji* to podstawowa funkcja rozróżniania między warstwami.

> [!NOTE]
> Wszystkie warstwy zoptymalizowane pod kątem standardowej i magazynu obsługują [elastyczne kombinacje replik i partycji](search-capacity-planning.md#chart) , dzięki czemu można [zoptymalizować system pod kątem szybkości lub magazynu](search-performance-optimization.md) , zmieniając saldo. Warstwa Podstawowa oferuje maksymalnie trzy repliki w celu zapewnienia wysokiej dostępności, ale ma tylko jedną partycję. Warstwy bezpłatne nie zapewniają dedykowanych zasobów: zasoby obliczeniowe są współużytkowane przez wielu subskrybentów.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Ocena wydajności

Pojemność i koszty uruchomienia usługi są dostępne. Warstwy nakładają limity na dwa poziomy: Magazyn i zasoby. Należy zastanowić się, że w zależności od tego, który limit dociera do pierwszego osiągnięcia, obowiązuje limit.

Wymagania biznesowe zwykle określają liczbę indeksów, które będą potrzebne. Na przykład może być potrzebny indeks globalny dla dużego repozytorium dokumentów. Lub może być potrzebne wiele indeksów opartych na regionie, aplikacji lub w trakcie pracy z firmą.

Aby określić rozmiar indeksu, należy go [skompilować](search-create-index-portal.md). Struktura danych w Azure Search jest przede wszystkim [odwróconą](https://en.wikipedia.org/wiki/Inverted_index) strukturą indeksu, która ma inne cechy niż dane źródłowe. W przypadku odwróconego indeksu rozmiar i złożoność są określane przez zawartość, a nie niekoniecznie ilość danych, które są do niego strumieniowo. Duże źródło danych o wysokiej nadmiarowości może spowodować zmniejszenie indeksu niż mniejszy zestaw danych, który zawiera wysoce zmienną zawartość. Jest to rzadko możliwe do wywnioskowania rozmiaru indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Chociaż oszacowanie przyszłych potrzeb dotyczących indeksów i magazynu może wyglądać podobnie jak wątpliwości, warto wykonać te czynności. Jeśli pojemność warstwy wyzostanie zbyt niska, należy udostępnić nową usługę w wyższej warstwie, a następnie [ponownie załadować indeksy](search-howto-reindex.md). Nie istnieje uaktualnienie w miejscu usługi z jednej jednostki SKU do innej.
>

### <a name="estimate-with-the-free-tier"></a>Szacowanie przy użyciu warstwy Bezpłatna

Jednym z metod oszacowania wydajności jest rozpoczęcie od warstwy Bezpłatna. Należy pamiętać, że bezpłatna usługa oferuje maksymalnie trzy indeksy, 50 MB miejsca do magazynowania i 2 minuty czasu indeksowania. Oszacowanie przewidywanego rozmiaru indeksu z tymi ograniczeniami może być trudne, ale te czynności są następujące:

+ [Utwórz bezpłatną usługę](search-create-service-portal.md).
+ Przygotuj mały reprezentatywny zestaw danych.
+ [Utwórz początkowy indeks w portalu](search-create-index-portal.md) i zanotuj jego rozmiar. Funkcje i atrybuty mają wpływ na magazyn. Na przykład dodawanie sugestii (typeahead) zwiększa wymagania dotyczące magazynu. Korzystając z tego samego zestawu danych, można spróbować utworzyć wiele wersji indeksu z różnymi atrybutami każdego pola, aby zobaczyć, jak różnią się wymagania dotyczące magazynu. Aby uzyskać więcej informacji, zobacz ["konsekwencje dotyczące magazynu" w temacie Tworzenie podstawowego indeksu](search-what-is-an-index.md#storage-implications).

Dzięki przybliżonemu szacunkowi możesz dwukrotnie określić wartość budżetu dla dwóch indeksów (rozwój i produkcja), a następnie wybrać odpowiednią warstwę.

### <a name="estimate-with-a-billable-tier"></a>Szacowanie przy użyciu warstwy rozliczeniowej

Dedykowane zasoby mogą obsługiwać większe próbkowanie i czasy przetwarzania dla bardziej realistycznych szacunków liczby indeksów, rozmiaru i woluminów zapytań podczas opracowywania. Niektórzy klienci przechodźą w prawo przy użyciu warstwy rozliczeniowej, a następnie ponownie ocenianej jako dojrzały projekt programistyczny.

1. [Przejrzyj limity usługi w każdej warstwie](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) , aby określić, czy niższe warstwy mogą obsługiwać wymaganą liczbę indeksów. W warstwach Podstawowa, S1 i S2 limity indeksu są odpowiednio 15, 50 i 200. Warstwa zoptymalizowana pod kątem magazynu ma limit 10 indeksów, ponieważ jest ona zaprojektowana do obsługi niskiej liczby bardzo dużych indeksów.

1. [Utwórz usługę w warstwie rozliczanej](search-create-service-portal.md):

    + Jeśli nie masz pewności co do planowanego obciążenia, Zacznij od warstwy Podstawowa lub S1.
    + Zacznij od o godzinie S2 lub nawet S3, Jeśli wiesz, że chcesz mieć indeksowanie dużej skali i ładowanie zapytań.
    + Zacznij od magazynu zoptymalizowanego pod kątem technologii L1 lub L2, jeśli indeksowanie dużej ilości danych jest stosunkowo niskie, podobnie jak w przypadku wewnętrznej aplikacji biznesowej.

1. [Utwórz początkowy indeks](search-create-index-portal.md) , aby określić sposób, w jaki dane źródłowe są tłumaczone na indeks. Jest to jedyny sposób oszacowania rozmiaru indeksu.

1. [Monitoruj magazyn, limity usług, woluminy zapytań i opóźnienia](search-monitor-usage.md) w portalu. W portalu są wyświetlane zapytania na sekundę, ograniczone zapytania i opóźnienie wyszukiwania. Wszystkie te wartości mogą pomóc zdecydować, czy wybrano odpowiednią warstwę. 

Liczba i rozmiar indeksu są równie ważne dla analizy. Wynika to z faktu, że maksymalne limity są osiągane za pomocą pełnego wykorzystania magazynu (partycji) lub maksymalnych limitów zasobów (indeksów, indeksatorów itd.), w zależności od tego, co nastąpi wcześniej. Portal pomaga śledzić oba elementy, pokazując bieżące użycie i maksymalne limity, obok strony przegląd.

> [!NOTE]
> Wymagania dotyczące magazynu mogą być naliczane, jeśli dokumenty zawierają dane nadmiarowe. W idealnym przypadku dokumenty zawierają tylko te dane, które są potrzebne do obsługi wyszukiwania. Dane binarne nie mogą być wyszukiwane i powinny być przechowywane osobno (mogą znajdować się w tabeli lub w magazynie obiektów blob platformy Azure). W indeksie należy dodać pole do przechowywania odwołania do danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu wynosi 16 MB (lub mniej, jeśli są przekazywane zbiorczo wiele dokumentów w jednym żądaniu). Aby uzyskać więcej informacji, zobacz [limity usługi w Azure Search](search-limits-quotas-capacity.md).
>

**Zagadnienia dotyczące woluminów zapytań**

Zapytania na sekundę (zapytań) to istotna Metryka w czasie dostrajania wydajności, ale ogólnie można wziąć pod uwagę tylko warstwę, jeśli na początku oczekuje się dużej ilości zapytań.

Warstwy standardowe mogą zapewnić równowagę między replikami i partycjami. Można zwiększyć szybkością oferowaną zapytania, dodając repliki do równoważenia obciążenia lub dodając partycje do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

Jeśli od samego początku oczekujesz dużej liczby trwałych woluminów zapytań, należy rozważyć wyższe warstwy standardowe. Następnie można przełączyć partycje i repliki w tryb offline, a nawet przełączać się do usługi niższej warstwy, jeśli te woluminy zapytań nie wystąpią. Aby uzyskać więcej informacji na temat obliczania przepływności zapytań, zobacz [Azure Search wydajność i optymalizacja](search-performance-optimization.md).

Warstwy zoptymalizowane pod kątem magazynu są przydatne w przypadku obciążeń dużych ilości danych, dzięki czemu można obsługiwać więcej ogólnego magazynu indeksów, gdy wymagania dotyczące opóźnień zapytań są mniej ważne. Nadal należy używać dodatkowych replik do równoważenia obciążenia i dodatkowych partycji do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

**Umowy dotyczące poziomu usług**

Funkcje warstwy Bezpłatna i wersja zapoznawcza nie zapewniają [umów dotyczących poziomu usług (umowy SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). W przypadku wszystkich warstw rozliczanych umowy SLA zacznie obowiązywać po wprowadzeniu wystarczającej nadmiarowości dla usługi. Musisz mieć co najmniej dwie repliki dla zapytania (Read) umowy SLA. Musisz mieć trzy lub więcej replik na potrzeby zapytań i indeksowania (do odczytu i zapisu) umowy SLA. Liczba partycji nie ma wpływu na umowy SLA.

## <a name="tips-for-tier-evaluation"></a>Wskazówki dotyczące oceny warstwy

+ Zezwalaj na metryki do kompilowania zapytań i Zbieraj dane dotyczące wzorców użycia (zapytania w godzinach pracy, indeksowanie w godzinach poza szczytem). Te dane służą do informowania o decyzjach o aprowizacji usług. Chociaż nie jest to praktyczne ani codzienne erze, można dynamicznie dostosować partycje i zasoby, aby uwzględnić planowane zmiany w woluminach zapytań. Możesz również uwzględnić niezaplanowane, ale nieplanowane zmiany, jeśli poziomy są wystarczająco długie, aby zagwarantować wykonanie akcji.

+ Należy pamiętać, że jedynym minusemem niepotrzebnej do obsłużenia jest konieczność odrywania usługi, jeśli rzeczywiste wymagania są większe niż przewidywane przewidywania. Aby uniknąć przerw w działaniu usługi, należy utworzyć nową usługę w tej samej subskrypcji w wyższej warstwie i uruchamiać ją równolegle do momentu, aż wszystkie aplikacje i żądania będą kierowane do nowego punktu końcowego.

## <a name="next-steps"></a>Następne kroki

Zacznij od warstwy Bezpłatna i skompiluj początkowy indeks przy użyciu podzestawu danych, aby zrozumieć jego cechy. Struktura danych w Azure Search jest odwrotną strukturą indeksu. Rozmiar i złożoność odwróconego indeksu jest określana przez zawartość. Należy pamiętać, że wysoce nadmiarowa zawartość jest wynikiem mniejszych indeksów niż wysoce nieregularna zawartość. Dlatego charakterystyki zawartości, a nie rozmiar zestawu danych określają wymagania dotyczące magazynu indeksu.

Po wstępnym oszacowaniu rozmiaru indeksu [zainicjuj usługę rozliczaną](search-create-service-portal.md) w jednej z warstw omówionych w tym artykule: wersja podstawowa, standardowa i zoptymalizowana pod kątem magazynu. Osłabij wszystkie sztuczne ograniczenia dotyczące ustalania rozmiarów danych i [ponownie skompiluj indeks](search-howto-reindex.md) , aby uwzględnić wszystkie dane, które mają być przeszukiwane.

[Przydziel partycje i repliki](search-capacity-planning.md) w miarę potrzeb, aby uzyskać wymaganą wydajność i skalowanie.

Jeśli wydajność i pojemność są dobrane, wszystko jest gotowe. W przeciwnym razie Utwórz ponownie usługę wyszukiwania w innej warstwie, która jest bardziej dokładnie wyrównana do Twoich potrzeb.

> [!NOTE]
> Jeśli masz pytania, Opublikuj je w usłudze [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).
