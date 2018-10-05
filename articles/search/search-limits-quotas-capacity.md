---
title: Usługa limity usługi Azure Search | Dokumentacja firmy Microsoft
description: Limity usługi używany do planowania wydajności i maksymalne limity żądań i odpowiedzi dla usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: heidist
ms.openlocfilehash: 8abcc90bf72544e6226d6c8487d2951b60ea6d29
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802156"
---
# <a name="service-limits-in-azure-search"></a>Limity usługi w usłudze Azure Search
Maksymalne zawartości w pamięci masowej, obciążeń i ilości indeksów i dokumentów, a inne obiekty zależne od tego, czy możesz [aprowizowanie usługi Azure Search](search-create-service-portal.md) na **bezpłatna**, **podstawowe**, lub **Standardowa** warstw cenowych.

+ **Bezpłatne** jest udostępnianą usługą wielodostępną, dostarczanego z subskrypcją platformy Azure.

+ **Podstawowe** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę.

+ **Standardowa** działa na dedykowanych maszynach o większej pojemności do przechowywania i przetwarzania na każdym poziomie. Standardowa jest oferowana w czterech poziomów: S1, S2 i S3 oraz wysoka gęstość S3.

  S3 High Density (wysoka gęstość S3) jest zaprojektowany z myślą o konkretnych obciążeń: [wielodostępu](search-modeling-multitenant-saas-applications.md) i dużych ilości małych indeksów (miliona dokumentów na indeks, trzy tysiące indeksów na usługę). Ta warstwa nie zapewnia [funkcji indeksatora](search-indexer-overview.md). Na wysoka gęstość S3 pozyskiwania danych musi korzystać z podejścia push przy użyciu wywołań interfejsu API wypychania danych ze źródła do indeksu. 

> [!NOTE]
> Usługa jest zainicjowana określonej warstwy. Przeskakiwanie poziomach w celu uzyskania pojemności obejmuje Inicjowanie obsługi administracyjnej nowej usługi (uaktualnienie w miejscu, nie istnieje). Aby uzyskać więcej informacji, zobacz [wybierz jednostkę SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej na temat dostosowywania wydajności w ramach usługi zostały już aprowizowane, zobacz [poziomy skalowania zasobów dla zapytań i indeksowania obciążeń](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity przestrzeni dyskowej
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksu

| Zasób | Bezpłatna | Podstawowe&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |
| Maksymalna pola indeksu |1000 |100 |1000 |1000 |1000 |1000 |
| Maksymalna [sugestory](https://docs.microsoft.com/rest/api/searchservice/suggesters) indeksu |1 |1 |1 |1 |1 |1 |
| Maksymalna [profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) indeksu |100 |100 |100 |100 |100 |100 |
| Maksymalna funkcje każdego profilu |8 |8 |8 |8 |8 |8 |

<sup>1</sup> podstawowe usługi utworzone po późno 2017 nie zwiększone limity indeksach 15, źródła danych i indeksatorów. Usługi utworzonej wcześniej mieć 5. Warstwa Basic jest tylko jednostki SKU z niższy limit 100 pól w indeksie.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity dokumentów 

Od października 2018 r. nie ma już wszystkie limity dokumentów usługi nowe utworzono wszelkie płatne warstwy (Basic, S1, S2, S3, wysoka gęstość S3), w dowolnym regionie. Podczas gdy większość regionów mieli nieograniczone dokumentu jest liczona od listopada/grudnia 2017 r., było pięciu regionach, które nakładają ograniczenia dokumentu w dalszym ciągu. W zależności od tego, kiedy i gdzie usługa wyszukiwania została utworzona może być uruchomiona usługa, która jest nadal podlega procesowi limity dokumentów.

Aby ustalić, czy usługa ma limity dokumentów, sprawdź Kafelek użycie na stronie Przegląd usługi. Liczba dokumentów są nieograniczone lub podlega limitowi opartego na warstwie.

  ![Kafelek użycie](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regiony wcześniej limity dokumentów

Portal wskazuje limit dokumentów, usługa została utworzona późno 2017 r., czy został utworzony w centrum danych przy użyciu klastrów niższej wydajności do obsługi usług Azure Search:

+ Australia Wschodnia
+ Azja Wschodnia
+ Indie Środkowe
+ Japonia Zachodnia
+ Środkowo-zachodnie stany USA

W przypadku usług, które podlegają limity dokumentów mają zastosowanie następujące maksymalny limit:

|  Bezpłatna | Podstawowa | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1 mln |15 mln na partycję lub 180 mln na usługę |60 mln na partycję lub 720 mln na usługę |120 mln na partycję lub 1,4 mld na usługę |1 mln na indeks lub 200 mln na partycję |

Jeśli usługa ma ograniczenia, które blokują możesz, Utwórz nową usługę i ponownie opublikować całą zawartość do tej usługi. Nie istnieje żaden mechanizm służący bezproblemowo reprovisioning usługi na nowym sprzęcie w tle.

> [!Note] 
> W przypadku usług S3 High Density utworzonych po 2017 pod koniec dokumentu 200 mln na partycję została usunięta ale dokument 1 mln na indeks limit pozostaje.


### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentu na wywołania interfejsu API

Rozmiar maksymalny dokumentu, podczas wywoływania interfejsu API indeksu to około 16 megabajtów.

Rozmiar dokumentu jest faktycznie limit rozmiaru indeksu interfejsu API treści żądania. Ponieważ jednocześnie można przekazać partii wiele dokumentów do indeksu interfejsu API, limit rozmiaru realistycznie zależy od liczby dokumentów w partii. Dla usługi batch za pomocą pojedynczego dokumentu rozmiar maksymalny dokumentu jest 16 MB JSON.

Aby zachować rozmiaru dokumentu,, pamiętaj, aby wykluczyć nie umożliwia zadawania zapytań dane z żądania. Obrazy i inne dane binarne nie są bezpośrednio umożliwia zadawania zapytań i nie powinny być zapisane w indeksie. Do integracji danych nie umożliwia zadawania zapytań w wynikach wyszukiwania, zdefiniuj niemożliwych pola, które przechowuje odwołanie adresu URL do zasobu.

## <a name="indexer-limits"></a>Limity indeksatora

Podstawowe usługi utworzonych po późno 2017 nie zwiększone limity indeksach 15, źródła danych, wykorzystując bieżące i indeksatorów.

Operacje dużej ilości zasobów, takich jak analiza obrazu w indeksowanie obiektów blob platformy Azure lub przetwarzania języka naturalnego w cognitive search mają krótsze maksymalne czasy uruchomione, więc inne zadania indeksowania są spełnione. Jeśli zadania nie można wykonać w ramach maksymalny dozwolony czas, spróbuj uruchomić go zgodnie z harmonogramem. Harmonogram przechowuje informacje o stanie indeksowania. Jeśli zaplanowane zadanie indeksowania zostanie przerwany z jakiegokolwiek powodu, indeksator może przejmą tam, gdzie ją ostatnia przerwaliśmy na następne zaplanowane uruchomienie.

| Zasób | Bezpłatne&nbsp;<sup>1</sup> | Podstawowe&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |ND |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |ND |
| Maksymalna dokładniejsze <sup>4</sup> |3 |5 lub 15 |50 |200 |200 |ND |
| Maksymalne obciążenie indeksowania dla wywołania |10 000 dokumentów |Ograniczone tylko przez maksymalna liczba dokumentów |Ograniczone tylko przez maksymalna liczba dokumentów |Ograniczone tylko przez maksymalna liczba dokumentów |Ograniczone tylko przez maksymalna liczba dokumentów |ND |
| Maksymalny czas działania <sup>5</sup> | 1 – 3 minuty |24 godziny |24 godziny |24 godziny |24 godziny |ND  |
| Maksymalny czas dokładniejsze wyszukiwanie kognitywne lub obiektu blob indeksowanie za pomocą analizy obrazów działania <sup>5</sup> | 3 – 10 minut |2 godziny |2 godziny |2 godziny |2 godziny |ND  |
| Indeksatora obiektów blob: rozmiar maksymalny obiektu blob, MB |16 |16 |128 |256 |256 |ND  |
| Indeksatora obiektów blob: Maksymalna liczba znaków w treści wyodrębnić z obiektu blob |32,000 |64,000 |4 mln |4 mln |4 mln |ND |

<sup>1</sup> do usług bezpłatnych ma indeksatora maksymalny czas wykonywania więcej niż trzy minuty źródła obiektów blob i 1 minuty dla wszystkich źródeł danych.

<sup>2</sup> podstawowe usługi utworzone po późno 2017 nie zwiększone limity indeksach 15, źródła danych i indeksatorów. Usługi utworzonej wcześniej mieć 5.

<sup>3</sup> wysoka gęstość S3 usługi nie ma obsługi indeksatorów.

<sup>4</sup> maksymalnie 30 umiejętności na zestawu umiejętności.

<sup>5</sup> obciążenia usługa cognitive search i analizy obrazów w indeksowanie obiektów blob platformy Azure mają krótszy czas uruchomione niż w przypadku Indeksowanie Zwykły tekst. Obraz analizy i przetwarzania języka naturalnego wymagają dużej mocy obliczeniowej i używanie nieproporcjonalnie duże ilości dostępnych mocy obliczeniowej. Czas działania został skrócony do umożliwić innych zadań w kolejce do uruchomienia.  

## <a name="queries-per-second-qps"></a>Zapytania na sekundę (QPS)

Liczby zapytań na Sekundę oszacowania musi być opracowana niezależnie przez każdy klient korzystający z. Rozmiar indeksu i złożoność, rozmiaru zapytania i złożoności i natężenie ruchu są podstawowym uwarunkowania liczby zapytań na Sekundę. Nie istnieje żaden sposób zapewniają znaczących szacunki, gdy takie czynniki są nieznane.

Szacunki są bardziej przewidywalna, gdy została obliczona dla usług uruchomionych na dedykowanych zasobów (w warstwach podstawowa i standardowa). Liczby zapytań na Sekundę więcej można oszacować ściśle, ponieważ masz kontrolę nad jeden z parametrów. Aby uzyskać wskazówki na temat szacowania podejście, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalna liczba 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL o rozmiarze 8 KB
* Maksymalna liczba dokumentów 1000 na partię indeksu przekazuje, scala lub usuwa
* Maksymalny 32 pola w klauzuli $orderby
* Rozmiar termin maksymalna to 32 766 bajtów (32 KB minus 2 bajtów) tekstu kodowany w formacie UTF-8

<sup>1</sup> w usłudze Azure Search treści żądania podlega górnego limitu 16 MB, nakładające praktyczne ograniczenie zawartość poszczególnych pól lub kolekcje, które w przeciwnym razie nie jest ograniczona przez limity teoretycznych (zobacz [obsługiwane danych typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) uzyskać więcej informacji o skład pola i ograniczenia).

## <a name="api-response-limits"></a>Ogranicza odpowiedź interfejsu API
* Maksymalna liczba dokumentów 1000 zwrócenia na stronę wyników wyszukiwania
* Maksymalna sugestie 100 zwracanych na żądanie zasugerować interfejsu API

## <a name="api-key-limits"></a>Ograniczenia klucza interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Klucze administratora są określone w nagłówku żądania i przyznać dostęp Pełna odczytu i zapisu do usługi. Klucze zapytania są tylko do odczytu, określona w polu adres URL i zazwyczaj dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 kluczy administratora usługi
* Maksymalnie 50 klucze zapytania na usługę
