---
title: Usługa limity w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Ograniczenia usługi używane do planowania pojemności i maksymalna limity żądań i odpowiedzi dla usługi Azure Search.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/26/2018
ms.author: heidist
ms.openlocfilehash: fb2234e79e8deb98a94068f31a40c8f0b415d7ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="service-limits-in-azure-search"></a>Ograniczenia usługi w usłudze Azure Search
Maksymalne zawartości w pamięci masowej, obciążenia i ilości indeksów, dokumentów, a inne obiekty są zależne od tego, czy możesz [udostępnić usługi Azure Search](search-create-service-portal.md) w **wolne**, **podstawowe**, lub **standardowe** warstwy cenowej.

* **Bezpłatne** jest wielodostępne usług udostępnionych, która pochodzi z subskrypcją platformy Azure. 
* **Podstawowe** zawiera dedykowany zasobów obliczeniowych dla obciążeń produkcyjnych na mniejszą skalę.
* **Standardowe** działa na dedykowanych maszyn o większej pojemności do przechowywania i przetwarzania na każdym poziomie. Standard składa się z czterech poziomów: S1, S2 S3 i o wysokiej gęstości S3 (S3 HD).

> [!NOTE]
> Usługa jest zainicjowana dla określonej warstwy. Przeskakiwanie warstw w celu uzyskania pojemności obejmuje Inicjowanie obsługi administracyjnej nowej usługi (uaktualnienie w miejscu, nie istnieje). Aby uzyskać więcej informacji, zobacz [wybierz jednostki SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej na temat dostosowywania wydajności w ramach usługi została już przydzielona, zobacz [skalowania zasobu poziomy kwerendy i indeksowania obciążeń](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="service-limits"></a>Limity usługi
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="index-limits"></a>Limity indeksu

| Zasób | Bezpłatna | Podstawowa | S1 | S2 | S3 | S3 (wysoka gęstość) |
| --- | --- | --- | --- | --- | --- | --- |
| Maksymalna pól w indeksie |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Maksymalna profil oceniania dla każdej indeksu |100 |100 |100 |100 |100 |100 |
| Maksymalna funkcje dla profilu |8 |8 |8 |8 |8 |8 |

<sup>1</sup> warstwa podstawowa jest tylko jednostki SKU z dolny limit 100 pól w indeksie.

## <a name="indexer-limits"></a>Limity indeksatora

| Zasób | Bezpłatna | Podstawowa | S1 | S2 | S3 | S3 (wysoka gęstość) |
| --- | --- | --- | --- | --- | --- | --- |
| Maksymalne obciążenie indeksowania dla wywołania |10 000 dokumentów |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |Ograniczone tylko dokumenty maksymalna |N/D <sup>1</sup> |
| Maksymalny czas działania | 1 – 3 minuty <sup>2</sup> |24 godziny |24 godziny |24 godziny |24 godziny |N/D <sup>1</sup> |
| Indeksator obiektów blob: rozmiar maksymalny obiektu blob, MB |16 |16 |128 |256 |256 |N/D <sup>1</sup> |
| Indeksator obiektów blob: Maksymalna liczba znaków zawartości wyodrębniony z obiektu blob |32,000 |64,000 |4 miliony |4 miliony |4 miliony |N/D <sup>1</sup> |

<sup>1</sup> S3 HD nie obsługuje obecnie indeksatorów. Jeśli masz pilną potrzebę dla tej funkcji, skontaktuj się z pomocą techniczną platformy Azure.

<sup>2</sup> indeksatora maksymalny czas wykonywania warstwę bezpłatna to 3 minuty źródła obiektów blob i 1 minuty dla wszystkich źródeł danych.


## <a name="document-size-limits"></a>Limity rozmiaru dokumentu
| Zasób | Bezpłatna | Podstawowa | S1 | S2 | S3 | S3 (wysoka gęstość) |
| --- | --- | --- | --- | --- | --- | --- |
| Rozmiar poszczególnych dokumentu na indeks interfejsu API |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |

Odwołuje się do rozmiar maksymalny dokumentu podczas wywoływania indeksu interfejsu API. Rozmiar dokumentu jest rzeczywiście limit rozmiaru treści żądania interfejsu API indeksu. Ponieważ jednocześnie można przekazać partii wielu dokumentów do interfejsu API indeksu, limit rozmiaru faktycznie zależy liczby dokumentów w partii. Dla partii z pojedynczego dokumentu rozmiar maksymalny dokumentu jest 16 MB JSON.

Aby zachować rozmiar dokumentu w dół, pamiętaj, aby wykluczyć z systemem innym niż kolejność danych w żądaniu. Obrazy i inne dane binarne nie są bezpośrednio zapytań i nie powinny być przechowywane w indeksie. Do integracji z systemem innym niż kolejność danych w wynikach wyszukiwania, zdefiniuj niemożliwych pole, które przechowuje URL odwołania do zasobu.

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
Klucze interfejsu API są używane do uwierzytelniania usługi. Istnieją dwa typy. Kluczy administratora są określone w nagłówku żądania i przyznać dostęp do odczytu zapisu do usługi. Klucze zapytania są tylko do odczytu, określona w adresie URL i zazwyczaj dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 kluczy administratora usługi
* Maksymalnie 50 kluczy zapytania dla usługi
