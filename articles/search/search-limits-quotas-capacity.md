---
title: Limity usługi dla warstw i jednostek SKU — Azure Search
description: Limity usługi używane do planowania pojemności oraz maksymalne limity żądań i odpowiedzi dla Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: c2d4cae1689701704c866833c99ca616bbd01ec5
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300658"
---
# <a name="service-limits-in-azure-search"></a>Limity usługi w Azure Search
Maksymalne limity dotyczące magazynu, obciążeń i ilości indeksów, dokumentów i innych obiektów zależą od tego, czy [Zainicjowano obsługę administracyjną Azure Search](search-create-service-portal.md) w warstwach **bezpłatna**, **podstawowa**, **standardowa**i **zoptymalizowana pod kątem magazynu** .

+ **Bezpłatna** to wielodostępna Usługa udostępniona z subskrypcją platformy Azure.

+ Funkcja **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych w mniejszej skali.

+ **Standardowe** przebiegi na dedykowanych maszynach o większej pojemności magazynu i przetwarzania na każdym poziomie. Standard obejmuje cztery poziomy: S1, S2, S3 i S3 HD.

+ **Zoptymalizowane pod kątem magazynu** są uruchamiane na dedykowanych maszynach o większej liczbie magazynów, przepustowości magazynu i pamięci niż w **warstwie Standardowa**. Optymalizacja pod kątem magazynu jest na dwa poziomach: L1 i L2

> [!NOTE]
> Od 1 lipca wszystkie warstwy są ogólnie dostępne, w tym warstwy zoptymalizowane pod kątem magazynu. Wszystkie ceny można znaleźć na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) .

  Wysoka gęstość S3 (S3 HD) jest zaprojektowana dla określonych obciążeń [](search-modeling-multitenant-saas-applications.md) : Wielodostępność i duże ilości małych indeksów (1 000 000 dokumentów na indeks, 3000 indeksów na usługę). Ta warstwa nie udostępnia [funkcji indeksatora](search-indexer-overview.md). W systemie S3 HD dane muszą wykorzystać podejście wypychane przy użyciu wywołań interfejsu API w celu wypychania danych ze źródła do indeksu. 

> [!NOTE]
> Usługa została zainicjowana z określoną warstwą. Warstwy przeskoków mające na celu uzyskanie pojemności wymagają aprowizacji nowej usługi (uaktualnienie w miejscu nie istnieje). Aby uzyskać więcej informacji, zobacz [Wybieranie jednostki SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej o dostosowywaniu pojemności w ramach już zainicjowanej usługi, zobacz [poziomy zasobów skalowania dla obciążeń zapytań i indeksowania](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity magazynu
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksu

| Resource | Wolne | Podstawowa&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | PAMIĘCI PODRĘCZNEJ L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |10 |10 |
| Maksymalna liczba prostych pól na indeks |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maksymalna liczba złożonych pól kolekcji na indeks |40 |40 |40 |40 |40 |40 |40 |40 |
| Maksymalna liczba elementów we wszystkich złożonych kolekcjach na dokument |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maksymalna głębokość pól złożonych |10 |10 |10 |10 |10 |10 |10 |10 |
| Maksymalna [](https://docs.microsoft.com/rest/api/searchservice/suggesters) liczba sugestii na indeks |1 |1 |1 |1 |1 |1 |1 |1 |
| Maksymalna liczba [profilów oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na indeks |100 |100 |100 |100 |100 |100 |100 |100 |
| Maksymalna liczba funkcji na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) w indeksach. Warstwa Podstawowa jest jedyną jednostką SKU z niższym limitem 100 pól na indeks.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity dokumentów 

Od października 2018 żadne dokumenty nie mają już żadnych ograniczeń<sup>1</sup> dla każdej nowej usługi utworzonej w dowolnej warstwie rozliczeniowej (podstawowa, S1, S2, S3, S3 HD) w dowolnym regionie. Chociaż większość regionów miało nieograniczoną liczbę dokumentów od listopada/grudnia 2017, istniały pięć regionów, które nadal nakładają limity dokumentów. W zależności od tego, kiedy i gdzie została utworzona usługa wyszukiwania, może być uruchomiona usługa, która nadal podlega limitom dokumentów.

Aby ustalić, czy usługa ma limity dokumentów, sprawdź kafelek użycie na stronie Przegląd usługi. Liczby dokumentów są nieograniczone lub podlegają limitowi zależnemu od warstwy.

  ![Kafelek użycie](media/search-limits-quotas-capacity/portal-usage-tile.png)

<sup>1</sup> pomimo tego, że nie ma żadnych limitów dokumentów specyficznych dla jednostki SKU, każdy indeks jest nadal objęty maksymalnym bezpiecznym limitem zapewniającym stabilność usługi. Ten limit pochodzi z Lucene. Każdy dokument Azure Search jest zaindeksowany wewnętrznie jako jeden lub więcej dokumentów Lucene. Liczba dokumentów Lucene na dokument usługi Azure Search zależy od łącznej liczby elementów w złożonych polach kolekcji. Każdy element jest indeksowany jako oddzielny dokument Lucene. Na przykład dokument z 3 elementami w polu kolekcja złożona zostanie zindeksowany jako 4 dokumenty Lucene-1 dla samego dokumentu i 3 dla elementów. Maksymalna liczba dokumentów Lucene jest około 25 000 000 000 na indeks.

### <a name="regions-previously-having-document-limits"></a>Regiony posiadające wcześniej limity dokumentów

Jeśli portal wskazuje limit dokumentu, usługa została utworzona przed późnym 2017 lub została utworzona w centrum danych przy użyciu klastrów o niższej pojemności do hostowania usług Azure Search:

+ Australia Wschodnia
+ Azja Wschodnia
+ Indie Środkowe
+ Japonia Zachodnia
+ Środkowo-zachodnie stany USA

W przypadku usług objętych limitami dokumentu obowiązują następujące maksymalne limity:

|  Wolne | Podstawowa | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1&nbsp;milion |15 mln na partycję lub 180 mln na usługę |60 mln na partycję lub 720 mln na usługę |120 mln na partycję lub 1,4 mld na usługę |1 mln na indeks lub 200 mln na partycję |

Jeśli usługa ma limity, które blokują, Utwórz nową usługę i ponownie Opublikuj całą zawartość do tej usługi. Nie istnieje mechanizm bezproblemowego ponownego inicjowania obsługi administracyjnej usługi na nowym sprzęcie w tle.

> [!Note] 
> W przypadku usług o wysokiej gęstości, które zostały utworzone po upływie 2017, dokument 200 000 000 na partycję został usunięty, ale pozostały limit rozmiaru dokumentu 1 000 000 na indeks.


### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentu dla wywołania interfejsu API

Maksymalny rozmiar dokumentu podczas wywoływania interfejsu API indeksu wynosi około 16 megabajtów.

Rozmiar dokumentu jest w rzeczywistości limitem rozmiaru treści żądania interfejsu API indeksu. Ponieważ jednocześnie można przekazać partię wielu dokumentów do interfejsu API indeksu, ograniczenie rozmiaru jest realistyczne od liczby dokumentów w partii. W przypadku partii z jednym dokumentem maksymalny rozmiar dokumentu wynosi 16 MB w formacie JSON.

Aby zachować rozmiar dokumentu w dół, pamiętaj, aby wykluczyć z żądania dane inne niż queryable. Obrazy i inne dane binarne nie są bezpośrednio Queryable i nie powinny być przechowywane w indeksie. Aby zintegrować dane niequeryablene z wynikami wyszukiwania, Zdefiniuj pole niedrukowalne, które przechowuje odwołanie do tego zasobu.

## <a name="indexer-limits"></a>Limity indeksatora

Maksymalne czasy działania są dostępne w celu zapewnienia równowagi i stabilności usługi jako całości, ale większe zestawy danych mogą wymagać więcej czasu indeksowania niż maksymalna dozwolona wartość. Jeśli zadanie indeksowania nie może zakończyć się w maksymalnym dozwolonym czasie, spróbuj uruchomić je zgodnie z harmonogramem. Harmonogram śledzi Stan indeksowania. Jeśli zaplanowane zadanie indeksowania zostanie przerwane z dowolnego powodu, indeksator może zostać pobrany w miejscu, w którym został on ostatnio pozostawiony podczas następnego zaplanowanego uruchomienia.


| Resource | Bezpłatnie&nbsp;<sup>1</sup> | Podstawowa&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |PAMIĘCI PODRĘCZNEJ L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |ND |10 |10 |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |ND |10 |10 |
| Maksymalna umiejętności <sup>4</sup> |3 |5 lub 15 |50 |200 |200 |ND |10 |10 |
| Maksymalne ładowanie indeksowania na wywołanie |10 000 dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |ND |Bez limitu |Bez limitu |
| Minimalny harmonogram | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maksymalny czas działania <sup>5</sup> | 1-3 minut |24 godziny |24 godziny |24 godziny |24 godziny |ND  |24 godziny |24 godziny |
| Maksymalny czas działania dla umiejętności wyszukiwania poznawczego lub indeksowania obiektów BLOB za pomocą analizy obrazów <sup>5</sup> | 3-10 minut |2 godziny |2 godziny |2 godziny |2 godziny |ND  |2 godziny |2 godziny |
| Indeksator obiektów blob: maksymalny rozmiar obiektu BLOB, MB |16 |16 |128 |256 |256 |ND  |256 |256 |
| Indeksator obiektów blob: Maksymalna liczba znaków zawartości wyodrębnionych z obiektu BLOB |32,000 |64,000 |4&nbsp;mln |4&nbsp;mln |4&nbsp;mln |ND |4&nbsp;mln |4&nbsp;mln |

<sup>1</sup> bezpłatne usługi mają maksymalny czas wykonywania indeksatora wynoszący 3 minuty dla źródeł obiektów blob i 1 minuty dla wszystkich innych źródeł danych. W przypadku indeksowania AI, które wywołuje Cognitive Services, bezpłatne usługi są ograniczone do 20 bezpłatnych transakcji dziennie, gdzie transakcja jest definiowana jako dokument, który został pomyślnie przeszedł przez potok wzbogacania.

<sup>2</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) dla indeksatorów, źródeł danych i umiejętności.

<sup>3</sup> usługi HD S3 nie obejmują obsługi indeksatora.

<sup>4</sup> maksymalnie 30 umiejętności na zestawu umiejętności.

<sup>5</sup> obciążeń wyszukiwania poznawczego i analizy obrazów w indeksie obiektów blob platformy Azure mają krótsze czasy działania niż zwykłe indeksowanie tekstu. Analiza obrazów i przetwarzanie języka naturalnego są intensywnie pracochłonne i zużywają nieproporcjonalne ilości dostępnej mocy obliczeniowej. Czas działania został zmniejszony w celu nadania innym zadania w kolejce możliwości uruchomienia.  

## <a name="synonym-limits"></a>Limity synonimów

Maksymalna liczba dozwolonych map synonimów jest różna w zależności od warstwy cenowej. Każda reguła może mieć do 20 rozszerzeń, gdzie rozwinięcie jest terminem equivalvent. Na przykład podaną "Cat", skojarzenie z "Kitty", "Feline" i "Felis" (rodzaj dla kotów) będzie liczona jako 3 rozszerzenia.

| Resource | Wolne | Podstawowa | S1 | S2 | S3 | S3 — HD |L1 | PAMIĘCI PODRĘCZNEJ L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maksymalne mapy synonimów |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maksymalna liczba reguł na mapę |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Zapytania na sekundę (zapytań)

Oszacowania zapytań muszą być opracowane niezależnie od każdego klienta. Rozmiar indeksu i złożoność, rozmiar zapytania i złożoność oraz wielkość ruchu to podstawowe znaczniki zapytań. Nie ma możliwości zaoferowania znaczących szacunków, gdy takie czynniki są nieznane.

Oszacowania są bardziej przewidywalne, gdy są obliczane w usługach uruchomionych na dedykowanych zasobach (warstwach Podstawowa i standardowa). Możesz oszacować zapytań bardziej blisko, ponieważ masz kontrolę nad większymi parametrami. Aby uzyskać wskazówki dotyczące podejścia do oceny, zobacz [Azure Search wydajność i optymalizacja](search-performance-optimization.md).

W przypadku warstw zoptymalizowanych pod kątem magazynu należy oczekiwać mniejszej przepływności zapytań i wyższych opóźnień niż w przypadku warstw standardowych.  Metodologia oszacowania wydajności zapytań, która będzie w trakcie pracy, jest taka sama jak w przypadku warstw standardowych.

## <a name="data-limits-cognitive-search"></a>Limity danych (wyszukiwanie poznawcze)

[Potok wyszukiwania poznawczego](cognitive-search-concept-intro.md) , który tworzy wywołania do zasobu analiza tekstu na potrzeby [rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), [wyodrębniania fraz kluczowych](cognitive-search-skill-keyphrases.md), [analizy tonacji](cognitive-search-skill-sentiment.md)i [wykrywania języka](cognitive-search-skill-language-detection.md) , podlega limitom danych. Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalnie 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL 8 KB
* Maksymalna 1000 dokumentów na partię operacji przekazywania, scalania lub usuwania indeksu
* Maksymalna 32 pól w klauzuli $orderby
* Maksymalny rozmiar terminu wyszukiwania to 32 766 bajtów (32 KB minus 2 bajty) tekstu zakodowanego w formacie UTF-8

<sup>1</sup> w Azure Search treść żądania podlega górnemu limitowi 16 MB, co nakłada praktyczny limit zawartości poszczególnych pól lub kolekcji, które nie są w inny sposób ograniczone przez limity teoretyczne (zobacz [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , aby uzyskać więcej informacje na temat kompozycji i ograniczeń pól).

## <a name="api-response-limits"></a>Limity odpowiedzi interfejsu API
* Maksymalna 1000 dokumentów zwróconych na stronę wyników wyszukiwania
* Maksymalna 100 sugestii zwróconych na żądanie interfejsu API sugestii

## <a name="api-key-limits"></a>Limity kluczy interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Klucze administratora są określone w nagłówku żądania i zapewniają pełny dostęp do odczytu i zapisu do usługi. Klucze zapytań są tylko do odczytu, określone w adresie URL i zazwyczaj są dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 klucze administracyjne na usługę
* Maksymalnie 50 kluczy zapytań na usługę
