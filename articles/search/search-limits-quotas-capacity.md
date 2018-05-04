---
title: Usługa limity w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Ograniczenia usługi używane do planowania pojemności i maksymalna limity żądań i odpowiedzi dla usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 59b6efd8459f675bc44891d7b04251260fdd38e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="service-limits-in-azure-search"></a>Ograniczenia usługi w usłudze Azure Search
Maksymalne zawartości w pamięci masowej, obciążenia i ilości indeksów, dokumentów, a inne obiekty są zależne od tego, czy użytkownik [udostępnić usługi Azure Search](search-create-service-portal.md) w **wolne**, **podstawowe**, lub **Standardowe** warstw cenowych.

+ **Bezpłatne** jest wielodostępne usług udostępnionych, która pochodzi z subskrypcją platformy Azure.

+ **Podstawowe** zawiera dedykowany zasobów obliczeniowych dla obciążeń produkcyjnych na mniejszą skalę.

+ **Standardowe** działa na dedykowanych maszyn o większej pojemności do przechowywania i przetwarzania na każdym poziomie. Standard składa się z czterech poziomów: S1, S2 S3 i S3 HD.

  S3 wysokiej gęstości (S3 HD) została zaprojektowana dla określonych obciążeń: [wielodostępność](search-modeling-multitenant-saas-applications.md) i dużych ilości małych indeksów (milion dokumentów w indeksie, indeksów trzy tysiące na usługi). Ta warstwa nie zapewnia [funkcji indeksatora](search-indexer-overview.md). Na S3 HD wprowadzanie danych musi korzystać z podejścia wypychania, przy użyciu interfejsu API przekazywaniu danych ze źródła do indeksu. 

> [!NOTE]
> Usługa jest zainicjowana dla określonej warstwy. Przeskakiwanie warstw w celu uzyskania pojemności obejmuje Inicjowanie obsługi administracyjnej nowej usługi (uaktualnienie w miejscu, nie istnieje). Aby uzyskać więcej informacji, zobacz [wybierz jednostki SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej na temat dostosowywania wydajności w ramach usługi została już przydzielona, zobacz [skalowania zasobu poziomy kwerendy i indeksowania obciążeń](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity magazynu
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksu

| Zasób | Bezpłatna | Podstawowe&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |
| Maksymalna pól w indeksie |1000 |100 |1000 |1000 |1000 |1000 |
| Maksymalna profil oceniania dla każdej indeksu |100 |100 |100 |100 |100 |100 |
| Maksymalna funkcje dla profilu |8 |8 |8 |8 |8 |8 |

<sup>1</sup> podstawowe usługi utworzone po zwiększenie limitu 15 indeksów, źródła danych i indeksatorów mieć późne 2017 r. Usługi utworzony wcześniej mają 5. Warstwa podstawowa jest tylko jednostki SKU z dolny limit 100 pól w indeksie.

## <a name="document-limits"></a>Limity dokumentów 

W większości regionów usługi Azure Search ceny warstw (Basic, S1, S2, S3, S3 HD) liczby dokumentów nieograniczone dla wszystkich usług utworzono po listopada/grudnia 2017 r. Tej sekcji wymieniono regionach, gdzie limity mają zastosowanie i jak ustalić, czy usługa dotyczy. 

Aby ustalić, czy usługa ma limity dokumentów, sprawdź na kafelku użycia na stronie przeglądu usługi. Dokument liczby są nieograniczone lub poddać limit oparte na warstwie.

  ![Kafelek użycie](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Regiony i usługi o limity dokumentów

Usługi o granicach utworzonych przed późne 2017 lub działają w centrach danych, z użyciem klastrów małą pojemność hostingu usług wyszukiwanie Azure. Centra danych dotyczy znajdują się w następujących regionach:

+ Australia Wschodnia
+ Azja Wschodnia
+ Indie Środkowe
+ Japonia Zachodnia
+ Środkowo-zachodnie stany USA

Dla usług limitom dokument Zastosuj następujące limity maksymalna:

|  Bezpłatna | Podstawowa | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1 mln |15 mln na partycję lub 180 mln na usługę |60 mln na partycję lub 720 mln na usługę |120 mln na partycję lub 1,4 mld na usługę |1 mln na indeks lub 200 mln na partycję |

> [!Note] 
> W przypadku usług o wysokiej gęstości S3 utworzone po późne 2017 dokument 200 milionów dla każdej partycji został usunięty ale dokumentu 1 milion na pozostaje limit indeksu.


### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentów na wywołania interfejsu API

Rozmiar maksymalny dokumentu podczas wywoływania indeksu interfejsu API jest około 16 MB.

Rozmiar dokumentu jest rzeczywiście limit rozmiaru treści żądania interfejsu API indeksu. Ponieważ jednocześnie można przekazać partii wielu dokumentów do interfejsu API indeksu, limit rozmiaru realistycznie zależy liczby dokumentów w partii. Dla partii z pojedynczego dokumentu rozmiar maksymalny dokumentu jest 16 MB JSON.

Aby zachować rozmiar dokumentu w dół, pamiętaj, aby wykluczyć z systemem innym niż kolejność danych w żądaniu. Obrazy i inne dane binarne nie są bezpośrednio zapytań i nie powinny być przechowywane w indeksie. Do integracji z systemem innym niż kolejność danych w wynikach wyszukiwania, zdefiniuj niemożliwych pole, które przechowuje URL odwołania do zasobu.

## <a name="indexer-limits"></a>Limity indeksatora

Podstawowe usługi utworzone po późne 2017 mają zwiększenie limitu 15 indeksów, źródła danych i indeksatorów.

| Zasób | Bezpłatne&nbsp;<sup>1</sup> | Podstawowe&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |ND |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |ND |
| Maksymalne obciążenie indeksowania dla wywołania |10 000 dokumentów |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |ND |
| Maksymalny czas działania | 1 – 3 minuty |24 godziny |24 godziny |24 godziny |24 godziny |ND  |
| Indeksator obiektów blob: rozmiar maksymalny obiektu blob, MB |16 |16 |128 |256 |256 |ND  |
| Indeksator obiektów blob: Maksymalna liczba znaków zawartości wyodrębniony z obiektu blob |32,000 |64,000 |4 miliony |4 miliony |4 miliony |ND |

<sup>1</sup> bezpłatnych usług ma indeksatora maksymalny czas wykonywania 3 minuty źródła obiektów blob i 1 minuty dla wszystkich źródeł danych.

<sup>2</sup> podstawowe usługi utworzone po zwiększenie limitu 15 indeksów, źródła danych i indeksatorów mieć późne 2017 r. Usługi utworzony wcześniej mają 5.

<sup>3</sup> S3 HD services nie obsługują indeksatora.

## <a name="queries-per-second-qps"></a>Zapytania na sekundę (QPS)

Szacuje QPS musi być opracowana niezależnie przez każdego klienta. Rozmiar indeksu i złożoność, rozmiar kwerendy i złożoność oraz ilość ruchu sieciowego są głównej uwarunkowania QPS. Nie istnieje sposób oferowanie szacuje znaczenie w przypadku takich czynników nieznany.

Szacuje są bardziej przewidywalne podczas obliczania na usługi działające na zasobów dedykowanych (warstwy Basic i Standard). QPS więcej można oszacować ściśle, ponieważ mają kontrolę nad więcej parametrów. Aby uzyskać wskazówki dotyczące szacowania podejście, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalnie 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL 8 KB
* Maksymalna 1000 dokumentów na partię indeksu przekazuje, scala lub usuwanie
* Maksymalny 32 pola w klauzuli $orderby
* Rozmiar termin wyszukiwania maksymalna to 32 766 bajtów (32 KB minus 2 bajty) tekstu kodowany w formacie UTF-8

<sup>1</sup> w usłudze Azure Search treści żądania podlega górny limit 16 MB, nakładające praktyczne limit na zawartość poszczególnych pól lub kolekcje, które w przeciwnym razie nie są ograniczone przez teoretycznego limity (zobacz [obsługiwane danych typy](https://msdn.microsoft.com/library/azure/dn798938.aspx) uzyskać więcej informacji dotyczących ograniczenia i pola kompozycji).

## <a name="api-response-limits"></a>Limity odpowiedzi interfejsu API
* Maksymalna 1000 zwrócone na stronę wyników wyszukiwania
* Maksymalną 100 sugestie zwracane zgodnie z żądaniem Sugeruj interfejsu API

## <a name="api-key-limits"></a>Limity klucz interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Kluczy administratora są określone w nagłówku żądania i przyznać dostęp do odczytu zapisu do usługi. Klucze zapytania są tylko do odczytu, określona w adresie URL i zazwyczaj dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 kluczy administratora usługi
* Maksymalnie 50 kluczy zapytania dla usługi
