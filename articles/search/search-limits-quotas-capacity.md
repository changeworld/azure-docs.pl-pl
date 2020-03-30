---
title: Limity usług dla warstw i skus
titleSuffix: Azure Cognitive Search
description: Limity usług używane do planowania pojemności i maksymalne limity żądań i odpowiedzi dla usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282980"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity usługi Azure Cognitive Search

Maksymalne limity dotyczące magazynu, obciążeń i ilości indeksów i innych obiektów zależą od tego, czy [aprowizujesz usługę Azure Cognitive Search](search-create-service-portal.md) w warstwach **cenowych Bezpłatna,** **Podstawowa,** **Standardowa**czy **Zoptymalizowana pod kątem magazynu.**

+ **Bezpłatna** to usługa współdzielona z wieloma dzierżawkami, która jest dostępna w ramach subskrypcji platformy Azure. Indeksowanie i żądania zapytań są wykonywane na repliki i partycje, które są używane przez innych dzierżawców.

+ **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę, ale dzieli część infrastruktury sieciowej z innymi dzierżawcami.

+ **Standard** działa na dedykowanych maszynach o większej pojemności magazynowej i przetwórczej na każdym poziomie. Standard jest dostępny na czterech poziomach: S1, S2, S3 i S3 HD.

+ **Zoptymalizowana pamięć masowa** działa na dedykowanych komputerach z większą całkowitą pojemnością, przepustowością pamięci masowej i **pamięcią**niż standard . Zoptymalizowana pamięć masowa jest dostępna na dwóch poziomach: L1 i L2

> [!NOTE]
> Od 1 lipca wszystkie warstwy są ogólnie dostępne, w tym warstwa Zoptymalizowana do magazynowania. Wszystkie ceny można znaleźć na stronie [Szczegóły cen.](https://azure.microsoft.com/pricing/details/search/)

  S3 High Density (S3 HD) jest przeznaczony do określonych obciążeń: [multi-najem](search-modeling-multitenant-saas-applications.md) i dużych ilości małych indeksów (jeden milion dokumentów na indeks, trzy tysiące indeksów na usługę). Ta warstwa nie zapewnia [funkcji indeksatora](search-indexer-overview.md). W S3 HD pozyskiwanie danych musi korzystać z metody wypychania, używając wywołań interfejsu API do wypychania danych ze źródła do indeksu. 

> [!NOTE]
> Usługa jest aprowizowana w określonej warstwie. Przeskakiwanie poziomów w celu uzyskania pojemności obejmuje inicjowanie obsługi administracyjnej nowej usługi (nie ma uaktualnienia w miejscu). Aby uzyskać więcej informacji, zobacz [Wybieranie jednostki SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej na temat dostosowywania pojemności w usłudze, która została już zainicjowana, zobacz [Skalowanie poziomów zasobów dla obciążeń zapytań i indeksowania](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity magazynowania
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksów

| Zasób | Bezpłatna | Podstawa&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 (właso. | L2 (l2) |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |10 |10 |
| Maksymalna liczba prostych pól na indeks |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maksymalna liczba złożonych pól zbierania na indeks |40 |40 |40 |40 |40 |40 |40 |40 |
| Maksymalna liczba elementów we&nbsp;wszystkich złożonych kolekcjach na dokument<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maksymalna głębokość złożonych pól |10 |10 |10 |10 |10 |10 |10 |10 |
| Maksymalna [liczba sugestów](https://docs.microsoft.com/rest/api/searchservice/suggesters) na indeks |1 |1 |1 |1 |1 |1 |1 |1 |
| Maksymalne [profile punktacji](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na indeks |100 |100 |100 |100 |100 |100 |100 |100 |
| Maksymalna liczba funkcji na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Podstawowe usługi utworzone przed grudniem 2017 r. mają niższe limity (5 zamiast 15) indeksów. Warstwa podstawowa jest jedyną jednostką SKU z dolnym limitem 100 pól na indeks.

<sup>2</sup> Posiadanie bardzo dużej liczby elementów w złożonych kolekcjach na dokument powoduje obecnie wysokie wykorzystanie magazynu. Jest to znany problem. W międzyczasie limit 3000 jest bezpieczną górną granicą dla wszystkich warstw usług. Ten limit jest wymuszany tylko dla operacji indeksowania, które wykorzystują najwcześniejszą ogólnie`2019-05-06`dostępną wersję interfejsu API (GA), która obsługuje złożone pola typów ( ) i począwszy od. Aby nie przerywać klientów, którzy mogą używać wcześniejszych wersji interfejsu API w wersji zapoznawczej (które obsługują złożone pola typu), nie będziemy wymuszać tego limitu dla operacji indeksowania, które używają tych wersji interfejsu API w wersji zapoznawczej. Należy zauważyć, że wersje interfejsu API w wersji zapoznawczej nie są przeznaczone do użycia w scenariuszach produkcyjnych i zdecydowanie zalecamy klientom przejście do najnowszej wersji interfejsu API ga.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity dokumentów 

Od października 2018 r. nie ma już żadnych limitów dokumentów dla każdej nowej usługi utworzonej w dowolnej warstwie podlegania rozliczania (Podstawowa, S1, S2, S3, S3 HD) w dowolnym regionie. Chociaż od listopada/grudnia 2017 r. w większości regionów liczono nieograniczoną liczbę dokumentów, kilka regionów nadal nakładało limity dokumentów po tej dacie. W zależności od tego, kiedy i gdzie utworzono usługę wyszukiwania, może być uruchomiona usługa, która nadal podlega limitom dokumentów.

Aby ustalić, czy usługa ma limity dokumentów, użyj [interfejsu API REST statystyki usługi GET](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Limity dokumentów są odzwierciedlane `null` w odpowiedzi, ze wskazaniem żadnych limitów.

> [!NOTE]
> Mimo że nie ma żadnych limitów określonych dokumentów jednostki SKU, każdy indeks nadal podlega maksymalnemu bezpiecznej granicy, aby zapewnić stabilność usługi. Limit ten pochodzi z Lucene. Każdy dokument usługi Azure Cognitive Search jest wewnętrznie indeksowany jako jeden lub więcej dokumentów Lucene. Liczba dokumentów lucene na dokument wyszukiwania zależy od całkowitej liczby elementów w złożonych polach kolekcji. Każdy element jest indeksowany jako oddzielny dokument Lucene. Na przykład dokument z 3 elementami w złożonym polu kolekcji, zostanie zindeksowany jako 4 dokumenty Lucene - 1 dla samego dokumentu i 3 dla elementów. Maksymalna liczba dokumentów Lucene wynosi około 25 miliardów na indeks.

### <a name="regions-previously-having-document-limits"></a>Regiony, w które wcześniej obowiązują limity dokumentów

Jeśli portal wskazuje limit dokumentów, usługa została utworzona przed końcem 2017 r. lub została utworzona w centrum danych przy użyciu klastrów o niższej pojemności do obsługi usług Azure Cognitive Search:

+ Australia Wschodnia
+ Azja Wschodnia
+ Indie Środkowe
+ Japonia Zachodnia
+ Zachodnio-środkowe stany USA

W przypadku usług podlegających limitom dokumentów obowiązują następujące maksymalne limity:

|  Bezpłatna | Podstawowa (Basic) | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1&nbsp;milion |15 mln na partycję lub 180 mln na usługę |60 mln na partycję lub 720 mln na usługę |120 mln na partycję lub 1,4 mld na usługę |1 mln na indeks lub 200 mln na partycję |

Jeśli usługa ma limity, które Cię blokują, utwórz nową usługę, a następnie ponownie opublikuj całą zawartość do tej usługi. Nie ma mechanizmu bezproblemowego ponownego obsługi administracyjnej usługi na nowym sprzęcie za kulisami.

> [!Note] 
> W przypadku usług O wysokiej gęstości S3 utworzonych po koniec 2017 r. usunięto 200 milionów dokumentów na partycję, ale pozostaje limit 1 miliona dokumentów na indeks.


### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentu na wywołanie interfejsu API

Maksymalny rozmiar dokumentu podczas wywoływania interfejsu API indeksu wynosi około 16 megabajtów.

Rozmiar dokumentu jest faktycznie limit na rozmiar treści żądania interfejsu API indeksu. Ponieważ można przekazać partię wielu dokumentów do interfejsu API indeksu naraz, limit rozmiaru realistycznie zależy od liczby dokumentów w partii. W przypadku partii z pojedynczym dokumentem maksymalny rozmiar dokumentu wynosi 16 MB JSON.

Aby zachować rozmiar dokumentu w dół, należy pamiętać, aby wykluczyć nierozpytywalne dane z żądania. Obrazy i inne dane binarne nie są bezpośrednio queryable i nie powinny być przechowywane w indeksie. Aby zintegrować dane nierozpytowe z wynikami wyszukiwania, zdefiniuj pole niesie zerkalne, które przechowuje odwołanie do adresu URL zasobu.

## <a name="indexer-limits"></a>Limity indeksatora

Maksymalny czas pracy istnieje, aby zapewnić równowagę i stabilność usługi jako całości, ale większe zestawy danych może wymagać więcej czasu indeksowania niż maksymalna pozwala. Jeśli zadanie indeksowania nie może zakończyć się w maksymalnym dozwolonym czasie, spróbuj uruchomić go zgodnie z harmonogramem. Harmonogram śledzi stan indeksowania. Jeśli zaplanowane zadanie indeksowania zostanie przerwane z jakiegokolwiek powodu, indeksator może podnieść miejsce, w którym ostatnio zostało przerwane przy następnym zaplanowanym uruchomieniu.


| Zasób | Za&nbsp;darmo<sup>1</sup> | Podstawowe&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;&nbsp;HD<sup>3</sup>|L1 (właso. |L2 (l2) |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalne zestawy umiejętności <sup>4</sup> |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalne obciążenie indeksowania na wywołanie |10 000 dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Nie dotyczy |Bez ograniczeń |Bez ograniczeń |
| Minimalny harmonogram | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maksymalny czas pracy <sup>5</sup> | 1-3 minuty |24 godziny |24 godziny |24 godziny |24 godziny |Nie dotyczy  |24 godziny |24 godziny |
| Maksymalny czas pracy dla umiejętności wyszukiwania poznawczego lub indeksowania obiektów blob z analizą obrazu <sup>5</sup> | 3-10 minut |2 godziny |2 godziny |2 godziny |2 godziny |Nie dotyczy  |2 godziny |2 godziny |
| Indeksator obiektów blob: maksymalny rozmiar obiektu blob, MB |16 |16 |128 |256 |256 |Nie dotyczy  |256 |256 |
| Indeksator obiektów blob: maksymalna liczba znaków zawartości wyodrębniona z obiektu blob |32,000 |64,000 |4&nbsp;miliony |8&nbsp;milionów |16&nbsp;milionów |Nie dotyczy |4&nbsp;miliony |4&nbsp;miliony |

<sup>1</sup> Bezpłatne usługi mają maksymalny czas wykonywania indeksatora 3 minuty dla źródeł obiektów blob i 1 minutę dla wszystkich innych źródeł danych. W przypadku indeksowania AI, który wywołuje usługi Cognitive Services, bezpłatne usługi są ograniczone do 20 bezpłatnych transakcji dziennie, gdzie transakcja jest zdefiniowana jako dokument, który pomyślnie przechodzi przez potok wzbogacania.

<sup>2</sup> Podstawowe usługi utworzone przed grudniem 2017 r. mają niższe limity (5 zamiast 15) indeksatorów, źródeł danych i skillsets.

<sup>3</sup> Usługi S3 HD nie obejmują obsługi indeksatora.

<sup>4</sup> Maksymalnie 30 umiejętności na zestaw umiejętności.

<sup>5</sup> Obciążeń wyszukiwania poznawczego i analizy obrazu w indeksowaniu obiektów blob platformy Azure mają krótszy czas pracy niż regularne indeksowanie tekstu. Analiza obrazu i przetwarzanie języka naturalnego są intensywne pod względem obliczeniowym i zużywają nieproporcjonalną ilość dostępnej mocy obliczeniowej. Czas pracy został skrócony, aby dać innym zadaniem w kolejce możliwość uruchomienia.  

> [!NOTE]
> Jak podano w [limitach indeksu,](#index-limits)indeksatory będą również wymuszać górny limit 3000 elementów we`2019-05-06`wszystkich złożonych kolekcjach na dokument, począwszy od najnowszej wersji interfejsu API GA, która obsługuje złożone typy ( ) począwszy od. Oznacza to, że jeśli utworzono indeksator z wcześniejszą wersją interfejsu API, nie będzie podlegać temu limitowi. Aby zachować maksymalną zgodność, indeksator, który został utworzony z wcześniejszą `2019-05-06` wersją interfejsu API, a następnie zaktualizowany za pomocą wersji interfejsu API lub nowszej, nadal będzie **wykluczony** z limitów. Klienci powinni być świadomi negatywnego wpływu posiadania bardzo dużych złożonych kolekcji (jak wspomniano wcześniej) i zdecydowanie zalecamy utworzenie nowych indeksatorów z najnowszą wersją interfejsu API ga.

## <a name="synonym-limits"></a>Limity synonimów

Maksymalna dozwolona liczba map synonimów zależy od warstwy cenowej. Każda reguła może mieć do 20 rozszerzeń, gdzie rozszerzenie jest równoważnym terminem. Na przykład, biorąc pod uwagę "kot", skojarzenie z "kotkiem", "kotem" i "felis" (rodzaj dla kotów) będzie liczone jako 3 rozszerzenia.

| Zasób | Bezpłatna | Podstawowa (Basic) | S1 | S2 | S3 | S3-HD |L1 (właso. | L2 (l2) |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maksymalna liczba map synonimów |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maksymalna liczba reguł na mapę |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Zapytania na sekundę (QPS)

Szacunki QPS muszą być opracowywane niezależnie przez każdego klienta. Rozmiar i złożoność indeksu, rozmiar i złożoność zapytania oraz natężenie ruchu są podstawowymi determinantami QPS. Nie ma możliwości przedstawienia znaczących szacunków, gdy takie czynniki są nieznane.

Szacunki są bardziej przewidywalne po obliczeniu usług uruchomionych na dedykowanych zasobach (warstwy podstawowe i standardowe). Można oszacować QPS bliżej, ponieważ masz kontrolę nad więcej parametrów. Aby uzyskać wskazówki dotyczące podejścia do szacowania, zobacz [Wydajność i optymalizacja usługi Azure Cognitive Search.](search-performance-optimization.md)

W warstwach zoptymalizowane pod kątem magazynu należy oczekiwać niższej przepływności zapytań i większego opóźnienia niż warstwy standardowe.  Metodologia szacowania wydajności kwerendy, które będą występowy, jest taka sama jak w warstwach Standardowa.

## <a name="data-limits-ai-enrichment"></a>Limity danych (wzbogacanie AI)

[Potok wzbogacania sztucznej inteligencji,](cognitive-search-concept-intro.md) który wywołuje zasób analizy tekstu w [celu rozpoznawania jednostek,](cognitive-search-skill-entity-recognition.md) [wyodrębniania fraz kluczowych,](cognitive-search-skill-keyphrases.md) [analizy tonacji,](cognitive-search-skill-sentiment.md) [wykrywania języka](cognitive-search-skill-language-detection.md)i [wykrywania informacji umożliwiających identyfikację](cognitive-search-skill-pii-detection.md) użytkowników podlega limitom danych. Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Ograniczanie limitów

Żądania zapytań wyszukiwania i indeksowania są ograniczane w miarę zbliżania się systemu do szczytowej pojemności. Ograniczanie zachowuje się inaczej dla różnych interfejsów API. Interfejsy API kwerendy (Wyszukiwanie/sugerowanie/Autouzupełnianie) i indeksowanie interfejsów API dynamicznie ograniczać się na podstawie obciążenia usługi. Indeksuj interfejsy API mają statyczne limity szybkości żądań. 

Limity żądań szybkości statycznej dla operacji związanych z indeksem:

+ Indeksy listy (GET /indeksy): 5 na sekundę na jednostkę wyszukiwania
+ Pobierz indeks (GET /indexes/myindex): 10 na sekundę na jednostkę wyszukiwania
+ Utwórz indeks (POST /indeksy): 12 na minutę na jednostkę wyszukiwania
+ Tworzenie lub aktualizowanie indeksu (PUT /indexes/myindex): 6 na sekundę na jednostkę wyszukiwania
+ Usuń indeks (DELETE /indexes/myindex): 12 na minutę na jednostkę wyszukiwania 

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalnie 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL 8 KB
* Maksymalnie 1000 dokumentów na partię przesyłania, scalania lub usuwania indeksu
* Maksymalnie 32 pola w $orderby klauzuli
* Maksymalny rozmiar wyszukiwanego terminu to 32 766 bajtów (32 KB minus 2 bajty) tekstu zakodowanego na mocy UTF-8

<sup>1</sup> W usłudze Azure Cognitive Search treść żądania podlega górnej granicy 16 MB, nakładając praktyczny limit zawartości poszczególnych pól lub kolekcji, które w przeciwnym razie nie są ograniczone teoretycznymi limitami (zobacz [obsługiwane typy danych,](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) aby uzyskać więcej informacji na temat składu pól i ograniczeń).

## <a name="api-response-limits"></a>Limity odpowiedzi interfejsu API
* Maksymalnie 1000 dokumentów zwróconych na stronę wyników wyszukiwania
* Maksymalnie 100 sugestii zwróconych na żądanie interfejsu API sugestii

## <a name="api-key-limits"></a>Limity kluczy interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Klucze administracyjne są określone w nagłówku żądania i udzielają pełnego dostępu do odczytu i zapisu do usługi. Klucze kwerendy są tylko do odczytu, określone w adresie URL i zazwyczaj dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 klucze administratora na usługę
* Maksymalnie 50 kluczy zapytań na usługę